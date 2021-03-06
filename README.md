# docker_pdns
Configuration files and docker compose file for installation of Power DNS, with NSedit as frontend and MariaDB as backend

Requirements:
* Ubuntu 16.04 / Debian
* Docker
* Docker Compose
* Sudo access

This setup installs following services:
1. **PowerDNS** authoritative server: Version 4.1.4
2. **MariaDB** backend for PowerDNS: Version 10.2

File Descriptions:
1. **`docker-compose.yml`**: Contains service definitions of all docker containers
2. **`pdns.conf`**: Configuration file for PowerDNS server
3. **`pdns_schema.sql`**: SQL schema for PowerDNS required at installation
5. **`example_config`**: Example configuration files from a single host setup (All service on one host)

These services can be installed on a single host but it is recommended to install them on separate hosts

Steps to Setup PowerDNS with MariaDB backend and NSedit as frontend:

1. Install Docker:
    > sudo curl -sSL https://get.docker.io | bash
2. Install Docker Compose: https://docs.docker.com/compose/install/#install-compose
    > sudo curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
    
    > sudo chmod +x /usr/local/bin/docker-compose
    
    > docker-compose --version
3. Clone the repository:
    > git clone https://github.com/sahilkalra1991/docker_pdns.git
   
    > cd docker_pdns
4. Configure and Install **MariaDB**: https://hub.docker.com/_/mariadb/
    * Configure via Environemnt variables in **`docker-compose.yml`**:
      - `MYSQL_ROOT_PASSWORD`: root user password for Mariadb
      - `MYSQL_DATABASE`: Empty database to be created
      - `MYSQL_USER`: New User to create with MYSQL_DATABASE access
      - `MYSQL_PASSWORD`: New User's password
      - Other variables: https://hub.docker.com/_/mariadb/
    * Create data volume: 
        > docker volume create --name=mariadb-data
    * Start service: 
        > docker-compose up -d mariadb
5. Configure and Install **PowerDNS**: https://hub.docker.com/r/psitrax/powerdns/
    * Configure via Configuration file **`pdns.conf`**
        * `pdns.conf` will be overwritten to `/etc/pdns/pdns.conf` inside the container.
        * **Required:** Provide All settings marked with `<TODO>` i.e. search for TODO in `pdns.conf` and provide required values e.g. api_key, allow-axfr-ips, allow-notify-from, gmysql-host etc
        * Optional: Other settings in the file. Change as per requirement
    * Create PowerDNS schema via **`pdns_schema.sql`**: 
        > mysql -h <host_ip> -u pdns -p pdns < pdns_schema.sql
    * Start service: 
        > docker-compose up -d pdns-server

Check **Logs** for respective services:
 * Use command: `docker-compose logs -f <service_name>` e.g.
    > docker-compose logs -f pdns-server
    
Other docker-compose **commands**:
*  Check status of services:
    > docker-compose ps
* Stop service(s)
    > docker-compose stop [service_name]
* Remove container(s)
    > docker-compose rm [service_name]
* Logs for a service     
    > docker-compose logs [-f] service_name
