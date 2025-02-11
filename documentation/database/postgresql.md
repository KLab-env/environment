# How to install PostgreSQL in docker

## Service configuration

### [docker-compose.yaml](../../services/docker-compose.yaml)

```
services:
  db:
    container_name: postgres
    image: postgres:16.4                                            # database replication works - checked 
    environment:
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
      PGDATA: /var/lib/postgresql/data/pgdata
      POSTGRES_DB: postgres
    volumes:
      - ./postgres/data:/var/lib/postgresql/data/pgdata             # mount data folder
      - ./postgres/init/:/docker-entrypoint-initdb.d/               # initial script for database
      - ./postgres/postgresql.conf:/etc/postgresql/postgresql.conf  # database configuration
    ports:
      - "5432:5432"
    env_file:
      - .env
    restart: always
    healthcheck:
      test: [ "CMD-SHELL", "pg_isready -d postgres -U ${POSTGRES_USER}" ]
      interval: 30s
      timeout: 10s
      retries: 5


```

> [!NOTE]
> __.evn__ - file contains environment variables


### How to check connection

> psql -U `<postgres user>` -h `<host>` -d postgres 

