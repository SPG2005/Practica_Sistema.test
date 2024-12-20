# Práctica DNS Maestro-Esclavo

# Configuración del Proyecto
Configuración de Bind9
La configuración de Bind9 se realiza en ambos servidores. Asegúrate de configurar named.conf.options, named.conf.local y los archivos de zona en ambos.

Maestro (tierra.sistema.test)
Configurar la Zona Directa e Inversa Agregar en named.conf.local:


```
zone "sistema.test" {
    type master;
    file "/etc/bind/db.sistema.test";
    allow-transfer { 192.168.57.102; };  # IP del esclavo
};

zone "57.168.192.in-addr.arpa" {
    type master;
    file "/etc/bind/db.57.168.192";
    allow-transfer { 192.168.57.102; };
};
```
Configurar Opciones Globales En named.conf.options, configurar:

```
options {
    listen-on { 192.168.57.101; };
    allow-query { 192.168.57.0/24; 127.0.0.1; };
    dnssec-validation yes;
    forwarders { 208.67.222.222; };
};
Esclavo (venus.sistema.test)
Configurar la Zona como Esclava En named.conf.local:
```
```
zone "sistema.test" {
    type slave;
    masters { 192.168.57.101; };  # IP del maestro
    file "/var/cache/bind/db.sistema.test";
};

zone "57.168.192.in-addr.arpa" {
    type slave;
    masters { 192.168.57.101; };
    file "/var/cache/bind/db.57.168.192";
};
```
Comprobaciones
Verificar Resolución de los Registros A

```
dig @192.168.57.101 tierra.sistema.test  # En el maestro
dig @192.168.57.102 tierra.sistema.test  # En el esclavo
```

```
dig @192.168.57.101 -x 192.168.57.101
dig @192.168.57.102 -x 192.168.57.101
```

```
dig @192.168.57.101 ns1.sistema.test
dig @192.168.57.102 ns2.sistema.test
```

```
dig @192.168.57.101 sistema.test NS
dig @192.168.57.102 sistema.test NS
```

```
dig @192.168.57.101 sistema.test MX
```

Verificar Transferencia de Zona Para verificar la transferencia de zona desde el maestro al esclavo:

```
dig @192.168.57.102 sistema.test AXFR
```

#Estructura del Proyecto
  -Vagrantfile: Define y configura las máquinas virtuales.
  -db.sistema.test y db.57.168.192: Archivos de zona para la configuración de DNS.
  -README.md: Explicación de la práctica.
  -LICENSE: Licencia del proyecto.

#Licencia
Este proyecto está licenciado bajo la licencia de tu elección (indicar en LICENSE).

#Notas Adicionales
Es recomendable reiniciar Bind9 en ambas máquinas tras cada cambio:

```
sudo systemctl restart bind9
```