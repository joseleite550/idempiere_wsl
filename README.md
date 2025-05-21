# iDempiere. Community Powered Enterprise

iDempiere Business Suite ERP/CRM/SCM done the community way.  Focus is on the Community that includes Subject Matter Specialists, Implementors and End-Users.

Please visit the project front page containing links for most of our resources: https://www.idempiere.org/

You can have a first-hand experience about the software in one of our live cloud test sites: https://www.idempiere.org/test-sites

If you want to install it by yourself, the most used way is to follow the guide: https://wiki.idempiere.org/en/Installing_iDempiere

In case of doubts please use our support forums at https://groups.google.com/group/idempiere  
Or our Mattermost support channel in https://mattermost.idempiere.org/  
Don't forget to check our [code of conduct](CODE_OF_CONDUCT.md) and our [contribution guide](CONTRIBUTING.md)

If you find a bug or want to register a feature request please consider discussing it first in our forums, and after you're totally sure, please use the JIRA ticketing system: https://idempiere.atlassian.net

Continuous integration when changing this repository is managed with Jenkins at https://jenkins.idempiere.org/

# Inicialização rápida do sistema para desenvolvimento

## Instale o wsl 
```bash
wsl -–install -d Ubuntu
```

## Configure a chave ssh do git e cadastre a nova chave gerada no github
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
```bash
eval "$(ssh-agent -s)"
```
```bash
ssh-add ~/.ssh/id_rsa
```
```bash
cat ~/.ssh/id_rsa.pub
```
## Copie a chave gerada e vá para https://github.com/settings/keys

```bash
ssh -T git@github.com
```

## Instale a jdk 17
```bash
sudo apt-get update
```
```bash
sudo apt-get install openjdk-17-jdk-headless
```

## Preparando projeto
```bash
git clone git@github.com:idempiere/idempiere.git
```
```bash
cd idempiere
```
```bash
git checkout release-11
```
### Inatalando postgresql 15
```bash
sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
```
```bash
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install postgresql-15
```

### Abrindo o arquivo: /etc/postgresql/15/main/pg_hba.conf
```bash
sudo nano /etc/postgresql/15/main/pg_hba.conf
```
#### Altere a Linha
```
local   all             all                                     peer
```
#### Para
```
local   all             all                                     scram-sha-256
```
### Iniciando o serviço do postgres 
```bash
sudo service postgresql reload
```
## Criando usuário postgres
```bash
sudo su - postgres
```
```bash
psql -U postgres -c "CREATE ROLE adempiere SUPERUSER LOGIN PASSWORD 'adempiere'"
```
```bash
exit
```

# Instale o Maven 
```bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.8/binaries/apache-maven-3.9.8-bin.tar.gz -O maven.tar.gz
```
```bash
tar -xvzf maven.tar.gz
```
```bash
sudo mv apache-maven-3.9.8 /opt/maven
```

### Abra o arquivo bashrc
```bash
nano ~/.bashrc
```
#### Adicione as linhas no final do arquivo
```
export M2_HOME=/opt/maven
export PATH=$M2_HOME/bin:$PATH
```
#### Reload no arquivo
```bash
source ~/.bashrc
```

## Build maven
```bash
mvn verify
```

# Na home do WSL ~ Rode o comando para instalar e abrir o eclipse
```bash
wget https://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/2023-03/R/eclipse-jee-2023-03-R-linux-gtk-x86_64.tar.gz -O eclipse-jee.tar.gz
```
```bash
tar -xvzf eclipse-jee.tar.gz
```
```bash
sudo apt-get install -y libswt-gtk-4-java
```
```bash
eclipse/eclipse
```

## Criar banco de dados inicial
### Caso o comando solicitar utilizar senha criada na criação do usuario ubuntu
```bash
sudo su - postgres
```
### Caso o comando solicitar senha utilizar adempiere
```bash
createdb  --template=template0 -E UNICODE -O adempiere -U adempiere idempiere
```
```bash
psql -d idempiere -U adempiere -c "ALTER ROLE adempiere SET search_path TO adempiere, pg_catalog"
```
```bash
psql -d idempiere -U adempiere -c 'CREATE EXTENSION "uuid-ossp"'
```
```bash
exit
```

### Exporta dmp do banco de dados
```bash
cd /tmp
```
```bash
jar xvf ~/idempiere/org.adempiere.server-feature/data/seed/Adempiere_pg.jar
```
### Caso o comando solicitar senha utilizar adempiere
```bash
psql -d idempiere -U adempiere -f Adempiere_pg.dmp
```

