# Práctica DNS Maestro-Esclavo

# Configuración del Proyecto
//Antes de nada, todo está con los nombres default para la explicación nada más, luego en la práctica
va cambiando los nombres, lo he puesto en default como vienen en el pdf para que no haya confusiones y 
se entienda mejor.

1. Preparación del Entorno
Antes de comenzar, asegúrate de eliminar cualquier entrada previa en /etc/hosts de tus máquinas para evitar interferencias. Luego, instala los paquetes necesarios de BIND9 en las máquinas virtuales:

`sudo apt-get update`
`sudo apt-get install bind9 bind9utils bind9-doc`

2. Configuración del Servidor Maestro
2.1 Configuración de named.conf.options
Edita el archivo /etc/bind/named.conf.options:

`acl confiables {`
  `192.168.57.0/24;`
`};`

`options {`
  `directory "/var/cache/bind";`
  `allow-transfer { none; };`
  `listen-on port 53 { 192.168.57.101; };`
  `recursion yes;`
  `allow-recursion { confiables; };`
  `dnssec-validation yes;`
`};`

2.2 Configuración de named.conf.local
Edita el archivo /etc/bind/named.conf.local para declarar la zona sistema.test:

`zone "sistema.test" {`
  `type master;`
  `file "/var/lib/bind/sistema.test.dns";`
`};`

`zone "57.168.192.in-addr.arpa" {`
  `type master;`
  `file "/var/lib/bind/sistema.test.rev";`
`};`

2.3 Creación de Archivos de Zona
Crea el archivo de zona directa /var/lib/bind/sistema.test.dns:

$TTL 86400
@   IN  SOA ns1.sistema.test. admin.sistema.test. (
        1         ; Serial
        3600      ; Refresh
        1800      ; Retry
        604800    ; Expire
        7200 )   ; Negative Cache TTL
;
@       IN  NS    ns1.sistema.test.
@       IN  NS    ns2.sistema.test.
ns1     IN  A     192.168.57.101
ns2     IN  A     192.168.57.102
mail    IN  A     192.168.57.104

Y el archivo de zona inversa /var/lib/bind/sistema.test.rev:


$TTL 86400
@   IN  SOA ns1.sistema.test. admin.sistema.test. (
        1         ; Serial
        3600      ; Refresh
        1800      ; Retry
        604800    ; Expire
        7200 )   ; Negative Cache TTL
;
@       IN  NS    ns1.sistema.test.
101     IN  PTR   ns1.sistema.test.
102     IN  PTR   ns2.sistema.test.
104     IN  PTR   mail.sistema.test.

3. Configuración del Servidor Esclavo
3.1 Configuración de named.conf.local
Edita el archivo /etc/bind/named.conf.local en el servidor esclavo:

`zone "sistema.test" {`
  `type slave;`
  `masters { 192.168.57.101; };`
  `file "/var/cache/bind/sistema.test.dns";`
`};`

`zone "57.168.192.in-addr.arpa" {`
  `type slave;`
  `masters { 192.168.57.101; };`
  `file "/var/cache/bind/sistema.test.rev";`
};`

4. Verificación de la Configuración
Reinicia BIND en ambos servidores y verifica las configuraciones:

`sudo systemctl restart bind9`
`sudo systemctl status bind9`

Comprueba las zonas:

`named-checkzone sistema.test /var/lib/bind/sistema.test.dns`
`named-checkzone 57.168.192.in-addr.arpa /var/lib/bind/sistema.test.rev`

5. Comprobación de la Transferencia de Zonas
Comprueba que se ha realizado correctamente la transferencia de la zona entre el servidor maestro y el esclavo utilizando dig o nslookup:

`dig @192.168.57.101 sistema.test AXFR`

6. Verificación con dig y nslookup
Desde los clientes, realiza las siguientes comprobaciones:

`dig @192.168.57.101 ns1.sistema.test`
`nslookup ns1.sistema.test 192.168.57.101`

Resultados Esperados:

La configuración debería permitir la resolución de nombres y direcciones IP.
La transferencia de zona debería ser exitosa entre el servidor maestro y el esclavo.
Ambos servidores deberían responder correctamente a consultas.
