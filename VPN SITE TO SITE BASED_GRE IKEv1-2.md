# Documentación Técnica -- VPN Site‑to‑Site GRE sobre IPsec (IKEv1)

# link https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQBiK4JzP3KzSYdv4O-r7dRxAfJ1jpSYTNaPkbJFuOSbZ3Q?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=scWpGI

## 1. Descripción General
<img width="612" height="408" alt="image" src="https://github.com/user-attachments/assets/2b9ca6a5-522c-49df-a05b-7b7a3ee6d621" />


Este laboratorio implementa una **VPN Site‑to‑Site segura** entre dos
sedes utilizando:

-   **GRE Tunnel**
-   **IPsec**
-   **IKEv1**
-   **Autenticación Pre‑Shared Key**

El objetivo es permitir comunicación segura entre las redes LAN:

-   **LAN Sitio A (R1)** → 10.12.50.16/28
-   **LAN Sitio B (R3)** → 10.12.50.32/28

El router **R2** actúa como red intermedia (ISP).

------------------------------------------------------------------------

## 2. Objetivo de la VPN

La VPN permite:

-   Conectar dos redes privadas remotas.
-   Proteger el tráfico con **cifrado AES**.
-   Garantizar **integridad mediante SHA‑HMAC**.
-   Permitir el transporte de múltiples protocolos mediante **GRE**.

Arquitectura utilizada:

    LAN A --- R1 ===== Internet (R2) ===== R3 --- LAN B
                GRE + IPsec

------------------------------------------------------------------------

## 3. Topología de Red

  Dispositivo   Interfaz   Dirección IP     Descripción
  ------------- ---------- ---------------- -------------------
  R1            G1/0       10.12.50.1/30    Enlace hacia ISP
  R2            G1/0       10.12.50.2/30    Conexión hacia R1
  R2            G2/0       10.12.50.6/30    Conexión hacia R3
  R3            G1/0       10.12.50.5/30    Enlace hacia ISP
  R1            Fa0/0      10.12.50.17/28   LAN Sitio A
  R3            Fa0/0      10.12.50.33/28   LAN Sitio B
  R1            Tunnel0    10.12.50.9/30    GRE Tunnel
  R3            Tunnel0    10.12.50.10/30   GRE Tunnel

------------------------------------------------------------------------

## 4. Parámetros de Seguridad

### IKE Phase 1

    crypto isakmp policy 10
     encr aes
     authentication pre-share
     group 2

**Parámetros**

  Parámetro        Valor
  ---------------- ----------------
  Cifrado          AES
  Autenticación    Pre‑Shared Key
  Diffie‑Hellman   Grupo 2

Clave compartida:

    crypto isakmp key cisco123 address 10.12.50.X

------------------------------------------------------------------------

### IKE Phase 2 (IPsec)

    crypto ipsec transform-set TS esp-aes esp-sha-hmac
     mode transport

  Parámetro      Valor
  -------------- -----------
  Encriptación   AES
  Integridad     SHA-HMAC
  Modo           Transport

Perfil IPsec:

    crypto ipsec profile VPN_PROFILE
     set transform-set TS

------------------------------------------------------------------------

## 5. Configuración del Túnel GRE

Configuración en **R1**:

    interface Tunnel0
     ip address 10.12.50.9 255.255.255.252
     tunnel source GigabitEthernet1/0
     tunnel destination 10.12.50.5
     tunnel protection ipsec profile VPN_PROFILE

Configuración en **R3**:

    interface Tunnel0
     ip address 10.12.50.10 255.255.255.252
     tunnel source GigabitEthernet1/0
     tunnel destination 10.12.50.1
     tunnel protection ipsec profile VPN_PROFILE

Explicación:

-   **Tunnel source** → interfaz pública del router.
-   **Tunnel destination** → IP pública del router remoto.
-   **Tunnel protection** → aplica cifrado IPsec al túnel GRE.

------------------------------------------------------------------------

## 6. Rutas Estáticas

En **R1**:

    ip route 10.12.50.32 255.255.255.240 Tunnel0

Permite enviar tráfico hacia la LAN del sitio remoto a través del túnel.

En **R3**:

    ip route 10.12.50.16 255.255.255.240 Tunnel0

Permite alcanzar la LAN del sitio A.

------------------------------------------------------------------------

## 7. Verificación de la VPN

Comandos utilizados:

    show crypto isakmp sa
    show crypto ipsec sa
    show interface tunnel0
    ping

Resultados esperados:

-   Estado **QM_IDLE**
-   Contadores de paquetes **encapsulados y decapsulados**
-   Conectividad entre **PC1 y PC2**

------------------------------------------------------------------------

## 8. Capturas de Pantalla Recomendadas

Para completar la documentación se deben incluir capturas de:

1.  **show crypto isakmp sa**
2.  **show crypto ipsec sa**
3.  **show interface tunnel0**
4.  **ping entre PC1 y PC2**
5.  **tabla de rutas**

Cada captura debe incluir una breve explicación del resultado.

------------------------------------------------------------------------
VPN Site-to-Site GRE sobre IPsec (IKEv2)
1. Descripción General

Este laboratorio implementa una VPN Site-to-Site segura entre dos redes remotas utilizando las siguientes tecnologías:

GRE (Generic Routing Encapsulation)

IPsec

IKEv2

Autenticación mediante Pre-Shared Key

El objetivo es permitir comunicación segura entre dos redes LAN a través de una red intermedia que simula Internet.

Redes conectadas
Sitio	Red LAN
Sitio A	10.12.50.16 /28
Sitio B	10.12.50.32 /28

El router R2 actúa como ISP o red intermedia.

2. Objetivo de la VPN

La VPN permite:

Conectar dos redes privadas geográficamente separadas.

Cifrar el tráfico entre routers mediante IPsec.

Transportar múltiples protocolos usando GRE.

Proteger la comunicación contra interceptación.

Ventajas de usar GRE + IPsec
Tecnología	Función
GRE	Permite encapsular múltiples protocolos
IPsec	Proporciona cifrado y autenticación
IKEv2	Gestiona el intercambio seguro de claves
3. Topología de Red
LAN A ---- PC1
            |
           SW
            |
           R1 -------- R2 -------- R3
            |                        |
           SW                       SW
            |                        |
           PC1                      PC2

La comunicación entre R1 y R3 se realiza mediante un túnel GRE protegido con IPsec (IKEv2).

4. Direccionamiento IP
Dispositivo	Interfaz	Dirección IP	Descripción
R1	G1/0	10.12.50.1 /30	Conexión hacia ISP
R2	G1/0	10.12.50.2 /30	Enlace con R1
R2	G2/0	10.12.50.6 /30	Enlace con R3
R3	G1/0	10.12.50.5 /30	Conexión hacia ISP
R1	Fa0/0	10.12.50.17 /28	LAN Sitio A
R3	Fa0/0	10.12.50.33 /28	LAN Sitio B
R1	Tunnel0	10.12.50.9 /30	Túnel GRE
R3	Tunnel0	10.12.50.10 /30	Túnel GRE
5. Configuración de Seguridad (IKEv2)
IKEv2 Proposal

Define los algoritmos criptográficos utilizados.

crypto ikev2 proposal IKEV2-PROP
 encryption aes-cbc-256
 integrity sha256
 group 14
Explicación
Parámetro	Descripción
encryption aes-cbc-256	Algoritmo de cifrado
integrity sha256	Verificación de integridad
group 14	Grupo Diffie-Hellman
IKEv2 Policy
crypto ikev2 policy IKEV2-POLICY
 proposal IKEV2-PROP

Define qué propuesta criptográfica será utilizada durante la negociación.

Keyring (clave compartida)
crypto ikev2 keyring VPN-KEYRING
 peer R3
  address 10.12.50.5
  pre-shared-key cisco123

Define la clave compartida para autenticación entre routers.

IKEv2 Profile
crypto ikev2 profile VPN-PROFILE
 match identity remote address 10.12.50.5 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local VPN-KEYRING

Este perfil define:

Método de autenticación

Identidad del peer remoto

Keyring utilizado

6. Configuración IPsec
Transform Set

Define cómo se cifrará el tráfico.

crypto ipsec transform-set TS esp-aes esp-sha-hmac
 mode transport
Parámetro	Función
esp-aes	Cifrado del tráfico
esp-sha-hmac	Integridad
transport	Modo transporte
Perfil IPsec
crypto ipsec profile VPN_PROFILE
 set transform-set TS
 set ikev2-profile VPN-PROFILE

Este perfil se aplica al túnel GRE.

7. Configuración del Túnel GRE
Configuración en R1
interface Tunnel0
 ip address 10.12.50.9 255.255.255.252
 tunnel source GigabitEthernet1/0
 tunnel destination 10.12.50.5
 tunnel protection ipsec profile VPN_PROFILE
Configuración en R3
interface Tunnel0
 ip address 10.12.50.10 255.255.255.252
 tunnel source GigabitEthernet1/0
 tunnel destination 10.12.50.1
 tunnel protection ipsec profile VPN_PROFILE
Explicación
Parámetro	Función
tunnel source	Interfaz pública del router
tunnel destination	IP del router remoto
tunnel protection	Aplica cifrado IPsec
8. Rutas Estáticas
En R1
ip route 10.12.50.32 255.255.255.240 Tunnel0

Permite enviar tráfico hacia la red remota.

En R3
ip route 10.12.50.16 255.255.255.240 Tunnel0

Permite alcanzar la LAN del sitio A.

9. Verificación del Túnel

Comandos de diagnóstico:

show crypto ikev2 sa

Verifica el estado de la negociación IKEv2.

show crypto ipsec sa

Muestra paquetes cifrados y descifrados.

show interface tunnel0

Verifica el estado del túnel.

show ip route

Comprueba las rutas hacia la red remota.

10. Pruebas de Conectividad

Desde PC1:

ping 10.12.50.34

Resultado esperado:

Respuesta exitosa

Tráfico cifrado por IPsec

Incremento de contadores en show crypto ipsec sa

11. Capturas de Pantalla Recomendadas

Para completar la documentación se deben incluir:

Topología en GNS3

show crypto ikev2 sa

show crypto ipsec sa

show interface tunnel0

show ip route

Ping entre PC1 y PC2

Cada captura debe incluir una breve explicación.
-   Transportar múltiples tipos de tráfico.
-   Proteger la información mediante cifrado.

Este tipo de arquitectura es ampliamente utilizado en **redes
empresariales y entornos corporativos** para conectar sucursales.
