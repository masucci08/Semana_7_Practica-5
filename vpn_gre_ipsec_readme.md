# Documentación Técnica -- VPN Site‑to‑Site GRE sobre IPsec (IKEv1)

## 1. Descripción General

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

## 9. Conclusión

La implementación de **GRE sobre IPsec** permite:

-   Conectar redes remotas de forma segura.
-   Transportar múltiples tipos de tráfico.
-   Proteger la información mediante cifrado.

Este tipo de arquitectura es ampliamente utilizado en **redes
empresariales y entornos corporativos** para conectar sucursales.
