# Zigbee Gateway Lidl → Z2M → HAOS

Documentation technique complète pour :
1. **Flasher** la passerelle Zigbee Lidl SilverCrest HG06668 en coordinateur réseau TCP
2. **Intégrer** ce coordinateur dans Zigbee2MQTT sur Home Assistant OS

---

## ✅ Statut : validé en production

| Élément | Valeur |
|---|---|
| Passerelle | Lidl SilverCrest HG06668 |
| Firmware | Tasmota (EZSP v7) |
| IP | `192.168.2.224` |
| Port TCP | `8888` |
| Adapter Z2M | `ezsp` |
| Z2M version | `2.10.1` |
| Coordinator firmware | `6.5.0.0 build 188` |

> ⚠️ La puce n'est **pas** un CC2530 Z-Stack comme indiqué sur certaines sources — c'est **EZSP v7** (Silicon Labs). Utiliser `adapter: ezsp` et non `zstack`.

---

## Matériel concerné

| Produit | Référence | Puce Zigbee |
|---|---|---|
| Passerelle Zigbee Lidl SilverCrest | HG06668 | Silicon Labs EZSP v7 |

---

## Structure de la doc

```
docs/
├── 01_flash_passerelle.md   → Flash firmware Tasmota + gateway TCP
├── 02_installation_z2m.md   → Installation Z2M sur HAOS (config validée)
└── 03_troubleshooting.md    → Problèmes connus & solutions
```

---

## Prérequis

- Home Assistant OS installé (VM, NUC, RPi…)
- Accès réseau local à la passerelle Lidl flashée
- Add-on Mosquitto broker installé dans HAOS

---

## Liens utiles

- [Zigbee2MQTT officiel](https://www.zigbee2mqtt.io/)
- [Tasmota Zigbee](https://tasmota.github.io/docs/Zigbee/)
- [Repo add-on Z2M pour HAOS](https://github.com/zigbee2mqtt/hassio-zigbee2mqtt)
