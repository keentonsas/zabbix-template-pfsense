[![Keenton : Informatique / Cloud / Infogérance / SaaS](https://www.keenton.com/keenton_github.png)](https://www.keenton.com)

## Zabbix Template - pfSense
[README in english](#pfsense-zabbix-template)

Template FreeBSD orienté pfSense:
  - refonte total du monitoring de la mémoire
  - récupération des données du tampon mémoire utilisé par le réseau (_MBUF_)
  - Récupération des données sur l'utilisation de la Table des Etats (_State Table_)
  - amélioration de la visibilité des graphiques
  - triggers remaniés

#### On en parle sur notre blog
[Supervision pfSense avec agent Zabbix intégré](https://www.keenton.com/supervision-pfsense-avec-agent-zabbix-integre/)

#### Prérequis
  - Accès admin sur serveur Zabbix et firewall pfSense

#### Serveur Zabbix
Rien de particulier, il suffit d'importer les deux templates, créer l'hôte pour pfSense et lui lier le template adéquat (actif ou passif).

#### Agent Zabbix: Paramètres Utilisateur (_User Parameters_)
Dans la configuration de l'agent Zabbix sur le pfSense, il faut afficher les options de configuration avancées et ajouter le bloc ci-dessous dans le champs _User Parameters_ qui vient d'aparaitre:

    UserParameter=pfsense.states.max,grep "limit states" /tmp/rules.limits | cut -f4 -d ' '
    UserParameter=pfsense.states.current,grep "current entries" /tmp/pfctl_si_out | tr -s ' ' | cut -f4 -d ' '
    UserParameter=pfsense.mbuf.current,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f1
    UserParameter=pfsense.mbuf.cache,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f2
    UserParameter=pfsense.mbuf.max,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f4

Commandes utilisées:
  * `grep`: filtre du texte suivant une suite de caractères.
  * `netstat`: affiche des informations sur l'état du réseau.
  * `cut`: découpe une ligne de texte suivant un séparateur et permet de récupérer une _colonne_ particulière.
  * `tr`: remplace des caractères (remplace ici les suites d'_espaces_ par un _espace_ seul, nécessaire dans certains cas pour utiliser correctement la commande `cut`).

##### _Timeout!_
Dans la configuration de l'Agent Zabbix (sur pfSense) il faudra certainement augmenter la valeur de _timeout_ (réglée sur 3 secondes par défaut). En cas de soucis pour remonter les données sur le serveur Zabbix, on peut d'abord vérifier et augmenter le _timeout_ qui est souvent la source de problème quand on utilise des commandes particulières dans les _User Parameter_ Zabbix.

#### Template App Zabbix Agent
Le lien avec ce template a été retiré car il n'existe pas de version "Active" du template par défaut dans Zabbix. Ne pas oublier de le rajouter dans les templates liés.

_ps: pour avoir la version "Active" d'un template il suffit de cloner le template "Passif" et de modifier le type de chaque élément._

---
## pfSense Zabbix Template
FreeBSD template pfSense-oriented:
  - complete remodeling of memory monitoring
  - metrics from the memory buffer used by the network (_MBUF_)
  - metrics on State Table usage
  - improved visibility of graphics
  - revised triggers

#### We talk about it on our blog
[PfSense Monitoring with Integrated Zabbix Agent](https://www.keenton.com/supervision-pfsense-avec-agent-zabbix-integre/)

#### Requirements
  - _admin_ access on Zabbix server and firewall pfSense

#### Zabbix Server
Nothing special, just import both templates, create the host for pfSense and link it to the appropriate template (active or passive).

#### Zabbix Agent: _User Parameters_
In Zabbix agent config on pfSense, you must display the advanced configuration options and add to the _User Parameters_ field that just appeared:

    UserParameter=pfsense.states.max,grep "limit states" /tmp/rules.limits | cut -f4 -d ' '
    UserParameter=pfsense.states.current,grep "current entries" /tmp/pfctl_si_out | tr -s ' ' | cut -f4 -d ' '
    UserParameter=pfsense.mbuf.current,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f1
    UserParameter=pfsense.mbuf.cache,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f2
    UserParameter=pfsense.mbuf.max,netstat -m | grep "mbuf clusters" | cut -f1 -d ' ' | cut -d '/' -f4

Commands used:
  - `grep`: filter text by pattern.
  - `netstat`: show network status.
  - `cut`: cut out selected portions of each line.
  - `tr`: replace characters (used here to replace the space sequences with a single space, necessary in some cases to properly use the `cut` command).

##### _Timeout!_
In Zabbix Agent config (on pfSense) you may need to increase the timeout value (set to 3 seconds by default). It is certainly the first thing to check on Zabbix Agent if you find yourself unable to retrieve some _User Parameters_ metrics.

#### Template App Zabbix Agent
The link with this template has been remove for the "Active" version does not exist by default in Zabbix. Do not forget to add it in the linked templates.

_ps: you may create an "Active" template version by cloning the "Passive" one and changing the type of items._
