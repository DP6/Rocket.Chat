DP6: Hospedando Rocket.Chat
======

Neste documento são apresentados os passos básicos para configuração mínima do Rocket.Chat em uma instância do Compute Engine, no Google Cloud.

# Geral

O [Rocket.Chat](https://rocket.chat/) é uma plataforma web open source para comunicação de times.

Apesar de ser relativamente inferior ao [Slack](https://slack.com/) em termos de integrações e funcionalidades, o [Rocket.Chat](https://rocket.chat/) pode ser hospedado em um servidor próprio (conferindo autonomia sobre o sigilo das informações), não estando sujeito aos limites impostos pelo [Slack](https://slack.com/):

- 10,000 mensagens
- 5 GB de memória para arquivos anexados

# Implantação

Estão detalhados a seguir os passos realizados para hospedar o [Rocket.Chat](https://rocket.chat/) em uma instância do Compute Engine.

## Criação de Instância

Foram utilizadas as seguintes configurações na instância criada:

| Campo                   | Valor                                                                                                                                     |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------|
| Name                    | rocket-chat                                                                                                                               |
| Zone                    | us-central1-c                                                                                                                             |
| Machine type            | small (1 shared vCPU) 1.7 GB memory                                                                                                       |
| Boot disk               | New 10 GB SSD persistent disk / Debia GNU/Linux 8 (jessie)                                                                                |
| Identity and API access | Compute Engine default service account (Allow default access)                                                                             |
| Firewall                | Allow HTTP traffic = checked ; Allow HTTPS traffic = checked                                                                              |
| SSH key                 | Informar um SSH key criada na máquina local ([o que é isso?](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys--2)) |
| **Custo estimado**      | **$15.50 por mês**                                                                                                                        |

Ao finalizar a criação, já possível encontrar na tela o IP externo para acesso via SSH.

## Download do git

Ao acessar via SSH a máquina criada na seção anterior, é necessário instalar o git, para isso execute:

> seu.user@rocket-chat:~$ sudo apt-get install git-core

## Download do Rocket.Chat

O código fonte do [Rocket.Chat](https://rocket.chat/) pode ser baixado do GitHub através do comando:

> seu.user@rocket-chat:~$ git clone https://github.com/RocketChat/Rocket.Chat.git

## Download do Meteor

O [Rocket.Chat](https://rocket.chat/) é executado através do [Meteor](https://www.meteor.com/), que pode ser baixado da seguinte forma:

> seu.user@rocket-chat:~$ curl https://install.meteor.com/ | sh

## Iniciando o Rocket.Chat

Para iniciar o [Rocket.Chat](https://rocket.chat/), entre na pasta criada pelo **git clone**:

> seu.user@rocket-chat:~$ cd Rocket.Chat/

E execute o seguinte comando:

> seu.user@rocket-chat:~/Rocket.Chat$ sudo meteor run --port 80

Essa etapa deve levar vários minutos, mas se não houver erros, o [Rocket.Chat](https://rocket.chat/) estará disponível para acesso via navegador no mesmo IP externo utilizado para acesso via SSH.

## Configurando um web server

A referência: **Como configurar Nginx e Meteor** cobre todo esse processo.

As maiores dificuldades encontradas foram:

### Como gerar o certificado de SSL

Ao invés de usar a referência indicada, vá até sua home e execute:

```bash
openssl req -new -key tchola.dp6.io.key -out tchola.dp6.io.csr
```

Cheque que os dois arquivos (**tchola.dp6.io.key** e **tchola.dp6.io.csr**) existem na sua home.

Vá ao GoDaddy, gere o certificado informando o conteudo de **tchola.dp6.io.csr**.

O GoDaddy vai disponibilizar dois arquivos:

- 5c1a7329de363bf7.crt (o nome é uma string com 16 caracteres);
- gd_bundle-g2-g1.crt

Este é o certificado intermediário:

```bash
-----BEGIN CERTIFICATE-----
MIIE0DCCA7igAwIBAgIBBzANBgkqhkiG9w0BAQsFADCBgzELMAkGA1UEBhMCVVMx
EDAOBgNVBAgTB0FyaXpvbmExEzARBgNVBAcTClNjb3R0c2RhbGUxGjAYBgNVBAoT
EUdvRGFkZHkuY29tLCBJbmMuMTEwLwYDVQQDEyhHbyBEYWRkeSBSb290IENlcnRp
ZmljYXRlIEF1dGhvcml0eSAtIEcyMB4XDTExMDUwMzA3MDAwMFoXDTMxMDUwMzA3
MDAwMFowgbQxCzAJBgNVBAYTAlVTMRAwDgYDVQQIEwdBcml6b25hMRMwEQYDVQQH
EwpTY290dHNkYWxlMRowGAYDVQQKExFHb0RhZGR5LmNvbSwgSW5jLjEtMCsGA1UE
CxMkaHR0cDovL2NlcnRzLmdvZGFkZHkuY29tL3JlcG9zaXRvcnkvMTMwMQYDVQQD
EypHbyBEYWRkeSBTZWN1cmUgQ2VydGlmaWNhdGUgQXV0aG9yaXR5IC0gRzIwggEi
MA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQC54MsQ1K92vdSTYuswZLiBCGzD
BNliF44v/z5lz4/OYuY8UhzaFkVLVat4a2ODYpDOD2lsmcgaFItMzEUz6ojcnqOv
K/6AYZ15V8TPLvQ/MDxdR/yaFrzDN5ZBUY4RS1T4KL7QjL7wMDge87Am+GZHY23e
cSZHjzhHU9FGHbTj3ADqRay9vHHZqm8A29vNMDp5T19MR/gd71vCxJ1gO7GyQ5HY
pDNO6rPWJ0+tJYqlxvTV0KaudAVkV4i1RFXULSo6Pvi4vekyCgKUZMQWOlDxSq7n
eTOvDCAHf+jfBDnCaQJsY1L6d8EbyHSHyLmTGFBUNUtpTrw700kuH9zB0lL7AgMB
AAGjggEaMIIBFjAPBgNVHRMBAf8EBTADAQH/MA4GA1UdDwEB/wQEAwIBBjAdBgNV
HQ4EFgQUQMK9J47MNIMwojPX+2yz8LQsgM4wHwYDVR0jBBgwFoAUOpqFBxBnKLbv
9r0FQW4gwZTaD94wNAYIKwYBBQUHAQEEKDAmMCQGCCsGAQUFBzABhhhodHRwOi8v
b2NzcC5nb2RhZGR5LmNvbS8wNQYDVR0fBC4wLDAqoCigJoYkaHR0cDovL2NybC5n
b2RhZGR5LmNvbS9nZHJvb3QtZzIuY3JsMEYGA1UdIAQ/MD0wOwYEVR0gADAzMDEG
CCsGAQUFBwIBFiVodHRwczovL2NlcnRzLmdvZGFkZHkuY29tL3JlcG9zaXRvcnkv
MA0GCSqGSIb3DQEBCwUAA4IBAQAIfmyTEMg4uJapkEv/oV9PBO9sPpyIBslQj6Zz
91cxG7685C/b+LrTW+C05+Z5Yg4MotdqY3MxtfWoSKQ7CC2iXZDXtHwlTxFWMMS2
RJ17LJ3lXubvDGGqv+QqG+6EnriDfcFDzkSnE3ANkR/0yBOtg2DZ2HKocyQetawi
DsoXiWJYRBuriSUBAA/NxBti21G00w9RKpv0vHP8ds42pM3Z2Czqrpv1KrKQ0U11
GIo/ikGQI31bS/6kA1ibRrLDYGCD+H1QQc7CoZDDu+8CL9IVVO5EFdkKrqeKM+2x
LXY2JtwE65/3YR8V3Idv7kaWKK2hJn0KCacuBKONvPi8BDAB
-----END CERTIFICATE-----
```

Salve ele em um arquivo **g2_intermediate.crt**. Faça um backup do seu arquivo **tchola.dp6.io.csr**:

```bash
mv tchola.dp6.io.csr tchola.dp6.io.csr.bkp
```

Em seguida (com todos os arquivos na sua home):

```bash
cat 5c1a7329de363bf7.crt g2_intermediate.crt gd_bundle-g2-g1.crt > tchola.dp6.io.csr
```

Agora, faça o seguinte:

```bash
$ cd /etc/nginx
$ sudo chmod 777 ssl
$ sudo cd ssl
$ sudo cp ~/tchola.dp6.io.key .
$ sudo cp ~/tchola.dp6.io.crt .
$ cd ..
$ sudo chmod 700 ssl
```

Pronto, agora referencie estes dois arquivos em seu arquivo de configuração do Nginx:

```
...
ssl_certificate /etc/nginx/ssl/tchola.dp6.io.crt;
ssl_certificate_key /etc/nginx/ssl/tchola.dp6.io.key;
...
```

### Atualizando o MongoDB

A referência **Atualizar MongoDB** é suficiente, mas é necessário remover o MongoDB atual antes.

### Variáveis de Ambiente

Algumas das variáveis de ambiente definidas na referência **Como configurar Nginx e Meteor** não ficam 100% de acordo com o ambiente.

Na hora de configurar o *service* utilize o seguinte conteudo:

```bash
# upstart service file at /etc/init/collabdp6.conf
description "Meteor.js (NodeJS) application"
author "Daniel Speichert <daniel@speichert.pro>"

# When to start the service
start on started mongodb and runlevel [2345]

# When to stop the service
stop on shutdown

# Automatically restart process if crashed
respawn
respawn limit 10 5

# we don't use buil-in log because we use a script below
# console log

# drop root proviliges and switch to mymetorapp user
setuid collabdp6
setgid collabdp6

script
    export LC_PAPER=pt_BR.UTF-8
    export LC_ADDRESS=pt_BR.UTF-8
    export LC_MONETARY=pt_BR.UTF-8
    export LC_NUMERIC=pt_BR.UTF-8
    export LC_ALL=C
    export LC_TELEPHONE=pt_BR.UTF-8
    export LC_IDENTIFICATION=pt_BR.UTF-8
    export LC_MEASUREMENT=pt_BR.UTF-8
    export LC_TIME=pt_BR.UTF-8
    export LC_NAME=pt_BR.UTF-8

    export PATH=/usr/local/bin:/usr/bin:/bin:/usr/local/games:/usr/games:/usr/sbin:/sbin
    # set to home directory of the user Meteor will be running as
    export PWD=/home/collabdp6
    export HOME=/home/collabdp6
    # leave as 127.0.0.1 for security
    export BIND_IP=127.0.0.1
    # the port nginx is proxying requests to
    export PORT=8080
    # this allows Meteor to figure out correct IP address of visitors
    export HTTP_FORWARDED_COUNT=1
    # MongoDB connection string using collabdp6 as database name
    export MONGO_URL=mongodb://127.0.0.1:27017/collabdp6
    # The domain name as configured previously as server_name in nginx
    export ROOT_URL=https://collab.dp6.io
    # optional JSON config - the contents of file specified by passing "--settings" parameter to meteor command in development mode
    #export METEOR_SETTINGS='{ "somesetting": "someval", "public": { "othersetting": "anothervalue" } }'
    #export METEOR_SETTINGS=`cat /home/collabdp6/bundle/programs/server/config.json`
    # this is optional: http://docs.meteor.com/#email
    # commented out will default to no email being sent
    # you must register with MailGun to have a username and password there
    # export MAIL_URL=smtp://postmaster@mymetorapp.net:password123@smtp.mailgun.org
    # alternatively install "apt-get install default-mta" and uncomment:
    # export MAIL_URL=smtp://localhost
    exec node /home/collabdp6/bundle/main.js >> /home/collabdp6/collabdp6.log
end script
```

O conteudo acima deve ser salvo em um arquivo como **/etc/init/collabdp6.conf**, supondo que o usuário criado para o daemon seja **collabdp6**.

# Referências

- Repositório GitHub do projeto: https://github.com/RocketChat/Rocket.Chat#development
- Como instalar git em Debian: https://www.digitalocean.com/community/tutorials/how-to-install-git-on-debian-8
- Como instalar o Meteor: https://www.meteor.com/install
- Como criar um certificado SSL: https://www.digitalocean.com/community/tutorials/how-to-create-a-ssl-certificate-on-nginx-for-ubuntu-12-04
- Como configurar Nginx e Meteor: https://www.digitalocean.com/community/tutorials/how-to-deploy-a-meteor-js-application-on-ubuntu-14-04-with-nginx
- Upstart: http://upstart.ubuntu.com/download.html
- Atualizar MongoDB: https://docs.mongodb.com/v3.2/tutorial/install-mongodb-on-debian/
