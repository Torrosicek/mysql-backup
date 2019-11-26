# mysql-backup

This image runs mysqldump to backup data using cronjob to folder `/backup`

## Usage

    docker run -d \
        --env MYSQL_HOST=mysql.host \
        --env MYSQL_PORT=27017 \
        --env MYSQL_USER=admin \
        --env MYSQL_PASS=password \
        --volume host.folder:/backup
        yloeffler/mysql-backup

Moreover, if you link `yloeffler/mysql-backup` to a mysql container(e.g. `tutum/mysql`) with an alias named mysql, this image will try to auto load the `host`, `port`, `user`, `pass` if possible.

    docker run -d -p 27017:27017 -p 28017:28017 -e MYSQL_PASS="mypass" --name mysql tutum/mysql
    docker run -d --link mysql:mysql -v host.folder:/backup yloeffler/mysql-backup

## Parameters

    MYSQL_HOST          the host/ip of your mysql database
    MYSQL_PORT          the port number of your mysql database
    MYSQL_USER          the username of your mysql database
    MYSQL_PASS          the password of your mysql database
    MYSQL_PASS_FILE     the secret file of your mysql database password (optional)
    MYSQL_DB            the database name to dump. Default: `--all-databases`
    EXTRA_OPTS          the extra options to pass to mysqldump command
    COMPRESS            if set, compress the backup using `COMPRESS_CMD`
    COMPRESS_CMD        the compress command used to compress the dump. Default: `gzip`
    CRON_TIME           the interval of cron job to run mysqldump. `0 0 * * *` by default, which is every day at 00:00
    MAX_BACKUPS         the number of backups to keep. When reaching the limit, the old backup will be discarded. No limit by default
    INIT_BACKUP         if set, create a backup when the container starts
    INIT_RESTORE_LATEST if set, restores latest backup

## Restore from a backup

See the list of backups, you can run:

    docker exec mysql-backup ls /backup

To restore database from a certain backup:

-Restoring a specific database:

    docker exec mysql-backup /restore.sh [db_to_restore] [backupfile]

-Full restore:

    docker exec mysql-backup /restore.sh [backupfile]
