# Configuração para deploy

## Primeiros passos

- Prepare o local_settings.py
- Crie o seu servidor Ubuntu 20.04 LTS (onde preferir)

Comando para gerar SECRET_KEY

```
python -c "import string as s;from secrets import SystemRandom as SR;print(''.join(SR().choices(s.ascii_letters + s.digits + s.punctuation, k=64)));"
```

## Criando sua chave SSH

```
ssh-keygen -C 'COMENTÁRIO'
```

## No servidor

### Conectando

```
ssh usuário@IP_SERVIDOR
```

### Comandos iniciais

```
sudo apt update -y
sudo apt upgrade -y
sudo apt autoremove -y
sudo apt install build-essential -y

sudo add-apt-repository ppa:deadsnakes/ppa
sudo apt install python3.11 python3.11-venv -y

sudo apt install nginx -y
sudo apt install certbot python3-certbot-nginx -y
sudo apt install postgresql postgresql-contrib -y
sudo apt install libpq-dev -y
sudo apt install git -y
```

### Configurando o git

```
git config --global user.name 'Seu nome'
git config --global user.email 'seu_email@gmail.com'
git config --global init.defaultBranch main
```

Criando as pastas do projeto e repositório

```
mkdir ~/agendarepo ~/agendaapp
```

Configurando os repositórios

```
cd ~/agendarepo
git init --bare
cd ..
cd ~/agendaapp
git init
git remote add agendarepo ~/agendarepo
git add .
git commit -m 'Initial'
git push agendarepo main -u # erro
```

No seu computador local

```
git remote add agendarepo usuario@IP_SERVIDOR:~/agendarepo
git push agendarepo main
```

No servidor

```
cd ~/agendaapp
git pull agendarepo main
```

## Configurando o Postgresql

```
sudo -u postgres psql

postgres=# create role meu_usuario with login superuser createdb createrole password 'senha_do_usuario';
CREATE ROLE
postgres=# create database base_de_dados with owner meu_usuario;
CREATE DATABASE
postgres=# grant all privileges on database base_de_dados to meu_usuario;
GRANT
postgres=# \q

sudo systemctl restart postgresql
```

## Criando o local_settings.py no servidor

```
nano ~/agendaapp/project/local_settings.py
```

Cole os dados.

## Configurando o Django no servidor

```
cd ~/agendaapp
python3.11 -m venv venv
. venv/bin/activate
pip install --upgrade pip
pip install django
pip install pillow
pip install gunicorn
pip install psycopg
pip install faker

python manage.py runserver
python manage.py migrate
python manage.py collectstatic
python manage.py createsuperuser
```

## Permitir arquivos maiores no nginx

```
sudo nano /etc/nginx/nginx.conf
```

Adicione em http {}:

```
client_max_body_size 30M;
```

```
sudo systemctl restart nginx
```
