# Kubernetes (K8s) — Notes de révision structurées

> Objectif : Comprendre les concepts fondamentaux de Kubernetes et savoir déployer une application conteneurisée de manière scalable et résiliente.

---

## Résumé structuré

### C'est quoi ?

Kubernetes (K8s) est un orchestrateur de conteneurs open source permettant d'automatiser le déploiement, la gestion, la mise à l'échelle et la haute disponibilité des applications conteneurisées.

Il ne remplace pas Docker : Docker crée et exécute les conteneurs, tandis que Kubernetes les orchestre à grande échelle.

---

### Pourquoi ?

Lorsque le nombre de conteneurs augmente, leur gestion manuelle devient difficile :

- redémarrer les conteneurs en panne ;
- répartir la charge entre plusieurs instances ;
- mettre à jour une application sans interruption ;
- augmenter ou réduire automatiquement le nombre d'instances ;
- connecter les conteneurs entre eux.

Kubernetes automatise toutes ces tâches.

---

### Comment ?

Kubernetes fonctionne grâce à plusieurs objets qui collaborent :

Application → Container → Pod → ReplicaSet → Deployment → Service

Le Deployment décrit l'état souhaité, le ReplicaSet maintient le nombre de Pods demandé et le Service expose ces Pods afin qu'ils soient accessibles.

---

## Résumé narratif

Kubernetes est conçu pour gérer des applications composées de conteneurs. Contrairement à Docker qui exécute simplement un conteneur, Kubernetes supervise en permanence l'état de l'application afin qu'elle corresponde toujours à l'état désiré. Si un Pod tombe en panne, Kubernetes en crée automatiquement un nouveau. Si davantage de capacité est nécessaire, il crée des Pods supplémentaires. À l'inverse, si la charge diminue, il peut réduire le nombre de Pods.

Le déploiement d'une application suit généralement le même cycle : créer une image Docker, définir un Deployment pour préciser l'image et le nombre de réplicas, puis créer un Service qui rend l'application accessible. Les Services utilisent des labels et des selectors pour découvrir automatiquement les Pods à exposer, ce qui permet aux Pods d'être remplacés sans impacter les utilisateurs.

Cette architecture rend les applications plus robustes, plus facilement scalables et plus simples à maintenir.

---

## Les composants essentiels

### Cluster

Ensemble de machines exécutant Kubernetes.

Un cluster est composé de :

- Control Plane (Master)
- Worker Nodes

---

### Control Plane

C'est le cerveau du cluster.

Il :

- reçoit les requêtes (`kubectl`)
- décide où lancer les Pods
- surveille l'état du cluster
- maintient l'état désiré

---

### Worker Node

Machine qui exécute réellement les applications.

Chaque Worker contient :

- kubelet
- kube-proxy
- runtime de conteneurs (containerd, Docker...)

---

### Pod

Plus petite unité déployable dans Kubernetes.

Un Pod contient :

- un ou plusieurs conteneurs
- une adresse IP
- un stockage temporaire

À retenir :

- un Pod est éphémère
- on ne travaille presque jamais directement avec un Pod
- Kubernetes peut le recréer à tout moment

---

### ReplicaSet

Garantit qu'il existe toujours le nombre souhaité de Pods.

Exemple :

```
Replicas = 3
```

Si un Pod est supprimé :

```
3 Pods
↓

1 Pod supprimé

↓

ReplicaSet crée immédiatement un nouveau Pod
```

---

### Deployment

Objet utilisé quotidiennement.

Il permet de :

- créer des Pods
- gérer les ReplicaSets
- mettre à jour les applications
- effectuer des Rolling Updates
- revenir à une ancienne version (Rollback)

C'est l'objet recommandé pour déployer une application.

---

### Service

Les Pods changent constamment.

Leur IP n'est donc pas fiable.

Le Service fournit :

- une IP stable
- un nom DNS
- un point d'accès permanent

Le Service sélectionne les Pods grâce aux :

- Labels
- Selectors

Exemple :

```
label:
app: nginx
```

Le Service recherche :

```
selector:
app: nginx
```

Tous les Pods possédant ce label sont automatiquement connectés au Service.

---

## Les différents types de Service

### ClusterIP

- Type par défaut
- Accessible uniquement depuis le cluster

---

### NodePort

Expose l'application via :

```
NodeIP:Port
```

Utilisé principalement pour les tests.

---

### LoadBalancer

Utilisé dans le Cloud.

Crée automatiquement un Load Balancer externe.

---

## Cycle de vie d'un déploiement

```
Docker Image

↓

Deployment

↓

ReplicaSet

↓

Pods

↓

Service

↓

Utilisateur
```

---

## Scale

Le Scale consiste à modifier le nombre de Pods.

Exemple :

Avant

```
3 Pods
```

Après

```
10 Pods
```

Kubernetes crée automatiquement les nouveaux Pods.

---

## Rolling Update

Permet de mettre à jour une application sans interruption.

Exemple :

```
Version 1

↓

Création d'un Pod Version 2

↓

Suppression d'un Pod Version 1

↓

Répéter jusqu'à migration complète
```

Les utilisateurs continuent d'accéder au service pendant toute la mise à jour.

---

## Rollback

Si une mise à jour échoue :

```
Version 2

↓

Erreur

↓

Rollback

↓

Retour Version 1
```

---

## Les Labels

Les Labels sont des paires clé/valeur.

Exemple :

```
app=frontend

env=production

version=v2
```

Ils servent à :

- organiser les ressources
- filtrer les Pods
- connecter les Services aux Pods

---

## Les Selectors

Les Selectors recherchent les Pods possédant certains Labels.

Exemple :

```
Selector

app=frontend
```

Tous les Pods ayant :

```
app=frontend
```

seront sélectionnés.

---

## Workflow complet

```
Créer une image Docker

↓

Push vers un Registry

↓

Créer un Deployment

↓

Créer un Service

↓

Tester

↓

Scaler si besoin

↓

Mettre à jour

↓

Rollback si nécessaire
```

---

## Bonnes pratiques

- Ne jamais créer directement des Pods en production.
- Utiliser des Deployments pour gérer les applications.
- Exposer les Pods via un Service plutôt que par leur adresse IP.
- Utiliser des Labels cohérents et explicites.
- Garder les Pods stateless lorsque c'est possible.
- Effectuer les mises à jour avec des Rolling Updates.
- Vérifier régulièrement l'état du cluster et des Pods avec `kubectl`.

---

## Commandes essentielles

```bash
kubectl get pods
```

Lister les Pods.

```bash
kubectl get deployments
```

Lister les Deployments.

```bash
kubectl get services
```

Lister les Services.

```bash
kubectl describe pod <nom>
```

Afficher les détails d'un Pod.

```bash
kubectl logs <pod>
```

Consulter les journaux d'un Pod.

```bash
kubectl apply -f deployment.yaml
```

Créer ou mettre à jour une ressource.

```bash
kubectl delete -f deployment.yaml
```

Supprimer une ressource.

```bash
kubectl scale deployment nginx --replicas=5
```

Modifier le nombre de Pods.

```bash
kubectl rollout status deployment/nginx
```

Suivre une mise à jour.

```bash
kubectl rollout undo deployment/nginx
```

Revenir à la version précédente.

---

## Points clés à retenir

- Kubernetes orchestre des conteneurs, tandis que Docker les exécute.
- Le Pod est la plus petite unité déployable, mais il est éphémère.
- Le ReplicaSet garantit le nombre de Pods demandé.
- Le Deployment est l'objet principal pour déployer et mettre à jour des applications.
- Le Service fournit un point d'accès stable en utilisant des Labels et des Selectors.
- Le Scaling, les Rolling Updates et les Rollbacks assurent disponibilité et évolution sans interruption.
- Kubernetes repose sur le principe de l'état désiré : il compare en permanence l'état réel à l'état attendu et agit automatiquement pour les faire correspondre.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAyODU2MDAxNl19
-->