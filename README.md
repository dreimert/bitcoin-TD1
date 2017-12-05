# TD1 Bitcoin

## Premier contact

La version originale de ce TD est à l'adresse suivante : https://sfrenot.jumplyn.com/tp1-blockchain-tx/

Ce TD a pour objectif de tester l'outil de référence de bitcoin. L'outil 'officiel' de bitcoin issu du site http://bitcoin.org est à la fois un démon de validation des transactions de la blockchain officielle (bitcoind), un outil en ligne de commande pour interagir avec ce démon (bitcoin-cli) et un outil graphique de gestion du portefeuille (bitcoin-qt).

#### Q0: Qu'est ce qu'un démon ?

L'outil s'installe à partir du site de réference et pour cette première partie, on peut utiliser les outils binaires mis à disposition sur cette page : https://bitcoin.org/fr/telecharger (`brew install bitcoin` sur macOS si vous avez installé le gestionnaire de paquets `brew`). Ce TD a été écrit avec la version 0.13 sortie en Août 2016. Installez la dernière version. Si vous constatez des erreurs dans ce TD, n'hésitez pas à me le signaler ou à faire une pull request sur Github. C'est aussi vrai pour les autres TD/TP de ce cours.

    Récupérer les binaires du site
    Ajouter le repertoire bin dans votre path
    Vérifier que bitcoind, bitcoin-cli et bitcoin-qt sont accessibles en ligne de commande

Le demon de synchronisation avec la blockchain et le client graphique travaillent sur un répertoire par défault ($HOME/.bitcoin sur linux, $HOME/Library/Application Support/Bitcoin sur MacOs) qui va contenir toute l'information de synchronisation.

    !!! Attention si vous possédez des bitcoins, ne faites pas n'importe quoi.

    SINON, si vous avez lancé soit le client soit le synchronisateur le repertoire a été créé et contient les données de synchronisation.

    Vous pouvez à tout moment supprimer ce repertoire pour redémarrer.

#### Q1 : A votre avis, que ce passe t'il au démarrage du démon de synchronisation ? Vous pouvez regarder des options comme -debug, -printtoconsole pour voir les messages d'affichage.

Attendre la fin du lancement du demon risque de prendre quelques semaines, de plus il sera impossible de tester quoi que ce soit.

#### Q2 : Pourquoi ?

Bitcoin, met trois réseaux de blockchain en place.

* mainnet : est le réseau principal sur lequel fonctionne la blockchain officielle et sur laquelle la monnaie possède un cours officiel.
* testnet : est un réseau de test sur lequel une nouvelle blockchain a été lancée, mais sur lequel de la monnaie est mise à disposition. Pour les mêmes raisons que la Q2, le réseau testnet est difficilement exploitable. Le démon se connecte dessus en le lançant avec l'option  -testnet. Ce qui a pour effet de créer un sous-repertoire dans $HOME/.bitcoin/testnet qui contient une structure similaire à celui du parent.
* regtest : n'est pas un réseau dans la mesure ou le demon ne se synchronisera pas sur une blockchaine existante. En lançant le demon avec cette option, un repertoire regtest est créé. Il est alors possible simuler le fonctionnement des transactions bitcoin sur un certain nombre de noeuds que vous maîtrisez.
**Pour la fin de cette séance nous travaillerons exclusivement sur cette modalité.**

## Utilisation de regtest

### Lancement initial

#### Q3 : comparez les différences de lancement entre mainnet, testnet, regtest.

Supprimer les repertoires pour redémarrer proprement.
Pour interagir avec le démon de synchronisation, on utilise le client bitcoin-cli, qui permet de passer des instruction par rpc au demon. Pour interagir avec le regtest, il faut lancer bitcoin-cli avec l'option -regtest.

#### Q4 : que signifie cli ?
#### Q5 : que signifie rpc ?

Pour éviter de répeter la commande 'bitcoin-cli -regtest' *ad nauseam* on peut créer l'alias suivant :

    alias rt='bitcoin-cli -regtest'

On peut maintenant interagir avec le demon de synchronisation, qui ne synchronise rien dans cette option.
Voici quelques commandes d'interaction avec ce processus :

    rt -h : liste les commandes rpc
    rt getinfo : donne des informations sur le noeud. Cette commande est dépréciée mais vous sera utile plus tard.
    rt getblockchaininfo : donne des informations sur la blockchain
    rt getnetworkinfo : donne des informations sur le réseau
    rt getwalletinfo : donne des informations sur le portefeuille
    rt getpeerinfo : donne des informations sur le réseau de pairs
    rt getmininfo : donne des informations sur l'état du minage courant
    rt getbalance : donne le montant de le porte-monnaie

### Création de transaction

    rt generate <val> : permet de simuler la génération de <val> blocks.

Avec l'option -regtest, le demon ne se connecte sur aucun noeud et ne mine pas. Pour information, la dernière version de bitcoin 0.13, n'intègre plus de thread de minage CPU. L'appel rpc generate permet de simuler un minage réussi.

#### Q6: Indiquez la suite de commande pour obtenir une balance de 50BTC sur votre compte? Expliquez vos choix

Si vous avez réussi à obtenir 50 btc sur un compte vous pouvez donc le dépenser au profit de quelqu'un. Il y a deux possibilités pour tester cela. Le plus simple est d'effectuer un échange avec une autre machine de votre salle, mais il faut également savoir le faire de manière autonome sur votre machine.

### Echange de bitcoin avec votre voisin

    rt addnode <addr> onetry : permet d'ajouter un pair dans votre réseau
    rt getnewaddress : permet de générer une nouvelle adresse bitcoin
    rt sendtoaddress <btcaddr> <montant> : permet de transférer <montant> btc à l'addresse <btcaddr>
    
Si vous utilisez votre propre machine connectée à eduroam, il y a un blocage de ports. Je vous conseille de passer directement à l'étape suivante.

#### Q7 : Indiquez toutes les commandes cli à passer pour transferer 10BTC à votre voisin en partant d'une nouvelle chaîne.

### Echange de bitcoin sur votre propre machine

Pour échanger des bitcoins sur votre propre machine, il faut créer deux environnements séparés. Pour forcer un environnement, il suffit de lancer le demon sur un autre repertoire que $HOME/.bitcoin. Assurez-vous que le démon est lancé.

#### Q8 : quelle commande unix vous permet de le savoir ?

Lancer un second démon en utilisant un autre repertoire de référence.

    bitcoind -regtest -datadir="./demon2"

#### Q9: que ce passe t'il

Il est possible de faire tourner deux démons sur la même machine à condition qu'ils ne partagent pas le même environnement de blockchain, et qu'ils utilisent des ports différents.

Supprimez les données du repertoire demon2.

#### Q10: Quelle option de démarrage vous permet de lancer le demon ET d'interagir avec lui avec l'outil de CLI

Une fois lancé vous pouvez alors interagir avec vos deux demons séparément afin d'échanger des bitcoins, et donc tester la question 7 de manière autonome, sans ami.

Enfin il est possible de regrouper toutes les options de démarrage  -regtest, -debug, -xxx dans un fichier de configuration qui se situe à la racine du repertoire de configuration et qui a pour nom par défaut  bitcoin.conf.

Corrigez vos deux environnements afin de pouvoir lancer les deux démons suivants avec les appels suivants.

    bitcoind
    bitcoind -datadir=./demon

Vous pouvez agir avec votre second démon avec la commande rt2.

    alias rt2='bitcoin-cli -datadir="./demon"'

--> Maintenant que vous avez deux démons actifs en mode regtest, vous pouvez transférer de l'argent ou forger vos propres transactions bitcoin comme indiqué dans ce tutoriel.

https://bitcoin.org/en/developer-examples#testnet

==> N'hésitez pas à tester les mécanismes de transactions (tx) associés.
