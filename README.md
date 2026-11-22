# Gestion_Canton_Protocol

Bibliothèque partagée des commandes CAN Commands 29 bits entre CC et EXCC,
ainsi que des autres commandes du réseau Gestion Canton.

## Version 1.1.0

Ajoute les commandes du chemin de découverte et d’association du Booster :

| Sens | Code | Fonction |
|---|---:|---|
| EXCC → CC | `0xD3` | Résultat de validation de l’association Booster : transaction, ID cible et code résultat. |
| CC → EXCC | `0xED` | Demande explicite d’effacement de l’ID Booster mémorisé. |
| CC → EXCC | `0xEE` | Relais de la demande de validation CAN Service `0xC4`. |
| CC → EXCC | `0xEF` | Relais du mode de découverte CAN Service `0xC5`. |

Les charges utiles et les règles de relais sont définies dans `src/Protocol.h`
et dans la spécification du projet CFC. Cette publication définit les
commandes; leur traitement dans les firmwares CC et EXCC reste à implémenter.
