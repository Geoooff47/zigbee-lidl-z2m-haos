# 01 — Flash de la passerelle Lidl HG06668

## Matériel

- **Passerelle** : SilverCrest HG06668 (vendue chez Lidl)
- **Puce Zigbee** : Texas Instruments CC2530
- **Puce WiFi** : ESP8266 (flashable via OTA ou UART)
- **Firmware cible** : Tasmota + module Zigbee gateway (coordinateur réseau TCP)

---

## Principe

La passerelle Lidl contient deux puces :
- **ESP8266** : gère le WiFi et fait le pont série ↔ réseau
- **CC2530** : la vraie puce Zigbee, qui devient le coordinateur

Le flash consiste à :
1. Remplacer le firmware propriétaire de l'ESP8266 par **Tasmota**
2. Flasher le **firmware coordinator** sur le CC2530
3. Configurer Tasmota pour exposer le CC2530 via **TCP socket** sur le réseau local

---

## Étape 1 — Flash Tasmota sur l'ESP8266

### Option A : Flash OTA (si firmware d'origine encore présent)

Certaines versions du firmware Lidl autorisent un flash OTA via le portail Tuya.  
Utiliser [tuya-convert](https://github.com/ct-Open-Source/tuya-convert) :

```bash
git clone https://github.com/ct-Open-Source/tuya-convert
cd tuya-convert
./install_dependencies.sh
./start_flash.sh
```

> ⚠️ Tuya-convert ne fonctionne plus sur les appareils avec firmware récent (> 2021).  
> Si échec : passer en flash UART.

### Option B : Flash UART (méthode universelle)

**Matériel nécessaire** : adaptateur USB-UART (CH340, CP2102, FTDI)

**Câblage** (passerelle ouverte) :

| UART | ESP8266 |
|------|---------|
| TX   | RX      |
| RX   | TX      |
| GND  | GND     |
| 3.3V | 3.3V    |
| GND  | GPIO0 (mode flash) |

**Flash avec esptool** :

```bash
pip install esptool
esptool.py --port /dev/ttyUSB0 --baud 115200 write_flash -fs 1MB -fm dout 0x0 tasmota.bin
```

Télécharger `tasmota.bin` depuis [tasmota.github.io](https://tasmota.github.io/docs/Getting-Started/)

---

## Étape 2 — Flash du firmware coordinator sur le CC2530

Le CC2530 doit recevoir un firmware coordinator Z-Stack.

**Firmware à utiliser** :  
`CC2530_20211115.zip` — disponible sur le repo [Koenkk/Z-Stack-firmware](https://github.com/Koenkk/Z-Stack-firmware/tree/master/coordinator/Z-Stack_Home_1.2/bin/default)

**Flash via CCLoader ou CC Debugger** (si accès aux pins de debug du CC2530) ou via l'ESP8266 une fois Tasmota en place avec la commande `ZbFlash`.

---

## Étape 3 — Configuration Tasmota (passerelle réseau TCP)

Une fois Tasmota flashé et connecté au WiFi :

### Connexion WiFi initiale
- Tasmota crée un hotspot `tasmota-XXXXXX`
- Se connecter, aller sur `192.168.4.1`
- Renseigner le SSID et mot de passe du réseau local

### Configuration module

Dans l'interface Tasmota → **Console** :

```
# Définir le module comme passerelle Zigbee
Backlog Module 75

# Configurer le port série vers le CC2530
Backlog SerialLog 0; BaudRate 115200
```

### Activation du socket TCP

```
# Exposer le CC2530 sur le port TCP 8888
Backlog TCPStart 8888
```

### Vérification

Dans la console Tasmota, taper `Status 5` pour voir l'IP assignée.  
Le coordinateur est maintenant accessible via `tcp://192.168.X.X:8888`.

---

## Résultat attendu

```
tcp://192.168.X.X:8888  →  CC2530 coordinator Zigbee
```

Adapter type pour Z2M : **`zstack`**

---

## Points d'attention

- Assigner une **IP fixe** à la passerelle dans le DHCP du routeur (bail statique par MAC)
- Le port `8888` est le défaut Tasmota, vérifier dans la console si différent
- Ne pas couper l'alimentation pendant le flash du CC2530
