# Résilience en production — Notes de révision structurées

> **Objectif :** comprendre comment concevoir un système capable de **continuer à fonctionner malgré les pannes**, de récupérer rapidement après un incident et de respecter des objectifs de disponibilité et de reprise.

---

## 1. C'est quoi ?

La **résilience** désigne la capacité d'un système à **résister aux défaillances, maintenir son service autant que possible et revenir à un état opérationnel après un incident**.

Un système résilient ne cherche donc pas à empêcher toutes les pannes — ce qui est impossible — mais à **limiter leur impact et accélérer la récupération**.

```text
Panne
  ↓
Détection
  ↓
Tolérance / Mitigation
  ↓
Récupération
  ↓
Retour au fonctionnement normal
```

### Résilience ≠ disponibilité

* **Disponibilité** : le service est-il accessible ?
* **Résilience** : comment le système réagit-il lorsqu'une partie tombe en panne ?

---

# 2. Pourquoi la résilience est importante ?

En production, plusieurs éléments peuvent tomber en panne :

* application ;
* conteneur ;
* serveur ;
* base de données ;
* réseau ;
* zone de disponibilité ;
* dépendance externe ;
* déploiement.

L'objectif est d'éviter qu'une panne locale devienne une **indisponibilité générale**.

Exemple :

```text
Backend 1 ❌
     │
     ▼
Backend 2 ✅ ──► Service toujours disponible
```

La résilience permet donc de **réduire le Single Point of Failure (SPOF)** et de limiter le **blast radius** d'un incident.

---

# 3. Les principaux mécanismes de résilience

## Redondance

Ne pas dépendre d'une seule instance.

```text
Load Balancer
      │
 ┌────┴────┐
 ▼         ▼
App 1     App 2
```

Si App 1 tombe, App 2 peut continuer à servir les utilisateurs.

---

## Health Checks

Le système doit pouvoir déterminer si un service est réellement opérationnel.

Exemple :

```text
Load Balancer
      │
      ├──► App 1 → ❌
      │
      └──► App 2 → ✅
```

L'instance défaillante peut alors être retirée du trafic.

---

## Auto-healing

Lorsqu'un composant tombe, le système peut automatiquement le remplacer.

```text
Container ❌
    ↓
Détection
    ↓
Container supprimé
    ↓
Nouveau container
    ↓
Service restauré
```

C'est particulièrement important dans les environnements conteneurisés.

---

# 4. Tolérance aux pannes

Une architecture résiliente doit être capable de **tolérer certaines défaillances sans arrêter complètement le service**.

Cela peut être obtenu avec :

* plusieurs instances ;
* réplication ;
* load balancing ;
* déploiement multi-AZ ;
* réplication des données ;
* mécanismes de retry ;
* timeouts ;
* circuit breakers ;
* files de messages.

Le principe général :

> **Une panne d'un composant ne doit pas nécessairement entraîner la panne du système entier.**

---

# 5. Backup & Disaster Recovery

La résilience concerne également les données.

Un **backup** permet de restaurer des données après :

* corruption ;
* suppression accidentelle ;
* panne ;
* attaque ;
* erreur humaine.

Mais :

> **Avoir un backup ne signifie pas avoir une stratégie de Disaster Recovery.**

Il faut également savoir :

* où sont les sauvegardes ;
* à quelle fréquence elles sont réalisées ;
* combien de temps elles sont conservées ;
* comment les restaurer ;
* combien de temps prend la restauration ;
* si la restauration fonctionne réellement.

---

# 6. RPO — Recovery Point Objective

Le **RPO** définit la quantité maximale de données que l'on accepte de perdre après un incident.

Exemple :

```text
RPO = 15 min
```

Cela signifie qu'en cas de catastrophe, l'objectif est de ne pas perdre plus d'environ **15 minutes de données**.

Plus le RPO est faible, plus les sauvegardes ou réplications doivent être fréquentes.

```text
RPO élevé
→ plus de données potentiellement perdues

RPO faible
→ moins de données perdues
→ stratégie plus exigeante
```

### Dans ton projet

Tu avais obtenu :

**RPO réel : 10 min**

pour un objectif de :

**RPO : 15 min**

➡️ **Objectif respecté.**

---

# 7. RTO — Recovery Time Objective

Le **RTO** définit le temps maximal acceptable pour restaurer le service après une panne.

Exemple :

```text
RTO = 30 min
```

L'objectif est donc de remettre le service opérationnel dans les **30 minutes**.

### Dans ton projet

Tu avais obtenu :

**RTO réel : 22 min**

pour un objectif de :

**RTO : 30 min**

➡️ **Objectif respecté.**

---

# 8. RPO vs RTO

| Indicateur | Question                                       |
| ---------- | ---------------------------------------------- |
| **RPO**    | Combien de données puis-je perdre ?            |
| **RTO**    | Combien de temps puis-je rester indisponible ? |

Exemple :

```text
Incident
  │
  ├──────────────► RTO
  │               ↓
  │         Service restauré
  │
  └──► RPO
       ↓
 Quantité de données perdue
```

Tes résultats :

| Objectif |  Cible |       Réel | Résultat   |
| -------- | -----: | ---------: | ---------- |
| RPO      | 15 min | **10 min** | ✅ Respecté |
| RTO      | 30 min | **22 min** | ✅ Respecté |

---

# 9. SLA, SLO et SLI

La résilience doit être mesurable.

### SLI — Service Level Indicator

C'est **la mesure réelle** du service.

Exemples :

```text
Disponibilité
Latence
Taux d'erreur
```

### SLO — Service Level Objective

C'est **l'objectif technique** fixé.

Exemple :

```text
Disponibilité ≥ 99,9 %
P95 latency ≤ 100 ms
```

### SLA — Service Level Agreement

C'est l'engagement formel envers les utilisateurs ou clients.

```text
SLI → mesure
SLO → objectif
SLA → engagement
```

---

# 10. Les objectifs de ton environnement

Tu avais notamment défini les objectifs suivants :

```text
Disponibilité : 99,9 %
Latence P95   : 100 ms
RTO           : 30 min
RPO           : 15 min
```

Ces indicateurs permettent de transformer une notion abstraite comme **« notre système est fiable »** en objectifs mesurables.

---

# 11. Tester la résilience

Une stratégie de résilience ne doit pas rester théorique.

Il faut provoquer ou simuler des pannes et mesurer la réaction du système.

Exemples :

```text
Arrêt d'un container
        ↓
Le service reste-t-il disponible ?

Suppression d'une instance
        ↓
Une autre prend-elle le relais ?

Restauration d'un backup
        ↓
Les données sont-elles récupérables ?

Panne du service
        ↓
Combien de temps avant récupération ?
```

On mesure ensuite :

* temps de détection ;
* temps de récupération ;
* données perdues ;
* disponibilité pendant l'incident.

---

# 12. Observabilité + Résilience

L'observabilité et la résilience sont fortement liées.

```text
Observabilité
     │
     ▼
Détecter la panne
     │
     ▼
Résilience
     │
     ▼
Mitiger / récupérer
     │
     ▼
Observabilité
     │
     ▼
Vérifier le retour à la normale
```

Les métriques, logs et alertes permettent donc de **mesurer l'efficacité réelle des mécanismes de résilience**.

---

# 13. Exemple avec une architecture DevOps

```text
                    Utilisateur
                         │
                         ▼
                   Load Balancer
                    /         \
                   ▼           ▼
               App 1         App 2
                  │             │
                  └──────┬──────┘
                         ▼
                     Database
                         │
                         ▼
                      Backup
```

Si `App 1` tombe :

```text
App 1 ❌
   │
   ▼
Health Check
   │
   ▼
Trafic → App 2
```

Si la base de données tombe :

```text
Database ❌
     │
     ▼
Failover / Recovery
     │
     ▼
Database restaurée
```

La résilience consiste donc à prévoir **ce qui se passe lorsqu'un composant échoue avant que l'incident ne survienne**.

---

# 14. Bonnes pratiques

* Éliminer autant que possible les **Single Points of Failure**.
* Répliquer les composants critiques.
* Utiliser des health checks pertinents.
* Prévoir des mécanismes d'auto-healing.
* Mettre en place des backups réguliers.
* Tester réellement les restaurations.
* Définir clairement **RPO et RTO**.
* Mesurer la disponibilité et la latence.
* Combiner résilience et observabilité.
* Tester régulièrement les scénarios de panne.
* Documenter les procédures de récupération.
* Automatiser autant que possible le Disaster Recovery.

---

# 15. Ce que ton apprentissage a permis de comprendre

L'un des points essentiels est qu'une infrastructure **fonctionnelle n'est pas nécessairement une infrastructure résiliente**.

Il faut se poser systématiquement la question :

> **« Que se passe-t-il si ce composant tombe ? »**

Puis construire une réponse :

```text
Composant critique
       ↓
Que se passe-t-il s'il tombe ?
       ↓
Impact ?
       ↓
Mécanisme de mitigation ?
       ↓
Stratégie de récupération ?
       ↓
RPO / RTO respectés ?
       ↓
Test effectué ?
```

C'est cette démarche qui permet de passer d'une infrastructure simplement **opérationnelle** à une infrastructure **préparée aux incidents**.

---

# 16. Points clés à retenir

* **La résilience ne signifie pas empêcher toutes les pannes, mais limiter leur impact et récupérer rapidement.**
* La redondance permet d'éviter certains Single Points of Failure.
* Les health checks permettent de détecter les composants défaillants.
* L'auto-healing permet de restaurer automatiquement certains composants.
* Les backups protègent les données, mais doivent être **testés**.
* **RPO = quantité de données acceptable à perdre.**
* **RTO = temps acceptable pour restaurer le service.**
* **SLI = mesure réelle ; SLO = objectif ; SLA = engagement.**
* L'observabilité permet de mesurer et diagnostiquer les incidents.
* La résilience doit être **testée**, pas simplement déclarée.

---

# Synthèse finale

```text
             RÉSILIENCE
                  │
       ┌──────────┼──────────┐
       ▼          ▼          ▼
   Redondance   Backup    Monitoring
       │          │          │
       ▼          ▼          ▼
 Tolérance    Recovery    Détection
       │          │          │
       └──────────┼──────────┘
                  ▼
             RTO / RPO
                  │
                  ▼
          Tests de résilience
                  │
                  ▼
        Système plus fiable
```

### À retenir en une phrase

> **La résilience consiste à concevoir, mesurer et tester un système capable de continuer à fournir son service malgré les pannes, puis de récupérer dans les limites définies par ses objectifs de disponibilité, RTO et RPO.**

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyMDUyODA3ODNdfQ==
-->