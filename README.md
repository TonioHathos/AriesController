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
