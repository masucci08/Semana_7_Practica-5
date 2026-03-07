# Configuraciones de VPN SITE TO SITE BASED POLICIES IKEv1 & IKEv2 

**Estudiante:** Masucci Franco Rincón  
**Matrícula:** 2024-1250  
**Asignatura:** Seguridad de Redes  
**Fecha:** 6/03/2026  
---
# Link VPN site to site based polices IKEv1 & IKEv2: https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQC9IS7YTMRFRrfhPv42-4O2ARYEEgvGf_kd9sbM2h1V5q0?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=HKtfxR


# 🚀 PRIMERA PARTE: VPN SITE TO SITE BASED POLICIES IKEv1

### Descripción y Topología 
Esta topología en GNS3 representa un escenario clásico de conectividad segura entre dos sucursales geográficamente separadas a través de una red pública (como Internet). 

<img width="600" height="686" alt="image" src="https://github.com/user-attachments/assets/c34d1990-1766-4d2c-a9ba-fe59d1573a98" />


### Detalles de la Topología

| Dispositivo | Interfaz | Dirección IP | Máscara de Subred | Gateway Predeterminado |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | g1/0 | 10.20.12.249 | 255.255.255.252 (/30) |    |
| **R1** | g2/0 | 10.20.12.253 | 255.255.255.252 (/30) |    |
| **R2** | G1/0 | 10.20.12.250 | 255.255.255.252 (/30) |    |
| **R2** | f0/0 | 10.20.12.1 | 255.255.255.192 (/26) |    |
| **R3** | G1/0 | 10.20.12.254 | 255.255.255.252 (/30) |    |
| **R3** | f0/0 | 10.20.12.65 | 255.255.255.192 (/26) |    |
| **Sw1** | N/A | N/A |  N/A |  N/A |
| **PC1)** | e0 | 10.20.12.2 | 255.255.255.192 (/26) | 10.20.12.1 |
| **PC2** | e0 | 10.20.12.66  | 255.255.255.192 (/26) | 10.20.12.65 |


### Objetivo del Script
El objetivo principal de esta práctica es implementar y validar un canal de comunicación seguro (túnel VPN) entre dos sucursales a través de una red no confiable, garantizando la confidencialidad, integridad y autenticación de los datos transmitidos.



### Parámetros Usados
## Parámetros de Configuración - VPN Site-to-Site (IKEv1)

| Parámetro | Valor |
| :--- | :--- |
| **Versión IKE** | IKEv1 |
| **Mensajes negociación** | 6 (Main Mode - Por defecto) |
| **Autenticación** | Pre-Shared Key |
| **Clave PSK** | `cisco123` |
| **Cifrado IKEv1** | AES |
| **Integridad** | SHA-1 (Por defecto) |
| **DH Group** | Group 2 |
| **Cifrado IPSec** | ESP-AES |
| **Hash IPSec** | ESP-SHA-HMAC |
| **Modo IPSec** | Tunnel |
| **PFS** | No configurado |
| **Selección de tráfico** | Crypto ACL (`VPN-TRAFFIC`) |

## Capturas de pantalla de puntos específicos de la configuración con su explicación.
## 1- Configuración de la Fase 1 (Túnel de Gestión - ISAKMP)
<img width="638" height="193" alt="image" src="https://github.com/user-attachments/assets/ef8ae037-f676-4067-8043-036dd4bee2d5" />

En esta sección se definen las políticas ISAKMP para la Fase 1 de la VPN. El objetivo es establecer un canal de comunicación seguro entre los enrutadores antes de enviar datos. Se configuró el algoritmo de cifrado AES, el grupo 2 de Diffie-Hellman para el intercambio seguro de claves, y autenticación mediante una llave precompartida (cisco123) que apunta a la dirección IP pública del enrutador remoto




## 2- Configuración de la Fase 2 (Túnel de Datos - IPsec)
<img width="681" height="101" alt="image" src="https://github.com/user-attachments/assets/65207bb3-95b7-4a02-8070-abbc82c8c455" />

Aquí se configura el Transform Set (conjunto de transformaciones) para la Fase 2 de IPsec. Esto dicta cómo se protegerá el tráfico real de los usuarios una vez que el túnel de gestión esté establecido. Se utiliza el protocolo ESP (Encapsulating Security Payload) con AES para el cifrado de los datos y SHA-HMAC para garantizar la integridad de los paquetes. Opera en modo túnel (por defecto), encapsulando el paquete IP original por completo.






## 3- Definición del "Tráfico Interesante" (ACL)
<img width="315" height="50" alt="image" src="https://github.com/user-attachments/assets/f448f692-6e94-455a-b4b6-ab344ddf37d4" />

Al ser una VPN basada en políticas, es necesario identificar qué tráfico debe ser enrutado por el túnel cifrado y qué tráfico debe ir hacia Internet de forma normal. Mediante esta Lista de Control de Acceso (ACL) extendida, se le indica al enrutador que capture y cifre exclusivamente los paquetes que se originan en la red LAN local (10.12.50.0/26) y tienen como destino la red LAN remota (10.12.50.64/26)



## 3- Crypto Map
<img width="508" height="429" alt="image" src="https://github.com/user-attachments/assets/32db70d5-5870-4671-8b9e-cb7d3a988266" />

El Crypto Map actúa como el integrador de toda la configuración. En esta estructura se vincula la dirección del Peer remoto, el Transform Set (Fase 2) y la ACL que define el tráfico interesante. Finalmente, este mapa criptográfico se aplica a la interfaz física de salida (GigabitEthernet1/0), indicándole al enrutador que evalúe todo el tráfico saliente contra estas reglas de seguridad




# 🛠️ PARTE 2: VPN SITE TO SITE BASED POLICIES IKEv2

# Link VPN site to site based polices IKEv1 & IKEv2: https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQC9IS7YTMRFRrfhPv42-4O2ARYEEgvGf_kd9sbM2h1V5q0?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=HKtfxR

### Descripción y Topología 
La topología sigue representando una conexión segura entre dos sucursales a través de una red pública (R1). Sin embargo, al utilizar IPSec IKEv2, la lógica de negociación cambia significativamente:

**Eficiencia:** En lugar de 6 mensajes (Main Mode de IKEv1), IKEv2 establece el túnel de gestión en solo 4 mensajes (IKE_SA_INIT y IKE_AUTH).

**Modularidad:** A diferencia de IKEv1 donde todo se mezclaba en una sola política, IKEv2 separa la configuración en bloques de construcción lógicos: Proposal (qué algoritmos usar), Policy (activar la propuesta), Keyring (almacenar las contraseñas) y Profile (unir la autenticación con el destino).
<img width="630" height="348" alt="image" src="https://github.com/user-attachments/assets/02910771-e012-4e33-abd3-3225b08291a6" />


### Detalles de la Topología

| Dispositivo | Interfaz | Dirección IP | Máscara de Subred | Gateway Predeterminado |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | g1/0 | 10.20.12.1 | 255.255.255.252 (/30) |    |
| **R1** | g2/0 | 10.20.12.5 | 255.255.255.252 (/30) |    |
| **R2** | G1/0 | 10.20.12.2 | 255.255.255.252 (/30) |    |
| **R2** | f0/0 | 10.20.12.65 | 255.255.255.192 (/26) |    |
| **R3** | G1/0 | 10.20.12.6 | 255.255.255.252 (/30) |    |
| **R3** | f0/0 | 10.20.12.129 | 255.255.255.192 (/26) |    |
| **Sw1** | N/A | N/A |  N/A |  N/A |
| **PC1)** | e0 | 10.20.12.2 | 255.255.255.192 (/26) | 10.20.12.1 |
| **PC2** | e0 | 10.20.12.66  | 255.255.255.192 (/26) | 10.20.12.65 |


### Objetivo del Script
El objetivo principal es implementar un túnel VPN Site-to-Site robusto utilizando estándares criptográficos modernos de grado empresarial (Suite B).
Se busca dominar la estructura modular de IKEv2 en Cisco IOS y aplicar conceptos avanzados de seguridad, como el uso de algoritmos de cifrado fuertes (AES-256), hashing seguro (SHA-256) y la implementación de PFS (Perfect Forward Secrecy) con el Grupo 14 de Diffie-Hellman, garantizando que si una clave es comprometida en el futuro, las sesiones pasadas sigan siendo seguras.



### Parámetros Usados
## Parámetros de Configuración - VPN Site-to-Site IKEv2 (Route-Based / VTI)

| Parámetro | Valor |
| :--- | :--- |
| **Versión IKE** | IKEv2 |
| **Tipo de Arquitectura** | Route-Based VPN (VTI - Virtual Tunnel Interface) |
| **Autenticación** | Pre-Shared Key (PSK) |
| **Clave PSK** | `cisco123` |
| **Cifrado IKEv2 (Fase 1)** | AES-CBC-256 |
| **Integridad IKEv2 (Fase 1)** | SHA-256 |
| **Grupo Diffie-Hellman** | Group 14 |
| **Cifrado IPSec (Fase 2)** | ESP-AES-256 |
| **Hash IPSec (Fase 2)** | ESP-SHA256-HMAC |
| **Modo IPSec** | Tunnel |
| **Selección de Tráfico** | Enrutamiento estático hacia la interfaz `Tunnel0` |

## Capturas de pantalla de puntos específicos de la configuración con su explicación.
## 1- Propuesta y Política IKEv2 (Criptografía de la Fase 1)
<img width="227" height="96" alt="image" src="https://github.com/user-attachments/assets/102b0f3a-1b22-4e63-80f2-82cc5b629dee" />


En esta sección se define la robustez criptográfica del túnel de gestión (Fase 1). Se configuró un Proposal de IKEv2 utilizando algoritmos de alta seguridad: cifrado simétrico con AES de 256 bits, validación de integridad con SHA-256 y el Grupo 14 de Diffie-Hellman para un intercambio seguro de llaves. Finalmente, esta propuesta se asocia a una política de IKEv2 para activarla en el router




## 2- Llavero y Perfil IKEv2 (Autenticación e Identidad)
<img width="370" height="157" alt="image" src="https://github.com/user-attachments/assets/7cbf7577-8dfd-4da3-8587-f5f8c495636b" />


La autenticación en IKEv2 es altamente modular. Primero, se crea un Keyring (llavero) para almacenar la llave precompartida (cisco123) asociada exclusivamente a la IP pública del enrutador R2. Luego, se configura un Profile que actúa como integrador de la Fase 1, indicando las identidades locales y remotas, el método de autenticación a utilizar y vinculando el llavero previamente creado.




## 3- Transform Set y Perfil IPsec (Fase 2)
<img width="383" height="75" alt="image" src="https://github.com/user-attachments/assets/e1fe234c-ef0a-4aad-9240-23c1e4c662a2" />


Para la Fase 2, se define cómo se protegerán los datos reales de los usuarios. Se configura un Transform Set que utiliza ESP con AES-256 para cifrar la carga útil y SHA-256-HMAC para garantizar que los paquetes no sean alterados en tránsito. A diferencia de las VPNs tradicionales, aquí se crea un IPsec Profile que unifica los parámetros de la Fase 2 con el perfil de IKEv2 (Fase 1), preparándolo para ser aplicado directamente a una interfaz.



## 4- Implementación VTI (Interfaz Virtual y Enrutamiento)
<img width="278" height="167" alt="image" src="https://github.com/user-attachments/assets/cffad89a-780e-4444-bc67-22bb4ff3a783" />

Este es el núcleo de una VPN basada en rutas (VTI). En lugar de usar Crypto Maps y listas de control de acceso (ACLs) complejas, se crea una interfaz virtual (Tunnel0). A esta interfaz se le asignan las direcciones IP de origen y destino del túnel público y se le aplica la protección del IPsec Profile. Finalmente, la selección del tráfico que debe ser cifrado se vuelve tan simple como crear una ruta estática que dirija el tráfico de la LAN remota hacia la interfaz Tunnel0.
