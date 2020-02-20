## Nextcloud and ONLYOFFICE Document Server Docker Integration

Nextcloud and ONLYOFFICE Document Server Docker integration will install the preconfigured version of [ONLYOFFICE Document Server](https://github.com/ONLYOFFICE/DocumentServer) connected to Nextcloud on a single host running docker containers.

## Requirements

* The latest version of Docker (can be downloaded here: [https://docs.docker.com/engine/installation/](https://docs.docker.com/engine/installation/))
* Docker compose (can be downloaded here: [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/))

## Installation

1. Get the latest version of this repository running the command:

    ```
    git clone https://github.com/chadlabs/docker-onlyoffice-nextcloud
    cd docker-onlyoffice-nextcloud
    ```

2. Run Docker Compose:

    **Please note**: the action must be performed with **root** rights.

    ```
    docker-compose up -d
    ```

    **Please note**: you might need to wait a couple of minutes when all the containers are up and running after the above command.

3. Now launch the browser and enter the webserver address. The Nextcloud wizard webpage will be opened. Enter all the necessary data to complete the wizard.

4. Go to the project folder and run the `set_configuration.sh` script:

    **Please note**: the action must be performed with **root** rights.

    ```
    bash set_configuration.sh
    ```

Now you can enter Nextcloud and create a new document. It will be opened in ONLYOFFICE Document Server.

## Installing SSL Server Certificates into nginx
The docker compose file is assuming these containers on running on a Linux host 
and has mounted the local letsencrypt directory into the nginx container. This allows
the automated creation of SSL certs for the host via the certbot process for the Let's Encrypt CA. 

The process below is for a CentOS 8 docker host.

1. Edit nginx.conf and install the server name (look for EDIT) and then restart nginx
  ```
  cd docker-onlyoffice-nextcloud
  vim nginx.conf
  docker restart nginx-server
  ```
2. Download and install certbot
  ```
  wget https://dl.eff.org/certbot-auto
  sudo mv certbot-auto /usr/local/bin/certbot-auto
  sudo chown root /usr/local/bin/certbot-auto
  sudo chmod 0755 /usr/local/bin/certbot-auto
  ```
3. With port 80 world accessible, use certbot to create the SSL cert for the server
  ```
  certbot-auto certonly --webroot --agree-tos --email admin@yourdomain.net -d server.yourdomain.net -w /var/lib/docker/volumes/docker-onlyoffice-nextcloud_app_data/_data
  ```
4. Edit nginx.conf and remove the comments from SSL sections (look for EDIT) and then restart nginx
  ```
  vim nginx.conf
  docker restart nginx-server
  ```

## Last Steps
Currently, the Nextcloud login page is broken when HTTPS is enabled. Add the "overwriteprotocol" to the php configuration (see references below)
  ```
  cd /var/lib/docker/volumes/docker-onlyoffice-nextcloud_app_data/_data
  vim config/config.php
  docker restart app-server
  ```

## References

### Nextcloud

[Official Website](https://nextcloud.com/)

[Docker Hub](https://hub.docker.com/_/nextcloud)

[Code Repository](https://github.com/nextcloud) 

### ONLYOFFICE
[Official Website](https://www.onlyoffice.com)

[Docker Hub](https://hub.docker.com/r/onlyoffice/documentserver)

[Code Repository (Docker)](https://github.com/ONLYOFFICE/docker-onlyoffice-nextcloud)

his repo offers Nextcloud with preconfigured free version of the ONLYOFFICE Document Server.  
Here is a guide to all of the [ONLYOFFICE offerings](https://github.com/ONLYOFFICE/docker-onlyoffice-nextcloud#onlyoffice-document-server-editions).

### Integration Guidance

[Nextcloud and ONLYOFFICE integration](https://www.linuxbabe.com/docker/onlyoffice-nextcloud-integration-docker)

### SSL Certificates
[certbot for nginx on RHEL/CentOS 8](https://certbot.eff.org/lets-encrypt/centosrhel8-nginx)

### Last Steps
[Broken login page with HTTPS](https://github.com/nextcloud/server/issues/17409)

[overwriteprotocol](https://docs.nextcloud.com/server/10.0/admin_manual/configuration_server/reverse_proxy_configuration.html#multiple-domains-reverse-ssl-proxy)
