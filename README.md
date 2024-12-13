# [Self-Host Penpot](https://penpot.app/self-host)

To selfhost Penpot ensure you already have docker installed.

## Start Penpot

As first step you will need to obtain the docker-compose.yaml file. You can download it from Penpot repository.

```bash
# using wget
https://raw.githubusercontent.com/penpot/penpot/main/docker/images/docker-compose.yaml

# or curl
curl -o docker-compose.yaml https://raw.githubusercontent.com/penpot/penpot/main/docker/images/docker-compose.yaml
```

Then simply launch composer:

```bash
docker compose -p penpot -f docker-compose.yaml up -d
```

At the end it will start listening on `http://localhost:9001`

> `Note`: if you cannot connect with localhost above, use:
> ```bash
> # get penpot-frontend-1 network id
> docker network ls
> # then copy the ipv4 address directly for use in your browser
> # eg http://172.19.0.7/16:9001
> docker network inspect [THENETWORKID]
> ```

## Stop Penpot

If you want to stop running Penpot, just type

```bash
docker compose -p penpot -f docker-compose.yaml down
```

## Configure Penpot with Docker

The configuration is defined using environment variables in the docker-compose.yaml file. The default downloaded file already comes with the essential variables already set, and other ones commented out with some explanations.

## Create users using CLI

By default (or when disable-email-verification flag is used), the email verification process is completely disabled for new registrations but it is highly recommended enabling email verification or disabling registration if you are going to expose your penpot instance to the internet.

If you have registration disabled, you can create additional profiles using the command line interface:

```bash
docker exec -ti penpot-penpot-backend-1 python3 manage.py create-profile
```

> `NOTE`: the exact container name depends on your docker version and platform. For example it could be penpot-penpot-backend-1 or penpot_penpot-backend-1. You can check the correct name executing docker ps.

> `NOTE`: This script only will works when you properly have the enable-prepl-server flag set on backend (is set by default on the latest docker-compose.yaml file)

You can find all configuration options in the Configuration section.

## Update Penpot

To get the latest version of Penpot in your local installation, you just need to execute:

```bash
docker compose -f docker-compose.yaml pull
```

This will fetch the latest images. When you do docker compose up again, the containers will be recreated with the latest version.

> `Important`: Upgrade from version 1.x to 2.0

The migration to version 2.0, due to the incorporation of the new v2 components, includes an additional process that runs automatically as soon as the application starts. If your on-premises Penpot instance contains a significant amount of data (such as hundreds of penpot files, especially those utilizing SVG components and assets extensively), this process may take a few minutes.

In some cases, such as when the script encounters an error, it may be convenient to run the process manually. To do this, you can disable the automatic migration process using the disable-v2-migration flag in PENPOT_FLAGS environment variable. You can then execute the migration process manually with the following command:

```bash
docker exec -ti <container-name-or-id> ./run.sh app.migrations.v2
```

## Backup Penpot

Penpot uses Docker volumes to store all persistent data. This allows you to delete and recreate containers whenever you want without losing information.

This also means you need to do regular backups of the contents of the volumes. You cannot directly copy the contents of the volume data folder. Docker provides you a volume backup procedure, that uses a temporary container to mount one or more volumes, and copy their data to an archive file stored outside of the container.

If you use Docker Desktop, there is an extension that may ease the backup process.

If you use the default docker compose file, there are two volumes used: one for the Postgres database and another one for the assets uploaded by your users (images and svg clips). There may be more volumes if you enable other features, as explained in the file itself.
