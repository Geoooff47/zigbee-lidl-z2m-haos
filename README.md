# Zigbee Gateway Lidl → Z2M → HAOS

Documentation technique complète pour :
1. **Flasher** la passerelle Zigbee Lidl SilverCrest HG06668 en coordinateur réseau TCP
2. **Intégrer** ce coordinateur dans Zigbee2MQTT sur Home Assistant OS

---

## Matériel concerné

| Produit | Référence | Puce Zigbee |
|---|---|---|
| Passerelle Zigbee Lidl SilverCrest | HG06668 | Texas Instruments CC2530 |

---

## Structure de la doc

```
docs/
├── 01_flash_passerelle.md   → Flash firmware Tasmota + Z2M gateway
├── 02_installation_z2m.md   → Installation Z2M sur HAOS
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
