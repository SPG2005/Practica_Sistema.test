# Práctica DNS Maestro-Esclavo

# Configuración del Proyecto
Practica de despliegue sobre un servidor dns

1. Creamos la estructura de las maquinas en el archivo de configuración de Vagrantfile
(En el archivo vagrantfile está)


2. Sacamos los archivos necesarios de la maquina tierra a vagrant y creamos una carpeta file con los archivos
(En la carpeta files se encuentran todos)

3. Configuramos los archivos que hemos sacado a vagrant anteriormente

  Añadimos la configuración necesaria a el archivo vagrantfile para que los archivos que hemos modificado se copien tanto en la maquina  maestro como en la maquina esclava
  


  Añadimos en el archivo named la configuración para que se escuche por ipv4



  Añadimos las acl confiables y le decimos que permitimos  la transferencia de archivos de esas acl y además le decimos que no permitimos ipv6



  Añadimos las zonas tanto de MASTER como de ESCLAVO



  Configuramos las zonas tanto la directa como la inversa del servidor


