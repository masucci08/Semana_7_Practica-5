# Ejecucion de Inundacion DoS al protocolo CDP con Scapy

**Estudiante:** Masucci Franco Rincón  
**Matrícula:** 2024-1250  
**Asignatura:** Seguridad de Redes  
**Fecha:** 6/03/2026  

https://itlaedudo-my.sharepoint.com/:v:/g/personal/20241250_itla_edu_do/IQC9IS7YTMRFRrfhPv42-4O2ARYEEgvGf_kd9sbM2h1V5q0?nav=eyJyZWZlcnJhbEluZm8iOnsicmVmZXJyYWxBcHAiOiJPbmVEcml2ZUZvckJ1c2luZXNzIiwicmVmZXJyYWxBcHBQbGF0Zm9ybSI6IldlYiIsInJlZmVycmFsTW9kZSI6InZpZXciLCJyZWZlcnJhbFZpZXciOiJNeUZpbGVzTGlua0NvcHkifX0&e=HKtfxR
---

### Descripción y Topología 

Esta topología en GNS3 representa un escenario clásico de conectividad segura entre dos sucursales geográficamente separadas a través de una red pública (como Internet).


### Detalles de la Topología

* **Direccionamiento IP:** Subred `10.20.12.0/24`.

<img width="649" height="341" alt="topo" src="https://github.com/user-attachments/assets/8ca35b0c-8747-472b-a923-9ec0c9fe120f" />

| Dispositivo | Interfaz | Dirección IP | Máscara de Subred | Gateway Predeterminado |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | e0/0 | 10.20.12.1 | 255.255.255.0 (/24) |    |
| **Sw1** | VLAN 1 | 10.20.12.2  | 255.255.255.0 (/24) | 10.20.12.1 |
| **gnsattack (atacante)** | eth0 | 10.20.12.254 | 255.255.255.0 (/24) | 10.20.12.1 |
| **VPCS (víctima)** | e0/0 | 10.20.12.50  | 255.255.255.0 (/24) | 10.20.12.1 |


### Objetivo del Script
El script `cdp.py` tiene como objetivo saturar la tabla de vecinos CDP (Cisco Discovery Protocol) del switch objetivo, provocando una Denegación de Servicio en la capacidad de administración del equipo.



### Parámetros Usados
* **Interfaz:** `eth0`
* **Dirección Destino:** Multicast Cisco `01:00:0c:cc:cc:cc`.
* **Campos Falsificados (TLVs):**
    * *Device ID:* Generado aleatoriamente (`R1`).
    * *Port ID:* Simulación de interfaces Ethernet (`Ethernet0/x`).
    * *Capabilities:* Flag `0x0001` (Simulación de rol de Router).

## Evidencia de Ejecución
## 1-
<img width="467" height="279" alt="tabla cdpvacia" src="https://github.com/user-attachments/assets/aa6fdd03-2f98-47c9-b43a-0c7aa154e9a6" />




## 2-
<img width="301" height="241" alt="ejecucion scapy" src="https://github.com/user-attachments/assets/1c044ad6-b447-467f-96b8-23d58dcd8e87" />





## 3-
<img width="459" height="272" alt="tabla cdlfull" src="https://github.com/user-attachments/assets/5518d5ea-19b6-4283-950f-017930def08e" />





---

### Requisitos para utilizar la herramienta.
Tener python3 instalado en la maquina atacante


### Medidas de Mitigación
Para proteger la infraestructura contra estos vectores de ataque, se recomiendan las siguientes configuraciones de endurecimiento (Hardening):

 ```bash
Desabilitar Cdp con el comando en el modo global:
### *NO CDP RUN*
