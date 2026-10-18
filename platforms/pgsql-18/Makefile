# This Makefile requires GNU Make.
MAKEFLAGS += --silent

# Settings

ifeq ($(strip $(OS)),Windows_NT) # is Windows_NT on XP, 2000, 7, Vista, 10...
    DETECTED_OS := Windows
	C_BLU=''
	C_GRN=''
	C_RED=''
	C_YEL=''
	C_END=''
else
    DETECTED_OS := $(shell uname) # same as "uname -s"
	C_BLU='\033[0;34m'
	C_GRN='\033[0;32m'
	C_RED='\033[0;31m'
	C_YEL='\033[0;33m'
	C_END='\033[0m'
endif

include ../../.env

ENGINE_PLTF="POSTGRES 18+"
ENGINE_LEAD=$(SERVICE_LEAD)
ENGINE_CNET=$(SERVICE_CNET)
ENGINE_HOST=$(SERVICE_HOST)
ENGINE_IMGK=$(DATABASE_IMGK)
ENGINE_CAAS=$(DATABASE_CAAS)
ENGINE_PORT=$(DATABASE_PORT)
ENGINE_BIND=$(DATABASE_BIND)
ENGINE_CPUS=$(DATABASE_CPUS)
ENGINE_MEMO=$(DATABASE_MEMO)
ENGINE_SWAP=$(DATABASE_SWAP)
ENGINE_DATA=$(DATABASE_DATA)
ENGINE_LOGS=$(DATABASE_LOGS)
SQL_ROOT=$(DATABASE_ROOT)
SQL_NAME=$(DATABASE_NAME)
SQL_USER=$(DATABASE_USER)
SQL_PASS=$(DATABASE_PASS)

DIR_ROOT=$(patsubst %/,%,$(dir $(realpath $(firstword $(MAKEFILE_LIST)))))
DIR_BASENAME=$(shell basename $(DIR_ROOT))

ENGINE_CONTAINER:=$(addsuffix -$(ENGINE_CAAS), $(ENGINE_LEAD))
ENGINE_EXEC_SSH=$(ENGINE_COMMAND) exec -it $(ENGINE_CONTAINER) sh

# -------------------------------------------------------------------------------------------------
#  Help
# -------------------------------------------------------------------------------------------------
.PHONY: help

help: ## shows this Makefile help message
	echo "Usage: $$ make "${C_GRN}"[target]"${C_END}
	echo ${C_GRN}"Targets:"${C_END}
	awk 'BEGIN {FS = ":.*?## "} /^[a-zA-Z0-9_-]+:.*?## / {printf "$$ make \033[0;33m%-30s\033[0m %s\n", $$1, $$2}' ${MAKEFILE_LIST} | column -t -c 2 -s ':#'

# -------------------------------------------------------------------------------------------------
#  System
# -------------------------------------------------------------------------------------------------
.PHONY: port-check

port-check: ## shows this project port availability on local machine
	echo "Checking configuration for "${C_YEL}"$(ENGINE_PLTF)"${C_END}" container:";
	if [ -z "$($(ENGINE_COMMAND) lsof -i :$(ENGINE_PORT))" ]; then \
		echo ${C_BLU}"$(ENGINE_PLTF)"${C_END}" > hostname:"${C_GRN}"$(ENGINE_PORT) is free to use."${C_END}; \
	else \
		echo ${C_BLU}"$(ENGINE_PLTF)"${C_END}" > hostname:"${C_RED}"$(ENGINE_PORT) is busy. Update ./.env file."${C_END}; \
	fi

# -------------------------------------------------------------------------------------------------
#  Enviroment
# -------------------------------------------------------------------------------------------------
.PHONY: env env-set

env: ## checks if docker .env file exists
	if [ -f ./docker/.env ]; then \
		echo ${C_BLU}$(ENGINE_PLTF)${C_END}" docker-compose.yml .env file "${C_GRN}"is set."${C_END}; \
	else \
		echo ${C_BLU}$(ENGINE_PLTF)${C_END}" docker-compose.yml .env file "${C_RED}"is not set."${C_END}" \
		Create it by executing "${C_YEL}"$$ make env-set"${C_END}; \
	fi

env-set: ## sets docker .env file
ifeq ($(DETECTED_OS), Windows)
	echo "Recipe do not support for Windows NT at this moment - You have to manually create .env file from ./docker/.env.example";
else
	@echo "COMPOSE_LEAD=$(ENGINE_LEAD)" > ./docker/.env
	@echo "COMPOSE_CNET=$(ENGINE_CNET)" >> ./docker/.env
	@echo "COMPOSE_IMGK=$(ENGINE_IMGK)" >> ./docker/.env
	@echo "COMPOSE_NAME=$(ENGINE_CAAS)" >> ./docker/.env
	@echo "COMPOSE_HOST=$(ENGINE_HOST)" >> ./docker/.env
	@echo "COMPOSE_PORT=$(ENGINE_PORT)" >> ./docker/.env
	@echo "COMPOSE_CPUS=$(ENGINE_CPUS)" >> ./docker/.env
	@echo "COMPOSE_MEMO=$(ENGINE_MEMO)" >> ./docker/.env
	@echo "COMPOSE_SWAP=$(ENGINE_SWAP)" >> ./docker/.env
	@echo "COMPOSE_DATA=\"$(ENGINE_DATA)\"" >> ./docker/.env
	@echo "COMPOSE_LOGS=\"$(ENGINE_LOGS)\"" >> ./docker/.env
	@echo "POSTGRES_DATABASE=$(SQL_NAME)" >> ./docker/.env
	@echo "POSTGRES_USER=$(SQL_USER)" >> ./docker/.env
	@echo "POSTGRES_PASSWORD=\"$(SQL_PASS)\"" >> ./docker/.env
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - "${C_YEL}"docker-compose.yml"${C_END}" .env file "${C_GRN}"has been set."${C_END};
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - Container name: "${C_GRN}$(ENGINE_CONTAINER)${C_END}
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - Container image: "${C_GRN}$(ENGINE_CONTAINER):$(ENGINE_IMGK)${C_END}
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - Max. CPUs: "${C_GRN}$(ENGINE_CPUS)${C_END}
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - Max. memory: "${C_GRN}$(ENGINE_MEMO)${C_END}" - Swap: "${C_GRN}$(ENGINE_SWAP)${C_END}
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - Localhost: http://127.0.0.1:"${C_GRN}"$(ENGINE_PORT)"${C_END};
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}" DATABASE "${C_YEL}$(ENGINE_PLTF)${C_END}" - Hostname: http://$(word 1,$(shell hostname -I)):"${C_GRN}"$(ENGINE_PORT)"${C_END};
endif

# -------------------------------------------------------------------------------------------------
#  Container
# -------------------------------------------------------------------------------------------------
.PHONY: info ssh up build network start stop restart clear destroy dev

info: ## shows container information
	echo ${C_BLU}"$(SERVICE_NAME)"${C_END}": "${C_GRN}"$(ENGINE_PLTF)"${C_END};
	printf ${C_YEL}"Container ID.: "${C_END}; $(ENGINE_COMMAND) ps -aqf "name=^$(ENGINE_CONTAINER)$$"; printf "%s";
	echo ${C_YEL}"Name.........: "${C_END}$(ENGINE_CONTAINER);
	echo ${C_YEL}"Image........: "${C_END}"$(ENGINE_CONTAINER):$(ENGINE_IMGK)";
	echo ${C_YEL}"CPUs.........: "${C_END}"$(ENGINE_CPUS)";
	echo ${C_YEL}"RAM..........: "${C_END}"$(ENGINE_MEMO)";
	echo ${C_YEL}"SWAP.........: "${C_END}"$(ENGINE_SWAP)";
	echo ${C_YEL}"Host.........: "${C_END}"$(ENGINE_HOST):$(ENGINE_PORT)";
	echo ${C_YEL}"Hostname.....: "${C_END}"$(word 1,$(shell hostname -I)):"$(ENGINE_PORT);
	printf ${C_YEL}"Docker.Host..: "${C_END};$(ENGINE_COMMAND) inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(ENGINE_CONTAINER);
	printf ${C_YEL}"NetworkID....: "${C_END};$(ENGINE_COMMAND) inspect -f '{{range.NetworkSettings.Networks}}{{.NetworkID}}{{end}}' $(ENGINE_CONTAINER);

ssh: ## enters the container shell
	$(ENGINE_EXEC_SSH)

up: ## starts and recreates containers if compose config or image changed, runnning in detached mode
	cd docker && $(ENGINE_COMPOSE) -f docker-compose.yml -f docker-compose.public.yml up -d

build: ## builds and ensures changes in the Dockerfile, build steps, or copied-in files are applied. Not --no-recreate
	cd docker && $(ENGINE_COMPOSE) -f docker-compose.yml -f docker-compose.public.yml up --build -d

network: ## starts up into an existing custom network for container-to-container communication, runnning in detached mode
	cd docker && $(ENGINE_COMPOSE) -f docker-compose.yml -f docker-compose.network.yml up -d

start: ## starts the container and put on running from latest configuration
	cd docker && $(ENGINE_COMPOSE) start

stop: ## stops the running container but data will not be destroyed
	cd docker && $(ENGINE_COMPOSE) stop

restart: ## restarts the running container
	cd docker && $(ENGINE_COMMAND) restart $(ENGINE_CONTAINER)

clear: ## removes container from Docker running containers
	cd docker && $(ENGINE_COMPOSE) kill || true
	cd docker && $(ENGINE_COMPOSE) down --volumes --remove-orphans || true

destroy: ## delete container image from Docker cache
	cd docker && $(ENGINE_COMMAND) rmi -f $(ENGINE_CONTAINER):$(ENGINE_IMGK)
	$(SUDO) rm -rf ./root
	$(SUDO) rm -rf $(ENGINE_DATA)
	$(SUDO) rm -rf $(ENGINE_LOGS)

dev: ## sets a development enviroment
	echo ${C_YEL}"\"dev\" recipe has not usage in this project"${C_END};

# -------------------------------------------------------------------------------------------------
#  Testing Database
# -------------------------------------------------------------------------------------------------

.PHONY: test-up test-down

test-up: ## creates a side database for testing porpuses
	$(ENGINE_COMMAND) exec -it $(ENGINE_CONTAINER) sh -c 'dropdb -f $(DATABASE_NAME)_testing -U "$(DATABASE_USER)"; createdb $(DATABASE_NAME)_testing -U "$(DATABASE_USER)"';

test-down: ## drops the side testing database
	$(ENGINE_COMMAND) exec -it $(ENGINE_CONTAINER) sh -c 'dropdb -f $(DATABASE_NAME)_testing -U "$(DATABASE_USER)";';

# -------------------------------------------------------------------------------------------------
#  PostgreSQL | directory path is defined in root .env file, e.g.: ./../../resources/database/
# -------------------------------------------------------------------------------------------------

.PHONY: sql-install sql-replace sql-backup sql-remote copy-remote

sql-install: ## migrates sql file with schema / data into the container main database to init a project
	$(ENGINE_COMMAND) exec -i $(ENGINE_CONTAINER) sh -c 'psql -d $(DATABASE_NAME) -U "$(DATABASE_USER)"' < $(DIR_ROOT)$(DATABASE_PATH)$(DATABASE_INIT)
	echo ${C_YEL}"$(SERVICE_NAME) DATABASE"${C_END}" has been copied to container from "${C_BLU}"$(DATABASE_PATH)$(DATABASE_INIT)"${C_END}

sql-replace: ## replaces the container main database with the latest database .sql backup file
	$(ENGINE_COMMAND) exec -i $(ENGINE_CONTAINER) sh -c 'psql -d $(DATABASE_NAME) -U "$(DATABASE_USER)"' < $(DIR_ROOT)$(DATABASE_PATH)$(DATABASE_BACK)
	echo ${C_YEL}"$(SERVICE_NAME) DATABASE"${C_END}" has been replaced from "${C_BLU}"$(DATABASE_PATH)$(DATABASE_BACK)"${C_END}

sql-backup: ## copies the container main database as backup into a .sql file
	[ -d .$(DATABASE_PATH)$(DATABASE_BACK) ] || touch .$(DATABASE_PATH)$(DATABASE_BACK)
	$(ENGINE_COMMAND) exec $(ENGINE_CONTAINER) sh -c 'pg_dump -d $(DATABASE_NAME) -U "$(DATABASE_USER)"' > $(DIR_ROOT)$(DATABASE_PATH)$(DATABASE_BACK)
	echo ${C_YEL}"$(SERVICE_NAME) DATABASE"${C_END}" backup has been created at "${C_BLU}"$(DATABASE_PATH)$(DATABASE_BACK)"${C_END}

sql-drop: ## drops the container main database but recreates the database without schema as a reset action
	$(ENGINE_COMMAND) exec -i $(ENGINE_CONTAINER) sh -c 'dropdb -f $(DATABASE_NAME) -U "$(DATABASE_USER)"; createdb $(DATABASE_NAME) -U "$(DATABASE_USER)"'
	echo ${C_YEL}"$(SERVICE_NAME) DATABASE"${C_END}" in container "${C_YEL}"$(ENGINE_CONTAINER)"${C_END}" has been deleted."
