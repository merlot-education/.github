# Set up MERLOT for local development

## Preparations
### SSO
In order to resolve the SSO for MERLOT to a different hostname than localhost, we need to prepare the `%windir%\system32\drivers\etc\hosts` file by adding the following line:

    127.0.0.1	key-server

### GitHub Package Read Token

To build the MERLOT services you need to generate a GitHub read-only token in order to be able to fetch maven packages from 
GitHub. You can create this token at https://github.com/settings/tokens with at least the scope "read:packages".
Keep this token safe as we will use it at a later step.

### WSL
To run MERLOT locally for development you need a Windows machine (tested on Windows 10 Pro 22H2) and install "Windows Subsystem for Linux" from the Microsoft Store.

If you already have a WSL set up, please verify it is the correct version by running

    wsl --version

in a Powershell or Command Line. If this does not print a `WSL-Version: 2.x.x.x` or the command or --version flag is not available, you might have installed an incompatible version of WSL. Please install the aforementioned version from the Store instead.

### Windows Terminal (optional)
For easier access to the WSL instance it is recommended to install "Windows Terminal" from the store. This allows to specifically select the MERLOT WSL as a terminal once it is installed.

## Setup MERLOT WSL

Checkout https://github.com/merlot-education/wsl-setup to your local machine and execute `merlot-wsl-setup.bat` in a Powershell.
This will install a MERLOT-specific Ubuntu WSL System with all dependencies for MERLOT.

If there were no errors during the setup you can proceed to running the development stack as described below.

## Run MERLOT development stack

Open a Windows Terminal within the MERLOT WSL and change to the localdeployment folder:

    cd ~/workspace/mpo/localdeployment

In order to be able to pull packages from GitHub you will now need your generated GitHub token from the Preparations step.
Paste the token value into the `~/workspace/mpo/localdeployment/secrets/git_auth_token.txt` file.

If you also want to be able to run data transfers using EDCs in the marketplace, you can optionally also fill in the respective secrets in the `~/workspace/mpo/localdeployment/secrets/edc_ionos_secrets.txt` file.

Once your secrets are set up you can run

    docker compose up -d

to start the full MERLOT stack. If there are failures during the build, please first just try again to run above command as sometimes the package registries become unavailable briefly. Similarly it is expected that the Organisation-Orchestrator fails to fully start the first time, please just restart it as it should run on the next start.

At this point you should have the MERLOT marketplace running locally. You can access the frontend at http://localhost:4200/ and log in using the username `user` and password `user`.

Similarly you can familiarize yourself with the running docker stack by accessing https://localhost:9443/ and creating a local account. This is a Portainer instance which provides you with a GUI for Docker within the MERLOT WSL.

You can also access the Keycloak admin panel at http://key-server:8080/admin using the user `admin` and password `admin`.

For accessing the PostgreSQL database you can use PGAdmin at http://localhost:5050/ with user `admin@admin.admin` and password `admin`.
In PGAdmin you then need to set up a connection with the host `postgres`, port `5432`, user `postgres` and password `postgres`.

Finally you can access the Neo4j database at xxx with URL `bolt://localhost:7687`, user `neo4j` and password `neo12345`.

## Development IDE setup

We advise to use the devonfw IDE setup to make sure that you use the same formatting and linting options as the rest of the development team.

To set up devonfw, you can download the latest release at https://github.com/devonfw/ide/releases and run the `setup.bat` file. When the setup asks you for a configuration URL, please enter https://github.com/merlot-education/ide-settings .

Afterwards you can start VSCode using the `vscode.bat` file or run IntelliJ by creating a `intellij.bat` file in the same folder with the following content:

    @echo off
    pushd %~dp0
    cd workspaces/main
    call devon.bat intellij
    popd

If you do not want to use devonfw you can technically of course use any other IDE of your preference to develop for MERLOT. You might need to take specific care of formatting conflicts though when creating a pull request later on, hence it is not officially supported by us.

## Develop/Adjust MERLOT Services

Previously in the Run MERLOT development stack section you started the whole MERLOT docker stack using 

    docker compose up -d

Let's say we would like to work on a specific microservice - say in this example the Organisations Orchestrator - you can stop the organisations-orchestrator Docker container either using the Portainer GUI that was introduced previously or by running 

    docker compose down organisations-orchestrator

in the WSL instance.
Once the container is stopped, you can access the repository of this service at `~/workspace/mpo/organisations-orchestrator` and open your IDE within this folder.

You can then make your adjustments and run the service directly from your IDE or terminal. It will behave the same way as if it was within the docker stack as the containers use the host network.

If you want to run the service with your changes directly within the docker stack instead, you can force a rebuild using

    docker compose up -d --build --force-recreate organisations-orchestrator

which will then start your new version of the service.

While this example handled the Organisations Orchestrator, this will of course work for any service within the stack. For viewing the deployed services, please consult the Portainer GUI or the `~/workspace/mpo/localdeployment/docker-compose.yml` file. 

## WSL Cheatsheet

Shutdown the MERLOT WSL instance
```
PS C:\Users\Bob.Ross> wsl --terminate merlot
```

Delete the MERLOT WSL instance
```
PS C:\Users\Bob.Ross> wsl --unregister merlot
```
