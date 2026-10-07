
# Manual Técnico — SmartCity Tech Park

**Curso:** Redes de Computadoras 1 — Universidad de San Carlos de Guatemala  
**Proyecto:** Proyecto 1 — Segundo Semestre 2026  
**Carné:** 202200057  
**Archivo Packet Tracer:** Proyecto1_202200057.pkt

---

# Comandos importantes
- Por si use "clear" (Control+Shift+6)
- Para ver modo stp = rapid-pvst (show spanning-tree)
- Para ver los trunk (show interfaces trunk)
- Para ver si es cliente o server (show vtp status )


---

# Parametros a seguir

| Parámetro / Protocolo | Valor Asignado |
|-----------------------|----------------|
| VTP Domain | Smart_5 |
| VTP Password | proyecto12S2026 |
| VLAN Nativa | VLAN 97 |
| Protocolo EtherChannel | PAgP (Modo desirable) |
| Protocolo Spanning-Tree | Rapid-PVST (rapid-pvst) |
| Banner MOTD | Acceso Restringido - TechPark_202200057 |

--- 
# Tabla de Dominios de Colisión y Broadcast por Dispositivo

| Área de la Red | Dispositivo | Puertos Activos (`Fa0/x`) | Dominios de Colisión | Dominios de Broadcast |
| :--- | :--- | :--- | :--- | :--- |
| **Centro de Datos** | Switch Core (`Centro`) | 12 puertos | **12** | 6 (VLANs 17, 27, 37, 47, 57, 97) |
| **Centro de Datos** | Switch Servidores 1 (`Disponibilidad1`) | 4 puertos | **4** | 2 (VLAN 97, 47) |
| **Centro de Datos** | Switch Servidores 2 (`Disponibilidad2`) | 4 puertos | **4** | 2 (VLAN 97, 47) |
| **Corporativo** | Switch Distribución | 3 puertos | **3** | 3 (VLANs 97, 17 y 57) |
| **Corporativo** | Switch Gerencia | 5 puertos | **5** | 2 (VLAN 97, 17) |
| **Corporativo** | Switch Visitantes | 2 puertos | **2** | 2 (VLAN 97, 57) |
| **Centro de I+D** | Switch `ID_1` | 6 puertos | **6** | 2 (VLAN 97, 27) |
| **Centro de I+D** | Switch `ID_2` | 8 puertos | **8** | 2 (VLAN 97, 27) |
| **Centro de I+D** | Switch `ID_3` | 6 puertos | **6** | 2 (VLAN 97, 27) |
| **Planta Producción** | Switch Producción | 2 puertos (`Fa0/1`, `Fa0/2`) | **2** | 2 (VLAN 97, 37) |
| **Planta Producción** | Hub Legacy (`hub_produccion`) | 5 puertos (`Fa0` al `Fa4`) | **1 (Compartido)** | 2 (VLAN 97, 37) |

# Configuracion de Vlans

| VLAN | Nombre | Descripción |
|------|--------|-------------|
| 17 | GERENCIA | Edificio Corporativo (Ala 1) |
| 27 | INVESTIGACION | Centro de I+D |
| 37 | PRODUCCION | Planta de Producción (Hub Legacy) |
| 47 | SERVIDORES | Centro de Datos |
| 57 | VISITANTES | Edificio Corporativo (Ala 2 / AP) |


---

# Tabla de Dominios de Broadcast


Se definieron un total de **6 dominios de broadcast independientes** correspondientes a cada VLAN activa configurada en el switch servidor VTP y propagada por el dominio **Smart_5**. Al operar exclusivamente en Capa 2, el aislamiento del tráfico de difusión entre departamentos se logra puramente mediante la segmentación lógica de VLANs, impidiendo que el tráfico de una red interfiera con otra.


| VLAN | Nombre de la VLAN | Ubicación / Área Física | Dominios de Broadcast | Dispositivos / Hosts Involucrados |
|------------|-------------------|--------------------------|------------------------|------------------------------------|
| VLAN 17 | Gerencia | Edificio Corporativo | 1 Dominio | Estaciones de trabajo administrativas. |
| VLAN 27 | Investigacion | Centro de I+D | 1 Dominio | Equipos de desarrollo y switches del anillo de I+D. |
| VLAN 37 | Produccion | Planta de Producción | 1 Dominio | Maquinaria industrial, Hub Legacy y sus PCs conectadas. |
| VLAN 47 | Servidores | Centro de Datos | 1 Dominio | Granja de servidores centrales y sus switches de disponibilidad. |
| VLAN 57 | Visitantes | Edificio Corporativo (AP) | 1 Dominio | Laptops de invitados conectadas al Access Point inalámbrico. |
| VLAN 97 | Nativa | Enlaces Troncales (Trunks) | 1 Dominio | Involucrado en todos los switch por ser la nativa |




# COMANDOS UTILIZADOS
Estos son los comandos que fui usando conforme iba configurando cada uno de los dispositivos. Hay momentos en donde el bloque se repite sin embargo la comfiguracion de todo esta listado a continuacion

## Swich - Centro de datos

### Configuracion de vlans


```cisco
enable
configure terminal
(config)# vtp domain Smart_5
(config)# vtp mode server
(config)# vtp password proyecto12S2026
(config)# vlan 17
(config-vlan)# name GERENCIA
(config)# vlan 27
(config-vlan)# name INVESTIGACION
(config)# vlan 37
(config-vlan)# name PRODUCCION
(config)# vlan 47
(config-vlan)# name SERVIDORES
(config)# vlan 57
(config-vlan)# name VISITANTES
(config)# vlan 97
(config-vlan)# name NATIVA
(config-vlan)# exit
```

### Configuracion Root Bridge

- Asignamos el protocolo rapid-pvst
- Asignamos al switch CentroDatos como RootBridge

```cisco
(config)# spanning-tree mode rapid-pvst
(config)# spanning-tree vlan 17,27,37,47,57,97 priority 4096
```

### Configuracion de port-channel (central - ID_1-2-3)
La configuracion debera hacerse ambos: Switch central y tambien en el switch ID_1 


```cisco
(config)#    interface range fastEthernet 0/x - x+1
(config-if)# channel-group 1 mode desirable
(config-if)# exit
(config)#    interface port-channel 1
(config-if)# switchport mode trunk
(config-if)# switchport trunk native vlan 97
(config-if)# switchport trunk allowed vlan 27, 97

- Verificar con "show etherchannel summary"
- Verificar con "show interfaces trunk"
- Verificar con "show spanning-tree summary"
```


---
# Configuracion Centro Investigacion y Desarrollo

```cisco
enable
configure terminal
(config)# banner motd #Acceso Restringido - TechPark_202200057#
(config)# vtp domain Smart_5
(config)# vtp password proyecto12S2026
(config)# vtp mode client
(config)# spanning-tree mode rapid-pvst
(config)# exit

- Verificar con "show vtp status"

```

## configuracion entre ID_1 - 2 - 3
```cisco

enable
configure terminal
(config)# interface range FastEthernet 0/x - x+1
(config)# switchport mode trunk
(config)# switchport trunk native vlan 97
(config)# switchport trunk allowed vlan 27, 97
(config)# exit


```

# Configuracion Edificio Corporativo

### Configuracion del switch de distribucion "corporativo"

```cisco
enable
configure terminal
(config)# hostname Corporativo
(config)# spanning-tree mode rapid-pvst
(config)# vtp domain Smart_5
(config)# vtp password proyecto12S2026
(config)# vtp mode client
(config)# banner motd #Acceso Restringido - TechPark_202200057#

(config)# interface FastEthernet0/x
(config)# switchport mode trunk
(config)# switchport trunk native vlan 97
(config)# switchport trunk allowed vlan 27, 97 | vlan all
(config)# exit
- verificar con "show vtp status"
```

### Configuracion switch "Gerentes y Visitas"
```cisco
enable
configure terminal
(config)# hostname Corporativo
(config)# spanning-tree mode rapid-pvst
(config)# vtp domain Smart_5
(config)# vtp password proyecto12S2026
(config)# vtp mode client
(config)# banner motd #Acceso Restringido - TechPark_202200057#

(config)# interface FastEthernet0/x
(config)# switchport mode trunk
(config)# switchport trunk native vlan 97
(config)# switchport trunk allowed vlan 27, 97 | vlan all
(config)# exit
- verificar con "show vtp status"
- verficar con "show interfaces trunk"
```
### Configuracion switch "visitas hacia AccesPoint"
```cisco
(config)# configure terminal
(config)# interface FastEthernet 0/3
(config)# switchport mode access
(config)# switchport access vlan 57
(config)# exit exit
```


## Configuracion PC VISITANTES "Inalambrico"

```cisco
- Seleccionar una PC o laptop
- Entrar en su configuracion
- Encontrarse en el menu "Physical" 
- Apagar la PC (boton y se debe apagar la luz)
- Desconectar el modulo de red (Arrastrarlo a la izq)
- Agregar el modulo wifi (WPC300N)
- Encender la PC
- Ir al menu "Desktop" y ahi a "Pc wireless"
- Conectarse a la red wifi
```
## Configuracion PC Corp- GERENCIA 

### Desde el switch corporativo
```cisco
enable
(config)# configure terminal
(config)# interface range fastEthernet 0/3 - 5
(config)# switchport mode access
(config)# switchport access vlan 17
(config)# no shutdown
(config)# end


```



# Configuracion Switch a PC de I+D 

### Desde el switch ID_3
```cisco
enable
configure terminal
(config)#interface range fastEthernet 0/5 - 8 
(config)#switchport mode access
(config)#switchport access vlan 27
(config)#no shutdown
(config)#end
```

### Desde el switch ID_2
```cisco
enable
configure terminal
(config)#interface range fastEthernet 0/5 - 6
(config)#switchport mode access
(config)#switchport access vlan 27
(config)#no shutdown
(config)#end
```

### Desde el switch ID_1
```cisco
enable
configure terminal
(config)#interface range fastEthernet 0/5 - 6
(config)#switchport mode access
(config)#switchport access vlan 27
(config)#no shutdown
(config)#end
```

# Configuracion Planta de produccion

### Switch la planta de produccion a switch central
```cisco
enable
configure terminal
(config)# interface range FastEthernet 0/1
(config)# switchport mode trunk
(config)# switchport trunk native vlan 97
(config)# switchport trunk allowed vlan 37, 97
(config)# exit
```
### Switch la planta de produccion a hub
```cisco
enable
configure terminal
(config)#interface fastEthernet 0/2
(config)#switchport mode access
(config)#switchport access vlan 37
(config)#no shutdown
(config)#end
```


# Configuracion Planta de servidores

### Desde Switch central a Switch Disponibilidad1
```cisco
enable
configure terminal
(config)# interface range FastEthernet 0/9-10
(config)# switchport mode trunk
(config)# switchport trunk native vlan 97
(config)# switchport trunk allowed vlan 47, 97
(config)# exit
```

### Configuracion los switch disponibilidad 1 - 2

```cisco
enable
configure terminal
(config)# banner motd #Acceso Restringido - TechPark_202200057#
(config)# vtp domain Smart_5
(config)# vtp password proyecto12S2026
(config)# vtp mode client
(config)# spanning-tree mode rapid-pvst
(config)# exit

(config)#    interface range fastEthernet 0/1 - 2
(config-if)# channel-group 4 mode desirable
(config-if)# exit
(config)#    interface port-channel 4 | 5 
(config-if)# switchport mode trunk
(config-if)# switchport trunk native vlan 97
(config-if)# switchport trunk allowed vlan 47, 97


configure terminal
interface range fastEthernet 0/1 - 2
switchport mode access
switchport access vlan 47
end
```

### Tabla de Asignación de Puertos por Switch

| Switch | Interfaz (Port) | Modo | VLAN Asignada / Nombre | Descripción / Conexión Destino |
|--------|-----------------|------|------------------------|-------------------------------|
| Switch_Core | Fa0/1 - Fa0/2 | Trunk | VLAN 97, 27 | Enlace EtherChannel (Po1) hacia ID_1 |
| Switch_Core | Fa0/3 - Fa0/4 | Trunk | VLAN 97, 27 | Enlace EtherChannel (Po2) hacia ID_2 |
| Switch_Core | Fa0/5 - Fa0/6 | Trunk | VLAN 97, 27 | Enlace EtherChannel (Po3) hacia ID_3|
| Switch_Core | Fa0/7 | Trunk | VLAN 97, 17, 57 |Enlaces troncales hacia el switch Corporativo  |
| Switch_Core | Fa0/8 | Trunk | VLAN 97, 37 | Enlaces troncales hacia el switch de planta de produccion |
| Switch_Core | Fa0/9 - Fa0/10 | Trunk | VLAN 97, 47 | Enlaces troncales hacia servidores (Disponibilidad1) |
| Switch_Core | Fa0/11 - Fa0/12 | Trunk | VLAN 97, 47 | Enlaces troncales hacia servidores (Disponibilidad2) |
| Switch_Servidores1 (Disponibilidad1) | Fa0/1 - Fa0/2 | Trunk | VLAN 97, 47 | Enlace troncal de subida hacia el Switch Core |
| Switch_Servidores1 (Disponibilidad1) | Fa0/3 | Access | VLAN 47 (Servidores) | Conexión hacia Server1 |
| Switch_Servidores1 (Disponibilidad1) | Fa0/4 | Access | VLAN 47 (Servidores) | Conexión hacia Server2 |
| Switch_Servidores2 (Disponibilidad2) | Fa0/1 - Fa0/2 | Trunk | VLAN 97 47 | Enlace troncal de subida hacia el Switch Core |
| Switch_Servidores2 (Disponibilidad2) | Fa0/3 | Access | VLAN 47 (Servidores) | Conexión hacia Server3 |
| Switch_Servidores2 (Disponibilidad2) | Fa0/4 | Access | VLAN 47 (Servidores) | Conexión hacia Server4 |
| Switch_Corporativo | Fa0/1 | Trunk | VLAN 97,17,57 | Enlace troncal hacia el switch del centro de datos |
| Switch_Corporativo | Fa0/2 | Trunk | VLAN 97,17 | Enlace troncal Del switch de distribucion al switch de gerencia|
| Switch_Corporativo | Fa0/3 | Trunk | VLAN 97,57 | Enlace troncal Del switch de distribucion al switch de visitas|
| Switch_Gerencia | Fa0/3 | Access | VLAN 17 (Gerencia) | Conexión hacia PC_Gerente3 |
| Switch_Gerencia | Fa0/4 | Access | VLAN 17 (Gerencia) | Conexión hacia PC_Gerente2 |
| Switch_Gerencia | Fa0/5 | Access | VLAN 17 (Gerencia) | Conexión hacia PC_Gerente1 |
| Switch_Visitantes | Fa0/1 | Trunk | VLAN 97, 57 | Enlace troncal hacia el switch de distribución corporativo |
| Switch_Visitantes | Fa0/3 | Access | VLAN 57 (Visitantes) | Conexión hacia el Access Point inalámbrico |
| Switches I+D (ID_1, ID_3, etc.) | Fa0/1 - Fa0/2 | Trunk | VLAN 97 (Nativa) | Enlaces troncales del anillo de I+D y hacia el Core |
| Switches I+D (ID_1, ID_3, etc.) | Fa0/5 en adelante | Access | VLAN 27 (Investigacion) | Conexión hacia las Laptops/PCs de I+D |
| Switch_Produccion | Fa0/1 | Trunk | VLAN 97, 37 | Enlace troncal de subida hacia el Switch Core |
| Switch_Produccion | Fa0/2 | Access | VLAN 37 (Produccion) | — |



#  Justificación del switch servidor para VTP. 

Se configuró el switch central Switch Core como **Servidor VTP** para centralizar la administración de toda la base de datos de VLANs de la red. Se realizo en el switch central de el area de "Centro de datos" por que es la que esta mas cercana al alcance de los servidores. y puede tener una distribucion adecuada a cada uno de los edificios

![Estado VTP Server](/Imagenes/Justif_vtp.png)

# Justificación del root bridge para cada VLAN.
Evidentemente Se configuró  al **Switch Core** como el **Root Bridge** principal para todas las VLANs activas de la topología bajo el protocolo **Rapid-PVST** 
Debido a que este switch para mi topologia es el nucleo de todo.

![Spanning17](/Imagenes/Spanning_17.png)
Con el comando "show spanning-tree vlan 17" el cual tiene su unica salida al switch de corporativo

![Spanning57](/Imagenes/Spanning_57.png)
Con el comando "show spanning-tree vlan 57" que al igual que la vlan 17 tiene su unica salida al switch de corporativo

![Spanning27](/Imagenes/Spanning_27.png)
Con el comando "show spanning-tree vlan 27" el cual tiene 3 salidas mediante los port-channel 1, 2, 3 respectivamente hacia los switch ID_1 ID_2 ID3

![Spanning37](/Imagenes/Spanning_37.png)
Con el comando "show spanning-tree vlan 37" El cual tiene su unica salida hacia el switch del edificio "Planta de produccion"

![Spanning47](/Imagenes/Spanning_47.png)
Con el comando "show spanning-tree vlan 47" El cual tiene dos salidas hacia dos port-channel los cuales serian el po4 y po5 que van a los switch de recurrencia de el area de servidores

![Spanning97](/Imagenes/Spanning_97.png)
Con el comando "show spanning-tree vlan 97" La cual identificamos como vlan nativa presente en todas las salidas de nuestro root bridge


# Justificación de los Etherchannel utilizados
En este caso utilice 5 etherchannel
Los primeros 3 se utilizaron para manejar la alta carga de informacion que requiere el edificio de Investigacion y desarrollo por lo cual ahi usamos po1 po2 y po3. Luego en el area de servidores donde debe haber concurrencia se utilizaron otros dos etherchannel debido a que por ser el area de servidores necesitamos mas capacidad de mover informacion por lo cual se utilizaron po4 y po5 respectivamente para cada switch.


![Etherchannel](/Imagenes/Etherchannel.png)

---

# Comandos


- show spanning-tree 
(Mostrados anteriormente uno a uno que es muy largo todo junto)


- show etherchannel summary 
![Etherchannel](/Imagenes/Etherchannel.png)



- show interfaces trunk 
![InterfacesTrunk](/Imagenes/InterfacesTrunk.png)

- show vtp status (Servidor)
![vtpstatus](/Imagenes/vtp_status.png)



# Presupuesto Estimado de Infraestructura Física (SmartCity Tech Park)


## Justificacion
Para la elaboracion de esta topologia se decide utilizar un aproximado de 500 metros de cable de tipo "Fibra optica" Esta seria utilziara en todas aquellas conexiones que sean entre edificios y hacia los servidores es decir. Desde nuestro nucleo que es el switch central de "Centro de datos" las conexiones Etherchannel hacia ID, las conexiones hacia Etherchannel hacia los servidores, y las conexiones hacia los edificios seran de fibra optica. 


Tambien Entre los switches de cada uno de los departamentos como la interconexion de tipo malla en los Switches ID_1 ID_2 ID_3 contaran con fibra optica para mayor capacidad de paso de informacion

Ahora bien para aquellas conexiones que son desde los swiches de distribucion hacia los dispositivos (PC) esos seran de tipo cable UTP debido a que no se necesita una cantidad exagerada de velocidad de transmision de informacion. Para ellos se compraran 2 cajas de cable UPT con el estandar Cat6 para dichas conexiones



| Cantidad | Componente / Descripción | Especificación Técnica | Precio Unitario (GTQ) | Total (GTQ) |
|----------|---------------------------|-------------------------|------------------------|--------------|
| 1 | Switch Administrable Core | Cisco Catalyst 3650 (24 puertos Gigabit) para el Centro de Datos | Q17,500.00 | Q17,500.00 |
| 9 | Switches de Acceso / Distribución | Cisco Catalyst 2960-24TT (24 puertos FastEthernet/Gigabit uplink) | Q6,000.00 | Q54,000.00 |
| 24 | Módulos Transceptores SFP | Módulos SFP 1000BASE-SX/LX para enlaces de fibra entre switches y EtherChannels | Q900.00 | Q21,600.00 |
| 1 | Access Point Inalámbrico | Cisco Business Wireless AP (para la red de visitantes en Corporativo) | Q1,800.00 | Q1,800.00 |
| 1 | Hub Ethernet Legacy | Hub de 8 puertos 10/100 Mbps (Planta de Producción) | Q450.00 | Q450.00 |
| 500 m | Cable de Fibra Óptica Exterior | Bobina de Fibra Óptica Monomodo/Multimodo (considerando tramos de 30-50m entre edificios + margen de curvatura) | Q12.00 /m | Q6,000.00 |
| 2 cajas | Cable UTP Cat 6 (305 m c/u) | Cable UTP 100% cobre para conexión de computadoras, servidores y APs | Q1,350.00 /caja | Q2,700.00 |
| Global | Materiales de Conectividad | Conectores RJ45, Jacks Keystone Cat 6, Patch Cords y canaletas plásticas | Q2,500.00 | Q2,500.00 |
| **TOTAL** | **Inversión Estimada del Proyecto** | | | **Q106,550.00** |


---
Topologia con etiquetas de conexion
![Etiquetado](/Imagenes/Etiquetado.png)


# Tipologia completa y por area
Tipologia limpia (Sin etiquetas)
![Etiquetado](/Imagenes/Topologia.png)
---
Centro de datos
![CentroDatos](/Imagenes/CentroDatos.png)
---
Centro de Investigacion y Desarrollo
![ID](/Imagenes/Centro_ID.png)
---
Edificio Corporativo
![Corporativo](/Imagenes/Corporativo.png)
---
planta de produccion
![Corporativo](/Imagenes/Produccion.png)
---

