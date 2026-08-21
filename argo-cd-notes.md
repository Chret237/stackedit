# Argo CD — Notes de révision structurées

> **Objectif :** comprendre le fonctionnement du **GitOps avec Argo CD** et comment automatiser le déploiement et la synchronisation d'applications Kubernetes à partir d'un dépôt Git.

----------

## 1. C'est quoi ?

**Argo CD** est un outil de **Continuous Delivery (CD) orienté GitOps pour Kubernetes**.

Il considère **Git comme la source de vérité** de l'état souhaité du cluster.

Au lieu qu'une pipeline CI exécute directement des commandes `kubectl` pour déployer l'application, Argo CD surveille le dépôt Git et s'assure que le cluster Kubernetes correspond à ce qui y est déclaré.

```text
Git Repository
      │
      │ État souhaité
      ▼
   Argo CD
      │
      │ Synchronisation
      ▼
Kubernetes Cluster
      │
      ▼
Application
```

----------

# 2. Pourquoi utiliser Argo CD ?

Dans une approche traditionnelle, la CI peut construire l'image puis déployer directement :

```text
Code → CI → Docker → Registry → kubectl → Kubernetes
```

Cette approche donne davantage de responsabilités à la pipeline.

Avec GitOps + Argo CD :

```text
Code
 │
 ▼
CI
 │
 ├── Tests
 ├── Build
 └── Push image
       │
       ▼
    Registry

Configuration Kubernetes
       │
       ▼
      Git
       │
       ▼
    Argo CD
       │
       ▼
  Kubernetes
```

Le principal avantage est la **séparation des responsabilités** :

-   la **CI** construit et teste ;
    
-   **Git** conserve l'état souhaité ;
    
-   **Argo CD** déploie et synchronise ;
    
-   **Kubernetes** exécute l'application.
    

----------

# 3. Comment fonctionne GitOps ?

GitOps repose sur une idée centrale :

> **L'état du système doit être décrit dans Git et Git doit être la source de vérité.**

Par exemple :

```yaml
replicas: 3
image:
  repository: my-app
  tag: v2
```

Git indique qu'on souhaite :

```text
3 Pods
+
Image v2
```

Argo CD compare ensuite :

```text
État Git
   ↕
État Kubernetes
```

Si les deux correspondent :

```text
Synced
```

S'ils diffèrent :

```text
OutOfSync
```

Argo CD peut alors effectuer une synchronisation pour ramener le cluster vers l'état déclaré dans Git.

----------

# 4. Les composants essentiels

## Repository Git

Contient les manifestes Kubernetes ou les Charts Helm utilisés pour déployer l'application.

Exemple :

```text
gitops-repo/
│
├── app/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
│
└── environments/
    ├── dev/
    └── prod/
```

Le dépôt devient donc la **source de vérité**.

----------

## Argo CD

Argo CD surveille le dépôt Git et le cluster Kubernetes.

Il détecte les différences entre :

**Desired State**

et

**Live State**

puis permet de les synchroniser.

----------

## Application Argo CD

L'objet **Application** est central dans Argo CD.

Il définit notamment :

-   le dépôt Git à utiliser ;
    
-   le chemin contenant les manifests ;
    
-   le cluster cible ;
    
-   le namespace cible ;
    
-   la stratégie de synchronisation.
    

Conceptuellement :

```text
Application
     │
     ├── Source : Git
     ├── Path : manifests/
     ├── Cluster : Kubernetes
     └── Namespace : production
```

----------

# 5. Desired State vs Live State

C'est l'un des concepts les plus importants.

### Desired State

Ce qui est déclaré dans Git.

```text
Git
 ↓
3 replicas
image:v2
```

### Live State

Ce qui existe réellement dans Kubernetes.

```text
Cluster
 ↓
2 replicas
image:v1
```

Argo CD détecte :

```text
Desired ≠ Live

       ↓

OutOfSync
```

Puis peut synchroniser :

```text
Git
 ↓
Argo CD
 ↓
Kubernetes
 ↓
Desired State atteint
```

----------

# 6. Synchronisation

La **Sync** permet d'appliquer dans Kubernetes l'état décrit dans Git.

Deux approches sont possibles.

### Synchronisation manuelle

L'opérateur déclenche la synchronisation depuis Argo CD.

```text
Git change
    ↓
OutOfSync
    ↓
Sync manuelle
    ↓
Kubernetes mis à jour
```

### Synchronisation automatique

Argo CD détecte le changement et synchronise automatiquement.

```text
Git change
    ↓
Argo CD détecte
    ↓
Sync automatique
    ↓
Kubernetes mis à jour
```

L'**Auto-Sync** est particulièrement intéressant dans une démarche GitOps.

----------

# 7. Détection du Drift

Le **Drift** correspond à une différence entre l'état déclaré dans Git et l'état réel du cluster.

Exemple :

```text
Git
replicas: 3

        ≠

Kubernetes
replicas: 1
```

Argo CD détecte cette différence.

```text
Git
 │
 │ Desired State
 ▼
Argo CD
 │
 │ comparaison
 ▼
Kubernetes
 │
 ▼
Drift détecté
```

Cela permet de détecter rapidement les modifications effectuées manuellement dans le cluster.

----------

# 8. Self-Healing

Avec **Self-Healing**, Argo CD peut automatiquement corriger certaines modifications manuelles effectuées dans Kubernetes.

Exemple :

```text
Git
replicas: 3

Kubernetes
replicas: 3
```

Un administrateur modifie manuellement :

```bash
kubectl scale deployment app --replicas=1
```

On obtient :

```text
Git = 3
K8s = 1
     ↓
   Drift
     ↓
Argo CD
     ↓
Retour à 3
```

Le cluster revient ainsi vers l'état déclaré dans Git.

----------

# 9. Helm et Argo CD

Argo CD peut également déployer des applications **Helm**.

Les deux outils ont alors des responsabilités différentes :

### Helm

Permet de :

-   créer des Charts ;
    
-   utiliser des templates ;
    
-   gérer `values.yaml` ;
    
-   paramétrer une application Kubernetes.
    

### Argo CD

Permet de :

-   surveiller Git ;
    
-   détecter les changements ;
    
-   synchroniser Helm/Kubernetes ;
    
-   suivre l'état du déploiement.
    

```text
Git
 │
 ▼
Helm Chart
 │
 ▼
Argo CD
 │
 ▼
Kubernetes
```

**Helm = packaging/configuration**

**Argo CD = GitOps / déploiement continu**

----------

# 10. CI/CD vs GitOps

Une distinction importante :

### CI

La CI s'occupe principalement de :

```text
Code
 ↓
Build
 ↓
Tests
 ↓
Image
 ↓
Registry
```

### CD traditionnel

```text
CI
 ↓
kubectl apply
 ↓
Kubernetes
```

### GitOps avec Argo CD

```text
CI
 ↓
Image → Registry

GitOps Repository
 ↓
Argo CD
 ↓
Kubernetes
```

La pipeline **ne doit donc pas nécessairement avoir accès directement au cluster Kubernetes**.

Cela améliore notamment la séparation des responsabilités et la sécurité.

----------

# 11. Workflow GitOps complet

```text
Développeur
    │
    ▼
Code Application
    │
    ▼
Git
    │
    ▼
CI/CD
    │
    ├── Tests
    ├── Build
    └── Push Image
              │
              ▼
           Registry

GitOps Repository
    │
    ▼
Configuration Kubernetes
    │
    ▼
   Argo CD
    │
    ▼
Kubernetes Cluster
    │
    ▼
Application
```

Lorsqu'une nouvelle version est disponible, la configuration Git est mise à jour avec le nouveau tag d'image, puis Argo CD détecte le changement et synchronise le cluster.

----------

# 12. Avantages du GitOps avec Argo CD

### Traçabilité

Chaque changement est enregistré dans Git :

```text
Qui ?
Quand ?
Quoi ?
Pourquoi ?
```

### Reproductibilité

Un environnement peut être recréé à partir de sa configuration Git.

### Auditabilité

L'historique Git permet de connaître les modifications apportées à l'infrastructure applicative.

### Rollback

Revenir à une ancienne configuration Git permet de restaurer une version précédente.

### Détection du Drift

Les modifications manuelles peuvent être détectées.

### Automatisation

Les changements validés dans Git peuvent être déployés automatiquement.

----------

# 13. Bonnes pratiques

-   Utiliser **Git comme source de vérité**.
    
-   Séparer le repository applicatif du repository GitOps lorsque cela apporte de la clarté.
    
-   Ne pas modifier directement les ressources en production avec `kubectl` lorsque le workflow GitOps est établi.
    
-   Utiliser des branches et des Pull/Merge Requests pour contrôler les changements.
    
-   Versionner les images plutôt que d'utiliser `latest`.
    
-   Séparer les configurations **dev / staging / production**.
    
-   Utiliser Helm ou Kustomize pour gérer les différences entre environnements.
    
-   Activer l'**automated sync** et le **self-healing** lorsque le contexte le permet.
    

----------

# 14. Commandes / opérations à connaître

Argo CD peut être utilisé via son interface Web ou sa CLI.

Exemples :

```bash
argocd login <ARGOCD_SERVER>
```

Se connecter au serveur Argo CD.

```bash
argocd app list
```

Lister les applications.

```bash
argocd app get <APP_NAME>
```

Voir l'état d'une application.

```bash
argocd app sync <APP_NAME>
```

Synchroniser une application.

```bash
argocd app history <APP_NAME>
```

Consulter son historique.

```bash
argocd app rollback <APP_NAME> <ID>
```

Revenir à une version précédente.

----------

# 15. Terraform, Ansible, Helm et Argo CD

Les outils étudiés ont des responsabilités différentes et peuvent se compléter :

```text
Terraform
   │
   │ Provisionner
   ▼
Infrastructure
   │
   ▼
Ansible
   │
   │ Configurer
   ▼
Machines / environnement
   │
   ▼
Kubernetes
   ▲
   │
Argo CD
   │
   │ Déployer depuis Git
   ▼
Applications
   ▲
   │
Helm
   │
   │ Packager / paramétrer
   ▼
Charts Kubernetes
```

### À retenir

Outil

Rôle principal

**Terraform**

Provisionnement de l'infrastructure

**Ansible**

Configuration des machines

**Helm**

Packaging et paramétrage Kubernetes

**Argo CD**

GitOps et Continuous Delivery

**Kubernetes**

Orchestration des applications

----------

# 16. Points clés à retenir

-   **Argo CD est un outil GitOps de Continuous Delivery pour Kubernetes.**
    
-   **Git devient la source de vérité** de l'état souhaité.
    
-   Argo CD compare le **Desired State** avec le **Live State**.
    
-   `Synced` signifie que le cluster correspond à Git.
    
-   `OutOfSync` indique une différence entre Git et Kubernetes.
    
-   **Auto-Sync** permet de déployer automatiquement les changements Git.
    
-   **Self-Healing** permet de restaurer l'état souhaité après certains changements manuels.
    
-   Argo CD peut fonctionner avec **Helm** et d'autres outils de gestion de manifests.
    
-   Dans une architecture GitOps, la CI construit et publie les artefacts tandis qu'Argo CD s'occupe du déploiement.
    
-   Git apporte **traçabilité, auditabilité, reproductibilité et possibilité de rollback**.
    

----------

## Synthèse finale

> **Argo CD transforme Kubernetes en un système piloté par Git : on modifie Git, Argo CD détecte la différence et ramène automatiquement le cluster vers l'état déclaré.**

```text
       DEVELOPER
           │
           ▼
      Git Repository
           │
           │ Desired State
           ▼
        Argo CD
       ↙       ↘
   Compare     Sync
       ↘       ↙
     Kubernetes
           │
           ▼
      Application
```

**Terraform construit l'environnement, Ansible le configure, Helm structure les applications Kubernetes et Argo CD assure leur déploiement continu selon Git.**
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzMzU0MTU1NjVdfQ==
-->