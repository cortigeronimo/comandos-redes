# Comandos-redes

## A tener en cuenta
- PC - Switch(consola) => (Cable consola) (RS232 - Console)
- PC - Switch => Cable directo (fastEthernet - fastEthernet)
- Switch - Switch => Cable cruzado (última fastEthernet - última fastEthernet)
- PC - Router => Cable cruzado
- Switch - Router => Cable directo
- Router - Router (WAN) => Cable DCE (Serialx/0) (No importa quién tiene el reloj, que es el encargado de enviar el bit de sincronismo, a efectos académicos da igual)
- En redes WLAN, se utilizan cables Serial DTE para la conexión entre routers. Dentro de la LAN local, se utiliza fastEthernet para la conexión entre Router y Switch.
- Regla verga: si son dispositivos iguales, cruzado, si son distintos, derecho.

Tabla de la verdad:

Cable Cruzado:
- Router a Router
- HUB a HUB
- Switch a Switch
- PC a PC
- Router a PC

Cable Directo:
- Router con Switch
- Router con HUB
- HUB con Switch
- HUB con PC
- Switch con PC

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

- `show mac-address-table` => muestra las direcciones mac aprendidas por un switch

## Modo Configuración global

- `hostname $name` => asignar nombre al switch
- `enable secret $password` => configurar contraseña para el modo configuración global

### Crear Vlan
- `vlan $numero` => crea una vlan con un identificador
- `name $nombre` => le pone nombre a la vlan

### Telnet o ssh son protocolos de capa 3, antes de usarse, tiene que configurarse los dispositivos en capa 3
- `line vty 0 1` => habilita las líneas virtuales 0 y 1. Cuántos usuarios
- `login` => forzas a que te pida logearte cuando queres ingresar 
- `password $password`
- `exec-timeout $minutos`

### Asignacion de puerto a una vlan
- `interface f0/x`
- `switchport access vlan $numero` => asigna el puerto a la vlan

### Configurar de troncales
- `interface f0/x` => la que conecta a los switches entre si
- `switchport mode trunk` => configura el puerto para que envie tráfico en todas las vlans. Pone la interface en modo permanente trunking y negocia convertir el enlace en un enlace troncal. La interface llega a ser una interface troncal aún si la interface vecina no es una interface troncal.

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
- `interface x0/0`
- `switchport mode access` => coloca la interface en un modo permanente nontrunking y negocia convertir el enlace en un enlace no troncal. La interface llega a ser una interface non trunk aún si la interface vecina no es una interface troncal.
- `switchport port-security` => lo prende
- `switchport port-security maximum 1` => cantidad de macs permitidas
- `switchport port-security mac-address xxxx.xxxx.xxxx.xxxx` => configurar las macs específicas
- `switchport port-security violation shutdown` => en caso de violación de seguridad, apaga el puerto.

### Configurar una ip para una interfaz
- `interface x0/0`
- `ip address xxx.xxx.xxx.xxx yyy.yyy.yyy.yyy` (ip and mask)
- `no shutdown`

### Configurar enrutamiento dinámico con RIP
- `router rip|eigrp|ospf` => entras al modo de configuracion de routeo rip
- `version 2` => es la versión mejora de RIP (solo para RIP)
- `network 192.168.1.0` => este comando se ejecuta "n" veces, siendo "n" la cantidad de redes que tengo conectada DIRECTAMENTE a mi router
- `exit`

### Configurar enrutamiento dinámico con OSPF
- `router ospf $id` => entras al modo de configuracion de routeo ospf, hay que especificar un identificador
- `network x.x.x.x y.y.y.y area 0` => este comando se ejecuta "n" veces, siendo "n" la cantidad de redes que tengo conectada DIRECTAMENTE a mi router. x.x.x.x es la dirección ip e y.y.y.y es la wildcard, y especifica los 1 que se refieren a host. Si es /24 será 0.0.0.255 (al revés que la máscara).Una máscara wildcard es una máscara de bits que indica qué partes de una dirección de IP son relevantes para la ejecución de una determinada acción.
- `exit`

### Configurar enrutamiento dinámico con IGRP
- `router igrp $id` => entras al modo de configuracion de routeo igrp, hay que especificar un identificador
- `network x.x.x.x` => especificar la red que esta directamente conectada
- `exit`


### Autenticar cuando alguien se va a conectar automaticamente por telnet por ejemplo
- `interface serialX/X`
- `encapsulation ppp` => es el modo con el que encapsulan los cables seriales (solo utilizar para seriales, tiene que estar configurado en ambos extremos )

### Configuración estática de próximo salto
- `ip route xxxx.xxxx.xxxx.xxxx yyyy.yyyy.yyyy.yyyy zzzz.zzzz.zzzz.zzzz` => donde xxxx.xxxx.xxxx.xxxx es la IP de la red externa, yyyy.yyyy.yyyy.yyyy es la máscara de la red externa y zzzz.zzzz.zzzz.zzzz es la ip del próximo salto. También, en vez de especificar la ip del próximo router, se puede especificar la interfaz por donde tiene que salir la trama (es lo mismo).
- `ip route 0.0.0.0 0.0.0.0 zzzz.zzzz.zzzz.zzzz` => estoy diciendo que absolutamente todas las redes están para el lado del próximo salto zzzz.zzzz.zzzz.zzzz

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

### Creación de una ACL estándar (permite o no que X dispositivo se conecte a otro)
- `access-list Nro_ACL permit|deny 192.168.1.0 0.0.0.127` => Nro_Acl va de 1 a 99 y de 1300 a 1999
- `interface x0/0`
- `ip access-group Nro_ACL in|out` => es una buen a práctica poner "in" cuando es standard

### Creación de una ACL extendida (usar en caso que nos pidan más cosas que denegar/permitir)
- `access-list Nro_ACL permit|deny ip Origen Destino [Operador Nro_puerto] [established] [echo | echo-reply]` => Nro_ACL va de 100 a 199 y 2000 a 2699. Ejemplo: - `access-list Nro_ACL permit|deny ip xxxx.xxxx.xxxx.xxxx $wildcard yyyy.yyyy.yyyy.yyyy $wildcard` esto se de forma viceversa (según corresponda)
- `interface x0/0`
- `ip access-group Nro_ACL [in|out]` => es una buen a práctica poner "out" cuando es extendida

### Crear subinterface
- `interface x0/0.vlan_number` 
- `encapsulation dot1Q vlan_number`
- `ip address xxxx.xxxx.xxxx.xxxx yyyy.yyyyy.yyyy.yyyy` => xxxx.xxxx.xxxx.xxxx ip de default gateway y yyyy.yyyyy.yyyy.yyyy la máscara

### Activar el ACL
- `interface f0/0`
- `ip access-group 1 in`

### Configuración de IKE (primera fase de negociación segura)
- `configure terminal`
- `crypto isakmp policy 10` => política criptográfica (método de distribución de claves)
- `encr AES` => algoritmo de cifrado
- `authentication pre-share` => método de autenticación
- `group 5` => (Diffie-Hellman grupo 5 – clave de 1536 bits)
- `lifetime 3600` =>  (tiempo de vida en segundos) 
PD: para el router extremo es exactamente lo mismo

### Definición de clave simétrica en el otro extremo del túnel
`crypto isakmp key cisco address X.X.X.X` => cisco es la clave criptográfica y X.X.X.X es la ip del router contrario con el que quiero establecer la comunicación (la pública)
PD: es en ambos routers

### Configuración IPSec modo túnel (segunda fase de negociación segura)
`crypto ipsec transform-set MYTSETNAME esp-aes 256 esp-shahmac mode tunnel` => MYSETNAME es un número

### Configurar el mapa que determina la IP del extremo remoto del túnel y el tráfico de interés que será encapsulado.
`crypto map LEFTY_TO_RIGHTY 10 ipsec-isakmp set peer X.X.X.X match address 101 set transform-set MYTSETNAME` => donde X.X.X.X es la ip del router extremo (la pública)

### Activar el túnel (en el router donde sale la data, interfaz pública)
- `int fastEthernet 0/0` 
- `crypto map LEFTY_TO_RIGHTY`

## Todos los modos
- `exit` => volver un modo hacia atrás
- `ctrl + shift + 6` => abortar comando



router eigrp 99
 network 55.0.0.0
 network 30.0.0.0
 auto-summary
!
router rip
 version 2
 redistribute eigrp 99 
 network 30.0.0.0
 network 55.0.0.0
