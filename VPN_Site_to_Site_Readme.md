# VPN Site-to-Site basada en Routing (IKEv1) – Documentación Técnica

**Estudiante:** Masucci Franco Rincón  
**Matrícula:** 2024-1250  
**Asignatura:** Seguridad de Redes  
**Fecha:** 6/03/2026  

# 🚀 PRIMERA PARTE: VPN SITE TO SITE BASED ROUTING IKEv1

## 1. Objetivo de la VPN
El objetivo de esta implementación es crear una **VPN Site-to-Site IPsec basada en routing (VTI)** entre dos redes LAN ubicadas en diferentes sedes.  
La comunicación segura se establece a través de Internet utilizando **IPsec con IKEv1**, permitiendo que los hosts de ambas redes puedan comunicarse de forma cifrada.

## LINK https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQAC8yFYdB17RoQtPUXOY0N8AdJ3sooPjaeK7RzIE170XPk?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=7gbhSC

### Redes conectadas
- **LAN Sitio A:** 10.12.50.64/27
- **LAN Sitio B:** 10.12.50.96/27

La VPN protege el tráfico entre estas redes utilizando un **túnel IPsec (Tunnel0)**.

---

## 2. Topología de Red
<img width="923" height="720" alt="image" src="https://github.com/user-attachments/assets/1ed7c669-cf81-40f6-a976-ddcdab4ef6f2" />


La topología está compuesta por:

- **R2:** Router del Sitio A
- **R3:** Router del Sitio B
- **R1:** Router del ISP
- **PC1:** Host de la red del Sitio A
- **PC2:** Host de la red del Sitio B

Imagen de la topología incluida en el documento PDF.

---

## 3. Direccionamiento IP

| Dispositivo | Interfaz | Dirección IP | Descripción |
|-------------|----------|--------------|-------------|
| R2 | G1/0 | 10.12.50.1/30 | Enlace hacia ISP |
| R1 | G1/0 | 10.12.50.2/30 | ISP hacia R2 |
| R1 | G2/0 | 10.12.50.5/30 | ISP hacia R3 |
| R3 | G1/0 | 10.12.50.6/30 | Enlace hacia ISP |
| R2 | F0/0 | 10.12.50.65/27 | LAN Sitio A |
| R3 | F0/0 | 10.12.50.97/27 | LAN Sitio B |
| R2 | Tunnel0 | 10.12.50.9/30 | Túnel VPN |
| R3 | Tunnel0 | 10.12.50.10/30 | Túnel VPN |

---

## 4. Parámetros de Seguridad VPN

### IKE Fase 1

- **Encryption:** AES
- **Authentication:** Pre-shared key
- **DH Group:** 2
- **Clave compartida:** cisco123

### IPsec Fase 2

- **Transform-set:** ESP-AES
- **Integridad:** SHA-HMAC
- **Modo:** Tunnel

---

## 5. Capturas de pantallas con su explicacion

### 1- Política IKE (Fase 1)

<img width="338" height="110" alt="image" src="https://github.com/user-attachments/assets/a3e0f3f6-4bbf-4255-b47c-9a985d611d75" />

Esta configuración define los parámetros de IKE Fase 1, que se utiliza para crear un canal seguro inicial entre los routers antes de establecer el túnel IPsec.

### 2- Clave Pre-Compartida
<img width="298" height="25" alt="image" src="https://github.com/user-attachments/assets/4d0bc361-8a4c-4774-b874-39eee2a0eee7" />

Esta línea configura la clave pre-compartida (PSK) usada para autenticar los routers en la fase IKE.
cisco123 → clave secreta que debe coincidir en ambos routers.
10.12.50.6 → dirección IP del router remoto (R3).


## 3- Transform Set (IPsec Fase 2)
<img width="317" height="47" alt="image" src="https://github.com/user-attachments/assets/cab85524-bbde-438c-828d-a7f87367f34e" />

El Transform Set define cómo se protegerá el tráfico en IPsec Fase 2.

esp-aes → cifrado del tráfico con AES.

esp-sha-hmac → autenticación e integridad usando SHA.

mode tunnel → encapsula completamente el paquete IP original.

Esto asegura confidencialidad e integridad de los datos entre sitios.



# 🔐 SEGUNDA PARTE: VPN Site-to-Site basada en Routing usando IKEv2

# LINK https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQAC8yFYdB17RoQtPUXOY0N8AdJ3sooPjaeK7RzIE170XPk?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=7gbhSC

## 📌 Descripción del Proyecto

Este laboratorio implementa una **VPN Site-to-Site IPsec basada en routing (VTI)** utilizando **IKEv2** para establecer un túnel seguro entre dos redes LAN ubicadas en diferentes sitios.

La VPN permite que los dispositivos de ambas redes se comuniquen de forma segura a través de una red intermedia (ISP) mediante **cifrado IPsec**.

Este proyecto fue desarrollado en **GNS3** utilizando routers Cisco IOS.

---

# 🎯 Objetivo de la VPN

El objetivo de esta implementación es:

* Conectar **dos redes LAN remotas** mediante una VPN segura.
* Utilizar **IPsec con IKEv2** para proteger el tráfico.
* Implementar un **Virtual Tunnel Interface (VTI)** para facilitar el enrutamiento.
* Permitir comunicación entre **PC1 y PC2** a través del túnel cifrado.

---

# 🖥️ Topología de Red

La siguiente topología muestra la arquitectura del laboratorio:

<img width="911" height="749" alt="image" src="https://github.com/user-attachments/assets/6ead78b1-c0f8-41d8-b63a-ef5944014184" />


### Dispositivos

* **R1** – Router del sitio A
* **R2** – Router del ISP
* **R3** – Router del sitio B
* **IOU2 / IOU3** – Switches de acceso
* **PC1 / PC2** – Hosts de las redes LAN

---

# 🌐 Plan de Direccionamiento IP

La red utilizada es **10.12.50.0/24**, segmentada de la siguiente manera:

| Segmento       | Red            |
| -------------- | -------------- |
| Enlace R1 – R2 | 10.12.50.0/30  |
| Enlace R2 – R3 | 10.12.50.4/30  |
| Túnel VPN      | 10.12.50.8/30  |
| LAN Sitio A    | 10.12.50.64/27 |
| LAN Sitio B    | 10.12.50.96/27 |

---

# 🔑 Parámetros de Seguridad (IKEv2)

### Fase IKE

| Parámetro      | Valor          |
| -------------- | -------------- |
| Encryption     | AES            |
| Integrity      | SHA256         |
| Diffie-Hellman | Group 14       |
| Authentication | Pre-Shared Key |
| Clave          | cisco123       |

---

# 🔒 Configuración de Seguridad

## Propuesta IKEv2

```cisco
crypto ikev2 proposal IKEV2_PROPOSAL
 encryption aes-cbc-256
 integrity sha256
 group 14
```

### Explicación

Define los algoritmos criptográficos utilizados durante la negociación IKEv2.

* **AES-256** para cifrado
* **SHA256** para integridad
* **DH Group 14** para intercambio de claves seguro

---

# 🔑 Keyring (Autenticación)

```cisco
crypto ikev2 keyring VPN_KEYRING
 peer R3
  address 10.12.50.6
  pre-shared-key cisco123
```

### Explicación

El **Keyring** almacena las claves utilizadas para autenticar el router remoto.

---

# 👤 Perfil IKEv2

```cisco
crypto ikev2 profile IKEV2_PROFILE
 match identity remote address 10.12.50.6 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local VPN_KEYRING
```

### Explicación

Este perfil define cómo se identificará y autenticará el peer remoto durante la negociación.

---

# 🔐 Transform Set (IPsec)

```cisco
crypto ipsec transform-set TS esp-aes esp-sha-hmac
 mode tunnel
```

### Explicación

Define cómo se protegerá el tráfico dentro del túnel VPN.

* **ESP-AES** → cifrado del tráfico
* **SHA-HMAC** → integridad de los datos

---

# 🌉 Configuración del Túnel VPN

```cisco
interface Tunnel0
 ip address 10.12.50.9 255.255.255.252
 tunnel source GigabitEthernet1/0
 tunnel destination 10.12.50.6
 tunnel mode ipsec ipv4
 tunnel protection ipsec profile IPSEC_PROFILE
```

### Explicación

La interfaz **Tunnel0** crea un túnel lógico protegido por IPsec que permite enrutar tráfico entre ambas redes.

---

# 🧭 Enrutamiento

## Rutas hacia redes remotas

### Router R1

```cisco
ip route 10.12.50.96 255.255.255.224 Tunnel0
```

### Router R3

```cisco
ip route 10.12.50.64 255.255.255.224 Tunnel0
```

### Explicación

Estas rutas envían el tráfico hacia la red remota a través del túnel VPN.

---

# 🧪 Veri
