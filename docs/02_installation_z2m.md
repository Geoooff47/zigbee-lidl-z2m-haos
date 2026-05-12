# 02 — Installation Zigbee2MQTT sur HAOS

## Prérequis

- Home Assistant OS opérationnel
- Passerelle Lidl HG06668 flashée et accessible via TCP (voir `01_flash_passerelle.md`)
- IP fixe assignée à la passerelle dans le DHCP du routeur

---

## Étape 1 — Installer Mosquitto Broker

Z2M communique avec Home Assistant via MQTT. Mosquitto est le broker requis.

**Paramètres → Add-ons → Boutique d'add-ons → "Mosquitto broker"**

→ **Installer** → **Démarrer** → activer **Démarrage automatique**

Aucune configuration particulière n'est nécessaire par défaut.

---

## Étape 2 — Ajouter le dépôt Zigbee2MQTT

L'add-on Z2M n'est pas dans la boutique officielle par défaut.

**Paramètres → Add-ons → Boutique d'add-ons → ⋮ (menu) → Dépôts**

Ajouter :
```
https://github.com/zigbee2mqtt/hassio-zigbee2mqtt
```

Rafraîchir la page, chercher **Zigbee2MQTT** → **Installer**.

---

## Étape 3 — Configurer Z2M

Avant le premier démarrage, aller dans l'onglet **Configuration** de l'add-on.

### ✅ Configuration validée en production

```yaml
serial:
  port: 'tcp://192.168.2.224:8888'
  adapter: ezsp

mqtt:
  server: mqtt://core-mosquitto

homeassistant:
  enabled: true

frontend:
  enabled: true
  port: 8099
```

> **IP passerelle Lidl HG06668 : `192.168.2.224`**  
> **Port TCP Tasmota : `8888`**  
> ⚠️ `adapter: ezsp` — PAS `zstack` (la puce interne est EZSP, pas CC2530 Z-Stack)

### Paramètres expliqués

| Paramètre | Valeur | Explication |
|---|---|---|
| `serial.port` | `tcp://192.168.2.224:8888` | Coordinateur exposé via socket TCP par Tasmota |
| `serial.adapter` | `ezsp` | Type de puce réel détecté : EZSP v7 |
| `mqtt.server` | `mqtt://core-mosquitto` | Broker Mosquitto interne HAOS |
| `homeassistant.enabled` | `true` | Découverte automatique des appareils Zigbee dans HA |
| `frontend.port` | `8099` | Interface web Z2M : `http://IP-HAOS:8099` |

---

## Coordinator détecté (info réelle)

```
type: EZSP v7
firmware: 6.5.0.0 build 188
IEEE: 0x60a423fffe15d0b6
```

---

## Étape 4 — Démarrer Z2M

**Démarrer l'add-on** → consulter les **Logs**.

### Logs attendus au démarrage nominal

```
zh:ezsp:znp: Socket connected
zh:ezsp:znp: Socket ready
z2m: zigbee-herdsman started
z2m: Coordinator firmware version: EZSP v7 6.5.0.0 build 188
z2m: Connected to MQTT server
z2m: Started frontend on port 8099
z2m: Zigbee2MQTT started!
```

---

## Étape 5 — Appairage d'un appareil Zigbee

1. Ouvrir l'interface Z2M : `http://IP-HAOS:8099`
2. Cliquer sur **Permit join (All)** → le réseau est ouvert 3 minutes
3. Mettre l'appareil Zigbee en mode appairage (reset long ou bouton dédié)
4. L'appareil apparaît dans Z2M et est automatiquement découvert dans HA

---

## Intégration Home Assistant

Les entités créées par Z2M apparaissent dans :

**Paramètres → Appareils et services → Zigbee2MQTT**

Chaque appareil Zigbee appairé crée automatiquement ses entités (capteurs, interrupteurs, etc.).

---

## Notes importantes

- La passerelle a l'IP `192.168.2.224` — **assigner un bail DHCP statique** par MAC dans le routeur
- Le port `8888` est le défaut Tasmota ; vérifier dans la console Tasmota avec `Status 13`
- L'interface frontend Z2M est accessible même sans passer par HA (utile pour debug)
- Mosquitto doit être démarré avant Z2M (géré automatiquement par HAOS)
- Le warning `ezsp deprecated` dans les logs est normal pour ce firmware 6.5.x — fonctionnel
