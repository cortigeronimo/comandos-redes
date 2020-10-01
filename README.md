# Comandos-redes

## A tener en cuenta
- PC - Switch(consola) => (Cable consola) (RS232 - Console)
- PC - Switch => Cable directo (fastEthernet - fastEthernet)
- Switch - Switch => Cable cruzado (última fastEthernet - última fastEthernet)
- PC - Router => Cable cruzado
- Switch - Router => Cable directo
- Router - Router (WAN) => Cable DCE (Serialx/0)
- En redes WLAN, se utilizan cables Serial DTE para la conexión entre routers. Dentro de la LAN local, se utiliza fastEthernet para la conexión entre Router y Switch.

## Modo usuario
- `?` => ver comandos (en cualqueir modo de ejecución)
- `show ?` => listar todos los comandos show disponibles
- `enable` => modo privilegiado

## Modo privilegiado

- `show running-config` => mostrar la configuración en memoria RAM
- `show startup-config` => mostrar la configuración en memoria flash
- `show vlan` => muestra las vlans
- `show interface` => mostrar información sobre el estado de las interfaces
- `show spanning-tree`

- `configure terminal` => modo de configuración global

- `copy running-config startup-config` => copia configuracion de memoria ram a memoria flash

- `show ip route` => muestra la tabla de routeo

- `show ip protocols` => muestra el protocolo de enrutamiento

- `debug ip rip` => modo debug protocolo RIP

- `show access-lists`

## Modo Configuración global

- `hostname $name` => asignar nombre al switch
- `enable secret $password` => configurar contraseña para el modo configuración global

### Crear Vlan
- `vlan $numero` => crea una vlan con un identificador
- `name $nombre` => le pone nombre a la vlan

### Telnet o ssh son protocolos de capa 3, antes de usarse, tiene que configurarse los dispositivos en capa 3
- `line vty 0 1`
- `login`
- `password $password`
- `exec-timeout $minutos`

### Asignacion de puerto a una vlan
- `interface f0/x`
- `switchport access vlan $numero` => asigna el puerto a la vlan

### Configurar de troncales
- `interface f0/x` => la que conecta a los switches entre si
- `switchport mode trunk` => configura el puerto para que envie tráfico en todas las vlans

### Configurar STP para que nodo sea seleccionado raiz
- `spanning-tree vlan 1,10,20 root primary`

### Configurar agregado de enlace LACP
- `port-channel load-balance {dst-mac | src-mac}` => trafico de router a switch (una MAC origen a muchas MAC destino) DST-MAC, y tráfico de desde PC a SWITCH (muchas MAC origen a una destino) SRC-MAC
`interface gigabitethernet 1/1`
`switchport mode trunk`
`channel-protocol LACP`
`channel-group 1 mode active`
`exit`

- `interface f0/x` => entrar en modo configuracion de interfaz
- `interface range f0/x-y` => entrar en modo configuracion rango de interface
- `no shutdown` => apaga el puerto
- `shutdown` => enciendo el puerto

### Configuracion de seguridad de puerto por MAC
- `switchport mode access`
- `switchport port-security`
- `switchport port-security maximum 1`
- `switchport port-security mac-address xxxx.xxxx.xxxx.xxxx`
- `switchport port-security violation shutdown`

### Configurar una ip para una interfaz
- `interface x0/0`
- `ip address xxx.xxx.xxx.xxx yyy.yyy.yyy.yyy` (ip and mask)
- `no shutdown`

### Configurar enrutamiento dinámico con RIP
- `router rip`
- `version 2`
- `network 192.168.1.0`
- `exit`

### Desactivar tráfico generado por RIP
- `router rip`
- `passive-interface f0/0` => Con este comando a la interfaz marcada no se le envían las actualizaciones del RIP desde el Router. Se utiliza cuando en la interfaz marcada no hay otro Router conectados (hay una pc o un Switch).

### Redistribuir ips estáticas a los otros routers
- `router rip`
- `redistribute static`

### NO Sumarizar rutas
- `router rip`
- `no auto-summary` => Al ingresar este comando le decimos al Router que NO sumarice las rutas que tiene. Es de gran
utilidad cuando no tenemos redes contiguas

### Creación de una ACL estándar.
- `access-list 1 permit 192.168.1.0 0.0.0.127` 

### Activar el ACL
- `interface f0/0`
- `ip access-group 1 in`

## Todos los modos
- `exit` => volver un modo hacia atrás
- `ctrl + shift + 6` => abortar comando
