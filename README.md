<div id="top-header" style="with:100%;height:auto;text-align:right;">
    <img src="./resources/docs/images/pr-banner-long.png">
</div>

# INFRASTRUCTURE PLATFORMS

[![Generic badge](https://shields.io/badge/version-2.0-blue.svg)](https://shields.io/)
[![Open Source? Yes!](https://badgen.net/badge/Open%20Source%20%3F/Yes%21/blue?icon=github)](./)
[![MIT license](https://shields.io/badge/License-MIT-blue.svg)](./LICENSE)
<br><br>

# NGINX + NODE JS 24 + PHP 8.5 & POSTGRESQL 18+


## Included Platforms

- APP: [NGINX + NODE JS 24](./platforms/nginx-nodejs-24/README.md)
- API: [NGINX + PHP 8.5](./platforms/nginx-php-8.5/README.md)
- Database: [POSTGRESQL 18+](./platforms/pgsql-18/README.md)
- Mail Sandbox: [MAILHOG 1+](./platforms/mailhog-1/README.md)
- Message Broker: [RABBITMQ 4+](./platforms/rabbitmq-4/README.md)
<br>

## Index Of Content

- [Repository Objectives](#repository-objectives)
- [Orchestration & Platform Specifics](#orchestration)
- [Containers Networking](#container-networking)
- [Platforms Setup](#platforms-setup)
- [Platforms Startup](#platforms-startup)
- [Use this Platform Repository for your own projects](#platforms-usage-for-projects)
- [Projects with AI Support](#platforms-for-projects-ai-support)
<br><br>


## <a id="repository-objectives"></a>Repositoy Objectives

This repository serves as a multi-engine containerized infrastructure boilerplate designed to streamline local development and remote services for back-end or front-end applications. It provides a set of pre-configured, loosely coupled platform services optimized to work interchangeably with either Docker or Podman.

It abstracts complex orchestration down to simple automation layers, allowing you to instantly spin up independent, ready-to-scale platform components.

By decoupling the runtime services, this boilerplate ensures your primary application layer connects seamlessly to its supporting infrastructure components via standard local orchestration.

The boilerplate in this repository refers to sections of code, text, or configuration files that can be reused in multiple places with little or no modification.

### Key principles and goals

This repository provides a consistent framework for local development that mirrors production environments. In production, APIs run on cloud instances (AWS, Azure, GCP) or Kubernetes pods. Meanwhile, the database layer resides on managed services like AWS RDS, Azure Database, or GCP Cloud SQL, utilizing Multi-AZ deployments for high availability and read replicas to scale performance. This structure ensures network connections between application and database tiers remain decoupled.

By leveraging Platform Engineering principles, this project reduces cognitive load for developers. It treats the Internal Developer Platform (IDP) as a product, offering self-service tools and automated workflows. This streamlines the entire lifecycle—from building to monitoring—allowing teams to innovate faster.

- **Self-service:** Provide developers with easy-to-use tools and automated workflows to manage their own infrastructure needs without having to file tickets or rely on other teams.

- **Standardization:** Use standardized tools and environments to ensure consistency, reliability, and security across projects.

- **Reduced cognitive load:** Abstract away underlying complexity so developers can focus on writing code and delivering business value rather than managing infrastructure details.

- **Developer experience:** Build a positive and productive environment for developers, making them feel empowered and less frustrated.

- **Operational efficiency:** Automate repetitive tasks and standardize processes to improve the speed and reliability of software delivery.

### How it works

- **Internal Developer Platform (IDP)**: A dedicated platform built by the platform engineering team that provides a curated set of tools, services, and infrastructure.

- **Golden Paths**: Predefined, optimized workflows and best practices that developers can follow to accomplish common tasks quickly and easily.

- **Treating the platform as a product**: Platform engineers treat their IDP like a product, with developers as their customers, to ensure it meets the needs of the organization.

### Key Characteristics

- **Non-business logic**: It does not contain your core application features or unique business rules. It is the background plumbing required to make the program run.

- **Repetitive**: Developers often have to write or copy it over and over for every new project or file.

- **Standardized**: It acts as a starting foundation or project scaffold.

### Core Components

- **Infrastructure Configuration**: Pre-configured setup files for tools like Docker or Podman, handling environment variables and platform stack definitions.

- **Standard Local Stacks**: Boilerplate wrapper code to quickly spin up basic environments (e.g., PHP/MariaDB, Java/OracleDB, Python/PostgreSQL) complete with local mail sandboxes and message brokers.

### Why Is It Used?

- **Saves time**: It lets developers skip repetitive setup and start building unique features faster.

- **Consistency**: Teams use standardized starter templates so every project follows the same architecture and rules.

### Read more:

- [What is platform engineering? - IBM](https://www.ibm.com/think/topics/platform-engineering)
- [Understanding platform engineering - Red Hat](https://www.redhat.com/en/topics/platform-engineering)
- [Platform engineering - Prescriptive Guidance - AWS](https://docs.aws.amazon.com/prescriptive-guidance/latest/aws-caf-platform-perspective/platform-eng.html)
- [What is an internal developer platform (IDP)? - Google Cloud](https://cloud.google.com/solutions/platform-engineering)
- [What is platform engineering? - Microsoft](https://learn.microsoft.com/en-us/platform-engineering/what-is-platform-engineering)
- [What is Platform engineering? - Github](https://github.com/resources/articles/what-is-platform-engineering)
<br><br>


## <a id="orchestration"></a>Orchestration & Platform Specifics

### Supported Operating Systems

![Linux](https://shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![MacOS](https://shields.io/badge/MacOS-f0f0f0?logo=apple&logoColor=black&style=for-the-badge)
![Windows](https://shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![Windows WSL2](https://shields.io/badge/Windows-WSL2-4E9A06?style=for-the-badge&logo=windows&logoColor=white)

* **Linux** (Native)
* **macOS** (Intel & Apple Silicon M1–M5)
* **Windows 10 / 11** (Most recommended via WSL2 or Hyper-V)

### Minimum Hardware Requirements

By default, local development environments run a minimal stack and do not process live production traffic. Therefore, resource utilization remains low. 

| Machine       | Machine's minimum characteristics to run this repository                                          |
| ------------- | ------------------------------------------------------------------------------------------------- |
| CPU           | x86_64 / ARM64 - Supports modern Intel, AMD, and Apple Silicon chips.                             |
| RAM           | 1 GB - Scale up if compiling memory-heavy stacks (e.g., Java, Angular).                           |
| DISK          | 5 GB available space - Actual usage depends on downloaded container images and project cache.     |

💡 **Tip 1 - Resource Management:** Properly restricting container CPU and memory allocations prevents resource starvation on your local host machine and closely mirrors production security best practices.

💡 **Tip 2 - Environment Separation:** The automation commands provided in this repository are optimized for local development. In production or staging environments, standard cloud infrastructure designs typically distribute these platforms across separate VPS or cloud instances rather than running them all via a single local orchestration layer. To see how these components are isolated for production, you can find the individual single-service platforms listed under my [GitHub Repositories Profile](https://github.com/pabloripoll?tab=repositories).
<br>

### Task Automation (GNU Make)

![GNU](https://shields.io/badge/gnu-%23A42E2B.svg?style=for-the-badge&logo=gnu&logoColor=white)

It is highly recommended to use **GNU Make** on local to manage automated workflows from the root directory. On remote, you most probably choose bash script with or without a instance personalized Makefile.

* **Recommended:** Ensure make is installed on your host system to use shortened convenience commands.
* **Alternative:** If make is unavailable, commands must be executed manually within their respective subdirectories.

### Ephemeral Environments

The root `.env` file allows developers to leverage customizable variables to isolate parallel development environments on local or remote development and testing machines. These are often referred to as **ephemeral environments** or **feature environments**.

Because these variables represent a unique instance, variant, or feature suffix rather than just a broad environment tier or a static name set directly inside the `docker-compose.yml` file, you can easily spin up and manage different versions of the same project simultaneously.

The automation layer dynamically adapts commands using variables defined in your root `.env` file. These variables control how your environment invokes the runtime engine.

This repository supports local development using either **Docker** or **Podman**. You can configure your preferred container engine in your root `.env` file *(copied from `.env.example`)*: 

```sh
# ENGINE DEFINITION (docker or podman) - VARIABLES FOR AUTOMATE CONTAINER SERVICES
SUDO=sudo                                         # <- user priviledge for running engine commands, left blank if user runs it without sudo -----------> #
ENGINE=docker                                     # <- must define container engine system ------------------------------------------------------------> #
ENGINE_COMMAND=$(SUDO) $(ENGINE)                  # <- engine core command generator ------------------------------------------------------------------> #
ENGINE_COMPOSE=$(SUDO) $(ENGINE) compose          # <- targets "docker compose" / "docker-compose" or "podman compose" --------------------------------> #

# CONTAINERS BASE INFORMATION FOR BUILDING WITH docker-compose.yml
SERVICE_NAME="PLATFORMS DOCKER"                   # <- this variable will be served for automation terminal outputs -----------------------------------> #
SERVICE_LEAD=myproj                               # <- lead abbreviation or acronym as part of the related containers naming rule ---------------------> #
SERVICE_SLUG=dev                                  # <- instance/branch identifier (e.g., dev_a, dev_b) as part of the related containers naming rule --> #
SERVICE_CNET=$(SERVICE_LEAD)-net-$(SERVICE_SLUG)  # <- network vairiable for container connections between each other when is required ----------------> #
SERVICE_HOST=127.0.0.1                            # <- host machine loopback address for isolated networks --------------------------------------------> #

# PLATFORMS...
## ....
```

From the above example, the end result will be that the container will be named as `myproj-api-dev` and the network `myproj-net-dev`. **The advantages of these configurations**, is that you can have running the same project but with different purposes or different projects with the same stack on the same machine.

Also, these abstractions allow the root Makefile to trigger standard shortcuts (e.g., `make [target]-up`, `make [target]-down`) seamlessly across different setups.
<br><br>


## Multi-Engine Architecture

![Docker](https://shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Podman](https://shields.io/badge/-Podman-892CA0?style=for-the-badge&logo=podman&logoColor=white)

The magic happens because modern Podman builds natively include the podman compose subcommand. It intercepts your standard, unmodified multi-container docker-compose.yml blueprints and automatically translates them down to Podman specifications.

Because this repository abstracts commands like `$ sudo docker compose up -d` via the root `Makefile` using the environment variables like `($(DOCKER_COMPOSE))`, the custom automation orchestration *(gmake/make recipes)* will point cleanly to **Podman** behind the scenes. No translation layers or custom rewrites needed.

This repository relies on a standard multi-container design (docker-compose.yml). You do not need to maintain multiple orchestration blueprints to switch engines:

- **Docker Native:** Runs commands through the standard dockerd daemon.

- **Podman Compatibility:** Modern versions of Podman include a built-in podman compose engine. When CONTAINER_ENGINE=podman is selected, Podman intercepts the docker-compose.yml specifications and automatically translates them into a Podman native configuration. No third-party translation tools are required.

### Platform-Specific Setup Notes

#### Linux & Windows (WSL2)

Ensure your chosen container daemon is installed and running. If your local user account belongs to the docker or podman security group (allowing rootless container operations), you should set SUDO= to a blank value in your .env file.

#### macOS

Because macOS does not natively run Linux containers, both Docker and Podman spin up a background Linux Virtual Machine (VM) to host your workloads. If you choose to use Podman on macOS, execute the following steps once to initialize your engine environment:

#### Docker

Despite Docker’s cross-platform compatibility, for intermediate to advanced software development on environments other than Windows NT or macOS, automating the platform build and streamlining the process of starting feature development is crucial. This automation enables a more dynamic and efficient software development lifecycle.

- [Docker Documentation](https://docs.docker.com/engine/)
- [Docker Installation](https://docs.docker.com/engine/install/)

#### Podman

Because macOS with cannot natively run Linux containers, there are two quick command-line commands you must run exactly once right after your brew install to initialize the engine. Open your terminal and run these commands to set up the Podman virtual machine:
```sh
# 1. Install Podman via Homebrew
$ brew install podman

# 2. Create the lightweight Apple Silicon Linux VM (Run once)
$ podman machine init

# 3. Fire up the Podman engine VM
$ podman machine start
```

- [Podman Documentation](https://podman.io/docs)
- [Podman Installation](https://podman.io/docs/installation)
<br><br>


## <a id="containers-networking"></a>Containers Networking - Access Modes

- If no application is on *e.g.* `./service/api-rest` directory *(or your custom binded directory name)* once container is up it wont provide a application and therefore NGINX will respond with an error. Copy an start-up example application or create a parking page.

- Each container have a directory to set the required environment values in `./docker/.env` from `./docker/.env.example` if no GNU Make will be applied.

- Also, each container may need to set the required configuration files by coping and updating them depending on your project requirements.

- Containers availability by building the container with `docker-composer.yml` in separated configuration layers
    - Stand-alone
        - The container is intended to be published directly and accessed from the host network, typically via `0.0.0.0:<port>`. It does not require a shared Docker network. It is a common setting for local development.
    - Inside a Custom Network
        - The container is attached to a custom Docker network and is intended to be accessed through a reverse proxy or other containers on the same network.
        - This network setting is useful for isolating services while still allowing container-to-container communication.
        - It is a strongly recommended setting for remote deployment to avoid exposing the localhost port in used and protect by firewall.
        - <b>Connect from one container to another inside the custom network, by container name and its own exposed port</b>.
    - Host-Gateway
        - The container can reach services running on the host machine using the Docker host gateway mapping. This is useful when the container must access local services on the VPS/host, while public access is still handled through a reverse proxy. It is a recommended setting for remote deployment too.
    - Public exposure is controlled by the `ports` mapping.
    - `0.0.0.0:<port>` means externally accessible.
    - `127.0.0.1:<port>` means local-only access on the host and requires a reverse proxy, e.g. NGINX.
    - Docker network attachment controls container-to-container communication.
    - Host-gateway controls container-to-host communication.
<br><br>


## <a id="platforms-setup"></a>Platforms Setup

Create the root `./.env` file from the [./.env.example](./.env.example) and follow its description to configure the platforms required environment variables.

Also create the root `./Makefile` file from [./resources/automation/local/Makefile](./resources/automation/local/Makefile) so it will be easy to manage the platforms from one location in the project.

Each recipe from this root `Makefile` has its own action description and can be read to remember on terminal by executing `make help` command to see them all. This streamlines the workflow for managing containers with mnemonic recipe names, avoiding the effort of remembering and typing each bash command line:
```bash
$ make help
```

⚠️ **Important Setup Requirement:** Core platform components require independent configuration prior to initialization. Ensure you review the specific setup guidance in each sub-directory's `README.md` before starting up the container orchestration layer.

Once variables are set, each Docker platform container environment variables can be set by GNU Make recipes placed in the root of this repository:

- Setup the website application container
  ```bash
  $ make webapp-set
  ```
  **Remember**: *the `./application` directory name is custimizable for binding between the container and local machine.*

- Setup the API container
  ```bash
  $ make apirest-set
  ```
  **Remember**: *the `./api-rest` directory name is custimizable for binding between the container and local machine.*

- Setup the database container
  ```bash
  $ make db-set
  ```

- Setup the mail service container
  ```bash
  $ make mailer-set
  ```

- Setup the message broker service container
  ```bash
  $ make broker-set
  ```
<br>

## <a id="platforms-startup"></a>Platforms Startup

- Create and startup the website application container
  ```bash
  $ make webapp-create
  ```
  You can test the container by visiting localhost + assigned port
<br>

- Create and startup the API container
  ```bash
  $ make apirest-create
  ```
  You can test the container by visiting localhost + assigned port
<br>

- Create and startup the database container
  ```bash
  $ make db-create
  ```
<br>

- Create and startup the mail service container
  ```bash
  $ make mailer-create
  ```
<br>

- Create and startup the message broker service container
  ```bash
  $ make broker-create
  ```
<br>

Remember to use `make help` command to see all available receipes, so you can execute multiple platforms recipes at once as the following example:
```bash
$ yes | make webapp-destroy apirest-destroy db-destroy mailer-destroy broker-destroy
```

Execute the Docker ps command to see the containers that are up and running.
```bash
$ sudo docker ps
```

If someone has failed, from the list you can see the the container logs to review the possible issue with it. E.g.:
```bash
$ make apirest-info # displays container information

$ sudo docker logs [container-name]
```
<br><br>


## <a id="platforms-usage-for-projects"></a>Use this Platform Repository for your own projects

Repository directories structure overview
```sh
.
├── platforms/                              # infrastructure platforms
│   ├── nginx-nodejs-24/
│   │   ├── docker/
│   │   │   ├── config
│   │   │   ├── .env
│   │   │   ├── docker-compose.yml
│   │   │   └── Dockerfile
│   │   └── Makefile
│   │
│   ├── nginx-php-8.5/
│   │   ├── docker/
│   │   │   ├── config
│   │   │   ├── .env
│   │   │   ├── docker-compose.yml
│   │   │   └── Dockerfile
│   │   └── Makefile
│   │
│   ├── pgsql-18/
│   │   ├── docker/
│   │   │   ├── .env
│   │   │   ├── docker-compose.yml
│   │   │   └── ...etc
│   │   └── Makefile
│   │
│   ├── mailhog-1.0/
│   │   ├── docker/
│   │   │   ├── .env
│   │   │   ├── docker-compose.yml
│   │   │   └── ...etc
│   │   └── Makefile
│   │
│   └── rabbitmq-4.2/
│       ├── docker/
│       │   ├── .env
│       │   ├── docker-compose.yml
│       │   └── ...etc
│       └── Makefile
│
├── resources
│   ├── automation/
│   │   ├── local/
│   │   │   ├── Makefile                    # this is the main makefile that goes in the root of this repository
│   │   │   └── Makefile.child              # this goes inside ./services/[service-directory]
│   │   └── remote/                         # this directory is intended for store remote instance makefile, bash scripts, etc.
│   │       └── ...
│   ├── databases/                          # database init or backup scheme
│   │   ├── example-init.sql
│   │   └── example-backup.sql
│   └── docs/                               # orientative documentation
│       └── ...
│
├── services/                               # active services respositories
│   ├── api-rest/
│   │   ├── src/
│   │   ├── .env
│   │   ├── vendor/
│   │   └── etc...
│   ├── api-rest-ai/                        # AI Agent assistance specialized directory
│   │   ├── .cursorrules                    # System instructions for Cursor IDE
│   │   ├── .github/
│   │   │   └── copilot-instructions.md     # System instructions for GitHub Copilot
│   │   ├── mcp-configs/                    # Model Context Protocol schemas
│   │   │   ├── postgres-mcp.json           # Bridge to inspect database schemas directly
│   │   │   └── filesystem-mcp.json         # Scope rules for directory restrictions
│   │   └── prompts/                        # Shared templates for onboarding tasks
│   │       ├── 01-add-api-endpoint.md
│   │       ├── 02-db-migration.md
│   │       └── 03-generate-unit-test.md
│   ├── application/
│   │   ├── dist/
│   │   ├── src/
│   │   ├── config.ts
│   │   └── etc...
│   └── application-ai/
│       ├── .cursorrules
│       ├── .github/
│       ├── mcp-configs/
│       └── prompts/
│
├── .env
├── Makefile
└── README.md
```
<br>

Setup the by platforms coping `.env.example` to `.env` and adjust settings (rest api port, database port, mail service port, container RAM usage, etc.)
<br>

### Managing the services directories by Submodule vs Detached Repository

To reuse any of the `./services` directory to install your desired repository inside, there are two alternatives for managing both the platform and your repository independently. Here’s a step-by-step guide for using this Platform repository along with your own REST API repository:

- E.g., remove the existing `./services/api-rest` directory contents from local and from git cache.

- Install your desired repository inside `./services/api-rest`.

- Choose between Git submodule and detached repository approaches:

#### 1. GIT Detached Repository *(Recommended)*

> Git commands can be executed **whether from inside the container or on the local machine**.

- Remove `./service/api-rest` from local and git cache:
  ```bash
  $ git rm -r --cached -- "service/api-rest/*" ":(exclude)service/api-rest/.gitkeep"
  $ git clean -fd
  $ git reset --hard
  $ git commit -m "maint: service api-rest directory and its default installation removed"
  ```

- Clone the desired repository as a detached repository:
  ```bash
  $ git clone git@[vcs]:[account]/[repository].git ./services/api-rest
  ```

- The .`services/api-rest` directory is now an **independent repository**, not tracked as a submodule in your main repo. From now on, you will be able to use git commands freely inside the .`services/api-rest` directory.
<br>

#### 2. GIT Sub-module

> Git commands can be executed **only from inside the container**.

- Remove `./services/api-rest` from local and git cache:
  ```bash
  $ rm -rfv ./services/api-rest/* ./services/api-rest/.[!.]*$
  $ git rm -r --cached apirest
  $ git commit -m "maint: apirest directory and its default installation removed"
  ```

- Add the desired repository as a submodule:
  ```bash
  $ git submodule add git@[vcs]:[account]/[repository].git ./services/api-rest
  $ git commit -m "maint: apirest as a git submodule added"
  ```

- To update submodule contents:
  ```bash
  $ cd ./services/api-rest
  $ git pull origin main  # or desired branch
  ```

- To initialize or update submodules after `git clone`:
  ```bash
  $ git submodule update --init --recursive
  ```

#### Summary Table

| Approach         | Repo independence | Where to run git commands | Use case                         |
|------------------|-------------------|---------------------------|----------------------------------|
| Submodule        | Tracked by main   | Inside container          | Main repo controls webapp version|
| Detached (rec.)  | Fully independent | Local or container        | Maximum flexibility              |

> **Note**: After new project cloned inside `./services/api-rest`, consider adding `./services/api-rest/.gitkeep` in it to prevent accidental tracking *(especially for detached repository)*.

<br>


## <a id="platforms-usage-for-projects"></a>Use this Platform Repository for your own projects

Transitioning from scattered application directories to a unified `./services/` workspace scales your environment beautifully. Introducing explicit `*-ai` specialized directories provides a forward-thinking setup for modern Platform Engineering, optimizing context boundaries for automated tooling.

### Why this Architecture is an Improvement

• **Cleaner Namespace (Logical Grouping)**: Moving services like `./services/api-rest` under a unified workspace declutters the root directory. It cleanly isolates core business logic from underlying infrastructure configuration (`/platforms`, `/resources`).

• **Context Isolation for AI Tools**: Storing prompt templates, agent configurations, context maps, or synthetic testing data inside dedicated `*-ai` folders prevents cluttering the main application codebases. Modern AI coding agents work best when given scoped context; this layout strictly enforces that operational boundary.

• **Safer Git/Detached Repo Tracking**: If you use the Git Detached Repository approach for your application services, keeping those external repositories isolated inside `./services/` prevents main-repo tracking pollution even more reliably.

### What goes inside the `*-ai` directories?

To maximize the value of this boilerplate, populate the `./services/api-rest-ai/` and `./services/website-ai/` directories with standardized developer-agent configurations:

• **`.cursorrules` or `copilot-instructions.md`**: Pre-baked instructions telling AI agents exactly how to format code, write tests, or use your specific architecture guidelines.

• **`prompts/`**: Shared prompt templates for common onboarding tasks (e.g., how to add a new database migration, how to create a new API endpoint).

• **`mcp-configs/`**: Configuration schemas if you use Model Context Protocol (MCP) servers to bridge your IDE with external developer tools.

### Service Volume Mapping & Multi-Engine Behavior

The underlying Docker or Podman Compose engine can bind multiple local directories into a single container by declaring separate entries under the `volumes` key. This allows you to mount application source code and AI context directories exactly where they are needed.

#### Docker Compose Configuration

You can map multiple local folders to distinct destination paths within the same container:
```yaml
services:
  api-rest-ai:
    image: nginx:alpine
    volumes:
      # Bind Mount 1: Core application business logic
      - ./services/api-rest:/var/www/html
      # Bind Mount 2: Isolated AI assistance schemas
      - ./services/api-rest-ai:/var/www/ai-context
```

> ⚠️ **Note**: You cannot bind mount two different local directories onto the exact same destination path inside the container simultaneously. The subsequent mount will overwrite or obscure the previous one.

#### Multi-Engine Compatibility: Podman Setup

Podman natively supports this structure. You can run the exact same `docker-compose.yml` configuration using two common workflows:

1. **Podman Compose (`podman-compose`)**: A Python-based utility that natively reads your Compose blueprints and recreates the environment using Podman commands.

2. **Native Docker Compose v2**: Modern versions of Podman (3.0+) provide a local UNIX socket that allows the official `docker compose` binary to communicate directly with the Podman backend instead of a Docker daemon.

#### The Podman Gotcha: SELinux Permissions

If you are running Podman on an enterprise Linux distribution (such as RHEL, Fedora, or CentOS) with SELinux enabled, you will likely encounter a `Permission Denied` error inside the container when it tries to read the host directories.

To fix this, append the **`:Z`** or **`:z`** flag to your volume definitions so Podman automatically adjusts the host file security contexts:
```yaml
services:
  api-rest-ai:
    image: nginx:alpine
    volumes:
      # ':Z' configures a private, unshared label exclusive to this container
      - ./services/api-rest:/var/www/html:Z
      # ':z' configures a shared label if multiple containers require access
      - ./services/api-rest-ai:/var/www/ai-context:z
```

### Why Decouple the AI Assistance Layer?

Instead of mixing AI prompt files, IDE configurations, and security credentials directly inside your application runtime directory, this architecture structurally isolates them.

• **Zero Production Payload Pollution**: Keeping your orchestration or prompt files (like `.cursorrules`, MCP server configs, or onboarding templates) out of `./services/api-rest` ensures that your production builds, CI/CD artifacts, and vendor dependencies stay completely lightweight. Your production environment never pulls in development-only AI assets.

• **Strict Token & Security Boundaries**: AI tools often require custom environment variables or third-party bridge configurations. Isolating these configurations within an independent `*-ai` repository folder prevents secrets from accidentally leaking into your public application codebase or committing local token contexts into production Git histories.

• **Parallel Automation & Execution**: By mapping the `*-ai` directory to a dedicated, independent path inside the container environment, you can run isolated background workers, testing frameworks, or automated linting agents alongside your main API application loop without causing namespace collisions or file-lock issues.
<br><br>


## Contributing

Contributions are very welcome! Please open issues or submit PRs for improvements, new features, or bug fixes.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/YourFeature`)
3. Commit your changes (`git commit -am 'feat: Add new feature'`)
4. Push to the branch (`git push origin feature/YourFeature`)
5. Create a new Pull Request
<br><br>


## License

This project is open-sourced under the [MIT license](LICENSE).

<!-- FOOTER -->
<br>

---

<br>

- [GO TOP ⮙](#top-header)

<div style="with:100%;height:auto;text-align:right;">
    <img src="./resources/docs/images/pr-banner-long.png">
</div>