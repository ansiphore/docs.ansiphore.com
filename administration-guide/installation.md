# Installation

You can install Semaphore in 4 ways:

* [Snap](installation.md#snap)
* [Package manager](installation.md#package-manager)
* [Docker](installation.md#docker)
* [Binary file](installation.md#binary-file)

### Snap

To install Semaphore via snap run following command in terminal:

```bash
sudo snap install semaphore
```

Semaphore will be available by URL [https://localhost:3000](https://localhost:3000).&#x20;

But to log in, you should create an admin user. Do it by the following command:

```bash
sudo snap stop semaphore

sudo semaphore user add --admin \
--login john \
--name=John \
--email=john1996@gmail.com \
--password=12345

sudo snap start semaphore
```

You can check the status of the Semaphore service using the following command:

```
sudo snap services semaphore
```

It should print the following table:

```
Service               Startup  Current  Notes
semaphore.semaphored  enabled  active   -
```

After installation, you can set up Semaphore via [Snap Configuration](https://snapcraft.io/docs/configuration-in-snaps). Use following command to see your Semaphore configuration:

```
sudo snap get semaphore
```

&#x20;List of available options you can find in [Configuration options reference](https://docs.ansible-semaphore.com/administration-guide/configuration#configuration-options).

### Package manager

{% hint style="info" %}
Python, Ansible and Git should be installed on your system.
{% endhint %}

Download package file from [Releases page](https://github.com/ansible-semaphore/semaphore/releases).

&#x20;`*.deb` for Debian and Ubuntu, `*.rpm` for CentOS and RedHat.&#x20;

Install it with using package manager.

{% tabs %}
{% tab title="Debian / Ubuntu (x64)" %}
```bash
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.8.9/semaphore_2.8.9_linux_amd64.deb

sudo dpkg -i semaphore_2.8.9_linux_amd64.deb
```
{% endtab %}

{% tab title="Debian / Ubuntu (ARM64)" %}
```
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.8.9/semaphore_2.8.9_linux_arm64.deb

sudo dpkg -i semaphore_2.8.9_linux_arm64.deb
```
{% endtab %}

{% tab title="CentOS (x64)" %}
```
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.8.9/semaphore_2.8.9_linux_amd64.rpm

sudo yum install semaphore_2.8.9_linux_amd64.rpm
```
{% endtab %}

{% tab title="CentOS (ARM64)" %}
```
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.8.9/semaphore_2.8.9_linux_arm64.rpm

sudo yum install semaphore_2.8.9_linux_arm64.rpm
```
{% endtab %}
{% endtabs %}

Setup Semaphore by following command:

```
semaphore setup
```

Now you can run Semaphore:

```
semaphore service --config=./config.json
```

Semaphore will be available by URL [https://localhost:3000](https://localhost:3000).

### Docker

&#x20;Create file `docker-compose.yml` with following content:

```yaml
version: '2'

services:

  mysql:
    ports:
      - 3306:3306
    image: mysql:5.6
    hostname: mysql
    environment:
      MYSQL_RANDOM_ROOT_PASSWORD: 'yes'
      MYSQL_DATABASE: semaphore
      MYSQL_USER: semaphore
      MYSQL_PASSWORD: semaphore

  semaphore:
    ports:
      - 3000:3000
    image: ansiblesemaphore/semaphore:latest
    environment:
      SEMAPHORE_DB_USER: semaphore
      SEMAPHORE_DB_PASS: semaphore
      SEMAPHORE_DB_HOST: mysql
      SEMAPHORE_DB_PORT: 3306
      SEMAPHORE_DB: semaphore
      SEMAPHORE_PLAYBOOK_PATH: /tmp/semaphore/
      SEMAPHORE_ADMIN_PASSWORD: cangetin
      SEMAPHORE_ADMIN_NAME: admin
      SEMAPHORE_ADMIN_EMAIL: admin@localhost
      SEMAPHORE_ADMIN: admin
    depends_on:
      - mysql
```

Run following command to start Semaphore with MySQL:

```
docker-compose up
```

&#x20;Semaphore will be available by URL [https://localhost:3000](https://localhost:3000).

For more information about the Docker Compose, see the [Docker Compose reference](https://docs.docker.com/compose/).

### Binary file

{% hint style="info" %}
Python, Ansible and Git should be installed on your system.
{% endhint %}

Download `*.tar.gz` for your platform from [Releases page](https://github.com/ansible-semaphore/semaphore/releases). Unpack it and setup Semaphore.

{% tabs %}
{% tab title="Linux (x64)" %}
```
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.8.9/semaphore_2.8.9_linux_amd64.tar.gz

tar xf semaphore_2.8.9_linux_amd64.tar.gz

./semaphore setup
```
{% endtab %}

{% tab title="Linux (ARM64)" %}
```
wget https://github.com/ansible-semaphore/semaphore/releases/\
download/v2.8.9/semaphore_2.8.9_linux_arm64.tar.gz

tar xf semaphore_2.8.9_linux_arm64.tar.gz

./semaphore setup
```
{% endtab %}

{% tab title="Windows (x64)" %}
```
Invoke-WebRequest `
-Uri ("https://github.com/ansible-semaphore/semaphore/releases/" +
      "download/v2.8.9/semaphore_2.8.9_windows_amd64.zip") `
-OutFile semaphore.zip

Expand-Archive -Path semaphore.zip  -DestinationPath ./

./semaphore setup
```
{% endtab %}
{% endtabs %}

Now you can run Semaphore:

```
./semaphore service --config=./config.json
```

Semaphore will be available by URL [https://localhost:3000](https://localhost:3000).

### Run as service

If you installed Semaphore via package manager or by downloading binary file, you should create Semaphore service manually.

Create systemd service file:

{% hint style="info" %}
Replace `/path/to/semaphore` and `/path/to/config.json` to your semaphore and config file path
{% endhint %}

```
sudo cat > /etc/systemd/system/semaphore.service <<EOF
[Unit]
Description=Semaphore Ansible
Documentation=https://github.com/ansible-semaphore/semaphore
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/path/to/semaphore service --config=/path/to/config.json
SyslogIdentifier=semaphore
Restart=always

[Install]
WantedBy=multi-user.target
EOF
```

Start the Semaphore service:

```
sudo systemctl daemon-reload
sudo systemctl start semaphore
```

Check the Semaphore service status:

```
sudo systemctl status semaphore
```

To make the Semaphore service auto start:

```
sudo systemctl enable semaphore
```

