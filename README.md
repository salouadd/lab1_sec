# Mobexler Lab Setup – TP1

## Contexte

Ce rapport documente la mise en place de la plateforme **Mobexler** dans le cadre d'un atelier de sécurité applicative mobile.

Les travaux réalisés couvrent :

* L'importation de la machine virtuelle Mobexler
* La validation de la configuration réseau
* La sauvegarde de l'état initial de l'environnement
* La mise en place d'une communication ADB avec un émulateur Android exécuté sur la machine hôte Windows

---

## Référentiel Technique

| Élément             | Valeur                                                             |
| ------------------- | ------------------------------------------------------------------ |
| Image VM            | `Mobexler.ova`                                                     |
| Empreinte SHA-256   | `07C40D1456B564313996F8A8BD5402E9EBB825D7B817C5031C52396231DDF188` |
| Hyperviseur         | VMware                                                             |
| Adaptateur réseau 1 | NAT                                                                |
| Adaptateur réseau 2 | Host-Only                                                          |

### Vérification de l'intégrité de l'image

![Vérification SHA256](images/hash-verification.png)

### Configuration réseau VMware

![Configuration VMware](images/vmware-network-config.png)

---

## Démarrage de la Machine Virtuelle

L'image OVA a été importée puis démarrée avec succès.

L'écran de connexion apparaît correctement, confirmant le bon fonctionnement du système.

![Démarrage Mobexler](images/mobexler-boot.png)

---

## Validation de la Connectivité Réseau

### Interfaces Réseau Actives

La commande suivante a été exécutée :

```bash
ip a
```

Deux interfaces réseau sont disponibles :

| Interface | Adresse IP        | Fonction  |
| --------- | ----------------- | --------- |
| ens33     | 192.168.17.175/24 | NAT       |
| ens34     | 192.168.85.132/24 | Host-Only |

![Interfaces réseau](images/ip-a.png)

---

### Table de Routage

La commande :

```bash
ip route
```

montre que la route par défaut pointe vers :

```text
192.168.17.2 via ens33
```

Cela confirme que l'accès Internet s'effectue via l'interface NAT.

![Table de routage](images/routing-table.png)

---

### Tests de Connectivité

#### Test ICMP vers Internet

```bash
ping 8.8.8.8
```

#### Test de Résolution DNS

```bash
ping google.com
```

Les deux tests sont concluants et valident :

* l'accès Internet
* la résolution DNS
* le bon fonctionnement de la passerelle NAT

![Ping IP](images/ping-ip.png)

![Ping DNS](images/ping-dns.png)

---

## Sauvegarde de l'État Initial

Une fois la configuration validée, un snapshot VMware a été créé :

```text
CLEAN_BASELINE_TP1
```

Ce point de restauration servira de référence avant toute manipulation ultérieure.

![Snapshot VMware](images/snapshot-clean-baseline.png)

---

## Mise en Place de la Cible Android

### Émulateur Android

Pour ce laboratoire, l'émulateur intégré à Android Studio a été retenu en remplacement de Genymotion.

L'émulateur démarre correctement sur l'hôte Windows.

![Android Emulator](images/android-emulator.png)

---

### Vérification Locale via ADB

Depuis Windows :

```bash
adb devices
```

Résultat :

```text
List of devices attached
emulator-5554    device
```

L'émulateur est correctement détecté par ADB.

![ADB Local](images/adb-local.png)

---

## Connexion ADB Distante Depuis Mobexler

L'émulateur étant exécuté sur la machine hôte Windows, Mobexler doit se connecter au serveur ADB distant.

Commande utilisée :

```bash
platform-tools/adb -H 192.168.17.1 -P 5037 devices
```

Résultat :

```text
List of devices attached
emulator-5554    device
```

L'émulateur apparaît avec l'état **device**, confirmant la communication avec le serveur ADB distant.

![ADB Remote](images/adb-remote-devices.png)

---

### Ouverture d'un Shell Android

Un shell interactif a ensuite été ouvert avec succès :

```bash
platform-tools/adb -H 192.168.17.1 -P 5037 shell
```

Cette étape valide entièrement la communication ADB entre :

* Mobexler
* Android Emulator
* le serveur ADB Windows

![ADB Shell](images/adb-shell.png)

---

## Architecture du Laboratoire

```text
+---------------------+
| Windows Host        |
|                     |
| Android Emulator    |
| ADB Server :5037    |
+----------+----------+
           |
           | NAT
           |
+----------v----------+
| Mobexler VM         |
| VMware NAT Network  |
| 192.168.17.175      |
+---------------------+
```

---

## Bilan

L'environnement de laboratoire est pleinement opérationnel.

### Éléments validés

* ✅ Importation et démarrage de la VM Mobexler
* ✅ Connectivité Internet via l'interface NAT
* ✅ Réseau Host-Only fonctionnel
* ✅ Création du snapshot `CLEAN_BASELINE_TP1`
* ✅ Déploiement de l'émulateur Android Studio
* ✅ Détection de la cible via ADB
* ✅ Communication ADB distante depuis Mobexler
* ✅ Ouverture d'un shell Android distant

L'environnement est désormais prêt pour les prochains travaux d'analyse et de sécurité applicative mobile.
