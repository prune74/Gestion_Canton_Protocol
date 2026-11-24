# Gestion_Canton_Protocol

Bibliothèque partagée des commandes CAN Commands 29 bits entre CC et EXCC,
ainsi que des autres commandes du réseau Gestion Canton.

## Version candidate 1.5.0 — préparée localement, non publiée

Le prochain contrat prévoit les relais de profil/consigne : CAN Commands
`0xE6` (CC → EXCC, demande `0xC9` inchangée) et `0xDE` (EXCC → CC, résultat
applicatif), ainsi que UART `0x1A`/`0x1B` entre EXCC et Booster. Le format des
relais est consigné dans le suivi. Les résultats UART sont fixés : `0` accepté,
`1` mauvais mode, `2` profil invalide, `3` consigne invalide, `4` échec
d'application; `5` signifie conflit de transaction et devient le résultat CAN
`7`. EXCC réessaie UART `0x1A` une fois après 500 ms; sans réponse applicative
dans les 1 000 ms suivant la première tentative, il retourne le résultat CAN
`5=Booster non confirmé`. Ces modifications candidates ne font pas partie de
la version publiée `1.4.0` et ne sont pas intégrées aux firmwares.
Les destinataires gardent en RAM la dernière transaction `0xC9`, sa demande
exacte et son résultat; une répétition identique renvoie ce résultat, un
contenu différent avec le même numéro renvoie `7`. Une nouvelle transaction
remplace l’entrée; un redémarrage l’efface. L’ERM attend la fin ou le délai
d’expiration avant d’en démarrer une autre.

## Version 1.4.0

Ajoute le relais de mesure de tension des rails Booster vers le CC :

| Sens | Code | Fonction |
|---|---:|---|
| EXCC → CC | `0xDD` | Relais de la mesure des rails du Booster vers le CC. |

Trame CAN Commands de 8 octets : `[0..1]` tension en millivolts, uint16
little-endian; `[2]` validité (`1` valide, `0` invalide); `[3..7]` réservés
à zéro. Le CC déduit l’ID du Booster de son association avec l’EXCC et relaie
la mesure vers CAN Service `0xC7`.

Ajoute aussi le type UART v2 `BOOSTER_RAIL_VOLTAGE (0x19)`, DLC 3 : tension
en millivolts uint16 little-endian, puis validité (`1` valide, `0` invalide).

## Version 1.3.0

Ajoute le résultat de lecture des corrections Booster (`0xD5`) et son relais
CC → EXCC (`0xE8`). Ajoute également `src/UartProtocol.h`, le format UART v2
commun utilisé par l’EXCC et le Booster : types de trames, séquences, CRC-16,
encodage et décodage.

La lecture des corrections enregistrées utilise CAN Service `0xC6` et UART
`0x18`; le résultat revient sur UART `0x14`. Elle ne modifie pas le Booster.
Le réglage reste séparé sous `0xBA` et UART `0x13`.

La version `v1.3.0` est publiée sur GitHub. Les firmwares consommateurs
CC, EXCC, Booster et ERM sont compilés contre cette version.

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
