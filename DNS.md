# Instalación de Bind9 y editor Nano

```bash
sudo apt install bind9 bind9-utils nano
```

## 3. Comprobación de Bind9

Verificamos que Bind9 esté funcionando (errores son normales en este punto):

```bash
systemctl status bind9
```

## 4. Permitir acceso en el firewall

```bash
sudo ufw allow bind9
```

## 5. Configuración inicial de Bind9

Editamos el archivo `/etc/bind/named.conf.options`:

```bash
sudo nano /etc/bind/named.conf.options
```

Incluimos las siguientes líneas:

```plaintext
listen-on { any; };
allow-query { localhost; 10.10.20.0/24; };
forwarders {
  8.8.8.8;
  8.8.4.4;
};
dnssec-validation no;
```

## 6. Uso exclusivo de IPv4

Editamos `/etc/default/named`:

```bash
sudo nano /etc/default/named
```

Modificamos:

```plaintext
OPTIONS="-u bind -4"
```

## 7. Verificación y reinicio de Bind9

```bash
sudo named-checkconf
sudo systemctl restart bind9
systemctl status bind9
```

## 8. Configuración de zonas

Editamos `/etc/bind/named.conf.local`:

```bash
sudo nano /etc/bind/named.conf.local
```

Añadimos:

```plaintext
zone "networld.cu" IN {
 type master;
 file "/etc/bind/zonas/db.networld.cu";
};

zone "20.10.10.in-addr.arpa" {
 type master;
 file "/etc/bind/zonas/db.10.10.20";
};
```

## 9. Creación de archivos de zona

Creamos el directorio para los archivos:

```bash
sudo mkdir /etc/bind/zonas
```

### Archivo directo `/etc/bind/zonas/db.networld.cu`

```bash
sudo nano /etc/bind/zonas/db.networld.cu
```

Contenido:

```plaintext
$TTL 1D
@ IN SOA ns1.networld.cu. admin.networld.cu. (
  1 ; Serial
  12h ; Refresh
  15m ; Retry
  3w ; Expire
  2h ) ; Negative Cache TTL
; Registros NS
 IN NS ns1.networld.cu.
ns1 IN A 10.10.20.13
www IN A 10.10.20.13
```

### Archivo inverso `/etc/bind/zonas/db.10.10.20`

```bash
sudo nano /etc/bind/zonas/db.10.10.20
```

Contenido:

```plaintext
$TTL 1d ;
@ IN SOA ns1.networld.cu admin.networld.cu. (
  20210222 ; Serial
  12h ; Refresh
  15m ; Retry
  3w ; Expire
  2h ) ; Negative Cache TTL
;
@ IN NS ns1.networld.cu.
1 IN PTR www.networld.cu.
```

## 10. Verificación de los archivos de zona

```bash
sudo named-checkzone networld.cu /etc/bind/zonas/db.networld.cu
sudo named-checkzone 20.10.10.in-addr.arpa /etc/bind/zonas/db.10.10.20
```

Ambas verificaciones deben devolver OK.

## 11. Reinicio de Bind9

```bash
sudo systemctl restart bind9
```

## 12. Pruebas de funcionamiento

Desde otra PC, realiza un ping al dominio configurado:

```bash
ping www.networld.cu
