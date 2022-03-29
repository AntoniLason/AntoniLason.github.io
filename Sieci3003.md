# Sieci ćw 71
## Zadanie A

### 0.
Skonfigurować router<br>
Uwaga, ip i nazwa porut są przykłądowe
```
Router(config)#interface FastEthernet 0/1 
Router(config-if)#ip address 192.168.123.100 255.255.255.0
Router(config-if)#no shutdown 
```

### 1. Wpsęona konfiguracja
Spiąć dwa rutery przez Ethernet lub Serial. <br>
Zdefiniować po 2 loopbacki na karzdym <br>
Uwaga!! adresy dla loopback muszą być unikalne w skali instalacji<br>

Uwaga, poniższe w trybie ```Router(config)#```, adresy ip są przykłądowe i nie koniecznie poprawne <br>
Pierwszy router
```
interface Loopback 1
ip address 10.0.0.1 255.255.255.0
no shutdown 

interface Loopback 2
ip address 10.0.0.2 255.255.255.0
no shutdown 
```

Drugi router

```
interface Loopback 3
ip address 10.0.0.3 255.255.255.0
no shutdown 

interface Loopback 4
ip address 10.0.0.4 255.255.255.0
no shutdown 
```

### 2. Ping
```
Router#ping 200.200.200.1 source 100.100.100.1 
```
#### Uwaga! 
 100.100.100.1 to adres IP interfejsu loopback, a 200.200.200.1 to adres hosta sprawdzanego. Naturalnie wywoływanie tej komendy będzie sensowne dopiero po skonfigurowaniu procesów rutowania dynamicznego, co zostanie przeprowadzone w kolejnych punktach. 