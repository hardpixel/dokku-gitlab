Dokku GitLab CE
===============

Instructions for installing [GitLab](https://gitlab.com) as a [Dokku](http://dokku.viewdocs.io/dokku) application, using the official [GitLab CE](https://hub.docker.com/_/gitlab-community-edition) Docker image.


Setup application
-----------------

Create dokku application:

```
dokku apps:create gitlab
```

Create storage folders:

```
mkdir -p /home/storage/gitlab/data
mkdir -p /home/storage/gitlab/logs
mkdir -p /home/storage/gitlab/config
```

Mount storage folders:

```
dokku storage:mount gitlab /home/storage/gitlab/config:/etc/gitlab
dokku storage:mount gitlab /home/storage/gitlab/logs:/var/log/gitlab
dokku storage:mount gitlab /home/storage/gitlab/data:/var/opt/gitlab
```

Set proxy ports map and docker options:

```
dokku proxy:ports-set gitlab http:80:80
dokku docker-options:add gitlab deploy '-p 2222:22'
```

Edit configuration
------------------

Edit or add `gitlab.rb` in `/home/storage/gitlab/data` and add the following settings:

```ruby
# GitLab URL
##! URL on which GitLab will be reachable.
##! For more details on configuring external_url see:
##! https://docs.gitlab.com/omnibus/settings/configuration.html#configuring-the-external-url-for-gitlab
external_url 'https://gitlab.example.com'

##! **Override only if you use a reverse proxy**
##! Docs: https://docs.gitlab.com/omnibus/settings/nginx.html#setting-the-nginx-listen-port
nginx['listen_port'] = 80

##! **Override only if your reverse proxy internally communicates over HTTP**
##! Docs: https://docs.gitlab.com/omnibus/settings/nginx.html#supporting-proxied-ssl
nginx['listen_https'] = false
```

Enable SSL
----------

Generate LetsEncrypt certificates:

```
dokku letsencrypt gitlab
```

Configure GIT
-------------

Update `ssh` config, located at `~/.ssh/config`, on your local machine:

```
Host gitlab.example.com
    Port 2222
```

Reconfigure GitLab
------------------

Using `dokku run` does not work. Enter dokku application prompt:

```
dokku enter gitlab
```

Run reconfigure on application prompt:

```
gitlab-ctl reconfigure
```
