
# 📦 Nextcloud no Raspberry Pi com Docker, HD Externo e Acesso Remoto via Ngrok

## ✅ Visão Geral

Este guia cobre a instalação e configuração do Nextcloud com Docker, usando:
- Banco de dados MariaDB
- HD externo USB como armazenamento de dados
- Acesso remoto via domínio fixo gratuito do **Ngrok**
- Inicialização automática com o Raspberry Pi

---

## 🔧 1. Instalação via Docker

Crie um arquivo `docker-compose.yml`:

```yaml
version: '3'

services:
  db:
    image: mariadb
    restart: always
    command: --transaction-isolation=READ-COMMITTED --binlog-format=ROW
    volumes:
      - db:/var/lib/mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_PASSWORD: userpass
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud

  app:
    image: nextcloud
    ports:
      - 8080:80
    links:
      - db
    volumes:
      - /mnt/nextcloud-data/nextcloud:/var/www/html
    restart: always
    environment:
      MYSQL_PASSWORD: userpass
      MYSQL_DATABASE: nextcloud
      MYSQL_USER: nextcloud
      MYSQL_HOST: db

volumes:
  db:
```

---

## 💽 2. Montar HD Externo

1. Monte o HD externo:
   ```bash
   sudo mkdir -p /mnt/nextcloud-data
   sudo mount /dev/sda1 /mnt/nextcloud-data
   ```

2. Dê as permissões corretas:
   ```bash
   sudo chown -R 33:33 /mnt/nextcloud-data
   ```

---

## 🚀 3. Subir os containers

```bash
docker-compose up -d
```

Acesse o Nextcloud via: `http://<ip_local>:8080`

---

## 🌍 4. Expor na Internet com domínio fixo Ngrok

### 4.1 Instalar Ngrok

```bash
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-stable-linux-arm.zip
unzip ngrok-stable-linux-arm.zip
sudo mv ngrok /usr/local/bin
```

### 4.2 Autenticar com seu token

```bash
ngrok config add-authtoken SEU_TOKEN
```

### 4.3 Criar script de inicialização

```bash
nano ~/start-ngrok.sh
```

Conteúdo do script (ajuste `meuapp.ngrok.io`):

```bash
#!/bin/bash
sleep 10
ngrok http --domain=meuapp.ngrok.io 8080 --log=stdout > /home/pi/ngrok.log &
```

Permissão de execução:

```bash
chmod +x ~/start-ngrok.sh
```

---

## 🔁 5. Executar Ngrok no boot

Adicione ao `crontab`:

```bash
crontab -e
```

Linha no final:

```bash
@reboot /home/pi/start-ngrok.sh
```

---

## 🛡️ 6. Adicionar domínio Ngrok ao Nextcloud

Dentro do container Nextcloud:

```bash
docker exec -it <nome_do_container> bash
php occ config:system:set trusted_domains 2 --value=meuapp.ngrok.io
exit
```

---

## ✅ Pronto!

Você agora tem:
- Nextcloud funcionando com HD externo
- Domínio fixo exposto via Ngrok
- Inicialização automática ao ligar o Raspberry Pi
