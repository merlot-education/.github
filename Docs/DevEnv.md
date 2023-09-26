# WSL

Checkout https://github.com/merlot-education/wsl-setup to you local machine and execute `merlot-wsl-setup.bat` 
This will install a Ubuntu WSL System with all requirements for MERLOT. (See: `merlot-wsl-setup.sh`)

If not already installed, install the Windows Terminal App. (On Windows o)
![Microsoft Store](/assets/TerminalApp.PNG)

# MERLOT

TODO hosts, usermgmt

![MERLOT Terminal](/assets/WSLTerminal.PNG)
```
cd ~/workspace/mpo/localdeployment
docker compose up -d
```
This config starts all services. If you want to work on a selected project, deactiviate the container.
 
Background services 
 * vault
 * postgre
 * neo4j
 * server (GXFS Catalog)
 * rabbitmq
 * keycloak
 * sd-creation-wizard-api

MERLOT Projects (To debug)
 * marketplace
 * organisations-orchestrator
 * aaam-orchestrator
 * serviceoffering-orchestrator
 * contract-orchestrator

![Running Marketplace](/assets/dockercomposeup.PNG)

Final step, seed data to the catalog
```
cd ~/workspace/mpo/gxfs-example-flows/
./initialize_catalog.sh
```

# Dev IDE
## Download devonfw

Download current release of devonfw from `https://github.com/devonfw/ide/releases` and install to `D:\devonfw\MERLOT` via `setup.bat`
![devonfw](/assets/devonfw.PNG)
When installing set the config repo to: https://github.com/merlot-education/ide-settings

![devonfw](/assets/devonfw_settings.PNG)

## IDEs

### VS Code
```
D:\devonfw\MERLOT\vscode.bat
```
![devonfw](/assets/VSCode_WSL.PNG)

To start from commandline
```
cd ~/workspace/mpo/marketplace/

ng serve
```

`http://localhost:4200`

### IntelliJ
Create a `intellij.bat` in the devonfw folder `D:\devonfw\MERLOT` with the following content
```
@echo off
pushd %~dp0
cd workspaces/main
call devon.bat intellij
popd
```

Start the IntelliJ IDE.
Create a new project and adjust the following settings:


Finally select a project from MERLOT to open from your WSL instance.
```
D:\devonfw\MERLOT\intellij.bat
```
![devonfw](/assets/IntelliJ_OpenFileOrProject.PNG)

To start from commandline
```
cd ~/workspace/mpo/xxx-orcestrator/

mvn clean install
mvn spring-boot:run
```

## WSL Cheatsheet

Shutdown the MERLOT WSL instance
```
PS C:\Users\Bob.Ross> wsl --terminate merlot
```

Delete the MERLOT WSL instance
```
PS C:\Users\Bob.Ross> wsl --unregister merlot
```
