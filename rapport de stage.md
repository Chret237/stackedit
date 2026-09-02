# INTRODUCTION GÉNÉRALE
Le développement des applications modernes ne se limite plus à l'écriture du code. La mise à disposition rapide, fiable et sécurisée d'une application nécessite aujourd'hui la collaboration entre les différentes étapes du cycle de vie logiciel : développement, intégration, tests, déploiement, supervision et amélioration continue.

C'est dans ce contexte que s'inscrit mon stage effectué au sein de **Formuloo**, dans lequel j'ai été amené à découvrir puis à mettre en pratique différentes technologies et méthodes liées au domaine du **DevOps**.

> **Objectif :** L'objectif de cette expérience était de rapprocher les connaissances théoriques acquises au cours de ma formation des réalités d'un environnement professionnel. Le stage m'a ainsi permis de travailler progressivement sur plusieurs domaines : gestion du code source, conteneurisation avec Docker, intégration et déploiement continus, infrastructure as code, orchestration, gestion de configuration, GitOps, observabilité et résilience

Cette progression a été organisée autour de **trois sprints**, chacun permettant d'approfondir un ensemble de compétences avant leur mise en application dans le projet **Formuloo Compta** qui constitue le fil conducteur de cette expérience. Il s'agit ici d'une application web composée d'un **frontend Angular**, d'un **backend Django** et d'une base de données **PostgreSQL**. L'objectif était notamment de parvenir à automatiser son déploiement et à disposer d'une infrastructure reproductible dans le cloud.

# Organisation du stage et progression des 3 sprints

## Sprint 1 — Méthodes de travail, productivité et fondamentaux numériques

Le premier sprint avait pour objectif de construire les bases méthodologiques et professionnelles nécessaires avant d’aborder l’infrastructure et les outils DevOps plus avancés.

**1. Développement agile et méthodologie Scrum**

Cette formation m’a permis de comprendre le fonctionnement d’une équipe agile et l’organisation du travail autour des Sprints. Les notions principales abordées comprenaient notamment le travail itératif, la priorisation, la collaboration, l’amélioration continue ainsi que le rôle des différents membres d’une équipe Scrum.

J’ai également été sensibilisé à certaines pratiques de développement telles que la simplicité des solutions, l’amélioration progressive du code et l’importance de produire régulièrement une version exploitable du produit.

**2. Utilisation de l’intelligence artificielle pour améliorer la productivité — cas de ChatGPT**

Cette formation avait pour objectif d’apprendre à utiliser l’intelligence artificielle comme outil d’assistance dans les activités professionnelles.

Elle a notamment porté sur la formulation de prompts efficaces, la génération et la reformulation de contenus, la synthèse d’informations, le brainstorming, l’aide au raisonnement ainsi que les limites et risques associés à l’utilisation d’outils comme ChatGPT.

L’un des principaux acquis a été de comprendre que l’IA doit être considérée comme un **outil d’assistance à la réflexion et à la productivité**, et non comme un substitut au jugement humain.

**3. Marketing digital — campagnes Facebook Ads**

Cette formation m’a permis d’élargir ma compréhension du cycle de vie d’un produit au-delà de son aspect purement technique.

J’ai étudié les étapes nécessaires à la préparation d’une campagne publicitaire, notamment :

-   la définition de l’objectif ;
    
-   l’identification de l’audience ;
    
-   le ciblage ;
    
-   la création des publicités ;
    
-   le budget ;
    
-   les placements ;
    
-   le suivi des performances ;
    
-   l’optimisation ;
    
-   l’A/B testing.
    

Cette partie m’a permis de comprendre qu’un produit technique doit également répondre à un besoin, atteindre une cible et être correctement présenté à ses utilisateurs.

**4. Personal branding — cas de LinkedIn**

Le travail sur le personal branding m’a permis de réfléchir à la manière de construire et de communiquer une identité professionnelle cohérente.

J’ai notamment travaillé sur :

-   le positionnement professionnel ;
    
-   la présence numérique ;
    
-   l’e-réputation ;
    
-   la ligne éditoriale ;
    
-   la création de contenu ;
    
-   LinkedIn comme canal professionnel ;
    
-   la valorisation des projets et apprentissages.
    

Cette formation a également été appliquée pendant le stage à travers la publication de contenus autour de ma progression DevOps.

**5. Utilisation de la ligne de commande**

Enfin, le Sprint 1 s’est terminé par le développement de compétences plus techniques autour du terminal.

Cette formation m’a permis de mieux maîtriser :

-   la navigation dans l’arborescence ;
    
-   la création et la suppression de fichiers et répertoires ;
    
-   la manipulation de fichiers ;
    
-   l’exécution de commandes ;
    
-   l’utilisation d’un environnement Linux.
    

Cette maîtrise était indispensable pour la suite du stage, puisque Docker, Kubernetes, Terraform, Ansible et de nombreux outils DevOps sont largement utilisés en ligne de commande.

----------

## Sprint 2 — Conteneurisation, orchestration, Infrastructure as Code et CI/CD

Le Sprint 2 constitue véritablement le passage vers le **cœur technique du DevOps**.

Son objectif était de comprendre comment construire, sécuriser et déployer une application moderne.

### 1. Kubernetes

Cette formation m’a permis de découvrir l’orchestration des conteneurs et plusieurs concepts fondamentaux :

-   Pods ;
    
-   Deployments ;
    
-   ReplicaSets ;
    
-   Services ;
    
-   configuration ;
    
-   réplication ;
    
-   mise à jour progressive ;
    
-   disponibilité des applications.
    

Elle m’a notamment permis de comprendre qu’il existe une différence importante entre **exécuter un conteneur Docker** et **orchestrer une application conteneurisée en production**.

### 2. Helm

Helm a ensuite permis de simplifier et de structurer les déploiements Kubernetes.

Les principaux concepts abordés comprenaient :

-   les Charts ;
    
-   les templates ;
    
-   `values.yaml` ;
    
-   le paramétrage des déploiements ;
    
-   la réutilisation des configurations.
    

Helm peut ainsi être considéré comme un mécanisme de packaging permettant de rendre les ressources Kubernetes plus maintenables et configurables.

### 3. Terraform

Terraform a introduit le concept d'**Infrastructure as Code**.

J’ai appris à décrire une infrastructure à partir de fichiers déclaratifs et à utiliser notamment :

-   les providers ;
    
-   les resources ;
    
-   les variables ;
    
-   les outputs ;
    
-   `terraform init` ;
    
-   `terraform plan` ;
    
-   `terraform apply` ;
    
-   `terraform destroy` ;
    
-   la notion de state.
    

Cette approche permet de disposer d’une infrastructure reproductible, versionnable et automatisable.

### 4. Sécurité Kubernetes

Une formation spécifique a également été consacrée à la sécurisation des environnements Kubernetes.

Les principaux sujets étudiés concernaient notamment :

-   les identités ;
    
-   les Service Accounts ;
    
-   RBAC ;
    
-   la gestion des permissions ;
    
-   les Secrets ;
    
-   la provenance des images ;
    
-   le scanning des images ;
    
-   la signature des images ;
    
-   la limitation des privilèges.
    

Cette formation m’a permis de comprendre qu’un cluster fonctionnel n’est pas automatiquement un cluster sécurisé.

----------

### Mini-projet Sprint 2 — Formuloo Compta

Le mini-projet du Sprint 2 a constitué la première grande mise en pratique des acquis DevOps.

#### Objectif

> **Conteneuriser Formuloo Compta, mettre en place une pipeline CI/CD GitLab et déployer l’application sur AWS.**

L’application était constituée de :

```text
Frontend Angular
       │
       ↓
Backend Django
       │
       ↓
PostgreSQL
```

Le travail réalisé a progressivement conduit à la chaîne suivante :

```text
Code source
    ↓
GitLab
    ↓
Pipeline CI/CD
    ↓
Tests / Build
    ↓
Images Docker
    ↓
Amazon ECR
    ↓
Infrastructure Terraform
    ↓
AWS ECS Fargate
    ↓
Application déployée
```

Ce projet a permis de réunir les notions de conteneurisation, CI/CD, registry Docker, Infrastructure as Code et cloud.

Il m’a également confronté à plusieurs difficultés réelles : configuration de la communication frontend/backend, erreurs `502 Bad Gateway`, mise à jour des Task Definitions ECS, erreurs `CannotPullContainerError`, gestion des credentials Terraform/AWS et analyse des coûts AWS.

Ces difficultés ont fortement contribué au développement de ma capacité de **debugging systématique**.

----------

## Sprint 3 — Configuration, GitOps, observabilité et résilience

Le Sprint 3 correspond à une nouvelle étape.

Après avoir appris à **déployer une application**, il fallait maintenant apprendre à :

> **la configurer, maintenir son état, l’observer et la rendre plus résiliente.**

### 1. Ansible — Configuration Management

La première formation concernait Ansible et la gestion automatisée de configuration.

J’ai notamment étudié :

-   control node ;
    
-   managed nodes ;
    
-   inventory ;
    
-   playbooks ;
    
-   tasks ;
    
-   modules ;
    
-   connexion SSH ;
    
-   idempotence.
    

Le laboratoire réalisé avec un environnement sous WSL et un nœud géré conteneurisé m’a également confronté à des difficultés liées à l’inventory et à SSH.

Cela m’a permis de comprendre que l’automatisation de configuration dépend également de couches sous-jacentes telles que le réseau, l’authentification et SSH.

### 2. Argo CD — GitOps

Argo CD m’a permis de découvrir le modèle GitOps.

L’idée fondamentale étudiée est la suivante :

```text
Git
 │
 │ état désiré
 ↓
Argo CD
 │
 │ comparaison / synchronisation
 ↓
Environnement réel
```

Les notions étudiées incluent :

-   desired state ;
    
-   live state ;
    
-   synchronisation ;
    
-   `OutOfSync` ;
    
-   auto-sync ;
    
-   drift ;
    
-   self-healing ;
    
-   réconciliation.
    

Cette formation m’a permis de mieux distinguer les rôles respectifs de la CI et de la CD.

### 3. Observabilité — Stack complète

La formation sur l’observabilité m’a permis d’aborder les trois piliers classiques :

```text
         Observabilité
        /      |      \
   Metrics    Logs    Traces
```

Un accent particulier a été mis sur les métriques à travers **Prometheus** et leur visualisation avec **Grafana**.

J’ai notamment appris à distinguer :

-   les métriques système ;
    
-   les métriques infrastructure ;
    
-   les métriques applicatives ;
    
-   les indicateurs réellement utiles pour analyser le fonctionnement d’un service.
    

Le travail autour des métriques Django m’a permis d’observer des informations telles que le nombre de requêtes et les temps de réponse.

L’utilisation de PromQL permet ensuite de transformer ces métriques brutes en indicateurs exploitables.

### 4. Résilience en production

La dernière formation était consacrée à la capacité d’un système à continuer à fonctionner ou à revenir rapidement à un état opérationnel après une défaillance.

Les concepts principaux étudiés étaient :

-   disponibilité ;
    
-   redondance ;
    
-   health checks ;
    
-   fault tolerance ;
    
-   auto-healing ;
    
-   sauvegarde ;
    
-   Disaster Recovery ;
    
-   RPO ;
    
-   RTO ;
    
-   SLI ;
    
-   SLO ;
    
-   SLA.
    

Cette formation a profondément modifié la manière d’analyser une architecture.

La question n’est plus seulement :

> « Est-ce que mon application fonctionne ? »

mais également :

> « Que se passe-t-il si un composant tombe en panne ? »

----------

### Mini-projet Sprint 3 — Observabilité Formuloo Compta

Le dernier mini-projet du stage consistait à mettre en œuvre l’observabilité de **Formuloo Compta** à l’aide d’une stack :

> **Prometheus + Grafana sur AWS EC2**

L’objectif était de passer d’une application simplement déployée à une application dont le comportement pouvait être observé.

Architecture logique :

```text
        Formuloo Compta
              │
              │ métriques
              ↓
         Prometheus
              │
              │ PromQL
              ↓
           Grafana
              │
              ↓
          Dashboards
```

Ce projet m’a permis de mettre en pratique plusieurs notions du Sprint 3 :

-   collecte de métriques ;
    
-   exposition de métriques Django ;
    
-   configuration de Prometheus ;
    
-   requêtes PromQL ;
    
-   création de dashboards Grafana ;
    
-   déploiement de la stack d’observabilité sur AWS EC2 ;
    
-   réflexion autour des indicateurs pertinents pour une application réelle.
    

----------

# En Résumé

La progression du stage est la suivante :

```text
SPRINT 1
Comment travailler efficacement et être productif
        ↓
Agile / Scrum
IA
Marketing
Personal Branding
Terminal
        ↓

SPRINT 2
Construire et déployer
        ↓
Kubernetes
Helm
Terraform
Sécurité Kubernetes
        ↓
FORMULOO COMPTA
Docker + GitLab CI/CD + AWS
        ↓

SPRINT 3
Exploiter, observer et fiabiliser
        ↓
Ansible
Argo CD
Observabilité
Résilience
        ↓
FORMULOO COMPTA
Prometheus + Grafana + AWS EC2
```
> **Sprint 1 : apprendre à travailler efficacement.**  
> **Sprint 2 : apprendre à construire et déployer.**  
> **Sprint 3 : apprendre à exploiter, observer et fiabiliser.**

Et derrière les trois :

> **Formuloo Compta a servi de terrain pratique permettant de transformer les formations en compétences opérationnelles.**

# CONCLUSION

Ce stage pratique m'a confronté à des problèmes réels, notamment des difficultés liées aux communications entre services, aux déploiements ECS, aux images Docker, aux credentials AWS et à la facturation de certaines ressources cloud. Ces situations ont constitué des occasions importantes d'appliquer une démarche de diagnostic et de résolution de problèmes.
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTIwMDgzNjM2LDEyNzIwNTI2NTMsLTcyMD
I3NDE0LDEzMDEwMDkzNDUsLTEyOTQ3MTcwNDksNTkyMzM4NzI1
XX0=
-->