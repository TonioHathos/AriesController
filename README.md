# Nos implémentations de contrôleur dans le cadre de TSP

Ce répertoire va vous permettre de créer plusieurs instances d'agents basées en local à l'aide de conteneurs docker en relation avec une blockchain de type indy dans le cadre d'une utilisation pour Télécom SudParis.

## Table des matières

- [Installation et activation de la blockchain](#installation-et-activation-de-la-blockchain)
- [Activation des agents](#activation-des-agents)
- [Liste des différentes fonctions autorisées pour les agents:](#liste-des-différentes-fonctions-autorisées-pour-les-agents)
  - ["Issuer": Télécom SudParis (TSP)](#issuer:-télécom-sudparis-(TSP))
  - ["Holder": Etudiant (Student)](#holder:-étudiant-(Student))
  - ["Verifier": Entreprise (Company)](#verifier:-entreprise-(Company))
- [Options supplémentaires lors de la configuration d'un agent:](#options-supplémentaires-lors-de-la-configuration-d-un-agent)
  - [Revocation](#revocation)
  - [DID Exchange](#did-exchange)
  - [Endorser](#endorser)
  - [Exécuter Indy-SDK en Backend](#executer-indy-sdk-en-backend)
  - [Mediation](#mediation)
  - [Multi-ledger](#multi-ledger)
  - [Multi-tenancy](#multi-tenancy)
  - [Multi-tenancy *with Mediation*!!!](#multi-tenancy-with-mediation)

## Installation et activation de la blockchain

Commencez par ouvrir un terminal de commande `bash` et clonez le`répertoire `von-network` :

```bash
git clone https://github.com/bcgov/von-network
cd von-network

```

Une fois que le repo a été cloné, vous allez construire des images docker pour le réseau VON et ensuite démarrer votre réseau Indy :


```bash
./manage build
./manage start --logs

```

Au fur et à mesure que les nœuds du réseau Indy démarrent, surveillez les traces "log" pour les messages d'erreur. Ajoutez l'option `--logs` de la commande start. Une fois que vous serez habitué à faire fonctionner une instance de réseau VON, vous voudrez peut-être laisser ce paramètre désactivé afin de revenir directement à la ligne de commande pour tout ce que vous faites d'autre.

Le script bash [`./manage`](../manage) simplifie le processus d'exécution du réseau VON, en fournissant les points d'entrée communs que vous devez utiliser. Il fournit également un certain nombre de variables d'environnement que vous pouvez utiliser pour personnaliser l'exécution du script.

Les images des conteneurs Docker sont construites à l'aide de scripts qui encapsulent les étapes de création de `indy-node` à partir de zéro, en commençant par les images Docker de base qui contiennent toutes les conditions préalables nécessaires. Les images de base sont créées une fois par version de `indy-node`, afin que toute la communauté puisse bénéficier de ce travail.

Pour voir ce que vous pouvez faire avec le script `./manage`, une fois que votre réseau fonctionne (grâce aux commandes ci-dessus), appuyez sur Ctrl-C dans le terminal pour revenir à l'invite de commande. Votre réseau fonctionne toujours. Exécutez la commande sans arguments pour voir les informations d'utilisation du script.

```bash
./manage

```

Si vous voulez revenir aux données de logs des noeuds, exécutez la commande avec le paramètre "logs" :

```bash
./manage logs

```

### Naviguer sur le ledger

Une fois que le ledger est lancé, vous pouvez le voir en allant sur le serveur web qui tourne en localhost sur le port 9000. Cela signifie aller à [http://localhost:9000](http://localhost:9000).


A partir du serveur web du réseau VON, vous pouvez :

* Voir l'état des nœuds du réseau.
* Consulter le fichier genèse du réseau.
* Créer un DID.
* Parcourir les trois ledgers qui composent un réseau Indy :
    * Le ledger "domain", où résident les DID, les schémas, etc.
    * Le ledger "pool", où l'ensemble des nœuds du réseau est suivi.
    * Le ledger "config", où les changements apportés à la configuration du réseau sont suivis.

### Visualisation des transactions

Cliquez sur le lien "Domain Ledger" dans le menu principal pour afficher les transactions du ledger. Sur un tout nouveau ledger, il y aura cinq transactions, la première pour l'administrateur DID et les quatre suivantes pour chacun des intendants du réseau. Les intendants sont les opérateurs des quatre nœuds fonctionnant sur le réseau.

**Note:** _Vous pouvez effectuer des recherches dans le ledger par type de transaction ou par le texte des transactions. Par exemple, dans le "Filtre", tapez "Trustee" et appuyez sur la touche "Entrée". Les quatre transactions "steward" seront masquées et vous ne verrez qu'une seule transaction. Ce n'est pas très utile si le ledger ne contient que cinq transactions, mais c'est très utile s'il y en a 100 000!_

### Création d'un DID

Pour créer un DID, allez dans le Ledger Browser (sur le port 9000) et trouvez la section "Authenticate a New DID" (authentifier un nouveau DID). Il y a plusieurs options, mais nous allons prendre le cas le plus simple :

1. Assurez-vous que l'option "Register from Seed" est sélectionnée.
2. Saisissez votre prénom dans le champ "Wallet Seed" et votre nom complet dans le champ "Alias".
3. Cliquez sur "Enregistrer DID".

Vous devriez recevoir une réponse contenant les détails du DID nouvellement créé.

1. Ensuite, allez dans le ledger "domain" et recherchez la transaction. Mettez l'alias que vous avez utilisé pour le DID dans le filtre pour voir si vous pouvez rechercher le DID.

### Visualisation du fichier Genesis

Dans le menu principal du Ledger Browser, cliquez sur le lien "Genesis Transaction" pour voir le fichier genèse du réseau. Lorsque vous exécutez une instance de réseau VON avec le Ledger Browser, vous obtenez toujours le fichier genèse à partir du point de terminaison /genesis - que nous pouvons utiliser comme paramètre de démarrage pour un agent Aries. 

Si vous voulez voir le lien entre le fichier genèse et les entrées du ledger, copiez et collez la valeur de l'élément "from" de l'une des lignes du fichier genesis (par exemple "`TWwCRQRZ2ZHMJFn9TzLp7W`"), puis retournez dans le ledger "domain" et recherchez une transaction avec le même identifiant.

**Note:** _Les DIDs dans le fichier genèse (les valeurs "from") sont les mêmes pour n'importe quelle instance d'un ledger indy sandbox, d'où l'ID dans le paragraphe précédent. Une fois que vous utilisez des ledger de production (ou de type production), le contenu des fichiers sera différent._

Une autre chose à noter à propos du fichier correspondant au ledger est l'adresse IP des nœuds qui le composent. Comme les nœuds sont exécutés dans un conteneur Docker, l'adresse IP est celle du conteneur Docker, et non celle de localhost. L'un des avantages de l'utilisation du réseau VON est qu'il prend en charge ce genre de détails, en veillant à ce que le fichier genèse soit toujours exact pour le réseau en cours d'exécution.

### Utilisation de l'interface de commande
`von-network` fournit un moyen d'accéder à l'interface de ligne de commande (CLI) d'Indy. Pour accéder à l'interface de ligne de commande d'Indy pour le réseau, allez à l'invite de commande (en utilisant Ctrl-C si nécessaire) et exécutez la commande :

```bash
./manage indy-cli
```

Exécutez la commande "help" pour voir ce que vous pouvez faire, et "exit" pour sortir de la session CLI.

Nous n'entrerons pas dans le détail de l'ILC d'Indy ici, mais il possède de puissantes capacités que vous pouvez utiliser pour créer des scripts permettant de configurer un réseau Indy et d'ajouter des objets au ledger.

Pour plus d'informations, reportez-vous à [Using the containerized `indy-cli`](./Indy-CLI.md)

### Arrêt et suppression d'un réseau VON

Pour arrêter et supprimer un réseau VON en cours d'exécution, accédez à l'invité en ligne de commande (en utilisant Ctrl-C si nécessaire), puis exécutez la commande suivante :

```bash
./manage down

```


Si vous voulez arrêter le réseau **SANS** effacer les données du ledger, utilisez la commande suivante : 

```bash
./manage stop

```

Vous pouvez ensuite redémarrer le ledger en exécutant la commande normale de démarrage du réseau :

```bash
./manage start

```

Veillez à ce que le stockage de l'agent et le ledger soient synchronisés. Si vous supprimez/réinitialisez l'un, vous supprimez/réinitialisez l'autre.

## Activation des agents

L'exécution de notre implémentation dans docker nécessite d'avoir une instance `von-network` (un bac à sable Hyperledger Indy public ledger) fonctionnant localement dans docker (voir partie précédente).

Ouvrez quatre shells `bash`. Pour les utilisateurs de Windows, `git-bash` est fortement recommandé. bash est le shell par défaut dans les sessions de terminal Linux et Mac.

Sur le premier terminal, activez von-network comme décrit ici: [Installation et activation de la blockchain](#installation-et-activation-de-la-blockchain).

Dans le second terminal, basculez sur le répertoire en `project` de votre clone du dépôt AriesController. Démarrez l'agent `TSP` en lançant la commande suivante:

```bash
cd project
./run_proj TSP
```

Dans le troisième terminal, basculez sur le répertoire en `project` de votre clone du dépôt AriesController. Démarrez l'agent `Student` en lançant la commande suivante:

```bash
cd project
./run_proj Student
```
Dans le quatrième et dernier terminal, basculez sur le répertoire en `project` de votre clone du dépôt AriesController. Démarrez l'agent `Company` en lançant la commande suivante:
```bash
cd project
./run_proj Company
```
Désormais basculez sur [Liste des différentes fonctions autorisées pour les agents:](#liste-des-différentes-fonctions-autorisées-pour-les-agents) pour voir les différentes fonctionnalités