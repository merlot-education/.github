# MERLOT Development VM **[DEPRECATED]**

Download and install Virtualbox 7.0.8 from: https://www.virtualbox.org/wiki/Downloads

## Configuration

|      | Minimum |  Recommended |
| ---- | ------- | ------------ |
| CPU  | 8       | >= 8         |
| RAM  | 16 GB   | >= 32 GB     |
| Disk | 100 GB  | >= 100 GB    | 

## Operating system

Download and install [Ubuntu 22.04 LTS](https://ubuntu.com/download/desktop/) and check for the latest updates.

```bash
sudo apt update
sudo apt upgrade
sudo apt autoremove
```

Install VirtualBox Guest Extensions
```bash
sudo apt-get install -y  virtualbox-guest-additions-iso
```

### git
Install git from the Ubuntu repository. 

```bash
sudo apt install -y git
```

### Node.js LTS (18.X)
Install the current node.js LTS (18.X) snap.

```bash
sudo snap install --classic node 
```

### Python 3
Install Python with pip from the Ubuntu repository.

```bash
sudo apt install -y python3 
sudo apt install -y python3-pip
```

### Docker
Install Docker with the following script. 

```bash
sudo apt-get install -y ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update

sudo apt-get -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin docker-compose

sudo usermod -aG docker $USER
sudo reboot
```

### Java
Install the OpenJDK SDKs from the Ubuntu repository.

```bash
sudo apt install -y openjdk-11-jdk openjdk-17-jdk openjdk-18-jdk
sudo apt install -y maven

sudo update-alternatives --config java
```

### VS Code
Install the VS Code snap with the following extensions.

```bash
sudo snap install --classic code
```

 * Live Share  - Microsoft
 * ESLint - Microsoft
 * Python- Microsoft
 * Angular Language Service
 * Prettier - Code Formatter - Prettier
 * vscode-icons - VSCode Icons Team

### JetBrains IntelliJ
Install Jetbrains IDEA Community from the Ubunutu App Store as Snap

### GitHub Desktop
Download and install GitHub Desktop for Linux from: https://github.com/shiftkey/desktop/releases

### Google Chrome
Download and install Google Chrome from: https://www.google.com/chrome/

### Angular tools
```bash
npm install -g @angular/cli
```

### The ***hosts*** file
For the GXFS catalog, add the one entry to your `/etc/hosts` file, wich points key-server to 127.0.0.1.

``` 
127.0.0.1 	localhost
127.0.1.1 	MERLOT-DEV
127.0.0.1   key-server #new entry

# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

## Snapshot
If everything works, don`t forget to make a snapshot of the VM.