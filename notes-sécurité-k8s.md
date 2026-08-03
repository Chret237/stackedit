# Sécurité Kubernetes — Notes de révision structurées

> Objectif : Comprendre les principaux mécanismes de sécurité de Kubernetes afin de protéger un cluster, ses applications et ses données.

---

## Résumé structuré

### C'est quoi ?

La sécurité Kubernetes consiste à protéger un cluster contre les accès non autorisés, les vulnérabilités des conteneurs et les mauvaises configurations. Elle couvre l'ensemble du cycle de vie de l'application : de l'image Docker jusqu'au Pod en exécution, en passant par les utilisateurs, les secrets et les communications réseau.

Kubernetes applique le principe du moindre privilège : chaque utilisateur, application ou composant ne doit disposer que des permissions strictement nécessaires.

---

### Pourquoi ?

Sans mécanismes de sécurité :

- n'importe quel utilisateur pourrait modifier le cluster ;
- un Pod compromis pourrait accéder à d'autres applications ;
- des mots de passe pourraient être exposés ;
- une image vulnérable pourrait être déployée en production ;
- un attaquant pourrait compromettre l'ensemble du cluster.

La sécurité permet donc de protéger les ressources, les données et la disponibilité des applications.

---

### Comment ?

La sécurité Kubernetes repose sur plusieurs couches :

- Authentification : identifier qui demande l'accès.
- Autorisation (RBAC) : définir ce que cette identité est autorisée à faire.
- Service Accounts : donner une identité aux applications.
- Secrets : stocker les informations sensibles.
- Network Policies : contrôler les communications entre Pods.
- Sécurité des conteneurs : utiliser des images fiables, les analyser et les signer.
- Security Context : limiter les privilèges des conteneurs.

---

## Résumé narratif

La sécurité dans Kubernetes ne se limite pas à empêcher les intrusions. Elle vise à protéger l'ensemble de la plateforme en contrôlant les accès, les communications et les ressources utilisées par les applications. Kubernetes distingue les utilisateurs humains et les applications : les premiers s'authentifient généralement via des certificats ou un fournisseur d'identité, tandis que les applications utilisent des Service Accounts.

Une fois l'identité connue, Kubernetes applique les règles RBAC (Role-Based Access Control) afin de déterminer les actions autorisées. Cette séparation entre identité et permissions permet d'appliquer le principe du moindre privilège et de réduire les risques en cas de compromission.

Le cours met également l'accent sur la sécurité de la chaîne de déploiement. Une image Docker doit provenir d'un registre de confiance, être analysée (-image scanning-) pour détecter les vulnérabilités et être signée (-image signing-) afin de garantir son authenticité. En production, il est recommandé d'utiliser des versions d'images immuables (par exemple `nginx:1.29.0`) plutôt que des tags variables comme `latest`, afin de garantir des déploiements reproductibles.

Enfin, Kubernetes protège les données sensibles grâce aux Secrets, limite les communications avec les Network Policies et réduit les privilèges des conteneurs via les Security Contexts. La sécurité est donc une démarche globale qui commence avant le déploiement et continue pendant toute la vie de l'application.

---

## Les concepts essentiels

### Authentification

L'authentification répond à la question :

> Qui es-tu ?

Elle vérifie l'identité de l'utilisateur ou de l'application.

Exemples :

- Certificats
- Tokens
- OpenID Connect (OIDC)

Une fois authentifié, Kubernetes connaît l'identité du demandeur.

---

### Autorisation (RBAC)

L'autorisation répond à la question :

> Que peux-tu faire ?

RBAC attribue des permissions à des utilisateurs ou des Service Accounts.

Exemple :

```text
Développeur
    ↓
Peut créer des Pods

↓

Ne peut pas supprimer les Nodes
```

Le principe est de n'accorder que les permissions nécessaires.

---

### Service Account

Un Service Account est une identité destinée aux applications exécutées dans Kubernetes.

Contrairement aux utilisateurs humains :

- il est utilisé par les Pods ;
- il permet aux applications de communiquer avec l'API Kubernetes ;
- ses permissions sont définies par RBAC.

> À retenir de ton apprentissage : le Service Account fournit l'identité, tandis que RBAC définit les autorisations associées.

---

### Secrets

Les Secrets permettent de stocker des informations sensibles :

- mots de passe ;
- clés API ;
- certificats ;
- jetons d'authentification.

Ils évitent d'inscrire ces informations directement dans les fichiers YAML ou dans les images Docker.

---

### ConfigMap vs Secret

| ConfigMap                  | Secret                |
| -------------------------- | --------------------- |
| Configuration non sensible | Données sensibles     |
| Variables d'environnement  | Mots de passe, tokens |
| Lisible en clair           | Encodé et à protéger  |

---

### Image Scanning

L'Image Scanning consiste à analyser une image de conteneur afin d'identifier :

- vulnérabilités connues (CVE) ;
- dépendances obsolètes ;
- bibliothèques non sécurisées.

Cette analyse est effectuée avant le déploiement.

> À retenir de ton apprentissage : le scanning détecte les vulnérabilités avant qu'elles n'atteignent la production.

---

### Image Signing

L'Image Signing garantit l'origine et l'intégrité d'une image.

Une image signée permet de vérifier :

- qu'elle provient d'un éditeur de confiance ;
- qu'elle n'a pas été modifiée depuis sa création.

> À retenir de ton apprentissage : le scanning vérifie la sécurité de l'image, tandis que la signature prouve son authenticité.

---

### Registre d'images

En production, les images doivent provenir d'un registre approuvé.

Exemples :

- Docker Hub (officiel)
- GitHub Container Registry
- Azure Container Registry
- Amazon ECR
- Google Artifact Registry

Limiter les registres autorisés réduit le risque de déployer des images malveillantes.

---

### Éviter le tag `latest`

Mauvaise pratique :

```text
nginx:latest
```

Bonne pratique :

```text
nginx:1.29.0
```

Pourquoi ?

Le tag `latest` peut changer à tout moment et produire des déploiements différents ou introduire des vulnérabilités.

> À retenir de ton apprentissage : utiliser une version fixe garantit la stabilité et la reproductibilité.

---

### Network Policies

Les Network Policies contrôlent les communications réseau entre les Pods.

Sans règle :

```text
Tous les Pods peuvent communiquer
```

Avec une Network Policy :

```text
Frontend
    │
    ▼
Backend

Base de données
▲
│
Accessible uniquement
depuis Backend
```

Elles limitent les déplacements d'un attaquant dans le cluster.

---

### Security Context

Le Security Context définit les privilèges d'un Pod ou d'un conteneur.

Bonnes pratiques :

- ne pas exécuter les conteneurs en tant que `root` ;
- rendre le système de fichiers en lecture seule lorsque possible ;
- supprimer les capacités Linux inutiles ;
- empêcher l'escalade de privilèges.

---

## Workflow sécurisé

```text
Créer l'image Docker

↓

Scanner l'image

↓
Signer l'image

↓

Publier dans un registre approuvé

↓

Déployer avec Helm/Kubernetes

↓

RBAC contrôle les accès

↓

Service Account fournit l'identité

↓

Secrets protègent les données

↓

Network Policies limitent les communications
```

---

## Bonnes pratiques

- Utiliser des images officielles et maintenues.
- Éviter le tag `latest` en production.
- Scanner toutes les images avant leur déploiement.
- Signer les images utilisées en production.
- Appliquer le principe du moindre privilège avec RBAC.
- Attribuer un Service Account dédié à chaque application.
- Stocker les informations sensibles dans des Secrets.
- Limiter les communications entre Pods avec des Network Policies.
- Éviter d'exécuter les conteneurs avec les privilèges `root`.
- Mettre régulièrement à jour Kubernetes et les images de conteneurs.

---

## Commandes essentielles

```bash
kubectl get serviceaccounts
```

Lister les Service Accounts.

```bash
kubectl get roles
kubectl get rolebindings
```

Afficher les rôles et leurs associations.

```bash
kubectl get secrets
```

Lister les Secrets.

```bash
kubectl describe secret <nom>
```

Afficher les informations d'un Secret.

```bash
kubectl auth can-i create pods
```

Vérifier si une action est autorisée pour l'identité courante.

```bash
kubectl get networkpolicies
```

Lister les Network Policies.

---

## Liens avec Docker, Kubernetes, Helm et Terraform

```text
Docker
│
├── Construire l'image
├── Scanner l'image
└── Signer l'image
        │
        ▼
Registry sécurisé
        │
        ▼
Terraform
(Crée l'infrastructure)
        │
        ▼
Kubernetes
(RBAC, Secrets, Network Policies,
Service Accounts, Security Context)
        │
        ▼
Helm
(Déploie l'application avec
une configuration sécurisée)
```

---

## Points clés à retenir

- La sécurité Kubernetes est une défense en profondeur : plusieurs mécanismes se complètent.
- Authentification = vérifier l'identité ; RBAC = définir les permissions.
- Les Service Accounts fournissent une identité aux applications ; RBAC contrôle ce qu'elles peuvent faire.
- Les Secrets protègent les informations sensibles.
- Le scanning détecte les vulnérabilités ; la signature garantit l'authenticité des images.
- Utiliser des versions d'images fixes plutôt que `latest` améliore la stabilité et la sécurité.
- Les Network Policies limitent les communications réseau entre Pods.
- Les Security Contexts réduisent les privilèges des conteneurs.
- La sécurité doit être intégrée dès la construction de l'image jusqu'à l'exécution de l'application.

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU2NzEzMTc3N119
-->