# Helm — Notes de révision structurées

> **Objectif :** Comprendre comment Helm simplifie le déploiement, la configuration et la gestion des applications Kubernetes.

---

## Résumé structuré

### C'est quoi ?

**Helm** est le **gestionnaire de packages de Kubernetes**. À l'image de `apt` pour Ubuntu ou `npm` pour Node.js, Helm permet d'installer, configurer, mettre à jour et supprimer facilement des applications Kubernetes.

Au lieu d'écrire et de maintenir plusieurs fichiers YAML, Helm les regroupe dans un **Chart**, un package réutilisable et paramétrable.

---

### Pourquoi ?

Déployer une application Kubernetes nécessite souvent plusieurs ressources :

* Deployment
* Service
* ConfigMap
* Secret
* Ingress
* PersistentVolumeClaim

Gérer ces fichiers individuellement devient rapidement complexe, surtout lorsqu'il faut déployer la même application dans plusieurs environnements (développement, test, production).

Helm permet de :

* automatiser les déploiements ;
* réutiliser les configurations ;
* personnaliser les déploiements sans modifier les fichiers YAML ;
* versionner les applications ;
* simplifier les mises à jour et les retours en arrière.

---

### Comment ?

Helm fonctionne autour de quatre concepts principaux :

* **Chart** : le package contenant les manifestes Kubernetes.
* **Release** : une instance installée d'un Chart dans un cluster.
* **Repository** : un dépôt où sont stockés les Charts.
* **Values** : les paramètres utilisés pour personnaliser un Chart.

Le cycle de vie est le suivant :

Repository → Chart → Values → Release → Ressources Kubernetes

---

## Résumé narratif

Helm est un outil conçu pour simplifier la gestion des applications Kubernetes. Sans Helm, chaque application nécessite plusieurs fichiers YAML qu'il faut créer, maintenir et appliquer manuellement avec `kubectl`. Cette approche devient rapidement difficile lorsque les applications évoluent ou doivent être déployées dans plusieurs environnements.

Helm résout ce problème en regroupant toutes les ressources Kubernetes dans un **Chart**. Ce package contient des modèles (templates) qui génèrent automatiquement les manifestes YAML en fonction des paramètres définis dans le fichier **values.yaml**. Ainsi, une même application peut être déployée avec des configurations différentes sans modifier les fichiers d'origine.

Lorsqu'un Chart est installé, Helm crée une **Release**, c'est-à-dire une instance de cette application dans le cluster. Helm garde un historique des Releases, ce qui facilite les mises à jour et permet d'effectuer un **rollback** en cas d'échec. Grâce à cette approche, Helm améliore la reproductibilité des déploiements, réduit les erreurs de configuration et facilite l'administration des applications Kubernetes.

---

## Les composants essentiels

### Chart

Le **Chart** est l'unité de base de Helm.

Il contient :

* les modèles Kubernetes (templates) ;
* les paramètres par défaut ;
* les métadonnées du package ;
* les dépendances éventuelles.

Un Chart représente une application complète.

---

#### Structure d'un Chart

```text
my-chart/
│
├── Chart.yaml
├── values.yaml
├── charts/
├── templates/
│   ├── deployment.yaml
│   ├── service.yaml
│   ├── ingress.yaml
│   └── ...
└── .helmignore
```

##### Rôle des fichiers

**Chart.yaml**

Décrit le Chart :

* nom
* version
* description
* dépendances

---

**values.yaml**

Contient les valeurs configurables.

Exemple :

```yaml
replicaCount: 3

image:
  repository: nginx
  tag: latest

service:
  type: ClusterIP
  port: 80
```

Il permet d'adapter le déploiement sans modifier les templates.

---

**templates/**

Contient les fichiers Kubernetes utilisant le langage de templating de Helm.

Exemple :

```yaml
replicas: {{ .Values.replicaCount }}
```

Au moment de l'installation, Helm remplace cette expression par la valeur définie dans `values.yaml`.

---

## Repository

Les Charts sont stockés dans des **Repositories Helm**.

Exemple :

```text
Bitnami Repository
```

On peut :

* ajouter un dépôt ;
* rechercher un Chart ;
* installer directement une application.

---

## Release

Une **Release** est une instance d'un Chart installée dans un cluster.

Exemple :

```text
Chart : nginx

↓

Release : mon-nginx
```

On peut installer plusieurs Releases d'un même Chart avec des configurations différentes.

---

## Values

Les **Values** permettent de personnaliser un Chart.

Exemple :

Développement :

```yaml
replicaCount: 1
```

Production :

```yaml
replicaCount: 5
```

Le même Chart est utilisé dans les deux cas.

---

## Templating

Helm utilise le moteur de templates Go.

Exemple :

```yaml
image:
  repository: {{ .Values.image.repository }}
```

Helm remplace automatiquement cette variable lors du déploiement.

---

## Workflow Helm

```text
Créer un Chart

↓

Définir les templates

↓

Configurer values.yaml

↓

Installer le Chart

↓

Créer une Release

↓

Kubernetes crée les ressources
```

---

## Cycle de vie d'une Release

```text
helm install

↓

Release créée

↓

helm upgrade

↓

Nouvelle version

↓

helm rollback

↓

Retour à la version précédente

↓

helm uninstall

↓

Suppression de la Release
```

---

## Bonnes pratiques

* Ne jamais modifier directement les templates pour changer un environnement.
* Utiliser `values.yaml` pour toute personnalisation.
* Versionner les Charts avec Git.
* Donner des noms explicites aux Releases.
* Tester un Chart avant son déploiement.
* Réutiliser les Charts existants lorsque c'est possible.
* Organiser les paramètres par environnement (`values-dev.yaml`, `values-prod.yaml`).

---

## Commandes essentielles

### Ajouter un dépôt

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```

---

### Mettre à jour les dépôts

```bash
helm repo update
```

---

### Rechercher un Chart

```bash
helm search repo nginx
```

---

### Créer un nouveau Chart

```bash
helm create mon-chart
```

---

### Installer un Chart

```bash
helm install mon-nginx bitnami/nginx
```

---

### Installer avec un fichier de configuration

```bash
helm install mon-app ./mon-chart -f values-prod.yaml
```

---

### Lister les Releases

```bash
helm list
```

---

### Mettre à jour une Release

```bash
helm upgrade mon-app ./mon-chart
```

---

### Voir l'historique

```bash
helm history mon-app
```

---

### Revenir à une ancienne version

```bash
helm rollback mon-app 1
```

---

### Désinstaller une Release

```bash
helm uninstall mon-app
```

---

## Helm vs kubectl

| kubectl                               | Helm                                  |
| ------------------------------------- | ------------------------------------- |
| Déploie des fichiers YAML individuels | Déploie un Chart complet              |
| Peu de réutilisation                  | Templates réutilisables               |
| Configuration souvent dupliquée       | Paramétrage via `values.yaml`         |
| Gestion manuelle des versions         | Historique et rollback intégrés       |
| Idéal pour des ressources simples     | Idéal pour des applications complètes |

---

## Points clés à retenir

* Helm est le **gestionnaire de packages de Kubernetes**.
* Un **Chart** regroupe toutes les ressources nécessaires à une application.
* Une **Release** est une instance installée d'un Chart.
* Le fichier **values.yaml** permet de personnaliser un déploiement sans modifier les templates.
* Les **templates** utilisent des variables remplacées lors de l'installation.
* Helm facilite les **mises à jour**, les **rollbacks** et le **versionnement** des applications.
* L'utilisation de Helm rend les déploiements Kubernetes **plus simples, reproductibles et maintenables**.

---

## Liens avec Kubernetes

```text
Docker
        │
        ▼
Image Docker
        │
        ▼
Kubernetes
(Deployment, Service, ConfigMap...)
        ▲
        │
Helm génère automatiquement
les manifestes Kubernetes
à partir d'un Chart
```

## À retenir

* **Docker** construit l'application.
* **Kubernetes** orchestre les conteneurs.
* **Helm** automatise et standardise le déploiement des ressources Kubernetes.

Helm est donc une couche d'abstraction au-dessus de Kubernetes, qui permet de gérer des applications complexes de manière simple, cohérente et reproductible.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwNzQ0MjEzODhdfQ==
-->