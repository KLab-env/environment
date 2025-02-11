# How to install PostgreSQL in docker

services:
db:
container_name: postgres
image: postgres:16.4
environment:
POSTGRES_USER: postgres
POSTGRES_PASSWORD: postgres
PGDATA: /opt/services/postgres/data
POSTGRES_DB: bank
volumes:
- db:/opt/services/postgres/data
ports:
- "5332:5432"
networks:
- db
restart: always
healthcheck:
test: [ "CMD-SHELL", "pg_isready -d postgres" ]
interval: 30s
timeout: 10s
retries: 5