# 100 Portainer with Docker

Based on "Deploying Portainer CE in Docker" at https://documentation.portainer.io/v2.0/deploy/ceinstalldocker/

## Deploying Portainer CE in Docker

Portainer is comprised of two elements, the Portainer Server, and the Portainer Agent. Both elements run as lightweight Docker containers on a Docker engine or within a Swarm cluster. Due to the nature of Docker, there are many possible deployment scenarios, however, we have detailed the most common below. Please use the scenario that matches your configuration.

Note that the recommended deployment mode when using Swarm is using the Portainer Agent.

By default, Portainer will expose the UI over the port 9000 and expose a TCP tunnel server over the port 8000. The latter is optional and is only required if you plan to use the Edge compute features with Edge agents.

To see the requirements, please, visit the page of requirements.

### Portainer Deployment

Use the following Docker commands to deploy the Portainer Server; note the agent is not needed on standalone hosts, however it does provide additional functionality if used (see Portainer and agent scenario below):

#### Docker on Linux

Portainer Server Deployment

```
$ docker volume create portainer_data
```

```
$ docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce
```

Portainer Agent Only Deployment

Run the following command to deploy the Agent in your Docker host.

```
docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent
```

### Advanced Options

CLI Configuration Options

Portainer can be easily tuned using CLI flags.

#### Admin password

***From the command line***

Portainer allows you to specify a bcrypt encrypted password from the command line for the admin account. You need to generate the bcrypt encrypted password first.

You can generate the encrypted password with the following command if you have installed apache2-utils package:

```
htpasswd -nb -B admin "your-password" | cut -d ":" -f 2
```

If your system does not have the mentioned command, you can run a container to run the command:

```
docker run --rm httpd:2.4-alpine htpasswd -nbB admin "your-password" | cut -d ":" -f 2
```

To specify the admin password from the command line, start Portainer with the --admin-password flag:

```
docker run -d -p 9000:9000 -p 8000:8000 -v /var/run/docker.sock:/var/run/docker.sock portainer/portainer-ce --admin-password='$2y$05$8oz75U8m5tI/xT4P0NbSHeE7WyRzOWKRBprfGotwDkhBOGP/u802u'
```

***Inside a file***

You can also store the plaintext password inside a file and use the --admin-password-file flag:

Add your password to a file running the following command:

```
echo -n mypassword > /tmp/portainer_password
```

Now you can start the Portainer container by running:

```
docker run -d -p 9000:9000 -p 8000:8000 -v /var/run/docker.sock:/var/run/docker.sock -v /tmp/portainer_password:/tmp/portainer_password portainer/portainer-ce --admin-password-file /tmp/portainer_password
```

This works well with Docker Swarm and Docker secrets too:

```
echo -n mypassword | docker secret create portainer-pass -

docker service create \
    --name portainer \
    --secret portainer-pass \
    --publish 9000:9000 \
    --publish 8000:8000 \
    --replicas=1 \
    --constraint 'node.role == manager' \
    --mount type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
    portainer/portainer-ce \
    --admin-password-file '/run/secrets/portainer-pass' \
    -H unix:///var/run/docker.sock
```
    
Note: This will automatically create an administrator account called admin with the specified password.
