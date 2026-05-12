# 03 — Troubleshooting

## Z2M ne démarre pas

### Symptôme : `Error: Failed to connect to the adapter`

**Cause probable** : mauvaise IP ou port TCP incorrect.

**Vérifications** :
1. Pinger l'IP de la passerelle depuis le réseau : `ping 192.168.X.X`
2. Vérifier le port dans la console Tasmota : `Status 13`
3. Vérifier que TCPStart est bien actif : dans la console Tasmota taper `TCPStart 8888`
4. Corriger l'IP dans la config Z2M si nécessaire

---

### Symptôme : `Error: MQTT cannot connect`

**Cause probable** : Mosquitto pas démarré ou mauvais serveur MQTT.

**Vérifications** :
1. Vérifier que l'add-on Mosquitto est bien **démarré** dans HAOS
2. Vérifier que `mqtt.server` est bien `mqtt://core-mosquitto` (et non une IP externe)

---

### Symptôme : Z2M démarre mais aucun appareil n'est découvert dans HA

**Cause probable** : `homeassistant.enabled` absent ou `false` dans la config.

**Correction** :
```yaml
homeassistant:
  enabled: true
```
Redémarrer Z2M après modification.

---

## La passerelle n'est plus joignable après redémarrage du routeur

**Cause** : IP de la passerelle a changé (DHCP dynamique).

**Solution** : Assigner un bail DHCP statique basé sur l'adresse MAC de la passerelle dans l'interface du routeur/box.

---

## Appairage impossible

**Vérifications** :
1. Z2M doit être en mode **Permit join** (bouton dans l'interface frontend)
2. L'appareil doit être en mode reset/appairage (souvent appui long 10s sur le bouton)
3. Vérifier dans les logs Z2M si la trame d'appairage est reçue

---

## Problème connu — Sonoff ZBDongle-Plus V2 sur Proxmox

> **Contexte** : Sur une installation HAOS en VM Proxmox avec passthrough USB d'un Sonoff ZBDongle-Plus V2, Zigbee2MQTT peut crasher au démarrage ou se déconnecter aléatoirement.

**Symptôme** : Z2M plante, les logs montrent une erreur série ou une perte du coordinateur.

**Cause identifiée** : Le passthrough USB sur Proxmox peut être instable selon la version du noyau et la configuration USB du Dongle.

**Solutions à tester** :
1. Utiliser le passthrough par ID USB plutôt que par bus/port dans la config Proxmox (`usbid` au lieu de `hostpci`)
2. Désactiver l'autosuspend USB dans le host Proxmox :
   ```bash
   echo -1 > /sys/module/usbcore/parameters/autosuspend
   ```
3. Passer le coordinateur en **réseau TCP** (même principe que la passerelle Lidl) via `ser2net` sur le host Proxmox

---

## Commandes utiles Tasmota (console)

| Commande | Résultat |
|---|---|
| `Status 5` | IP et infos réseau |
| `Status 13` | Config TCP (port ouvert) |
| `TCPStart 8888` | Démarrer le socket TCP sur port 8888 |
| `Restart 1` | Redémarrer Tasmota |
| `ZbStatus1` | État du coordinateur Zigbee |
