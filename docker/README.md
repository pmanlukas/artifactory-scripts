# How to populate a local artifactory repository with layers from docker hub
## Purpose
Repeatedly downloading layers from docker hub to your local machine gets really old really fast.  It's slow and consumes 
bandwidth you might not have.  On the other hand keeping those layers around when they're not being actively used chews
up a lot of disk space.

If you have an artifactory server locally these scripts will help a bit with that pain.  `pull-push-repo.sh` pulls 
images from docker hub onto your machine then pushes them to the artifactory server.  `localize-repo.sh` examines
images to determine what type of package manager that image uses and edits the configuration files to use your local
artifactory to provide the packages.

## Configuration
Since both scripts need the same sorts of information they share a common configuration file.  That file is hard-wired
to live in $HOME/.jfrog/docker.rc.  Currently there is a one to one correspondence between the configuration entry 
and each command line switch.

Any docker image has a name that conforms to this pattern ([registry:port]/)([namespace]/)[image]:[tag] 
e.g. artifactory:80/ubuntu:latest.  Namespace and registry are optional.  Both the all components must be 4 to 30
characters long consisting of only [a-z0-9-_.].  The registry must contain a : to distinguish it from the namespace.  

-a | --artifactory | artifactoryRegistry       | Target registry
-r | --remote      | repositoryName            | Image name on source registry
-l | --local       | destinationRepositoryName | Image name on target registry
-p | --prefix      | localPrefix               | Optional prefix to add to source image name to make destination 
-t | --tag         | specificTag               | Specific tag to act on
-f | --force       | forceUpdate               | Force the operation
-n | --noupdate    | noUpdate                  | Do not perform the operation
-h | --help        | help                      | Help

## Usage

Both programs take positional arguments or switches.  Positional would be (command) image tag registry, local name 
defaults to blank and prefix defaults to my.  So...
 
pull-push-repo ubuntu latest artifactory:80 would pull the image ubuntu:latest and push it to 
artifactory:80/ubuntu:latest.  If you put artifactoryRegistry=artifactory:80 into your configuration file the
command would simplify to pull-push-repo ubuntu latest, or pull-push-repo ubuntu if you wanted all ubuntu images
to be transferred.