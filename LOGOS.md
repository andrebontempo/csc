//cópia dos arquivos para dentro do container CSC
docker cp ./pics/. csc:/var/www/glpi/public/pics/logos/

//garante que o www-data é o dono
docker exec -u root csc chown -R www-data:www-data /var/www/glpi/public/pics/logos/
docker exec -u root csc chmod -R 755 /var/www/glpi/public/pics/logos/

//para atualizar os logos no glpi, o usuário deve fazer login e em "Administração" -> "Configurações" -> "Geral" clicar em "ApagarCache"
