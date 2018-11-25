# git-server-docker
A lightweight Git Server Docker image built with Alpine Linux. Available on [GitHub](https://github.com/jkarlosb/git-server-docker) and [Docker Hub](https://hub.docker.com/r/jkarlos/git-server-docker/)

!["image git server docker" "git server docker"](https://raw.githubusercontent.com/jkarlosb/git-server-docker/master/git-server-docker.jpg)

### Basic Usage

Run the container in port 2222 with a separa volumes for git repositories:

      - name: git server
        docker_container:
            name: git
            image: quay.io/falco/git-server
            restart_policy: unless-stopped
            ports:
                - "2222:22"
            env:
                my_keys_repo: https://github.com/xxxxxxxx/sshkeys
            volumes:
                /mnt/git:/rep

Public SSH keys come from $my_keys_repo.
	You need restart the container when keys are updated in your public git repository:
	$ docker restart <container-id>
	
How to check that container works (you must to have a key):

	$ ssh git@<ip-docker-server> -p 2222
	...
	Welcome to git-server-docker!
	You've successfully authenticated, but I do not
	provide interactive shell access.
	...

How to create a new repo:

	$ cd myrepo
	$ git init --shared=true
	$ git add .
	$ git commit -m "my first commit"
	$ cd ..
	$ git clone --bare myrepo myrepo.git

How to upload a repo:

	From host:
	$ mv myrepo.git ~/git-server/repos
	From remote:
	$ scp -r myrepo.git user@host:~/git-server/repos

How clone a repository:

	$ git clone ssh://git@<ip-docker-server>:2222/git-server/repos/myrepo.git

### Arguments

* **Expose ports**: 22
* **Volumes**:
 * */git-server/keys*: Volume to store the users public keys
 * */git-server/repos*: Volume to store the repositories

### SSH Keys

How generate a pair keys in client machine:

	$ ssh-keygen -t rsa

How upload quickly a public key to host volume:

	$ scp ~/.ssh/id_rsa.pub user@host:~/git-server/keys

### Build Image

How to make the image:

	$ docker build -t git-server-docker .
	
### Docker-Compose

You can edit docker-compose.yml and run this container with docker-compose:

	$ docker-compose up -d
