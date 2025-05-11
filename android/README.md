Instale o Termux:
link: https://f-droid.org/pt_BR/packages/com.termux/

Comandos para rodar no termux:

pkg update
pkg install php-apache nano wget

nano $PREFIX/etc/apache2/httpd.conf

descomente o mpm_prefork_module e comente mpm_worker_module

nova linha abaixo dessa

LoadModule php_module /data/data/com.termux/files/usr/libexec/apache2/libphp.so
<FilesMatch \\.php$>
    SetHandler application/x-httpd-php
<FilesMatch>

Salvar o arquivo no nano

Iniciar servidor web
'apachectl start'

Configuração do NextCloud

cd $PREFIX/share/apache2/default-site/htdocs/

rm -rf * (cuidado, com esse comando queremos deletar os arquivos dentro da pasta que acabamos de entrar, certifique que está na pasta correta, normalmente vem com um index.html e um manual)

wget https://download.nextcloud.com/server/installer/setup-nextcloud.php

trocar 'nextcloud' por '.' (opcional)

