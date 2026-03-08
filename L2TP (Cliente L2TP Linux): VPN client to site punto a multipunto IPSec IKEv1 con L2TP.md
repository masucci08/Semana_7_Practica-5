# Documentación Técnica – VPN Client-to-Site IPsec IKEv1 con L2TP 
# Link https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQDyzf29TAZ2TbAWPKr7lRxJAVPZDNFkbt1E5nLPjHF-qKc?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=I0ohhg

## 1. Descripción del Proyecto 
Este laboratorio implementa una VPN Client-to-Site punto a multipunto utilizando IPsec con IKEv1 y L2TP, permitiendo que clientes remotos se conecten de forma segura a una red privada a través de Internet. 
La VPN garantiza: 
1. Confidencialidad mediante cifrado IPsec 
2. Autenticación mediante IKEv1 
3. Integridad de datos mediante hashes 
4. Acceso remoto seguro desde clientes Linux y Windows 
  
## 2. Objetivo de la VPN 
El objetivo principal es permitir que clientes externos (Windows, Kali Linux o navegador Firefox) se conecten de forma segura al router R1, el cual actúa como servidor VPN, y accedan a recursos de la red privada. 
Beneficios 
- Acceso remoto seguro 
- Protección del tráfico en Internet 
- Conexión simultánea de múltiples clientes 
-	Autenticación centralizada 
  
# 3. Topología de Red 
La red está compuesta por: 

-	R1 → Servidor VPN 
-	R2 → Router remoto 
-	ISP → Red de tránsito 
-	Clientes → Kali Linux / Windows 
Diagrama de red

<img width="880" height="751" alt="image" src="https://github.com/user-attachments/assets/406686b3-7058-43c3-b382-281d508ba986" />
 
## Interfaces principales

| Dispositivo | Interfaz | IP | Descripción |
|-------------|----------|----|-------------|
| R1 | G1/0 | 10.12.50.1/26 | Red LAN |
| R1 | G2/0 | 10.12.50.138/29 | Conexión ISP |
| ISP | G1/0 | 10.12.50.137 | Gateway |
| R2 | G2/0 | 10.12.50.146  | Conexión ISP |
| Firefox | e0 | Estática | Cliente LAN R1 |
| Microsoft | e0 | Estática | Cliente LAN R2 |
## 4. Direccionamiento IP 
## Redes utilizadas

| Red | Máscara | Descripción |
|-----|--------|-------------|
| 10.12.50.0 | /26 | Red LAN |
| 10.12.50.136 | /29 | Red WAN ISP |
| Pool VPN | Dinámico | Clientes VPN |
  

# 5. Configuración IPsec 
```bash
Política IKEv1 
Define los parámetros de seguridad para la negociación del túnel. 
crypto isakmp policy 10  encr aes  hash sha 
 authentication pre-share  group 2  lifetime 86400 Explicación: 
Parámetro 	Función 
AES 	cifrado del túnel 
SHA 	integridad 
Pre-share 	autenticación 
DH group 2 intercambio de claves
``` 
  
# 7. Clave pre-compartida 
```bash
crypto isakmp key VPN123 address 0.0.0.0
``` 

# 8. Transform Set IPsec 
```bash
crypto ipsec transform-set VPN-SET esp-aes esp-sha-hmac 
```
# 9. Pool de direcciones VPN 
```bash
Direcciones asignadas a clientes. 
ip local pool VPNPOOL 192.168.100.10 192.168.100.50 
```

# 10. Configuración L2TP 
L2TP permite encapsular el tráfico PPP dentro del túnel IPsec. 
vpdn enable 
 vpdn-group L2TP-VPN  accept-dialin   protocol l2tp   virtual-template 1 
  
# 11. Interfaz Virtual Template 
```bash
Define la configuración para clientes conectados. 
interface Virtual-Template1  ip unnumbered GigabitEthernet1/0  peer default ip address pool VPNPOOL  ppp authentication ms-chap-v2 
 
Cliente Kali Linux 
Instalar paquetes: 
sudo apt install network-manager-l2tp Configurar: 
- Gateway → IP pública del router 
- Tipo → L2TP/IPsec 
- PSK → VPN123
```

