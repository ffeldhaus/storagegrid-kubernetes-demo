# storagegrid-kubernetes

[Register private docker registry](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)

```
read -p "Docker E-Mail: " docker_email && read -p "Docker Username: " docker_username && read -s -p "Docker Password: " docker_password && docker_registry="https://index.docker.io/v2/" && kubectl create secret docker-registry regcred --docker-server=$docker_registry --docker-username=$docker_username --docker-password=$docker_password --docker-email=$docker_email
```

Recommended AWS EC2 Worker Nodes:
- t2.2xlarge
- t3.2xlarge

## Deploy

```
helm install
```

## Troubleshooting

If the local helm version does not match the server version use helm upgrade

```
helm init --upgrade
```

## Varnish Cache

To run the Varnish cache, modify the file `varnish.vcl` and insert your backend IPs. Currently only HTTP is supported. Then you can run Varnish with

```
docker run -p 80:8080 -v $PWD/varnish.vcl:/etc/varnish/default.vcl -e VARNISHLOG="true" -e VARNISH_RAM_STORAGE=2G -e VARNISHD_DEFAULT_OPTS="-a :8080 -s default=file,/tmp/cache,4G -p vsl_mask=+Hash" hermsi/alpine-varnish:stable
```

This allows Varnish to use 2GB of memory and creates a local cache file of 4GB. You may want to adapt those settings. Varnish will be available via Port 80 (change `-p 80:8080` if you need a different port). Make sure to setup DNS, SSL Certificate and Domain Name Configuration in StorageGRID appropriately.