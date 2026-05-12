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

### Configuration minimale

```yaml
serial:
  port: 'tcp://192.168.2.224:8888'
  adapter: zstack

mqtt:
  server: mqtt://core-mosquitto

homeassistant:
  enabled: true

frontend:
  enabled: true
  port: 8099
```

> **IP de la passerelle Lidl HG06668 (flashée Tasmota) : `192.168.2.224`**  
> Port TCP par défaut Tasmota : `8888`

### Paramètres expliqués

| Paramètre | Valeur | Explication |
|---|---|---|
| `serial.port` | `tcp://192.168.2.224:8888` | Coordinateur CC2530 exposé via socket TCP par Tasmota |
| `serial.adapter` | `zstack` | Type de puce : CC2530 = Z-Stack |
| `mqtt.server` | `mqtt://core-mosquitto` | Broker Mosquitto interne HAOS |
| `homeassistant.enabled` | `true` | Découverte automatique des appareils Zigbee dans HA |
| `frontend.port` | `8099` | Interface web Z2M accessible sur `http://IP-HA:8099` |

---

## Étape 4 — Démarrer Z2M

**Démarrer l'add-on** → consulter les **Logs**.

### Logs attendus au démarrage nominal

```
[Z2M] Starting Zigbee2MQTT...
[Z2M] Connected to MQTT server
[Z2M] Coordinator firmware version: {"type":"zStack12","meta":{"revision":...}}
[Z2M] Started successfully
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

- La passerelle a l'IP `192.168.2.224` — **assigner un bail DHCP statique** par MAC dans le routeur pour que ça ne change pas
- Le port `8888` est le défaut Tasmota ; vérifier dans la console Tasmota avec `Status 13`
- L'interface frontend Z2M est accessible même sans passer par HA (utile pour debug)
- Mosquitto doit être **démarré avant** Z2M au boot (l'ordre est géré automatiquement par HAOS)
