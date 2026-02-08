ARP MITM Tool - Documentación Técnica
Nombre: Osvaldo Alejandro Solano Gonzalez
Matrícula: 2024-2361
🎯 Objetivo del Script
Este script realiza un ataque Man-in-the-Middle (MITM) mediante envenenamiento ARP en una red local, permitiendo la interceptación del tráfico entre un host objetivo y el gateway. Desarrollado exclusivamente para fines educativos y de prueba en entornos controlados donde se tenga autorización explícita.
________________________________________
1. Ejecución del Script
 <img width="1165" height="442" alt="image" src="https://github.com/user-attachments/assets/98ea2cda-9144-4467-8728-db93ae07829d" />

2. Tablas ARP antes/durante/después
Antes
 <img width="757" height="410" alt="image" src="https://github.com/user-attachments/assets/f310b9e6-5bcd-4310-b6ee-4dd6421fd128" />
Despues
 <img width="756" height="405" alt="image" src="https://github.com/user-attachments/assets/e1813755-586a-4f81-b604-c8833bf665ab" />
________________________________________

3. Topología de Red
<img width="751" height="663" alt="image" src="https://github.com/user-attachments/assets/931d7eb1-f932-4ac3-ace9-d671333dfd5e" />
Dispositivos y Configuración:
1. Router (Cisco - Osvaldo-Solano)
Configuración Principal:
cisco
enable
configure terminal
hostname Osvaldo-Solano
no ip domain-lookup
cdp run
Interfaz Física:
cisco
interface e0/0
 description Trunk hacia Switch
 no shutdown
exit
Subinterfaz VLAN 61 (LAB):
cisco
interface e0/0.61
 description VLAN 61 - LAB
 encapsulation dot1Q 61
 ip address 192.168.61.1 255.255.255.0
 cdp enable
exit
Servicio DHCP para VLAN 61:
cisco
ip dhcp excluded-address 192.168.61.1 192.168.61.30

ip dhcp pool VLAN61-LAB
 network 192.168.61.0 255.255.255.0
 default-router 192.168.61.1
 dns-server 8.8.8.8 1.1.1.1
 domain-name vlan61.lab
 lease 1 0 0
exit
Guardar Configuración:
cisco
end
write memory
2. Switch (Cisco - SW-Osvaldo)
Configuración Principal:
cisco
enable
configure terminal
hostname SW-Osvaldo
no ip domain-lookup
Creación de VLAN:
cisco
vlan 61
 name LAB-61
exit
IP de Administración:
cisco
interface vlan 61
 description IP de administracion del switch
 ip address 192.168.61.2 255.255.255.0
 no shutdown
exit
ip default-gateway 192.168.61.1
CDP:
cisco
cdp run
Puerto Trunk hacia Router:
cisco
interface gi0/0
 description Trunk hacia Router Osvaldo-Solano
 switchport mode trunk
 switchport trunk allowed vlan 61
 cdp enable
 no shutdown
exit
Puertos Access para Hosts:
cisco
interface range gi0/1 - 3
 description PCs / Kali Linux
 switchport mode access
 switchport access vlan 61
 spanning-tree portfast
 cdp enable
 no shutdown
exit
Guardar Configuración:
cisco
end
write memory
3. Hosts en VLAN 61 (LAB)
Dispositivo	Conectado al Switch	Configuración IP	Notas
Router	Puerto Gi0/0 (Trunk)	192.168.61.1/24	Gateway, DHCP Server
Switch	N/A (IP Management)	192.168.61.2/24	Dispositivo gestionable
Host Windows	Puerto Gi0/1 (Access)	DHCP o estática en red 192.168.61.0/24	Máquina víctima potencial
Host Linux (Kali)	Puerto Gi0/2 (Access)	DHCP o estática en red 192.168.61.0/24	Máquina atacante
Host Adicional/Net	Puerto Gi0/3 (Access)	DHCP o estática en red 192.168.61.0/24	Máquina de monitorización o segunda víctima
Direccionamiento IP (Resumen):
•	Red: 192.168.61.0/24
•	Gateway: 192.168.61.1
•	Switch Management IP: 192.168.61.2
•	Rango DHCP: 192.168.61.31 - 192.168.61.254
•	Rango Excluido (reservado): 192.168.61.1 - 192.168.61.30
•	DNS: 8.8.8.8, 1.1.1.1
•	Dominio: vlan61.lab
________________________________________
🛡️ Medidas de Mitigación
Para evitar este tipo de ataques en redes reales:
1.	ARP Static Tables: Configurar entradas ARP estáticas en equipos críticos.
2.	Network Segmentation: Usar VLANs para aislar tráfico sensible.
3.	Monitoring: Implementar detección de tráfico ARP irregular (IDS/IPS).
4.	Encryption: Usar protocolos cifrados (HTTPS, SSH, VPN) para que el tráfico interceptado no sea legible.
5.	Port Security: En switches, habilitar seguridad de puertos para limitar direcciones MAC por puerto.
6.	Dynamic ARP Inspection (DAI): En switches gestionados, habilitar DAI para validar paquetes ARP.
________________________________________
🎥 Video Demostración
<!-- Agrega aquí el enlace al video de máximo 8 minutos -->
Enlace al Video: [INSERTAR_ENLACE_AL_VIDEO_AQUÍ]
El video incluye:
1.	Mostrar topología con nombre y matrícula.
2.	Mostrar hora y fecha actual.
3.	Rostro y voz del autor.
4.	Demostración del funcionamiento correcto del script.
________________________________________
⚠️ Advertencia Legal
Este script es solo para EDUCACIÓN y PRUEBAS EN ENTORNOS CONTROLADOS CON AUTORIZACIÓN. El uso malintencionado de esta herramienta en redes no autorizadas es ilegal y puede acarrear consecuencias penales. El autor no se hace responsable del uso indebido.
________________________________________
Script desarrollado por: Osvaldo Alejandro Solano Gonzalez
