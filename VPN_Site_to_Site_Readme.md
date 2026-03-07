
# VPN Site-to-Site basada en Routing (IKEv1) – Documentación Técnica

## 1. Objetivo de la VPN
El objetivo de esta implementación es crear una **VPN Site-to-Site IPsec basada en routing (VTI)** entre dos redes LAN ubicadas en diferentes sedes.  
La comunicación segura se establece a través de Internet utilizando **IPsec con IKEv1**, permitiendo que los hosts de ambas redes puedan comunicarse de forma cifrada.

### Redes conectadas
- **LAN Sitio A:** 10.12.50.64/27
- **LAN Sitio B:** 10.12.50.96/27

La VPN protege el tráfico entre estas redes utilizando un **túnel IPsec (Tunnel0)**.

---

## 2. Topología de Red

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

## 5. Configuración del Túnel VPN

### Router R2

- Fuente del túnel: `GigabitEthernet1/0`
- Destino del túnel: `10.12.50.6`
- Perfil IPsec: `VTI_PROFILE`

### Router R3

- Fuente del túnel: `GigabitEthernet1/0`
- Destino del túnel: `10.12.50.1`
- Perfil IPsec: `VTI_PROFILE`

---

## 6. Rutas Estáticas

### R2

```
ip route 0.0.0.0 0.0.0.0 10.12.50.2
ip route 10.12.50.96 255.255.255.224 Tunnel0
```

### R3

```
ip route 0.0.0.0 0.0.0.0 10.12.50.5
ip route 10.12.50.64 255.255.255.224 Tunnel0
```

Estas rutas permiten que el tráfico hacia la red remota sea enviado a través del túnel VPN.

---

## 7. Verificación de la VPN

Comandos utilizados:

```
show crypto isakmp sa
show crypto ipsec sa
show interface tunnel0
ping <IP remota>
```

Resultados esperados:
- Estado **QM_IDLE** en ISAKMP
- Contadores de paquetes cifrados incrementando
- Conectividad entre PC1 y PC2

---

## 8. Conclusión

La VPN Site-to-Site permite conectar dos redes privadas de forma segura a través de Internet.  
El uso de **VTI (Virtual Tunnel Interface)** facilita el enrutamiento y permite utilizar protocolos de routing dinámico si se requiere en el futuro.
