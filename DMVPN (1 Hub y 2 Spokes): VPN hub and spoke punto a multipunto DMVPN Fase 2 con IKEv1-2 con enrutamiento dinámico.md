## Implementación de DMVPN Seguro con IPsec (IKEv1 e IKEv2) 
 

 
## Link https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQBxlcG6BEjQR6cFeSkJSc4CAZyJb_5H0kgr7NiMqJ6yevk?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=a4DHws
 
## 1. Descripción del Proyecto 
Este laboratorio implementa una arquitectura DMVPN (Dynamic Multipoint VPN) para interconectar múltiples sucursales (Spokes) con una sede central (Hub) utilizando túneles GRE multipunto protegidos con IPsec. 
El objetivo es proporcionar comunicación segura entre redes remotas a través de Internet utilizando: 
- NHRP (Next Hop Resolution Protocol) 
- GRE multipoint 
- IPsec 
- IKEv1 e IKEv2 
- EIGRP como protocolo de enrutamiento dinámico 

  
## 2. Topología de Red 
<img width="395" height="346" alt="image" src="https://github.com/user-attachments/assets/7427adb5-572d-4aa6-9ce6-4a8d332ec1dd" />

Diagrama de la red 


## Componentes 
- Dispositivo 	Rol 
- HUB 	Concentrador DMVPN 
- SPOKE1 	Sucursal 1 
- SPOKE2 	Sucursal 2 
- IOU1 	Switch LAN 
- IOU2 	Switch LAN 
- PC1 	Cliente LAN Spoke1 
- PC2 	Cliente LAN Spoke2 
- PC3 	Cliente LAN Hub 
  
## 3. Direccionamiento IP 
### Red WAN 
- Dispositivo Interface 	IP 
- HUB 	G1/0 	10.12.50.98/30 
- SPOKE1 	G1/0 	10.12.50.102/30 
- SPOKE2 	G1/0 	10.12.50.106/30 
  
### Red DMVPN (Tunnel) 
- Router Interface 	IP 
- HUB 	Tunnel0 10.12.50.113/28 
- SPOKE1 Tunnel0 10.12.50.114/28 
-SPOKE2 Tunnel0 10.12.50.115/28 
  
### Redes LAN 
	Red 	Ubicación 
	10.12.50.0/27 LAN SPOKE1 
	10.12.50.32/27 LAN SPOKE2 
	10.12.50.64/27 LAN HUB 
  
## 4. Objetivo de cada VPN 
### VPN DMVPN 
Permite que múltiples sucursales se conecten dinámicamente a un Hub central utilizando: 
•	GRE multipunto 
•	NHRP 
•	IPsec 
Ventajas: 
•	Escalable 
•	Permite comunicación spoke-to-spoke 
•	Reduce configuración manual de túneles 
  
VPN IPsec 
Proporciona: 
•	Confidencialidad (AES) 
•	Integridad (SHA) 
•	Autenticación (pre-shared key) 
Se implementa sobre el túnel GRE. 
  
## 5. Parámetros de Seguridad Utilizados
| Fase | Parámetro | Valor |
|-----|-----------|------|
| IKEv1 – Fase 1 | Encryption | AES |
| IKEv1 – Fase 1 | Authentication | Pre-shared key |
| IKEv1 – Fase 1 | Diffie-Hellman Group | 2 |
| IKEv1 – Fase 1 | ISAKMP Policy | `crypto isakmp policy 10` |
| IKEv1 – Fase 1 | Clave compartida | `crypto isakmp key cisco123 address 0.0.0.0` |
| IKEv1 – Fase 2 (IPsec) | Transform-set | `crypto ipsec transform-set TS esp-aes esp-sha-hmac` |
| IKEv1 – Fase 2 (IPsec) | Mode | `transport` |
| IKEv1 – Fase 2 (IPsec) | Encryption | AES |
| IKEv1 – Fase 2 (IPsec) | Integrity | SHA |
  
## 6. Configuración DMVPN 
Configuración del HUB 
Tunnel Interface 
interface Tunnel0 
 ip address 10.12.50.113 255.255.255.240  no ip redirects  ip mtu 1400 
 ip nhrp map multicast dynamic  ip nhrp network-id 1  no ip split-horizon eigrp 100  ip tcp adjust-mss 1360  tunnel source GigabitEthernet1/0  tunnel mode gre multipoint  tunnel key 12345  tunnel protection ipsec profile DMVPN_PROFILE 
 
### Explicación 
Comando 	Función tunnel mode gre multipoint 	Permite múltiples túneles dinámicos ip nhrp map multicast dynamic Permite registro dinámico de spokes tunnel protection ipsec 	Protege el túnel con IPsec 
  
## Configuración SPOKE 
interface Tunnel0 
 ip address 10.12.50.114 255.255.255.240  ip nhrp map 10.12.50.113 10.12.50.98  ip nhrp map multicast 10.12.50.98  ip nhrp nhs 10.12.50.113  ip nhrp network-id 1  tunnel source GigabitEthernet1/0  tunnel mode gre multipoint  tunnel key 12345  tunnel protection ipsec profile DMVPN_PROFILE 
### Explicación 
Comando 	Función 
ip nhrp nhs Define el Hub 
ip nhrp map Mapea dirección tunnel a dirección física 
  
## 7. Configuración de Enrutamiento (EIGRP) 
router eigrp 100  network 10.12.50.64 0.0.0.31  network 10.12.50.112 0.0.0.15 Funciones: 
•	Distribuir rutas entre HUB y SPOKES 
•	Permitir comunicación entre LAN remotas 
  
## 8. Verificación 
### Estado del túnel 
- show interface tunnel0 Debe aparecer:
  
Tunnel0 is up, line protocol is up

# 10. Implementación usando IKEv2 
<img width="1002" height="637" alt="image" src="https://github.com/user-attachments/assets/00d6709d-0c65-44c8-a92c-c131132e3be1" />

```bash
Fase 1 – IKEv2 Proposal 
crypto ikev2 proposal IKEV2_PROPOSAL 
encryption aes-cbc-256 
integrity sha256 
group 14
```
## IKEv2 Policy 
```bash
crypto ikev2 policy IKEV2_POLICY 
proposal IKEV2_PROPOSAL
```
## IKEv2 Keyring 
```bash
crypto ikev2 keyring DMVPN_KEYS 
peer SPOKES 
address 0.0.0.0 0.0.0.0 
pre-shared-key cisco123
```
## IKEv2 Profile 
```bash
crypto ikev2 profile DMVPN_PROFILE 
match identity remote any 
authentication remote pre-share 
authentication local pre-share 
keyring local DMVPN_KEYS
```
## IPsec Profile 
```bash
crypto ipsec profile DMVPN_IPSEC_PROFILE 
set transform-set TS 
set ikev2-profile DMVPN_PROFILE
```
# 11. Pruebas de Conectividad 
## Prueba entre LANs: 
- PC1 -> PC2 
#### Resultado esperado: 
- PING SUCCESS
