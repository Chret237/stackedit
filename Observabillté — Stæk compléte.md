# Observabilité — Notes de révision structurées

> **Objectif :** comprendre comment construire une stack complète d'observabilité permettant de **mesurer, visualiser, diagnostiquer et surveiller** le comportement d'une application et de son infrastructure.

---

# 1. C'est quoi ?

L'**observabilité** est la capacité à comprendre ce qui se passe à l'intérieur d'un système à partir des informations qu'il expose.

Elle repose principalement sur trois types de données :

* **Metrics** → mesurer
* **Logs** → expliquer
* **Traces** → suivre

On peut les résumer ainsi :

```text
Metrics → "Quel est le problème ?"
Logs    → "Que s'est-il passé ?"
Traces  → "Où et pourquoi cela s'est-il produit ?"
```

Une stack d'observabilité complète ajoute généralement :

```text
Application / Infrastructure
          │
     ┌────┼────┐
     ▼    ▼    ▼
 Metrics Logs Traces
     │    │    │
     └────┼────┘
          ▼
   Collecte / Stockage
          │
          ▼
       Grafana
          │
          ▼
     Dashboards
     + Alerting
```

---

# 2. Pourquoi ?

Une application peut fonctionner sans erreur apparente tout en ayant des problèmes :

* requêtes trop lentes ;
* consommation excessive de CPU ou mémoire ;
* erreurs HTTP ;
* augmentation du nombre de requêtes ;
* indisponibilité d'un service ;
* dépendance externe défaillante.

Sans observabilité, on découvre souvent le problème **par l'utilisateur**.

Avec une bonne stack :

```text
Problème
   ↓
Détection
   ↓
Investigation
   ↓
Identification de la cause
   ↓
Correction
   ↓
Vérification
```

L'objectif n'est donc pas seulement de collecter des données, mais de pouvoir **prendre une décision rapidement à partir de ces données**.

---

# 3. Comment fonctionne une stack d'observabilité ?

Le principe général est :

```text
Sources
  │
  ├── Application
  ├── Containers
  ├── Serveurs
  └── Services
       │
       ▼
   Collecteurs / Exporters
       │
       ▼
     Storage
       │
       ▼
    Grafana
       │
       ├── Dashboards
       └── Alerts
```

Chaque composant possède donc un rôle précis.

---

# 4. Les trois piliers

## Metrics

Les **métriques** sont des valeurs numériques mesurées dans le temps.

Exemples :

```text
CPU usage
Memory usage
Request count
Request duration
HTTP error rate
Number of active users
```

Elles permettent notamment de détecter les tendances et les anomalies.

Exemple :

```text
HTTP 5xx
   │
   │       ╭───╮
   │   ╭───╯   ╰──
   └──────────────────► Temps
```

Une augmentation soudaine du taux d'erreur peut déclencher une alerte.

---

## Logs

Les **logs** enregistrent des événements produits par l'application ou l'infrastructure.

Exemple :

```text
2026-09-01 10:20:32
ERROR
Database connection failed
```

Ils apportent généralement davantage de contexte qu'une simple métrique.

---

## Traces

Les **traces distribuées** permettent de suivre une requête à travers plusieurs services.

Exemple :

```text
Client
  │
  ▼
Frontend
  │
  ▼
API
  │
  ▼
Backend
  │
  ▼
Database
```

Une trace permet de déterminer où le temps est passé et quel service est responsable d'une latence importante.

---

# 5. Prometheus

**Prometheus** est principalement utilisé pour la **collecte et le stockage des métriques**.

Son fonctionnement repose notamment sur le modèle **Pull** :

```text
Application
    │
    │ /metrics
    ▼
Prometheus
    │
    ▼
Time Series Database
```

Prometheus interroge régulièrement les endpoints exposant les métriques.

---

# 6. Exporters

Toutes les applications ou infrastructures n'exposent pas directement les métriques au format Prometheus.

Les **Exporters** servent alors d'intermédiaires.

Exemple :

```text
Linux Server
     │
     ▼
Node Exporter
     │
     ▼
Prometheus
```

Un exporter transforme les informations d'une source en métriques exploitables par Prometheus.

### Point important de ton apprentissage

Tu avais notamment identifié que **Node Exporter mesure la machine sur laquelle il est installé**.

Donc si l'objectif est de mesurer le serveur qui exécute réellement le backend, l'exporter doit être positionné sur cette infrastructure — et non simplement sur une machine de supervision différente.

---

# 7. Métriques applicatives

Les métriques d'infrastructure ne suffisent pas.

Une application doit également exposer ses propres métriques.

Pour ton backend Django, les métriques observées pouvaient notamment concerner :

* nombre de requêtes HTTP ;
* latence ;
* erreurs ;
* méthodes HTTP ;
* statut HTTP ;
* métriques Python ;
* garbage collection.

Exemple conceptuel :

```text
Application Django
       │
       ▼
 /metrics
       │
       ▼
 Prometheus
       │
       ▼
   Grafana
```

Cela permet de surveiller **le comportement réel du service**, et pas uniquement celui de la machine qui l'héberge.

---

# 8. PromQL

**PromQL** est le langage utilisé pour interroger les données de Prometheus.

Exemple :

```promql
rate(http_requests_total[5m])
```

Cette requête permet d'observer le taux de requêtes sur une fenêtre de 5 minutes.

Pour une application, on cherche notamment à construire des indicateurs tels que :

```text
Request Rate
Error Rate
Latency
Availability
```

Ces indicateurs sont particulièrement importants pour mesurer la santé d'un service.

---

# 9. Grafana

**Grafana** est la couche de **visualisation**.

Il peut récupérer les données de Prometheus et les présenter sous forme de :

* graphiques ;
* tableaux ;
* jauges ;
* statistiques ;
* dashboards.

Architecture :

```text
Prometheus
     │
     │ Query
     ▼
  Grafana
     │
     ▼
Dashboard
```

Grafana ne collecte donc pas nécessairement les métriques lui-même.

> **Prometheus collecte et stocke ; Grafana visualise et aide à interpréter.**

---

# 10. Dashboards

Un bon dashboard ne doit pas simplement afficher beaucoup de graphiques.

Il doit permettre de répondre rapidement à des questions opérationnelles :

### Le service est-il disponible ?

```text
Availability
```

### Le trafic augmente-t-il ?

```text
Request Rate
```

### Les utilisateurs rencontrent-ils des erreurs ?

```text
Error Rate
```

### L'application devient-elle lente ?

```text
Latency
```

### L'infrastructure est-elle sous pression ?

```text
CPU / Memory / Disk
```

---

# 11. Alerting

L'observabilité devient réellement opérationnelle lorsqu'elle permet de **détecter automatiquement les problèmes**.

Exemple :

```text
Error Rate > seuil
       │
       ▼
    Alert
       │
       ▼
Notification
       │
       ▼
Intervention
```

Une alerte doit idéalement être :

* pertinente ;
* suffisamment précise ;
* actionnable ;
* basée sur un véritable problème utilisateur ou système.

Éviter les alertes qui déclenchent constamment sans nécessiter d'action : ce phénomène est appelé **alert fatigue**.

---

# 12. Corrélation des données

La vraie puissance d'une stack complète vient de la combinaison des données.

Exemple :

```text
Metric
"Latency increased"
       │
       ▼
Trace
"Backend is slow"
       │
       ▼
Log
"Database connection timeout"
       │
       ▼
Cause probable identifiée
```

On passe donc de :

> **« Le système est lent. »**

à :

> **« Les requêtes du backend sont lentes parce que les connexions à la base de données expirent. »**

C'est l'un des objectifs majeurs de l'observabilité.

---

# 13. Observabilité vs Monitoring

Ces deux notions sont proches mais différentes.

### Monitoring

Répond principalement :

> **« Le système fonctionne-t-il correctement ? »**

Exemple :

```text
CPU > 90%
HTTP 500 > seuil
Service DOWN
```

### Observabilité

Cherche davantage à répondre :

> **« Pourquoi le système ne fonctionne-t-il pas correctement ? »**

Elle combine :

```text
Metrics
+
Logs
+
Traces
+
Contexte
```

Le monitoring détecte le problème ; l'observabilité facilite son **investigation et sa compréhension**.

---

# 14. Architecture de la stack complète

```text
                     APPLICATION
                          │
              ┌───────────┼───────────┐
              │           │           │
              ▼           ▼           ▼
           Metrics       Logs       Traces
              │           │           │
              ▼           ▼           ▼
         Prometheus    Log system   Trace system
              │
              └───────────┬───────────┘
                          │
                          ▼
                       Grafana
                          │
               ┌──────────┴──────────┐
               ▼                     ▼
          Dashboards              Alerts
```

L'architecture exacte peut varier, mais le principe reste le même :

**Collecter → Stocker → Corréler → Visualiser → Alerter → Agir**

---

# 15. Application à Formuloo Compta

L'objectif du mini-projet d'observabilité autour de **Formuloo Compta** était d'aller plus loin que le simple déploiement de l'application.

Architecture envisagée :

```text
                 Formuloo Compta
                       │
              ┌────────┴────────┐
              ▼                 ▼
        Django Backend      Infrastructure
              │                 │
              ▼                 ▼
          /metrics         Node Exporter
              │                 │
              └────────┬────────┘
                       ▼
                   Prometheus
                       │
                       ▼
                    Grafana
                       │
              ┌────────┴────────┐
              ▼                 ▼
          Dashboard          Alerting
```

L'idée était donc de surveiller **à la fois le service et l'environnement qui l'exécute**.

---

# 16. Difficulté importante rencontrée

Une difficulté rencontrée concernait les premières requêtes PromQL.

Les métriques disponibles étaient nombreuses, notamment :

```text
Python GC
Django HTTP
Request latency
Request count
Python runtime
```

Mais toutes les métriques ne sont pas automatiquement utiles pour répondre aux questions opérationnelles.

Cela conduit à une notion importante :

> **Collecter beaucoup de métriques ne signifie pas être observable.**

Il faut sélectionner les métriques pertinentes et les transformer en **indicateurs exploitables**.

---

# 17. Les quatre signaux à privilégier

Pour une application web, une approche particulièrement utile consiste à suivre :

### 1. Traffic

Combien de requêtes arrivent ?

```text
Requests / second
```

### 2. Errors

Combien échouent ?

```text
HTTP 4xx / 5xx
```

### 3. Latency

Combien de temps prennent les requêtes ?

```text
p50
p95
p99
```

### 4. Saturation

Les ressources sont-elles proches de leur limite ?

```text
CPU
Memory
Disk
Connections
```

Ces quatre dimensions permettent déjà d'obtenir une vision opérationnelle solide.

---

# 18. Workflow d'investigation

Lorsqu'une alerte apparaît :

```text
ALERTE
  │
  ▼
Dashboard
  │
  ▼
Identifier le symptôme
  │
  ├── Traffic ?
  ├── Errors ?
  ├── Latency ?
  └── Saturation ?
  │
  ▼
Analyser les logs
  │
  ▼
Examiner la trace
  │
  ▼
Identifier la cause
  │
  ▼
Corriger
  │
  ▼
Vérifier les métriques
```

C'est cette boucle qui transforme l'observabilité en **outil de fiabilité**.

---

# 19. Bonnes pratiques

* Instrumenter **l'application**, pas uniquement l'infrastructure.
* Choisir des métriques répondant à de vraies questions opérationnelles.
* Éviter une explosion du nombre de labels (**high cardinality**).
* Construire des dashboards orientés utilisateurs et services.
* Définir des alertes **actionnables**.
* Conserver suffisamment de contexte pour investiguer les incidents.
* Corréler métriques, logs et traces lorsque cela est possible.
* Surveiller les ressources mais aussi les **performances applicatives**.
* Ne pas confondre quantité de données collectées et observabilité.

---

# 20. Points clés à retenir

* **Observabilité = comprendre l'état interne d'un système grâce à ses données.**
* Les trois piliers sont **Metrics, Logs et Traces**.
* **Prometheus** collecte et stocke principalement les métriques.
* Les **Exporters** exposent des métriques provenant de systèmes qui ne les fournissent pas directement.
* **Grafana** permet de visualiser et exploiter les données.
* Les **Dashboards** doivent répondre à des questions opérationnelles.
* L'**Alerting** permet de détecter automatiquement les problèmes nécessitant une intervention.
* Les métriques applicatives sont aussi importantes que les métriques d'infrastructure.
* `Traffic + Errors + Latency + Saturation` constituent une excellente base pour surveiller un service.
* L'observabilité prend toute sa valeur lorsqu'on peut passer de **« quelque chose ne va pas »** à **« voici pourquoi »**.

---

# Synthèse finale

```text
                  OBSERVABILITÉ
                       │
          ┌────────────┼────────────┐
          ▼            ▼            ▼
       Metrics        Logs        Traces
          │            │            │
          └────────────┼────────────┘
                       ▼
                 Collecte/Stockage
                       │
                       ▼
                    Grafana
                       │
              ┌────────┴────────┐
              ▼                 ▼
          Dashboard          Alerting
              │
              ▼
        Investigation
              │
              ▼
        Cause identifiée
              │
              ▼
           Action
```

### À retenir en une phrase

> **Le monitoring indique qu'un problème existe ; l'observabilité fournit les données nécessaires pour comprendre ce qui se passe, identifier pourquoi et agir rapidement.**

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTYxNDY3NzkwNV19
-->