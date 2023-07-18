# Setup 


## Clone repositories

### localdeployment (Required serives)
All required (background) services to run MERLOT are bundled in the [localdeployment](https://github.com/merlot-education/localdeployment) repository *(coming soon)*

 * PostgreSQL 
 * neo4j
 * Gaia-X Federated Catalog (fc-server)
 * keycloak
 * rabbitmq

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/localdeployment.git
 ```

### The Marketplace (marketplace)

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/marketplace.git
 ```

### Authentication/Authorisation/AccountManagement-Orchestrator (aaam-orchestrator)

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/aaam-orchestrator.git
 ```

### Organisations-Orchestrator (organisations-orchestrator)

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/organisations-orchestrator.git
 ```

### ServiceOffering-Orchestrator (serviceoffering-orchestrator)

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/serviceoffering-orchestrator.git
 ```

### Contract-Orchestrator (contract-orchestrator)

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/contract-orchestrator.git
 ```

### Signer tool (Initial Setup)
This tool is needed to generate signed Verifiable Presentations which are accepted in the GXFS catalog.
 ```bash
cd ~/Documents/GitLab
git clone https://gitlab.com/gaia-x/data-infrastructure-federation-services/cat/fc-tools/signer
 ```

### SD Creation Wizard API/Frontend (Initial Setup)
 ```bash
cd ~/Documents/GitLab
git clone https://gitlab.eclipse.org/eclipse/xfsc/self-description-tooling/sd-creation-wizard-api


git clone https://gitlab.eclipse.org/eclipse/xfsc/self-description-tooling/sd-creation-wizard-frontend
 ```

### GXFS Example Workflows (Initial Setup)

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/gxfs-catalog-example-flows
 ```

### GXFS Service Characteristics

 ```bash
cd ~/Documents/GitHub
git clone https://github.com/merlot-education/service-characteristics
 ```

## Initial setup for required serives (localdeployment)
### Preparation (See Readme.md)

Create a folder for persisting docker data:

```
mkdir docker_data
sudo chmod 777 docker_data
```

This compose file also assumes that the orchestrator repos are cloned "next to" the localdeployment repo in order to allow for changes to the code.


Start the docker deployment
```bash
cd ~/Documents/GitHub/localdeployment
docker-compose up --build --force-recreate server
```

### Keycloak
Login on the admin console at [http://localhost:8080](http://localhost:8080/admin/)  and import the Realms `gxfscatalog` and  `POC1` from the https://sso.dev.merlot-education.eu/ instance 
User:Password admin:admin

**TODO: Provide download**

Create a user with the name `usermgmt`, the password `usermgmt` and  the role `admin` in the `master` realm.

Create a user with the name `gxfscatalog`, the password `gxfscatalog` and  the role `Ro-MU-CA` in the `gxfscatalog` realm. (Role from client "federated-catalogue")

Set and get the clientsecret for the federated catalogue. Keycloak -> gxfscatalog -> Clients -> federated-catalogue -> Credentials. 

Set the variable `FC_CLIENT_SECRET` in the `.env` file of the `localdeployment` repository
```
...
### KEYCLOCK PROPERTIES ###
FC_CLIENT_SECRET=Top$ecret!123
...
```

Restart the localdeployment
```bash
docker kill $(docker ps -q)
docker-compose up
```

Create an user in the POC1 realm and assing it to one or more of the roles in that realm. e. g. `OrgLegRep_10`, `OrgLegRep_30`.
This will be your marketplace user.

### SD Creation Wizard API
(check after migration to Eclipse, try newest relese)
```bash
cd ~/Documents/GitLab/sd-creation-wizard-api
git checkout ed958790fb14bd424e4148e7034195fa803d939c 
mvn clean install
docker build -t springio/gs-spring-boot-docker .
docker run -p 8085:8080 springio/gs-spring-boot-docker
```


### Gaia-X Federated Catalog

Build the signer tool **with the changes from the snippet comment**.

```bash
cd ~/Documents/GitLab/signer
# currently the source code needs to be edited in order to use reasonable paths for the certificates:
# replace:
#    private static final String PATH_TO_PRIVATE_KEY = "src/main/resources/prk.ss.pem";
#    private static final String PATH_TO_PUBLIC_KEY = "src/main/resources/cert.ss.pem";
# with:
#    private static final String PATH_TO_PRIVATE_KEY = "prk.ss.pem";
#    private static final String PATH_TO_PUBLIC_KEY = "cert.ss.pem";
mv src/main/resources/prk.ss.pem src/main/resources/cert.ss.pem ./
mvn clean install
java -jar target/gxfsTest-0.1.0-jar-with-dependencies.jar  
# copy this file to the example workflows directory below if needed 
cd target
cp gxfsTest-0.1.0-jar-with-dependencies.jar ~/Documents/GitHub/gxfs-catalog-example-flows
```

Create MERLOT schemas and upload to the catalogue. Then upload the organisations `~/Documents/GitHub/gxfs-catalog-example-flows/orgas.json` 

```bash
cd ~/Documents/GitHub/service-characteristics/toolchain
pip install -r requirements.txt
cd ..
sh update_sc.sh

cd ~/Documents/GitHub/gxfs-catalog-example-flows
pip install -r requirements.txt
python3 upload_schemas_to_catalog.py
python3 add_orgas_to_catalog.py
```

## Start debugging

### The Marketplace (Angular JS/Node)
```bash
npm install
ng serve
```

### Orchestrators (Java)

Set the environment variable `keycoak->client-secret` for the Keycloak ClientSecret in the `appliation.yml`

```yaml
logging:
  level:
    org.springframework.security: DEBUG

server:
  port: '8082'
  servlet:
    context-path: /api
  error:
    include-stacktrace: "never"

gxfscatalog:
  base-uri: "http://localhost:8081"
  participants-uri: "${gxfscatalog.base-uri}/participants"

keycloak:
  client-id: "federated-catalogue"
  authorization-grant-type: "password"
  base-uri: "http://localhost:8080"
  oidc-base-uri: "${keycloak.base-uri}/realms/gxfscatalog/protocol/openid-connect"
  authorization-uri: "${keycloak.oidc-base-uri}/auth"
  token-uri: "${keycloak.oidc-base-uri}/token"
  logout-uri: "${keycloak.oidc-base-uri}/logout"
  jwk-set-uri: "${keycloak.oidc-base-uri}/certs"
  client-secret: "Top$ecret!123"
  gxfscatalog-user: "gxfscatalog"
  gxfscatalog-pass: "gxfscatalog"
```


```bash
export KEYCLOAK_CLIENT-SECRET=Top$ecret!123
mvn clean install
mvn spring-boot:run
```

## Snapshot
If everything works, don`t forget to make a snapshot of the VM.

## IP Ports overview

### localdeployment (required services)
| Port | Service                | Description      |
| ---- | ---------------------- | ---------------- |
| 8080 | keycloak               | (HTTP) SSO       |
| XXXX | neo4j                  |                  |
| 5432 | postgres               |                  |
| 5672 | rabitmq                |                  |
| 8081 | fc-service             |                  |
| ---- | ---------------------- |                  |
| 8085 | sd-creation-wizard-api | (HTTP)           |

### Marketplace

| Port | Service                          | Description |
| ---- | -------------------------------- | ----------- |
| 4200 | marketplace                      | (HTTP)      |
| 8082 | organisations-orchestrator       | (HTTP)      |
| 8083 | aaam-orchestrator                | (HTTP)      |
| 8084 | serviceoffering-orchestrator     | (HTTP)      |
| 8086 | contracts-orchestrator           | (HTTP)      |