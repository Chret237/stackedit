# Ansible — Notes de révision structurées

> **Objectif :** comprendre comment Ansible permet d'automatiser la configuration et l'administration de plusieurs machines de manière déclarative, reproductible et idempotente.

----------

## 1. C'est quoi ?

**Ansible** est un outil d'**automatisation et de gestion de configuration**. Il permet d'installer des logiciels, modifier des configurations, gérer des utilisateurs, déployer des applications ou exécuter des tâches sur plusieurs machines à partir d'une machine de contrôle.

Contrairement à une approche manuelle, on décrit **ce que l'on souhaite obtenir** dans des fichiers YAML appelés **Playbooks**.

Architecture de base :

```text
Control Node
     │
     │ SSH
     ├──────────────► Managed Node 1
     ├──────────────► Managed Node 2
     └──────────────► Managed Node 3
```

-   **Control Node** : machine depuis laquelle Ansible est exécuté.
    
-   **Managed Nodes** : machines administrées.
    
-   **Inventory** : liste des machines à gérer.
    
-   **Playbook** : description des tâches à exécuter.
    
-   **Module** : unité de travail utilisée par Ansible.
    

----------

# 2. Pourquoi utiliser Ansible ?

Administrer manuellement plusieurs serveurs entraîne rapidement :

-   des tâches répétitives ;
    
-   des erreurs humaines ;
    
-   des configurations différentes entre serveurs ;
    
-   une perte de temps ;
    
-   des difficultés à reproduire un environnement.
    

Ansible permet donc de :

-   **automatiser** les tâches ;
    
-   **standardiser** les configurations ;
    
-   **reproduire** facilement un environnement ;
    
-   gérer plusieurs machines simultanément ;
    
-   réduire les erreurs humaines ;
    
-   documenter l'infrastructure sous forme de code.
    

L'idée fondamentale est :

> **Une tâche réalisée une fois manuellement devient une tâche automatisée et reproductible avec Ansible.**

----------

# 3. Comment fonctionne Ansible ?

Le fonctionnement général est :

```text
Inventory
    │
    ▼
Playbook
    │
    ▼
Tasks
    │
    ▼
Modules
    │
    ▼
Managed Nodes
```

Exemple :

```text
"Installer Nginx sur tous les serveurs web"
                  │
                  ▼
             Playbook
                  │
                  ▼
          Module apt/yum
                  │
                  ▼
        Serveurs concernés
```

Ansible se connecte généralement aux machines Linux via **SSH** et exécute les opérations nécessaires.

----------

# 4. Les composants essentiels

## Control Node

C'est la machine sur laquelle Ansible est installé et depuis laquelle les commandes sont lancées.

Elle contient notamment :

-   les Playbooks ;
    
-   l'Inventory ;
    
-   les variables ;
    
-   les rôles.
    

Dans ton environnement d'apprentissage, cela peut correspondre à **Windows + WSL**, tandis qu'un ou plusieurs conteneurs peuvent servir de Managed Nodes.

----------

## Managed Node

Machine administrée par Ansible.

Elle n'a généralement **pas besoin d'installer Ansible**.

Ansible se connecte à celle-ci pour effectuer les tâches demandées.

----------

## Inventory

L'**Inventory** contient les machines que Ansible doit gérer.

Exemple :

```ini
[webservers]
web01
web02

[dbservers]
db01
```

On peut également utiliser des adresses IP :

```ini
[webservers]
192.168.1.10
192.168.1.11
```

L'Inventory permet notamment de **regrouper les machines par rôle**.

----------

# 5. Playbook

Un **Playbook** est un fichier YAML qui décrit les opérations à effectuer.

Exemple :

```yaml
- name: Configure web servers
  hosts: webservers

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

On y retrouve principalement :

-   `hosts` : machines ciblées ;
    
-   `tasks` : tâches à exécuter ;
    
-   `modules` : actions utilisées ;
    
-   `variables` : paramètres configurables.
    

----------

# 6. Tasks

Une **Task** représente une opération précise.

Exemple :

```yaml
- name: Install nginx
  apt:
    name: nginx
    state: present
```

Une tâche doit généralement être :

-   claire ;
    
-   ciblée ;
    
-   idempotente ;
    
-   facilement identifiable grâce à son `name`.
    

----------

# 7. Modules

Les **Modules** sont les unités de travail d'Ansible.

Ils permettent par exemple de :

-   installer un package ;
    
-   créer un utilisateur ;
    
-   gérer un fichier ;
    
-   démarrer un service ;
    
-   créer un répertoire ;
    
-   exécuter certaines commandes.
    

Exemples :

```text
apt
yum
package
copy
template
file
user
service
systemd
command
shell
```

Exemple :

```yaml
- name: Create application directory
  ansible.builtin.file:
    path: /opt/app
    state: directory
```

----------

# 8. Idempotence

C'est l'un des concepts **les plus importants d'Ansible**.

Une opération est **idempotente** lorsqu'elle peut être exécutée plusieurs fois sans provoquer de modifications inutiles.

Exemple :

```yaml
state: present
```

Si Nginx est déjà installé :

```text
1ère exécution → Installation
2ème exécution → Aucun changement
3ème exécution → Aucun changement
```

Ansible cherche donc à maintenir l'état souhaité plutôt qu'à exécuter aveuglément une série de commandes.

> **À retenir :** l'idempotence permet de rendre l'automatisation sûre, prévisible et reproductible.

----------

# 9. Variables

Les variables permettent de rendre les Playbooks réutilisables.

Exemple :

```yaml
vars:
  app_port: 8080
```

Puis :

```yaml
- name: Configure application
  ansible.builtin.template:
    src: app.conf.j2
    dest: /etc/app/app.conf
```

La configuration peut ainsi être adaptée à chaque environnement sans modifier la logique du Playbook.

----------

# 10. Templates

Ansible utilise **Jinja2** pour générer des fichiers de configuration dynamiques.

Exemple :

```jinja2
server_port={{ app_port }}
```

Avec :

```yaml
app_port: 8080
```

Ansible génère :

```text
server_port=8080
```

Cela est particulièrement utile pour gérer plusieurs environnements :

```text
Development
     │
     ├── port: 8080
     │
Production
     │
     └── port: 80
```

----------

# 11. Handlers

Les **Handlers** permettent d'effectuer une action uniquement lorsqu'une tâche provoque une modification.

Exemple :

```yaml
tasks:
  - name: Update nginx configuration
    ansible.builtin.template:
      src: nginx.conf.j2
      dest: /etc/nginx/nginx.conf
    notify: Restart nginx

handlers:
  - name: Restart nginx
    ansible.builtin.service:
      name: nginx
      state: restarted
```

Si la configuration ne change pas :

```text
Pas de changement
       ↓
Pas de restart
```

Cela évite les opérations inutiles.

----------

# 12. Roles

Lorsque les Playbooks deviennent importants, les **Roles** permettent d'organiser le code.

Structure typique :

```text
roles/
└── nginx/
    ├── tasks/
    ├── handlers/
    ├── templates/
    ├── files/
    ├── vars/
    ├── defaults/
    └── meta/
```

Un Role permet de regrouper toute la logique nécessaire à une fonctionnalité ou un composant.

Exemple :

```text
Role nginx
     │
     ├── installation
     ├── configuration
     ├── templates
     └── restart
```

Cela améliore :

-   la réutilisation ;
    
-   la maintenance ;
    
-   l'organisation ;
    
-   la collaboration.
    

----------

# 13. Commandes essentielles

### Vérifier l'inventaire

```bash
ansible-inventory --list
```

### Tester la connexion

```bash
ansible all -m ping
```

### Exécuter une commande sur les machines

```bash
ansible all -m command -a "uptime"
```

### Exécuter un Playbook

```bash
ansible-playbook playbook.yml
```

### Vérifier la syntaxe

```bash
ansible-playbook --syntax-check playbook.yml
```

### Tester un Playbook

```bash
ansible-playbook playbook.yml --check
```

Le mode `--check` permet de simuler les changements sans les appliquer réellement.

----------

# 14. Workflow Ansible

```text
Définir les machines
        │
        ▼
     Inventory
        │
        ▼
Écrire le Playbook
        │
        ▼
Définir les Tasks
        │
        ▼
Utiliser les Modules
        │
        ▼
ansible-playbook
        │
        ▼
Managed Nodes
        │
        ▼
État souhaité atteint
```

----------

# 15. Bonnes pratiques

-   Utiliser des **modules Ansible** plutôt que des commandes shell lorsque cela est possible.
    
-   Écrire des tâches **idempotentes**.
    
-   Organiser les machines avec des groupes dans l'Inventory.
    
-   Utiliser des variables plutôt que de coder les valeurs en dur.
    
-   Utiliser des **Roles** pour les configurations complexes.
    
-   Utiliser `--check` avant d'appliquer des changements importants.
    
-   Ne pas stocker les secrets directement dans les Playbooks.
    
-   Utiliser **Ansible Vault** pour protéger les informations sensibles.
    
-   Tester les Playbooks avant de les utiliser en production.
    

----------

# 16. Ansible dans une architecture DevOps

Ansible complète les outils étudiés précédemment, mais ne remplit pas exactement le même rôle.

```text
Terraform
    │
    │ Provisionne
    ▼
Infrastructure
    │
    ▼
Ansible
    │
    │ Configure
    ▼
Serveurs / Applications
    │
    ▼
Docker / Kubernetes
    │
    │ Exécute
    ▼
Applications
```

### Terraform vs Ansible

Terraform

Ansible

Provisionne l'infrastructure

Configure les machines

Crée des ressources

Configure des ressources existantes

Infrastructure as Code

Configuration Management

Décrit principalement l'état de l'infrastructure

Automatise principalement les opérations de configuration

Ils peuvent donc être **complémentaires**.

----------

# 17. Points clés à retenir

-   **Ansible automatise la configuration et l'administration des machines.**
    
-   Le **Control Node** exécute Ansible ; les **Managed Nodes** sont administrés.
    
-   L'**Inventory** définit les machines ciblées.
    
-   Les **Playbooks** décrivent les opérations à effectuer.
    
-   Les **Tasks** représentent les opérations individuelles.
    
-   Les **Modules** réalisent concrètement ces opérations.
    
-   Les **Variables** rendent les configurations flexibles.
    
-   Les **Templates** permettent de générer des configurations dynamiques.
    
-   Les **Handlers** déclenchent des actions uniquement lorsqu'un changement le nécessite.
    
-   Les **Roles** structurent et réutilisent les configurations complexes.
    
-   L'**idempotence** est essentielle pour obtenir une automatisation fiable.
    
-   **Terraform provisionne**, tandis qu'**Ansible configure**.
    

----------

## Synthèse finale

```text
Terraform
   │
   │ "Je crée l'infrastructure"
   ▼
Infrastructure disponible
   │
   │
Ansible
   │
   │ "Je configure les machines"
   ▼
Environnement configuré
   │
   │
Docker / Kubernetes
   │
   │ "J'exécute les applications"
   ▼
Application opérationnelle
```

**Idée centrale :**

> **Ansible transforme la configuration manuelle des serveurs en une configuration automatisée, déclarative, reproductible et idempotente.**
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTk4OTE2NDI2Ml19
-->