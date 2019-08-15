# kong-api-gateway-with-docker
Kong api gateway with docker

## The first steps with KONG using Docker:

1. Create an internal network for the containers
```
docker network create local-kong-net
```


2. Create a container for Cassandra DB:
```
docker run -d --name kong-cassandra-database --network=local-kong-net  -p 9042:9042 cassandra:3
```


3. Create a container for Postgresql DB : 
```
docker run -d --name kong-postgres-database --network=local-kong-net  -p 25432:5432  -e "POSTGRES_USER=kong"  -e "POSTGRES_DB=kong"  postgres:9.6
```


4. Run Kong Migrations 
```
docker run --rm --network=local-kong-net -e "KONG_DATABASE=postgres"  -e "KONG_PG_HOST=kong-postgres-database" -e "KONG_CASSANDRA_CONTACT_POINTS=kong-postgres-database"  kong:latest kong migrations bootstrap
```


5. Start a Kong container connected to the postgres and cassandra databases by  network local-kong-net
```
docker run -d --name kong --network=local-kong-net  -e "KONG_DATABASE=postgres"  -e "KONG_PG_HOST=kong-postgres-database"  -e "KONG_CASSANDRA_CONTACT_POINTS=kong-cassandra-database"  -e "KONG_PROXY_ACCESS_LOG=/dev/stdout"   -e "KONG_ADMIN_ACCESS_LOG=/dev/stdout"   -e "KONG_PROXY_ERROR_LOG=/dev/stderr"  -e "KONG_ADMIN_ERROR_LOG=/dev/stderr" -e "KONG_ADMIN_LISTEN=0.0.0.0:8001, 0.0.0.0:8444 ssl"  -p 8000:8000 -p 8443:8443  -p 8001:8001  -p 8444:8444 kong:latest
```
