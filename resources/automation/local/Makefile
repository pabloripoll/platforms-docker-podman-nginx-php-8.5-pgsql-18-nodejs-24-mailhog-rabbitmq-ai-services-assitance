# This Makefile requires GNU Make.
MAKEFLAGS += --silent

# Note: Do NOT indent these lines with a TAB. Use spaces or no indentation
# := prevents make from re-evaluating the shell context every single time the color variable is referenced
ifeq ($(OS),Windows_NT)
    DETECTED_OS := Windows
	PRINT_CMD = powershell -NoProfile -Command "Write-Host"
    # Windows native uses PowerShell to safely render ANSI colors
    C_BLU := `e[0;34m`
    C_GRN := `e[0;32m`
    C_RED := `e[0;31m`
    C_YEL := `e[0;33m`
    C_END := `e[0m`
else
    DETECTED_OS := $(shell uname -s)
	PRINT_CMD = echo -e
    # Linux / macOS / WSL2 codes for rendering colors
    C_BLU := '\033[0;34m'
    C_GRN := '\033[0;32m'
    C_RED := '\033[0;31m'
    C_YEL := '\033[0;33m'
    C_END := '\033[0m'
endif

# Environment constants
include .env

# Helpers
ROOT_DIR=$(patsubst %/,%,$(dir $(realpath $(firstword $(MAKEFILE_LIST)))))
DIR_BASENAME=$(shell basename $(ROOT_DIR))

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
.PHONY: local-info local-ownership local-ownership-set

local_ip ?= $(word 1,$(shell hostname -I))
local-info: ## shows local machine ip and container ports set
	echo ${C_BLU}"Local IP / Hostname:"${C_END} ${C_YEL}"$(local_ip)"${C_END}

user ?= ${USER}
group ?= root
local-ownership: ## shows local ownership
	echo $(user):$(group)

local-ownership-set: ## sets recursively local root directory ownership
	$(SUDO) chown -R ${user}:${group} $(ROOT_DIR)/

# -------------------------------------------------------------------------------------------------
#  Networking
# -------------------------------------------------------------------------------------------------
.PHONY: network-exists network-create network-info network-destroy

network-exists: ## lists all local networks
	$(ENGINE) network ls

network-create: ## creates network
	$(ENGINE) network create --driver bridge $(SERVICE_LEAD)-$(SERVICE_CNET)

network-info: ## shows network information
	$(ENGINE) network inspect $(SERVICE_LEAD)-$(SERVICE_CNET) --format '{{json .Containers}}'

network-destroy: ## destroys network
	$(ENGINE) network rm $(SERVICE_LEAD)-$(SERVICE_CNET)

# -------------------------------------------------------------------------------------------------
#  Frontend Application Service
# -------------------------------------------------------------------------------------------------
.PHONY: web-hostcheck web-info web-set web-create web-network web-ssh web-start web-stop web-destroy

web-hostcheck: ## shows this project ports availability on local machine for the container
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) port-check

web-info: ## shows the ENGINE related information
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) info

web-set: ## sets the enviroment file to build the container
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) env-set

web-build: ## builds and ensures changes in the ENGINEfile, build steps, or copied-in files are applied
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) build

web-create: ## starts up or creates the container for running in detached mode
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) up

web-network: ## starts up into an existing custom network for container-to-container communication, runnning in detached mode
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) clear network

web-host-gateway: ## starts up for container-to-host communication, runnning in detached mode
	$(MAKE) web-stop
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) clear host-gateway

web-ssh: ## enters the container shell
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) ssh

web-start: ## starts the container running
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) start

web-stop: ## stops the container but its assets will not be destroyed
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) stop

web-restart: ## restarts the running container
	cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) restart

web-down: ## stops and removes the container, network and volume established
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to stop and remove the "${C_BLU}"$(WEBAPP_PLTF)"${C_END}" container with its network and volume."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) clear; \
		echo ${C_GRN}"O.K.! $(WEBAPP_PLTF) has been cleared down."${C_END}; \$(ENGINE)
$(ENGINE)
	fi

web-destroy: ## destroys completly the container
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to remove the "${C_BLU}"$(WEBAPP_PLTF)"${C_END}" container and delete its image resource."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(WEBAPP_PLTF) && $(MAKE) clear destroy; \
		echo -n ${C_GRN}"Do you want to clear $(ENGINE) cache? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
			echo ${C_YEL}"The following command is delegated to be executed by user:"${C_END}; \
			echo "$$ $(ENGINE_COMMAND) system prune"; \
		else \
			$(ENGINE_COMMAND) system prune -f; \
			echo ${C_GRN}"O.K.! $(ENGINE) cache has been cleared up."${C_END}; \
		fi \
	fi

# -------------------------------------------------------------------------------------------------
#  Backend API Service
# -------------------------------------------------------------------------------------------------
.PHONY: api-hostcheck api-info api-set api-create api-network api-ssh api-start api-stop api-destroy

api-hostcheck: ## shows this project ports availability on local machine for apirest container
	cd ./platforms/$(API_PLTF) && $(MAKE) port-check

api-info: ## shows the apirest ENGINE related information
	cd ./platforms/$(API_PLTF) && $(MAKE) info

api-set: ## sets the apirest enviroment file to build the container
	cd ./platforms/$(API_PLTF) && $(MAKE) env-set

api-build: ## builds and ensures changes in the ENGINEfile, build steps, or copied-in files are applied
	cd ./platforms/$(API_PLTF) && $(MAKE) build

api-create: ## starts up or creates the container for running in detached mode
	cd ./platforms/$(API_PLTF) && $(MAKE) up

api-network: ## starts up into an existing custom network for container-to-container communication, runnning in detached mode
	cd ./platforms/$(API_PLTF) && $(MAKE) clear network

api-host-gateway: ## starts up for container-to-host communication, runnning in detached mode
	$(MAKE) api-stop
	cd ./platforms/$(API_PLTF) && $(MAKE) clear host-gateway

api-ssh: ## enters the apirest container shell
	cd ./platforms/$(API_PLTF) && $(MAKE) ssh

api-start: ## starts the apirest container running
	cd ./platforms/$(API_PLTF) && $(MAKE) start

api-stop: ## stops the apirest container but its assets will not be destroyed
	cd ./platforms/$(API_PLTF) && $(MAKE) stop

api-restart: ## restarts the running apirest container
	cd ./platforms/$(API_PLTF) && $(MAKE) restart

api-down: ## stops and removes the container, network and volume established
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to stop and remove the "${C_BLU}"$(API_PLTF)"${C_END}" container with its network and volume."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(API_PLTF) && $(MAKE) clear; \
		echo ${C_GRN}"O.K.! $(API_PLTF) has been cleared down."${C_END}; \
	fi

api-destroy: ## destroys completely the apirest container and its image
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to remove the "${C_BLU}"$(API_PLTF)"${C_END}" container and delete its image resource."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(API_PLTF) && $(MAKE) clear destroy; \
		echo -n ${C_GRN}"Do you want to clear $(ENGINE) cache? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
			echo ${C_YEL}"The following command is delegated to be executed by user:"${C_END}; \
			echo "$$ $(ENGINE_COMMAND) system prune"; \
		else \
			$(ENGINE_COMMAND) system prune -f; \
			echo ${C_GRN}"O.K.! $(ENGINE) cache has been cleared up."${C_END}; \
		fi \
	fi

# -------------------------------------------------------------------------------------------------
#  Database Service
# -------------------------------------------------------------------------------------------------
.PHONY: db-hostcheck db-info db-set db-create db-ssh db-start db-stop db-destroy

db-hostcheck: ## shows this project ports availability on local machine for database container
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) port-check

db-info: ## shows ENGINE related information
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) info

db-set: ## sets the database enviroment file to build the container
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) env-set

db-build: ## builds and ensures changes in the ENGINEfile, build steps, or copied-in files are applied
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) build

db-create: ## starts up or creates the container for running in detached mode
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) up

db-network: ## starts up into an existing custom network for container-to-container communication, runnning in detached mode
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) clear network

db-ssh: ## enters the container shell
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) ssh

db-start: ## starts the database container running
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) start

db-stop: ## stops the database container but its assets will not be destroyed
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) stop

db-restart: ## restarts the running database container
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) restart

db-down: ## stops and removes the container, network and volume established
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to stop and remove the "${C_BLU}"$(DATABASE_PLTF)"${C_END}" container with its network and volume."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(DATABASE_PLTF) && $(MAKE) clear; \
		echo ${C_GRN}"O.K.! $(DATABASE_PLTF) has been cleared down."${C_END}; \
	fi

db-destroy: ## destroys completly the database container with its data
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to remove the "${C_BLU}"$(DATABASE_PLTF)"${C_END}" container and delete its image resource and persistance data."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(DATABASE_PLTF) && $(MAKE) clear destroy; \
		echo -n ${C_GRN}"Do you want to clear $(ENGINE) cache? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
			echo ${C_YEL}"The following commands are delegated to be executed by user:"${C_END}; \
			echo "$$ $(ENGINE_COMMAND) system prune"; \
			echo "$$ $(ENGINE_COMMAND) volume prune"; \
		else \
			$(ENGINE_COMMAND) system prune -f; \
			$(ENGINE_COMMAND) volume prune -f; \
			echo ${C_GRN}"O.K.! $(ENGINE) cache has been cleared up."${C_END}; \
		fi \
	fi

.PHONY: db-test-up db-test-down

db-test-up: ## creates a side database for testing porpuses
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) test-up

db-test-down: ## drops the side testing database
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) test-down

.PHONY: db-sql-install db-sql-replace db-sql-backup db-sql-remote db-copy-remote

db-sql-install: ## migrates sql file with schema / data into the container main database to init a project
	$(MAKE) local-ownership-set;
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) sql-install

db-sql-replace: ## replaces the container main database with the latest database .sql backup file
	$(MAKE) local-ownership-set;
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) sql-replace

db-sql-backup: ## copies the container main database as backup into a .sql file
	$(MAKE) local-ownership-set;
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) sql-backup

db-sql-drop: ## drops the container main database but recreates the database without schema as a reset action
	$(MAKE) local-ownership-set;
	cd ./platforms/$(DATABASE_PLTF) && $(MAKE) sql-drop

# -------------------------------------------------------------------------------------------------
#  Mailer Service
# -------------------------------------------------------------------------------------------------
.PHONY: mailer-hostcheck mailer-info mailer-set mailer-create mailer-network mailer-ssh mailer-start mailer-stop mailer-destroy

mailer-hostcheck: ## shows this project ports availability on local machine for mailer container
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) port-check

mailer-info: ## shows the mailer ENGINE related information
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) info

mailer-set: ## sets the mailer enviroment file to build the container
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) env-set

mailer-build: ## builds mailer and ensures changes in the ENGINEfile, build steps, or copied-in files are applied
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) build

mailer-create: ## starts up mailer or creates the container for running in detached mode
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) up

mailer-network: ## starts up mailer into an existing custom network for container-to-container communication, runnning in detached mode
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) clear network

mailer-ssh: ## enters the mailer container shell
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) ssh

mailer-start: ## starts the mailer container running
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) start

mailer-stop: ## stops the mailer container but its assets will not be destroyed
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) stop

mailer-restart: ## restarts the running mailer container
	cd ./platforms/$(MAILER_PLTF) && $(MAKE) restart

mailer-down: ## stops and removes the container, network and volume established
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to stop and remove the "${C_BLU}"$(MAILER_PLTF)"${C_END}" container with its network and volume."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"K.O.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(MAILER_PLTF) && $(MAKE) clear; \
		echo ${C_GRN}"O.K.! $(MAILER_PLTF) has been cleared down."${C_END}; \
	fi

mailer-destroy: ## destroys completly the mailer container
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to remove the "${C_BLU}"$(MAILER_PLTF)"${C_END}" container and delete its image resource."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(MAILER_PLTF) && $(MAKE) clear destroy; \
		echo -n ${C_GRN}"Do you want to clear $(ENGINE) cache? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
			echo ${C_YEL}"The following command is delegated to be executed by user:"${C_END}; \
			echo "$$ $(ENGINE_COMMAND) system prune"; \
		else \
			$(ENGINE_COMMAND) system prune -f; \
			echo ${C_GRN}"O.K.! $(ENGINE) cache has been cleared up."${C_END}; \
		fi \
	fi

# -------------------------------------------------------------------------------------------------
#  Broker Service
# -------------------------------------------------------------------------------------------------
.PHONY: broker-hostcheck broker-info broker-set broker-create broker-network broker-ssh broker-start broker-stop broker-destroy

broker-hostcheck: ## shows this project ports availability on local machine for broker container
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) port-check

broker-info: ## shows the broker ENGINE related information
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) info

broker-set: ## sets the broker enviroment file to build the container
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) env-set

broker-build: ## builds broker and ensures changes in the ENGINEfile, build steps, or copied-in files are applied
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) build

broker-create: ## starts up broker or creates the container for running in detached mode
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) up

broker-network: ## starts up broker into an existing custom network for container-to-container communication, runnning in detached mode
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) clear network

broker-ssh: ## enters the broker container shell
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) ssh

broker-start: ## starts the broker container running
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) start

broker-stop: ## stops the broker container but its assets will not be destroyed$(ENGINE)
$(ENGINE)
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) stop

broker-restart: ## restarts the running broker container
	cd ./platforms/$(BROKER_PLTF) && $(MAKE) restart

broker-down: ## stops and removes the container, network and volume established
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to stop and remove the "${C_BLU}"$(BROKER_PLTF)"${C_END}" container with its network and volume."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"O.K.! container continues with its latest status."${C_END}; \
    else \
		cd ./platforms/$(BROKER_PLTF) && $(MAKE) clear; \
		echo ${C_GRN}"O.K.! $(BROKER_PLTF) has been cleared down."${C_END}; \
	fi

broker-destroy: ## destroys completly the broker container
	echo ${C_RED}"Attention!"${C_END};
	echo ${C_YEL}"You're about to remove the "${C_BLU}"$(BROKER_PLTF)"${C_END}" container and delete its image resource."${C_END};
	@echo -n ${C_RED}"Are you sure to proceed? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
        echo ${C_GRN}"K.O.! container has been stopped but not destroyed."${C_END}; \
    else \
		cd ./platforms/$(BROKER_PLTF) && $(MAKE) clear destroy; \
		echo -n ${C_GRN}"Do you want to clear $(ENGINE) cache? "${C_END}"[y/n]: " && read response && if [ $${response:-'n'} != 'y' ]; then \
			echo ${C_YEL}"The following command is delegated to be executed by user:"${C_END}; \
			echo "$$ $(ENGINE_COMMAND) system prune"; \
		else \
			$(ENGINE_COMMAND) system prune -f; \
			echo ${C_GRN}"O.K.! $(ENGINE) cache has been cleared up."${C_END}; \
		fi \
	fi

# -------------------------------------------------------------------------------------------------
#  Repository Helper
# -------------------------------------------------------------------------------------------------
.PHONY: repo-flush repo-commit

bounded_directory := $(strip $(API_BIND))
display_directory := $(subst ../../../,,$(bounded_directory))
repo-detach: ## echoes detaching command for sub-repository directory to be not tracked by git
	@echo ${C_YEL}"Detach sub-repository directory content from platforms repository command example:"${C_END}
	@echo ${C_YEL}"$$"${C_END}" git rm -r --cached -- \"$(display_directory)/*\" \":(exclude)$(display_directory)/.gitkeep\""

repo-flush: ## echoes clearing commands for git repository cache on local IDE
	@echo ${C_YEL}"Clear repository cache from untracked files:"${C_END}
	@echo ${C_YEL}"$$"${C_END}" git rm -rf --cached .; git add .; git commit -m \"maint: cache cleared for untracked files\""

repo-commits: ## echoes common git commands
	echo ${C_YEL}"For comment fixing over already pushed commit:"${C_END}
	echo ${C_YEL}"$$"${C_END}" git commit --amend"
	echo ${C_YEL}"$$"${C_END}" git push --force origin [branch]"
	echo ""
	echo ${C_YEL}"Common commands for creating commits:"${C_END}
	echo ${C_YEL}"$$"${C_END}" git add . && git commit -m \"feat: ... \""