# Sieci ćw 71

Ogulna uwaga do pliku, ostrożnie z nazwami interfejsów i adresami IP <br>
Dodatkowo:
- sprawdź se, czy `sh` to skrut od `show`, czy `shutdown`
- uwarzać na IP
- Zaleca się nie korzystać z seriali\

## Zadanie A

### 0.
Skonfigurować router<br>
Uwaga, ip i nazwa porut są przykłądowe
```
Router(config)#interface FastEthernet 0/1 
Router(config-if)#ip address 200.200.200.0 255.255.255.0
Router(config-if)#no shutdown 
```
dla drugiego routera 200.200.201.0

### 1. Wpsęona konfiguracja
Spiąć dwa rutery przez Ethernet lub Serial. <br>
Zdefiniować po 2 loopbacki na karzdym <br>
Uwaga!! adresy dla loopback muszą być unikalne w skali instalacji<br>

Uwaga, poniższe w trybie ```Router(config)#```, adresy ip są przykłądowe i nie koniecznie poprawne <br>
#### Todo: sprawdź te loopbacki
Pierwszy router
```
interface Loopback 1
ip address 10.0.1.1 255.255.255.0
no shutdown 

interface Loopback 2
ip address 10.0.2.2 255.255.255.0
no shutdown 
```

Drugi router

```
interface Loopback 3
ip address 10.0.3.3 255.255.255.0
no shutdown 

interface Loopback 4
ip address 10.0.4.4 255.255.255.0
no shutdown 
```

### 2. Ping
```
Router#ping 200.200.200.1 source 100.100.100.1 
```
#### Uwaga! 
 100.100.100.1 to adres IP interfejsu loopback, a 200.200.200.1 to adres hosta sprawdzanego. Naturalnie wywoływanie tej komendy będzie sensowne dopiero po skonfigurowaniu procesów rutowania dynamicznego, co zostanie przeprowadzone w kolejnych punktach. 
 
### 3. Włączenie routowania IP
```Router(config)#```
```
ip routing
ip classless
```

### 4. Routowanie OSPF

Rejestrowanie sieci bezpośrednio połącznonych
```
Router(config)#router ospf 150 
Router(config-router)#network 200.200.200.0 0.0.0.255 area 0 
Router(config-router)#network 200.200.201.0 0.0.0.255 area 0 
```

Sprawdzenie stanu OSPF
```Router#```
```
show ip protocols 
show ip interface brief 
show ip ospf interface fa 0/0 
```
Uwaga! W przypadku sieci NBMA pojawiają się dodatkowe problemy, więcej w instrukcji

Sprawdzenie pingowania:
```
Router#sh ip ospf interface fa 0/0 
Router#sh ip ospf neighbor
```

Próba wymuszenia zmiany i sprawdzenie priorytetu
```
Router(conf)#int fa 0/0 
Router(config-int)#ip ospf priority 0 
Router#show ip ospf interface //lub show ip ospf neighbor
```

Uwaga: w przypadku sieci point-to-point lub point-to-multipoint DR oraz BDR nie występują (nie ma sensu ich definiować). Sytuacja ta jest oznaczona symbolem "-" opisującym tryb pracy sąsiada OSPF.

Obserwowanie działania pakietów "hello" w OSPF
```
Router#debug ip ospf adjacency 
Router#sh ip ospf neighbor 
```

Zmiana interwału "hello"
#### Todo: brakuje ci tu przejścia do interfejsu (config-if)
```
Router(config-if)#ip ospf hello-interval 15 
```
Eksperymentalna zmiana kosztu połączenia między routerami
```
Router(config)#interface fa 0/0 
Router(config-if)#ip ospf cost 35
```

Na koniec debugowanie o ogulna dioagnostyka
```Router#```
```
debug ip ospf events 
show ip route 
show ip ospf interface 
```

### 5. W durzym skrucie tworzenie Virtual link

#### Todo: Przebuduj segmentację area OSPF tak, aby była zgodna z poniższym rysunkiem(należy ponownie zadeklarować sieci OSPF usuwając wcześniej z konfiguracji poprzednie deklaracje).
Przykład
#### Todo: przeczytaj Uwagę zanim przekleisz, albo popraw PDFa
```
RuterABR1(config)#router ospf 150 
RuterABR1(config-router)# area 1 virtual-link 5.5.5.5 
RuterABR2(config)#router ospf 150 
RuterABR2(config-router)# area 1 virtual-link 6.6.6.6
```
#### Uwaga
5.5.5.5 i 6.6.6.6 to nie adresy IP, lecz tzw. router ID odpowiednio ruterów R1 i R2, które są generowane przez proces OSPF (wyglądają tak samo jak adresy IP). Wartość router ID w domenie OSPF musi być unikatowa i jest najczęściej (domyślnie) obierana jako kopia adresu IP fizycznego interfejsu OSPF rutera o najwyższej numerycznej wartości. Wartość router ID trzeba sprawdzić komendą:
```
Router#sh ip ospf interface
```

Sprawdzenie konfiguracji
```
RuterABR1#show ip ospf virtual-links 
```

Dodawanie AREA z loopback i włączenie go w OSPF w AREA 1
```Router(config)```
```
Router(config)#int loopback 5 
Router(config-if)#ip addr 200.200.101.1 255.255.255.0 
Router(config-if)#exit 
Router(config)#router ospf 150 
Router(config-router)#network 200.200.101.0 0.0.0.255 area 1
```

Po odczekaniu okresu negocjacji protokołu rutowania należy sprawdzić zawartość tablicy rutowania rutera w area 0. Czy otrzymał on informację o 200.200.101.0 i jaką? <br>
Inna diagnostyka: 
```Router(config)#```
```
show ip ospf border-routers 
show ip ospf virtual-links 
```
można by spróbować
```
Router#show ip ospf interface 
```
## Zadanie B

### 1. Będziemy testować 2 protokoły routowania naraz

### 2.
Należy zbudować instalację składającą się z trzech ruterów (R1, R2, R3) oraz 
dwóch stacji PC (lub interfejsów loopback ruterów)

### 3. Adresy IP zgodnie z regułami  i od razu routowanie dynamiczne
IP, więc mogłem się pomylić
```Router(config)#```
Pierwszy router
```
interface FastEthernet 0/1 
ip address 200.200.201.1 255.255.255.0
no shutdown 
ip routing
ip classless
```
Drugi router
```
interface FastEthernet 0/1 
ip address 200.200.202.1 255.255.255.0
no shutdown 
ip routing
ip classless
```
Trzeci router
```
interface FastEthernet 0/1 
ip address 200.200.203.1 255.255.255.0
no shutdown 
ip routing
ip classless
```
### 4. i 5. Protokoły 
router 1
```
Router(config)#router rip 170 
Router(config-router)#network 200.200.200.0 0.0.0.255 area 0 
Router(config-router)#network 200.200.201.0 0.0.0.255 area 0 
```
router 3 !!!
```
Router(config)#router ospf 150 
Router(config-router)#network 200.200.203.0 0.0.0.255 area 0 
Router(config-router)#network 200.200.202.0 0.0.0.255 area 0 
```

### 6. Sprawdzenie tablic routowania
```
Router(config)# show ip route
Router(config)# show ip interface brief
```
???
 
### 7. Router 2 Konfigurowanie redystrybucji w kierunku OSPF -> RIP
router 2
```
R2(config)#router ospf 150 
R2(config-router)# redistribute rip metric 170 subnets 
```

Sprawdzenie
```
R1#show ip route
```

### 8. Router 2 Konfigurowanie redystrybucji w kierunku RIP -> OSPF
```
R2(config)#router rip 
R2(config-router)#redistribute ospf 150 metric 11 
```
Sprawdzenie
```
R1#show ip route
```

### 9. 
Usuń z ruterów OSPF i uruchom w jego miejscu EIGRP. Po skonfigurowaniu EIGRP spróbuj analogicznie jak w poprzednim punkcje uruchomić i przetestować mechanizm redystrybucji tras na linii RIP <-> EIGRP.
