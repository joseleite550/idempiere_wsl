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

## Para começarmos a trabalhar com o iDempiere, vamos utilizar o WSL. Execute o comando abaixo dentro de um terminal do powershell para instalar o WSL:

```bash
wsl --install -d Ubuntu
```

### Durante a instalação do WSL, será solicitado um nome de usuário e uma senha para criação do usuário com permissões de sudo. Atenção: o terminal não exibe a digitação da senha. Crie uma senha fácil de lembrar, pois ela será utilizada em outras situações.

# Configuração da chave SSH para utilizar o GitHub

## Execute os seguintes comandos dentro do WSL para criar uma chave SSH que será utilizada no GitHub:

#### Nesses passos, apenas confirme as ações solicitadas:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

```bash
eval "$(ssh-agent -s)"
```

```bash
ssh-add ~/.ssh/id_rsa
```

## Após gerar a chave, execute o comando abaixo
```bash
cat ~/.ssh/id_rsa.pub
```
![image](https://github.com/user-attachments/assets/7e571126-16d7-4418-ab8d-d84bfaacc7e2)

### Copie a chave exibida em tela e acesse: https://github.com/settings/keys para cadastrá-la.

![image](https://github.com/user-attachments/assets/68fcd60b-2a9e-47bb-96fb-e106e16e12d8)

## Cole a chave copiada no bloco key e de um nome para essa chave em title

![image](https://github.com/user-attachments/assets/56e8391f-f20b-45e7-9bfa-5a668beb6b1d)

#### Duvidas sobre como adicionar chave ssh no git ?. Acesse: https://docs.github.com/pt/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

## Após adicionar a nova chave ssh no git, execute o comando abaixo no WSL para testar a conexão:

```bash
ssh -T git@github.com
```

### Resultado esperado

![image](https://github.com/user-attachments/assets/209a9d8d-964a-4a03-96f3-222edfd08559)

## Caso aconteça o erro de permissão na porta 22, Execute:

```bash
cat << EOF > ~/.ssh/config
Host github.com
  Hostname ssh.github.com
  Port 443
  User git
EOF
```

## Teste novamente:

```bash
ssh -T git@github.com
```

## Com tudo pronto, vamos instalar a JDK 17. Execute os comandos abaixo:

```bash
sudo apt-get update
```

```bash
sudo apt-get install openjdk-17-jdk-headless
```


# Preparando projeto
## Acesse https://github.com/idempiere/idempiere e faça um fork do projeto do idempiere
![image](https://github.com/user-attachments/assets/8506be99-2b96-4dca-be41-6911bd54f8be)

## Após realizar o fork do projeto iDempiere para o seu repositório pessoal, acesse-o através da seção de repositórios no seu perfil e abra-o.

### Vá até a opção 'Code', selecione a aba 'SSH' e, em seguida, copie o link exibido.

![image](https://github.com/user-attachments/assets/800141ff-fd98-459b-9a55-0810f19ea557)

## Após copiar o link, volte ao terminal do WSL e execute os comandos para clonar o projeto.

### Exemplo 

![image](https://github.com/user-attachments/assets/7ad4f8ed-6808-43e0-bfa4-d3ab85c77753)

```bash
git clone cole_o_link
```

# Instalações necessárias para trabalhar com Idempiere:

## Inatalação do postgres 15
### Execute os comandos abaixo:
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

### Abra o arquivo: /etc/postgresql/15/main/pg_hba.conf com nano
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

### Após alterar a linha, pressione Ctrl + X (segure a tecla Ctrl e, sem soltá-la, pressione a tecla X) para iniciar o processo de saída do editor Nano.
Em seguida, tecle Y para confirmar que deseja salvar as alterações feitas no arquivo.
Por fim, pressione Enter para confirmar o nome do arquivo e concluir a gravação.
Dessa forma, você sairá do Nano e o arquivo será salvo com as modificações.

### Iniciando o serviço do postgres 
```bash
sudo service postgresql reload
```
#### Criando usuário postgres
```bash
sudo su - postgres
```
```bash
psql -U postgres -c "CREATE ROLE adempiere SUPERUSER LOGIN PASSWORD 'adempiere'"
```
```bash
exit
```

## Instalação do maven, execute:
```bash
wget https://dlcdn.apache.org/maven/maven-3/3.9.10/binaries/apache-maven-3.9.10-bin.tar.gz -O maven.tar.gz
```
```bash
tar -xvzf maven.tar.gz
```
```bash
sudo mv apache-maven-3.9.10 /opt/maven
```

### Abra o arquivo bashrc no nano
```bash
nano ~/.bashrc
```
##### Adicione as linhas no final do arquivo
```
export M2_HOME=/opt/maven
export PATH=$M2_HOME/bin:$PATH
```
### Após adicionar as linhas, pressione Ctrl + X (segure a tecla Ctrl e, sem soltá-la, pressione a tecla X) para iniciar o processo de saída do editor Nano.
Em seguida, tecle Y para confirmar que deseja salvar as alterações feitas no arquivo.
Por fim, pressione Enter para confirmar o nome do arquivo e concluir a gravação.
Dessa forma, você sairá do Nano e o arquivo será salvo com as modificações.

### Após adicionarmos as linhas ao arquivo, é necessário que o sistema recarregue as configurações. Para isso, execute o seguinte comando:
```bash
source ~/.bashrc
```

## Instalação da IDE Eclipse
```bash
wget https://www.eclipse.org/downloads/download.php?file=/technology/epp/downloads/release/2023-03/R/eclipse-jee-2023-03-R-linux-gtk-x86_64.tar.gz -O eclipse-jee.tar.gz
```
```bash
tar -xvzf eclipse-jee.tar.gz
```
```bash
sudo apt-get install -y libswt-gtk-4-java
```

# Agora, vamos executar o processo de build do projeto iDempiere. Siga os comandos abaixo:

## Entre no diretório do idempiere
```bash
cd idempiere
```
### Altere a branch para master
```bash
git checkout master
```
### Execute para preparar o projeto:

```bash
git remote add upstream git@github.com:idempiere/idempiere.git
```
```bash
git fetch upstream
```
```bash
git checkout -b release-12 upstream/release-12
```
```bash
mvn verify
```

# NÃO ESQUECA DE CRIAR UMA BRANCH APÓS ISSO PARA TRABALHAR COM SEU PROJETO A PARTIR DA BRANCH RELEASE-12

## Inicio Criar banco de dados inicial
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
### Para atualizar o banco de dados 
```bash
cd
```
```bash
cd idempiere
```
```bash
bash RUN_SyncDBDev.sh
```

## Abrindo Eclipse
```bash
cd
```
```bash
eclipse/eclipse
```
### Irá abrir o dialog de inicio do eclipse, clique em Browse...

![image](https://github.com/user-attachments/assets/cb1dae8b-de62-4f05-b34d-a6cbdb1cdb76)

### Encrontre a pasta do projeto idempiere, selecione e clique em open

![image](https://github.com/user-attachments/assets/9ab3f5db-f604-40e8-8c4d-ab18e4610c2e)

## Vá em project, desmarque o bulld automatico

![image](https://github.com/user-attachments/assets/fe76c01b-9d6b-4a61-b590-014c5f18078b)

## Vá em file, após em import

![image](https://github.com/user-attachments/assets/999174cb-86da-4c1e-ad03-3234ffb2d5d9)

## Vá em maven > Clique em existing maven files

![image](https://github.com/user-attachments/assets/6271e1a5-8a95-4741-a06f-9da888a3ccaa)

## Vá em Browse ... encontre o local que o idempiere foi clonado e clique em finsh

![image](https://github.com/user-attachments/assets/100ce322-6e0c-446e-8f4b-eb84a8250d0b)

## Encontre o arquivo org.idempiere.p2.targetplatform.target e abra

![image](https://github.com/user-attachments/assets/224e6490-e372-4f25-bb07-58274aecc001)

## Clique na opção

![image](https://github.com/user-attachments/assets/7c130cd2-032a-4dde-8dff-c83a3163ebc1)

## Após a barra de carregamento na direita completar, vá em project e marque o build novamente

## Agora vamos iniciar nosso projeto com um plugin separado

## Vá em Debug configurations 

![image](https://github.com/user-attachments/assets/0edd1f8e-eec4-4415-9c58-265d95f15599)

## Encontre dentro de eclipse application a opção install.app, após clique em debug

![image](https://github.com/user-attachments/assets/64250a00-c8b2-4bc0-a79c-fcae2f0c1d66)

## Preencha a janela aberta e clique em save

![image](https://github.com/user-attachments/assets/990b8715-f891-48e0-b33d-da3ace962ecc)

### Deverá ter essa saida no console

![image](https://github.com/user-attachments/assets/7021699f-785c-44f8-a2e8-9afb0c91b51e)

## Vá em Debug configurations novamente

![image](https://github.com/user-attachments/assets/0edd1f8e-eec4-4415-9c58-265d95f15599)

## Encontre dentro de eclipse application a opção server.product após clique em debug

### Deverá ter essa saida no console

![image](https://github.com/user-attachments/assets/21d72fcd-6145-4527-93bb-bc8ea72bad51)


## Agora acesse http://localhost:8080

## User SuperUser
## Password System


### Se necessário apagar o banco de dados, execute, após para criar um banco novamente execute o passo de criação do banco incial

```bash
psql -U adempiere -d postgres -c "drop database idempiere;"
```


