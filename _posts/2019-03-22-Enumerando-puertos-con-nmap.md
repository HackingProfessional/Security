---
layout: post
title: "Curso de Nmap I"
excerpt: "Escaneando redes con Nmap desde 0"
image: "https://hackingprofessional.github.io/Security/images/CourseNmap.png"
tags: 
  - Curso de Recopilacion de informacion.
  - Auditando redes con Nmap
  - Information Gathering
  - Enumeracion.
---

**Nmap** es la abreviatura de Network Mapper.  
Es una herramienta de seguridad informatica de código abierto para exploración de redes y auditorias de seguridad.
Afirmado por el mismo Gordon Lyon con esta gran herramienta se puede resolver las siguientes problematicas:

  - ¿Qué computadoras se encuentran funcionando en la red local?
  - ¿Qué direcciones IP se encuentran ejecutándose en la red local?
  - ¿Cuál es el sistema operativo de su máquina de destino?
  - ¿Averigue qué puertos están abiertos en la máquina que acabas de escanear?
  - Averigue si el sistema está infectado con malware o virus.
  - Busque servidores o servicios de red no autorizados en su red.
  - Encuentre y elimine equipos que no cumplan con el nivel mínimo de seguridad de la organización.

A continuacion, detallo diferentes ejemplos de comandos para ejecutar con Nmap:

### Escaneo #1
Como escanear un único host o una dirección IP (IPv4)?
```shell
### Escanee una sola dirección IP ### 
nmap 192.168.0.1
 
## Escanear un nombre de host ### 
nmap server1.binarychaos.com
 
## Escanee un nombre de host con más información ### 
nmap  -v server1.binarychaos.com
```  

### Escaneo #2
Como escanear múltiples direcciones IP (IPv4)?
```shell
nmap 192.168.1.1 192.168.1.2 192.168.1.3 
nmap 192.168.1.1,2,3
## También puede escanear un rango de direcciones IP:
nmap 192.168.1.1-20
## Escaneando una subred completa
nmap 192.168.1.0/24
```  

### Escaneo #3
Como escanear hosts o  direcciones IP (IPv4) desde un archivo?
Inicialmente creamos nuestro archivo donde alojaremos los equipos que deseamos escanear:
```shell
cat > /tmp/attack.txt
server1.binarychaos.com
192.168.1.0/24
192.168.1.1/24
10.1.2.3
localhost
```  
Luego con los parametros "-iL" y colocando la ruta del archivo se realiza el escaneo:
```shell
nmap -iL /tmp/attack.txt
```  

### Escaneo #4
Como excluir hosts o direcciones IP (IPv4) de un escaneo?
```shell
nmap 192.168.1.0/24 --excluye 192.168.1.5
nmap 192.168.1.0/24 --excluye 192.168.1.5,192.168.1.254
```  

### Escaneo #5
Como excluir hosts o direcciones IP (IPv4) de un escaneo?
```shell
nmap 192.168.1.0/24 --excluye 192.168.1.5
nmap 192.168.1.0/24 --excluye 192.168.1.5,192.168.1.254
### Excluyendo desde un archivo llamado /tmp/NoScan.txt
nmap -iL /tmp/attack.txt --excludefile /tmp/NoScan.txt
```  

### Escaneo #6
Como saber que sistema operativo esta corriendo un equipo?
```shell
nmap -A 192.168.1.254
nmap -A -iL /tmp/attack.txt 
```  

### Escaneo #7
Como saber si un equipo esta protegido por un firewall?
```shell
nmap -sA 192.168.1.254
nmap -sA server1.binarychaos.com
```  

### Escaneo #8
Como realizar un escaneo sin enviar ping a los equipo?
```shell
nmap -Pn 192.168.1.1
nmap -Pn server1.binarychaos.com
```  

### Escaneo #9
Como realizar un escaneo rapido?
```shell
nmap -F 192.168.1.1
```  

### Escaneo #10
Como escanear un único host o una dirección IP (IPv6)?
```shell
nmap -6 2607:f0d0:1002:51::4
nmap -6 -Pn 2607:f0d0:1002:51::4
nmap -6 server1.binarychaos.com
```  

### Escaneo #11
Como identificar porque un puerto se encuentra en un estado particular?
```shell
nmap --reason 192.168.1.1 
nmap --reason server1.binarychaos.com
```  

### Escaneo #12
Como mostrar solo los puertos abiertos de un equipo?
```shell
nmap --open 192.168.1.1 
nmap --open server1.binarychaos.com
```  

### Escaneo #13
Como mostrar todos los paquetes enviados y recibidos?
```shell
nmap --packet-trace 192.168.1.1
nmap --packet-trace server1.binarychaos.com
```  

### Escaneo #14
Como realizar un escaneo a un puerto especifico?
```shell
nmap -p [puerto] hostName
## Escaneando el puerto 80
nmap -p 80 192.168.1.1
 
## Escaneando el puerto 80 por el protocolo TCP
nmap -p T:80 192.168.1.1
 
## Escaneando el puerto 53 por el protocolo UDP
nmap -p U:53 192.168.1.1
 
## Escaneando 2 puertos
nmap -p 80,443 192.168.1.1
 
## Escaneando un rango de puertos
nmap -p 80-200 192.168.1.1
 
## Combinando todo lo anterior
nmap -p U:53,111,137,T:21-25,80,139,8080 192.168.1.1
nmap -p U:53,111,137,T:21-25,80,139,8080 server1.binarychaos.co
nmap -v -sU -sT -p U:53,111,137,T:21-25,80,139,8080 192.168.1.254
 
## Escaneando todos los puertos con * wildcard ##
nmap -p "*" 192.168.1.1
 
## Escaneando el top de puertos #5,#10 mas conocidos
nmap --top-ports 5 192.168.1.1
nmap --top-ports 10 192.168.1.1
```  

### Escaneo #15
Como controlar la velocidad de un escaneo con Nmap?
```shell
## Escaneo Con velocidad maxima
nmap -T5 192.168.1.0/24
```  

### Escaneo #16
Como habilitar la deteccion de sistema operativo ?
```shell
nmap -O 192.168.1.1
nmap -O  --osscan-guess 192.168.1.1
```  

### Escaneo #17
Como identificar la version de los servicios que esta corriendo un equipo?
```shell
nmap -sV 192.168.1.1
```  

### Escaneo #18
Como Escanear un host usando ping TCP ACK (PA) y TCP Syn (PS)?
_Esto es util si el firewall está bloqueando pings ICMP estándar:_
```shell
nmap -PS 192.168.1.1
nmap -PS 80,21,443 192.168.1.1
nmap -PA 192.168.1.1
nmap -PA 80,21,200-512 192.168.1.1
```

### Escaneo #19
Como Escanear un host usando el protocolo ping?
```shell
nmap -PO 192.168.1.1
```

### Escaneo #20
Como Escanear un host usando el protocolo ping UDP?
```shell
nmap -PU 192.168.1.1
nmap -PU 2000.2001 192.168.1.1
```

### Escaneo #21
Como Escanear los servicios de un equipo por el protocolo UDP?
```shell
nmap -sU binarychaos.co
nmap -sU 192.168.1.1
```

### Escaneo #22
Como detectar problemas de seguridad escaneando un firewall?
_Los siguientes tipos de escaneo explotan un vacío sutil en el protocolo TCP y son buenos para probar la seguridad de los ataques comunes:
_
```shell
## TCP Null Scan para engañar a un firewall y generar una respuesta ##
## No establece ningún bit (el encabezado del indicador TCP es 0) ##
nmap -sN 192.168.1.254
 
## TCP Fin scan para comprobar el firewall ##
## Establece solo el bit TCP FIN  ##
nmap -sF 192.168.1.254
```

### Escaneo #23
Como Escanear un equipo protegido por un firewall?
_La opción -f hace que la exploración solicitada (incluidas las exploraciones de ping) utilizandp pequeños paquetes de IP fragmentados. La idea es dividir el encabezado TCP en varios paquetes para que sea más difícil detectar para los filtros de paquetes, los sistemas de detección de intrusos y otras molestias._
```shell
nmap -f 192.168.1.1
nmap -f fw1.binarychaos.co

## Set your own offset size with the --mtu option ##
nmap --mtu 16 192.168.1.1
```

### Escaneo #24
Como escanear un host y especificar equipos señuelos que aparenten estar realizando un escaneo de parte de ellos?
```shell
nmap -n -D192.168.1.5,10.5.1.2,172.1.2.4,3.4.2.1 binarychaos.co
```

### Escaneo #25
Como escanear un host y reasignarse una direccion MAC?
```shell
### Spoof your MAC address ##
nmap --spoof-mac DIRECCION-MAC 192.168.1.1
 
### Asignando una direccion mac aleatoria ###
### el numero 0, significa que la asignacion de la mac sera aleatoria ###
nmap -v -sT -PN --spoof-mac 0 192.168.1.1
```

### Escaneo 26
Como guardar el resultado de un escaneo en un archivo?
```shell
nmap 192.168.1.1 > output.txt
nmap -oN /path/to/filename 192.168.1.1
nmap -oN output.txt 192.168.1.1
```
