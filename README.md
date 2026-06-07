Rapport de Laboratoire — Environnement Mobexler

Contexte
Ce rapport retrace les étapes de mise en place de la plateforme Mobexler dans le cadre d'un atelier de sécurité applicative mobile. Les travaux couvrent l'importation de la VM, la validation réseau, la sauvegarde de l'état initial, et l'établissement d'une communication ADB avec un émulateur Android tournant sur la machine hôte Windows.

Référentiel technique
ÉlémentValeurImage VMMobexler.ovaEmpreinte SHA-25607C40D1456B564313996F8A8BD5402E9EBB825D7B817C5031C52396231DDF188HyperviseurVMwareAdaptateur réseau 1NATAdaptateur réseau 2Host-only
Vérification de l'intégrité de l'image :
Show Image
Paramétrage des interfaces réseau dans VMware :
Show Image

Démarrage de la machine virtuelle
L'OVA a été importée puis démarrée sans anomalie. L'interface de connexion s'affiche correctement, attestant que le système est pleinement opérationnel.
Show Image

Validation de la connectivité réseau
Interfaces actives
L'exécution de ip a révèle deux interfaces montées :

ens33 — 192.168.17.175/24 (lien NAT)
ens34 — 192.168.85.132/24 (lien Host-only)

Show Image
Table de routage
La route par défaut pointe vers 192.168.17.2 via ens33, ce qui confirme que le trafic Internet transite bien par le mode NAT.
Show Image
Tests de connectivité
Un premier test en IP brute, suivi d'une résolution DNS, confirment l'accès Internet de bout en bout.
Show Image
Show Image

Sauvegarde de l'état initial
Après validation du réseau, un snapshot nommé CLEAN_BASELINE_TP1 a été créé. Ce point de restauration permettra de revenir à un environnement vierge avant chaque série de manipulations.
Show Image

Mise en place de la cible Android
Émulateur
Pour ce laboratoire, Android Studio Emulator a été choisi à la place de Genymotion. L'émulateur s'initialise correctement depuis l'hôte Windows.
Show Image
Détection via ADB local
La commande adb devices exécutée côté Windows confirme que la cible est reconnue sous l'identifiant emulator-5554.
Show Image

Connexion ADB distante depuis Mobexler
L'émulateur étant hébergé sur l'hôte, Mobexler se connecte au démon ADB Windows via le réseau. La commande suivante permet d'interroger le serveur distant :
bashplatform-tools/adb -H 192.168.17.1 -P 5037 devices
Le périphérique emulator-5554 apparaît bien avec le statut device.
Show Image
Un shell interactif a ensuite été ouvert avec succès, validant la communication ADB complète entre Mobexler et l'émulateur.
Show Image

Bilan
L'environnement de laboratoire est entièrement fonctionnel. Les points suivants ont été vérifiés et validés :

Démarrage normal de Mobexler après import de l'OVA
Accès Internet opérationnel via le lien NAT
Réseau de laboratoire disponible via l'interface Host-only
Snapshot de référence créé et nommé CLEAN_BASELINE_TP1
Émulateur Android Studio détecté et accessible depuis l'hôte
Communication ADB établie entre Mobexler et la cible Android distante
