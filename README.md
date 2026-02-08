# 🕵️‍♂️ ARP MITM Tool – Documentación Técnica

---

## 🎯 Objetivo del Script

Este script realiza un ataque **Man-in-the-Middle (MITM)** mediante **envenenamiento ARP** en una red local, permitiendo la interceptación del tráfico entre un **host objetivo** y el **gateway**.

El desarrollo y uso de esta herramienta está destinado **exclusivamente a fines educativos**, prácticas de laboratorio y entornos controlados donde exista **autorización explícita**.

---

## ▶️ 1. Ejecución del Script
<img width="1165" height="442" alt="image" src="https://github.com/user-attachments/assets/a5e9027b-6787-49c6-ae88-cefd52651673" />


---

## 🧾 2. Tablas ARP (Antes / Después)

### 🔹 Antes del Ataque
La tabla ARP muestra las direcciones MAC legítimas asociadas a cada dirección IP antes de iniciar el ataque.

<img width="757" height="410" alt="image" src="https://github.com/user-attachments/assets/3bf7c005-e886-48d9-b337-927dddc85daf" />


---

### 🔹 Después del Ataque
Luego de ejecutar el ataque MITM, se observa que la dirección MAC del atacante aparece asociada tanto al gateway como a la víctima, confirmando el **ARP Poisoning**.

<img width="756" height="405" alt="image" src="https://github.com/user-attachments/assets/b07c81d8-7f82-4089-aa37-346ea875757c" />


---

## 🌐 3. Topología de Red

La siguiente topología representa el entorno de laboratorio utilizado para la prueba del ataque ARP MITM.

<img width="751" height="663" alt="image" src="https://github.com/user-attachments/assets/f0694b18-b832-4e67-85f0-1d65b5d1a59e" />


---

## 🧱 Dispositivos y Configuración

### 1️⃣ Router Cisco – **Osvaldo-Solano**

#### Configuración Principal
```cisco
enable
configure terminal
hostname Osvaldo-Solano
no ip domain-lookup
cdp run
```

#### Interfaz Física
```cisco
interface e0/0
 description Trunk hacia Switch
 no shutdown
exit
```

#### Subinterfaz VLAN 61 (LAB)
```cisco
interface e0/0.61
 description VLAN 61 - LAB
 encapsulation dot1Q 61
 ip address 192.168.61.1 255.255.255.0
 cdp enable
exit
```

#### Servicio DHCP para VLAN 61
```cisco
ip dhcp excluded-address 192.168.61.1 192.168.61.30

ip dhcp pool VLAN61-LAB
 network 192.168.61.0 255.255.255.0
 default-router 192.168.61.1
 dns-server 8.8.8.8 1.1.1.1
 domain-name vlan61.lab
 lease 1 0 0
exit
```

#### Guardar Configuración
```cisco
end
write memory
```

---

### 2️⃣ Switch Cisco – **SW-Osvaldo**

#### Configuración Principal
```cisco
enable
configure terminal
hostname SW-Osvaldo
no ip domain-lookup
```

#### Creación de VLAN
```cisco
vlan 61
 name LAB-61
exit
```

#### IP de Administración
```cisco
interface vlan 61
 description IP de administracion del switch
 ip address 192.168.61.2 255.255.255.0
 no shutdown
exit

ip default-gateway 192.168.61.1
```

#### CDP
```cisco
cdp run
```

#### Puerto Trunk hacia Router
```cisco
interface gi0/0
 description Trunk hacia Router Osvaldo-Solano
 switchport mode trunk
 switchport trunk allowed vlan 61
 cdp enable
 no shutdown
exit
```

#### Puertos Access para Hosts
```cisco
interface range gi0/1 - 3
 description PCs / Kali Linux
 switchport mode access
 switchport access vlan 61
 spanning-tree portfast
 cdp enable
 no shutdown
exit
```

#### Guardar Configuración
```cisco
end
write memory
```

---

## 🖥️ Hosts en VLAN 61 (LAB)

| Dispositivo      | Puerto Switch | Configuración IP                | Rol |
|------------------|---------------|---------------------------------|-----|
| Router           | Gi0/0 (Trunk) | 192.168.61.1/24                 | Gateway / DHCP |
| Switch           | VLAN 61       | 192.168.61.2/24                 | Administración |
| Host Windows     | Gi0/1 (Access)| DHCP / IP estática VLAN 61      | Víctima |
| Host Kali Linux  | Gi0/2 (Access)| DHCP / IP estática VLAN 61      | Atacante |
| Host adicional   | Gi0/3 (Access)| DHCP / IP estática VLAN 61      | Monitoreo |

---

## 📌 Direccionamiento IP (Resumen)

- **Red:** 192.168.61.0/24  
- **Gateway:** 192.168.61.1  
- **IP de administración del switch:** 192.168.61.2  
- **Rango DHCP:** 192.168.61.31 – 192.168.61.254  
- **IPs reservadas:** 192.168.61.1 – 192.168.61.30  
- **DNS:** 8.8.8.8 / 1.1.1.1  
- **Dominio:** vlan61.lab  

---

## 🛡️ Medidas de Mitigación

Para prevenir ataques ARP MITM en redes reales se recomiendan las siguientes medidas:

1. Configurar **tablas ARP estáticas** en equipos críticos.
2. Implementar **segmentación de red** mediante VLANs.
3. Usar **IDS/IPS** para detectar tráfico ARP anómalo.
4. Utilizar **protocolos cifrados** (HTTPS, SSH, VPN).
5. Habilitar **Port Security** en switches.
6. Implementar **Dynamic ARP Inspection (DAI)** en switches gestionados.

---

## 🎥 Video Demostración

**Enlace al video:**  
https://youtu.be/9PApsuswCBE?si=0rRDHuwk0wDxuJiE

El video incluye:
1. Visualización de la topología con nombre y matrícula.
2. Fecha y hora actual del sistema.
3. Rostro y voz del autor.
4. Demostración completa del funcionamiento del script.

---

⚠️ **Aviso Legal**  
Este proyecto fue desarrollado únicamente con fines educativos y académicos. El autor no se responsabiliza por el uso indebido de la información fuera de entornos autorizados.
