# Configuración de SSL en Apache2

## Crear un certificado SSL e instalarlo en Apache2

1. Instalar OpenSSL:

   ```bash
   apt install openssl
   ```

2. Generar una clave privada:

   ```bash
   openssl genrsa -out server.key 1024
   ```

3. Generar una solicitud de firma de certificado (CSR):

   ```bash
   openssl req -new -key server.key -out server.csr
   ```

4. Crear un certificado autofirmado válido por 365 días:

   ```bash
   openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt
   ```

5. Copiar los archivos del certificado y la clave a la ubicación correcta:

   ```bash
   cp server.crt /etc/ssl/certs/
   cp server.key /etc/ssl/certs/
   ```

6. Verificar el estado de Apache2:

   ```bash
   systemctl status apache2
   ```

7. Habilitar el módulo SSL en Apache2:

   ```bash
   a2enmod ssl
   ```

8. Reiniciar Apache2:

   ```bash
   systemctl restart apache2
   ```

---

## Configurar un VirtualHost con SSL

1. Navegar al directorio de configuraciones de sitios de Apache2:

   ```bash
   cd /etc/apache2/sites-available
   ```

2. Crear o editar un archivo de configuración para el VirtualHost:

   ```bash
   nano prueba1.com.conf
   ```

3. Ejemplo de configuración del VirtualHost:

   ```apache
   <VirtualHost *:443>
       ServerName prueba1.com
       ServerAlias www.prueba1.com
       ServerAdmin webmaster@prueba1.com
       DocumentRoot /var/www/html/prueba1.com/public

       SSLEngine on

       SSLCertificateKeyFile /etc/ssl/certs/server.key
       SSLCertificateFile /etc/ssl/certs/server.crt

       <Directory /var/www/html/prueba1.com/public>
           Options -Indexes +FollowSymLinks
           AllowOverride All
       </Directory>

       ErrorLog ${APACHE_LOG_DIR}/prueba1.com-error.log
       CustomLog ${APACHE_LOG_DIR}/prueba1.com-access.log combined
   </VirtualHost>
   ```

4. Habilitar la configuración del sitio:

   ```bash
   a2ensite prueba1.com.conf
   ```

5. Reiniciar Apache2:

   ```bash
   systemctl restart apache2
   ```

6. Verificar el estado de Apache2 para confirmar que todo está funcionando correctamente:

   ```bash
   systemctl status apache2
   ```

---
