# Gestion_Canton_Protocol

Bibliothèque partagée des commandes CAN Commands 29 bits entre CC et EXCC,
ainsi que des autres commandes du réseau Gestion Canton.

## Version 1.2.0

Ajoute le relais de réglage des demi-bits du Booster :

| Sens | Code | Fonction |
|---|---:|---|
| EXCC → CC | `0xD4` | Résultat applicatif de réglage Booster, relayé vers la réponse Service `0xBA`. |
| CC → EXCC | `0xEA` | Relais de la demande Service `BOOSTER_TIMING_SET (0xBA)` vers UART `0x13`. |

Les corrections sont signées en microsecondes et limitées par le protocole
CFC à −5…+5 µs pour chaque demi-bit. Le Booster refuse l’application si sa
sortie DCC n’est pas arrêtée, sauvegarde les valeurs et confirme le résultat.

## Version 1.1.0 (historique)

Ajoute les commandes du chemin de découverte et d’association du Booster :

| Sens | Code | Fonction |
|---|---:|---|
| EXCC → CC | `0xD3` | Résultat de validation de l’association Booster : transaction, ID cible et code résultat. |
| CC → EXCC | `0xED` | Demande explicite d’effacement de l’ID Booster mémorisé. |
| CC → EXCC | `0xEE` | Relais de la demande de validation CAN Service `0xC4`. |
| CC → EXCC | `0xEF` | Relais du mode de découverte CAN Service `0xC5`. |

Les charges utiles et les règles de relais sont définies dans `src/Protocol.h`
et dans la spécification du projet CFC.
