# Comandos importantes
- Por si use "clear" (Control+Shift+6)
- Para ver modo stp = rapid-pvst (show spanning-tree)
- Para ver los trunk (show interfaces trunk)






# Parametros a seguir

| Parámetro / Protocolo | Valor Asignado |
|-----------------------|----------------|
| VTP Domain | Smart_5 |
| VTP Password | proyecto12S2026 |
| VLAN Nativa | VLAN 97 |
| Protocolo EtherChannel | PAgP (Modo desirable) |
| Protocolo Spanning-Tree | Rapid-PVST (rapid-pvst) |
| Banner MOTD | Acceso Restringido - TechPark_202200057 |

# Configuracion de Vlans

| VLAN | Nombre | Descripción |
|------|--------|-------------|
| 17 | GERENCIA | Edificio Corporativo (Ala 1) |
| 27 | INVESTIGACION | Centro de I+D |
| 37 | PRODUCCION | Planta de Producción (Hub Legacy) |
| 47 | SERVIDORES | Centro de Datos |
| 57 | VISITANTES | Edificio Corporativo (Ala 2 / AP) |


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

- Verificar con "show etherchannel summary"
- Verificar con "show interfaces trunk"
- Verificar con "show spanning-tree summary"
```

## pendiente ---------------------!"""#"""$#"
```cisco
configure terminal
interface range Port-channel 1 - 3
switchport trunk allowed vlan 17,27,37,47,57,97
end

