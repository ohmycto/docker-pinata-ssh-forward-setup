Setup instructions for Pinata SSH Forward image to use with Docker in local development.

# Why do I need this?

This is needed to forward your SSH key from host machine inside a Docker container (for example to clone private repos).

## Step 1. Prepare your Docker Compose config

This is the minimal configuration of `docker-compose.yml`:
```yaml
services:
  my_service:
    environment:
      SSH_AUTH_SOCK: /ssh-agent/ssh-agent.sock
    volumes:
      - ssh-agent:/ssh-agent
      
volumes:
  ssh-agent:
    external: true
```

## Step 2. Make sure your SSH key works without a passphrase and added to SSH Agent

As Docker can't ask you for a passphrase interactively during build process you must be sure that your Github key doesn't require a passphrase.

You may create a separate SSH key for Github if you don't want to remove a passphrase from your primary key. 

Make sure SSH Agent can access your Github key (empty output is OK):
```sh
$ ssh-add -T ~/.ssh/<key>
```

If you see something like 'Agent signature failed', add the key to Agent:
```sh
$ ssh-add ~/.ssh/<key>
```
And check again.

## Step 3. Install and run Docker SSH Agent Forward

The trick is to find a working repository of Pinata SSH Agent. I did it for you, so follow the installation instructions from [this repository](https://github.com/uber-common/docker-ssh-agent-forward/#installation).

## Step 4. Launch and test

Launch Pinata:
```sh
$ pinata-ssh-forward
ssh-agent
Connection to 127.0.0.1 port 2244 [tcp/nmsserver] succeeded!
```

Launch docker container and test your key (inside the container):
```sh
$ ssh -T git@github.com
```

If everything is OK, you should see the message:
```
Hi %username%! You've successfully authenticated, but GitHub does not provide shell access.
```
