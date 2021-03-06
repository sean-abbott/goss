# dgoss

dgoss is a convenience wrapper around goss that aims to bring the simplicity of goss to docker containers.

## Examples and Tutorials
* [video tutorial](https://youtu.be/PEHz5EnZ-FM) - Introduction to dgoss tutorial
* [blog tutorial](https://medium.com/@aelsabbahy/tutorial-how-to-test-your-docker-image-in-half-a-second-bbd13e06a4a9) - Same as above, but in written format
* [dgoss-examples](https://github.com/aelsabbahy/dgoss-examples) - Repo containing examples of using dgoss to validate docker images

## Usage

`dgoss [run|edit] <docker_run_params>`

### Run

Run is used to validate a docker container. It expects a `./goss.yaml` file to exist in the directory it was invoked from. In most cases one can just substitute the docker command for the dgoss command, for example:

**run:**

`docker run -e JENKINS_OPTS="--httpPort=8080 --httpsPort=-1" -e JAVA_OPTS="-Xmx1048m" jenkins:alpine`

**test:**

`dgoss run -e JENKINS_OPTS="--httpPort=8080 --httpsPort=-1" -e JAVA_OPTS="-Xmx1048m" jenkins:alpine`


`dgoss run` will do the following:
* Run the container with the flags you specified.
* Stream the containers log output into the container as `/goss/docker_output.log`
  * This allows writing tests or waits against the docker output
* (optional) Run `goss` with `$GOSS_WAIT_OPTS` if `./goss_wait.yaml` file exists in the current dir
* Run `goss` with `$GOSS_OPTS` using `./goss.yaml`


### Edit

Edit will launch a docker container, install goss, and drop the user into an interactive shell. Once the user quits the interactive shell, any `goss.yaml` or `goss_wait.yaml` are copied out into the current directory. This allows the user to leverage the `goss add|autoadd` commands to write tests as they would on a regular machine.

**Example:**

`dgoss edit -e JENKINS_OPTS="--httpPort=8080 --httpsPort=-1" -e JAVA_OPTS="-Xmx1048m" jenkins:alpine`

### Environment vars and defaults
The following environment variables can be set to change the behavior of dgoss.

##### GOSS_PATH
Location of the goss binary to use. (Default: `$(which goss)`)

##### GOSS_OPTS
Options to use for the goss test run. (Default: `--color --format documentation`)

##### GOSS_WAIT_OPTS
Options to use for the goss wait run, when `./goss_wait.yaml` exists. (Default: `-r 30s -s 1s > /dev/null`)

##### GOSS_SLEEP
Time to sleep after running container (and optionally `goss_wait.yaml`) and before running tests. (Default: `0.2`)

