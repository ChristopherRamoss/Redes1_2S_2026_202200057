

## CONFIGURACION DE MODO ACCESO 

- Switch> enable
- Switch# configure terminal (enter)


###  Configurar hacia MERCA
- Switch(config)# interface fa0/1
- Switch(config)# switchport mode trunk
- Switch(config)# exit

### Configurar hacia ADMIN
- Switch(config)# interface fa0/2
- Switch(config)# switchport mode trunk
- Switch(config)# exit

###  Configurar hacia VENTAS
- Switch(config)# interface fa0/3
- Switch(config)# switchport mode trunk
- Switch(config)# exit

###  Verificar el modo trunk
- Switch#show interfaces trunk


## CONFIGURACION VTP SERVER (Desde Swich0)
- Switch>enable
- Switch#configure terminal (enter)
- Switch(config)#vtp domain USAC       
- Switch(config)#vtp password redes
- Switch(config)#vtp mode server

## CONFIGURACION VTP CLIENTE (Desde ADMIN Y MERCA )
- Switch>enable
- Switch#configure terminal (enter)
- Switch(config)#vtp domain USAC       
- Switch(config)#vtp password redes
- Switch(config)#vtp mode client

## CONFIGURACION VTP TRANSPARENT (Desde VENTAS )
- Switch>enable
- Switch#configure terminal (enter)
- Switch(config)#vtp domain USAC       
- Switch(config)#vtp password redes
- Switch(config)#vtp mode trasnparent

---
### Verificar la configuracion vtp
- Switch(config)# exit
- Switch# show vtp status

   -  "VTP Operating Mode                : Server"
--- 


# CREACION DE VLANS

desde el swich0 

- Switch>enable
- Switch#configure terminal (enter)
---
- Switch(config)#vlan 10
- Switch(config-vlan)#name ADMIN
- Switch(config-vlan)#exit
---
- Switch(config)#vlan 20
- Switch(config-vlan)#name MERCA
- Switch(config-vlan)#exit
---
- Switch(config)#vlan 30
- Switch(config-vlan)#name VENTAS
- Switch(config-vlan)#exit

### Verificar vlans 
- Switch# show vlan brief


## CONFIGURAR LAS IP 

-- PEGAR LA IMAGEN

## CONFIGURAR VLAN SWITCH - PC

### Configuracion ADMIN
- Switch#configure terminal (enter)
- Switch(config)#interface fa0/2 
- Switch(config-if)#switchport mode access
- Switch(config-if)#switchport access vlan 10
- Switch(config-if)#exit

----
- Switch#configure terminal (enter)
- Switch(config)#interface fa0/3
- Switch(config-if)#switchport mode access
- Switch(config-if)#switchport access vlan 10
- Switch(config-if)#exit
---
Verificar que esten configuradas las vlan 
- Switch#show vlan brief

   - "10   ADMIN                            active    Fa0/2, Fa0/3" 

-----
### Configuracion MERCA
- Switch#configure terminal (enter)
- Switch(config)#interface fa0/2 
- Switch(config-if)#switchport mode access
- Switch(config-if)#switchport access vlan 20
- Switch(config-if)#exit

----
- Switch#configure terminal (enter)
- Switch(config)#interface fa0/3
- Switch(config-if)#switchport mode access
- Switch(config-if)#switchport access vlan 20
- Switch(config-if)#exit
---
Verificar que esten configuradas las vlan 
- Switch#show vlan brief

   - "20   MERCA                            active    Fa0/2, Fa0/3

   -----
### Configuracion VENTAS
- Switch#configure terminal (enter)
- Switch(config)#interface fa0/2 
- Switch(config-if)#switchport mode access
- Switch(config-if)#switchport access vlan 30
- Switch(config-if)#exit

----
- Switch#configure terminal (enter)
- Switch(config)#interface fa0/3
- Switch(config-if)#switchport mode access
- Switch(config-if)#switchport access vlan 30
- Switch(config-if)#exit
---
Verificar que esten configuradas las vlan 
- Switch#show vlan brief

   - "30   VENTAS                           active    Fa0/2, Fa0/3"
