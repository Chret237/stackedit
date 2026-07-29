# Terraform — Notes de révision structurées

> Objectif : Comprendre comment Terraform permet de créer, modifier et gérer une infrastructure informatique sous forme de code (Infrastructure as Code - IaC).

---

## Résumé structuré

### C'est quoi ?

Terraform est un outil d'Infrastructure as Code (IaC) développé par HashiCorp. Il permet de décrire une infrastructure (machines virtuelles, réseaux, bases de données, clusters Kubernetes, etc.) dans des fichiers de configuration, puis de laisser Terraform créer, modifier ou supprimer automatiquement ces ressources.

Au lieu de configurer une infrastructure manuellement via une interface graphique, on décrit l'état souhaité dans du code.

---

#### Pourquoi ?

La gestion manuelle d'une infrastructure présente plusieurs limites :

- erreurs humaines ;
- manque de reproductibilité ;
- difficulté à maintenir plusieurs environnements ;
- absence d'historique des changements.

Terraform permet de :

- automatiser le provisionnement des ressources ;
- reproduire la même infrastructure partout ;
- versionner l'infrastructure avec Git ;
- détecter les écarts entre l'infrastructure réelle et celle souhaitée ;
- collaborer facilement en équipe.

---

#### Comment ?

Terraform fonctionne selon un cycle simple :

1. Décrire l'infrastructure dans des fichiers `.tf`.
2. Initialiser le projet (`terraform init`).
3. Comparer l'état actuel avec l'état souhaité (`terraform plan`).
4. Appliquer les changements (`terraform apply`).
5. Mettre à jour le fichier d'état (-terraform.tfstate-).

Terraform répète ce processus à chaque modification afin que l'infrastructure réelle corresponde toujours à la configuration définie.

---

## Résumé narratif

Terraform permet de gérer une infrastructure comme on gère une application : avec du code. Plutôt que de créer manuellement des serveurs, des réseaux ou des bases de données, le développeur décrit l'infrastructure dans des fichiers de configuration. Terraform analyse ensuite l'infrastructure existante, compare cet état avec celui attendu et exécute uniquement les changements nécessaires.

L'un des concepts fondamentaux est l'état désiré (Desired State). Terraform ne recrée pas systématiquement toutes les ressources ; il compare l'état actuel avec la configuration et propose uniquement les opérations nécessaires pour les faire correspondre. Cette approche limite les erreurs, facilite les mises à jour et rend les déploiements reproductibles.

Pour fonctionner, Terraform conserve un State File (`terraform.tfstate`), qui représente l'état connu de l'infrastructure. Ce fichier est essentiel car il permet à Terraform de savoir quelles ressources il gère. En équipe, ce fichier est généralement stocké à distance (-Remote State-) afin que tous les membres travaillent sur une même référence et évitent les conflits.

---

## Les composants essentiels

### Infrastructure as Code (IaC)

Principe consistant à décrire une infrastructure sous forme de code.

Exemple :

Au lieu de créer une VM dans une console cloud :

```text
Créer une VM

↓

Configurer le réseau

↓

Créer une base de données
```

On écrit simplement :

```hcl
resource "aws_instance" "web" {
  ...
}
```

Terraform réalise ensuite le travail automatiquement.

---

#### Provider

Le Provider permet à Terraform de communiquer avec une plateforme.

Exemples :

- AWS
- Azure
- Google Cloud
- Kubernetes
- Docker
- GitHub

Chaque Provider expose les ressources qu'il est capable de créer.

---

#### Resource

Une Resource représente un élément d'infrastructure.

Exemples :

- Machine virtuelle
- Base de données
- Réseau
- Bucket S3
- Cluster Kubernetes

Exemple :

```hcl
resource "aws_instance" "web" {
    ami = "ami-xxxx"
    instance_type = "t2.micro"
}
```

Chaque bloc `resource` décrit une ressource à créer ou gérer.

---

#### Variables

Les variables rendent les configurations réutilisables.

Exemple :

```hcl
variable "instance_type" {
  default = "t2.micro"
}
```

Puis :

```hcl
instance_type = var.instance_type
```

Cela évite de modifier directement le code pour chaque environnement.

---

#### Outputs

Les Outputs permettent d'afficher certaines informations après le déploiement.

Exemple :

```hcl
output "public_ip" {
    value = aws_instance.web.public_ip
}
```

---

#### Modules

Les Modules permettent de réutiliser du code Terraform.

Exemple :

```
Module Réseau

↓

Module VM

↓

Module Base de données
```

Ils améliorent la maintenance et réduisent la duplication.

---

## Le State File

Terraform crée automatiquement :

```
terraform.tfstate
```

Ce fichier contient :

- les ressources créées ;
- leurs identifiants ;
- leurs relations ;
- leur état actuel.

### Pourquoi est-il indispensable ?

Sans lui, Terraform ne saurait pas :

- quelles ressources existent ;
- lesquelles modifier ;
- lesquelles supprimer.

---

## Remote State

En équipe, le State File ne doit pas rester sur un poste local.

Il est stocké dans un backend distant (ex. S3, Azure Storage, Terraform Cloud).

Avantages :

- tous les développeurs utilisent le même état ;
- évite les versions différentes d'une même infrastructure ;
- verrouille l'état (-state locking-) pour empêcher deux personnes de modifier l'infrastructure simultanément.

> À retenir de ton apprentissage : le Remote State évite que chacun travaille avec une version différente de l'infrastructure et empêche les modifications concurrentes grâce au verrouillage du fichier d'état.

---

## Le cycle Terraform

```
Écrire le code

↓

terraform init

↓

terraform plan

↓

terraform apply

↓

Infrastructure créée

↓

terraform.tfstate mis à jour
```

---

## Les commandes essentielles

### Initialiser un projet

```bash
terraform init
```

Télécharge les Providers et prépare le projet.

---

### Vérifier le code

```bash
terraform validate
```

Détecte les erreurs de syntaxe.

---

### Voir les changements

```bash
terraform plan
```

Compare :

- infrastructure actuelle ;
- infrastructure souhaitée.

Aucune ressource n'est encore modifiée.

> À retenir de ton apprentissage : `terraform plan` permet d'identifier les erreurs ou changements avant d'appliquer la configuration.

---

### Appliquer les changements

```bash
terraform apply
```

Crée, modifie ou supprime les ressources.

---

### Détruire l'infrastructure

```bash
terraform destroy
```

Supprime toutes les ressources gérées.

---

### Voir l'état

```bash
terraform show
```

Affiche l'état actuel enregistré.

---

## Bonnes pratiques

- Toujours exécuter `terraform plan` avant `terraform apply`.
- Stocker le State File à distance pour le travail collaboratif.
- Ne jamais modifier manuellement le fichier `terraform.tfstate`.
- Utiliser des Modules pour éviter la duplication.
- Déclarer les valeurs variables dans des Variables.
- Versionner le code Terraform avec Git.
- Garder une ressource par responsabilité lorsque cela améliore la lisibilité.

---

## Terraform vs Kubernetes

| Terraform                                  | Kubernetes                                       |
| ------------------------------------------ | ------------------------------------------------ |
| Gère l'infrastructure                      | Gère les applications conteneurisées             |
| Crée les VM, réseaux, bases de données     | Déploie les Pods et Services                     |
| Fonctionne avant le déploiement applicatif | Fonctionne après la création de l'infrastructure |
| Provisionnement                            | Orchestration                                    |

Exemple :

```
Terraform
        │
        ▼
Crée :
- VM
- Réseau
- Cluster Kubernetes
        │
        ▼
Kubernetes
        │
        ▼
Déploie :
- Pods
- Deployments
- Services
```

---

## Workflow DevOps complet

```
Code source

↓

Docker
(construction de l'image)

↓

Registry

↓

Terraform
(création de l'infrastructure)

↓

Kubernetes
(déploiement des conteneurs)

↓

Helm
(configuration et gestion des applications)
```

---

## Points clés à retenir

- Terraform est un outil d'Infrastructure as Code (IaC).
- L'infrastructure est décrite dans des fichiers HCL (`.tf`).
- Terraform compare en permanence l'état réel à l'état souhaité.
- `terraform plan` permet de prévisualiser les changements avant leur application.
- Le fichier `terraform.tfstate` est indispensable pour suivre les ressources gérées.
- Le Remote State facilite le travail en équipe et empêche les modifications simultanées.
- Les Providers permettent d'interagir avec différents services cloud et plateformes.
- Les Modules rendent le code réutilisable et plus facile à maintenir.
- Terraform est utilisé pour provisionner l'infrastructure, tandis que Kubernetes et Helm servent à déployer et gérer les applications sur cette infrastructure.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMzc1NjA2NzI5XX0=
-->