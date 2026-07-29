---


---

<h1 id="kubernetes-k8s-—-notes-de-révision-structurées">Kubernetes (K8s) — Notes de révision structurées</h1>
<blockquote>
<p>Objectif : Comprendre les concepts fondamentaux de Kubernetes et savoir déployer une application conteneurisée de manière scalable et résiliente.</p>
</blockquote>
<hr>
<h2 id="résumé-structuré">Résumé structuré</h2>
<h3 id="cest-quoi-">C’est quoi ?</h3>
<p>Kubernetes (K8s) est un orchestrateur de conteneurs open source permettant d’automatiser le déploiement, la gestion, la mise à l’échelle et la haute disponibilité des applications conteneurisées.</p>
<p>Il ne remplace pas Docker : Docker crée et exécute les conteneurs, tandis que Kubernetes les orchestre à grande échelle.</p>
<hr>
<h3 id="pourquoi-">Pourquoi ?</h3>
<p>Lorsque le nombre de conteneurs augmente, leur gestion manuelle devient difficile :</p>
<ul>
<li>redémarrer les conteneurs en panne ;</li>
<li>répartir la charge entre plusieurs instances ;</li>
<li>mettre à jour une application sans interruption ;</li>
<li>augmenter ou réduire automatiquement le nombre d’instances ;</li>
<li>connecter les conteneurs entre eux.</li>
</ul>
<p>Kubernetes automatise toutes ces tâches.</p>
<hr>
<h3 id="comment-">Comment ?</h3>
<p>Kubernetes fonctionne grâce à plusieurs objets qui collaborent :</p>
<p>Application → Container → Pod → ReplicaSet → Deployment → Service</p>
<p>Le Deployment décrit l’état souhaité, le ReplicaSet maintient le nombre de Pods demandé et le Service expose ces Pods afin qu’ils soient accessibles.</p>
<hr>
<h2 id="résumé-narratif">Résumé narratif</h2>
<p>Kubernetes est conçu pour gérer des applications composées de conteneurs. Contrairement à Docker qui exécute simplement un conteneur, Kubernetes supervise en permanence l’état de l’application afin qu’elle corresponde toujours à l’état désiré. Si un Pod tombe en panne, Kubernetes en crée automatiquement un nouveau. Si davantage de capacité est nécessaire, il crée des Pods supplémentaires. À l’inverse, si la charge diminue, il peut réduire le nombre de Pods.</p>
<p>Le déploiement d’une application suit généralement le même cycle : créer une image Docker, définir un Deployment pour préciser l’image et le nombre de réplicas, puis créer un Service qui rend l’application accessible. Les Services utilisent des labels et des selectors pour découvrir automatiquement les Pods à exposer, ce qui permet aux Pods d’être remplacés sans impacter les utilisateurs.</p>
<p>Cette architecture rend les applications plus robustes, plus facilement scalables et plus simples à maintenir.</p>
<hr>
<h2 id="les-composants-essentiels">Les composants essentiels</h2>
<h3 id="cluster">Cluster</h3>
<p>Ensemble de machines exécutant Kubernetes.</p>
<p>Un cluster est composé de :</p>
<ul>
<li>Control Plane (Master)</li>
<li>Worker Nodes</li>
</ul>
<hr>
<h3 id="control-plane">Control Plane</h3>
<p>C’est le cerveau du cluster.</p>
<p>Il :</p>
<ul>
<li>reçoit les requêtes (<code>kubectl</code>)</li>
<li>décide où lancer les Pods</li>
<li>surveille l’état du cluster</li>
<li>maintient l’état désiré</li>
</ul>
<hr>
<h3 id="worker-node">Worker Node</h3>
<p>Machine qui exécute réellement les applications.</p>
<p>Chaque Worker contient :</p>
<ul>
<li>kubelet</li>
<li>kube-proxy</li>
<li>runtime de conteneurs (containerd, Docker…)</li>
</ul>
<hr>
<h3 id="pod">Pod</h3>
<p>Plus petite unité déployable dans Kubernetes.</p>
<p>Un Pod contient :</p>
<ul>
<li>un ou plusieurs conteneurs</li>
<li>une adresse IP</li>
<li>un stockage temporaire</li>
</ul>
<p>À retenir :</p>
<ul>
<li>un Pod est éphémère</li>
<li>on ne travaille presque jamais directement avec un Pod</li>
<li>Kubernetes peut le recréer à tout moment</li>
</ul>
<hr>
<h3 id="replicaset">ReplicaSet</h3>
<p>Garantit qu’il existe toujours le nombre souhaité de Pods.</p>
<p>Exemple :</p>
<pre><code>Replicas = 3
</code></pre>
<p>Si un Pod est supprimé :</p>
<pre><code>3 Pods
↓

1 Pod supprimé

↓

ReplicaSet crée immédiatement un nouveau Pod
</code></pre>
<hr>
<h3 id="deployment">Deployment</h3>
<p>Objet utilisé quotidiennement.</p>
<p>Il permet de :</p>
<ul>
<li>créer des Pods</li>
<li>gérer les ReplicaSets</li>
<li>mettre à jour les applications</li>
<li>effectuer des Rolling Updates</li>
<li>revenir à une ancienne version (Rollback)</li>
</ul>
<p>C’est l’objet recommandé pour déployer une application.</p>
<hr>
<h3 id="service">Service</h3>
<p>Les Pods changent constamment.</p>
<p>Leur IP n’est donc pas fiable.</p>
<p>Le Service fournit :</p>
<ul>
<li>une IP stable</li>
<li>un nom DNS</li>
<li>un point d’accès permanent</li>
</ul>
<p>Le Service sélectionne les Pods grâce aux :</p>
<ul>
<li>Labels</li>
<li>Selectors</li>
</ul>
<p>Exemple :</p>
<pre><code>label:
app: nginx
</code></pre>
<p>Le Service recherche :</p>
<pre><code>selector:
app: nginx
</code></pre>
<p>Tous les Pods possédant ce label sont automatiquement connectés au Service.</p>
<hr>
<h2 id="les-différents-types-de-service">Les différents types de Service</h2>
<h3 id="clusterip">ClusterIP</h3>
<ul>
<li>Type par défaut</li>
<li>Accessible uniquement depuis le cluster</li>
</ul>
<hr>
<h3 id="nodeport">NodePort</h3>
<p>Expose l’application via :</p>
<pre><code>NodeIP:Port
</code></pre>
<p>Utilisé principalement pour les tests.</p>
<hr>
<h3 id="loadbalancer">LoadBalancer</h3>
<p>Utilisé dans le Cloud.</p>
<p>Crée automatiquement un Load Balancer externe.</p>
<hr>
<h2 id="cycle-de-vie-dun-déploiement">Cycle de vie d’un déploiement</h2>
<pre><code>Docker Image

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
</code></pre>
<hr>
<h2 id="scale">Scale</h2>
<p>Le Scale consiste à modifier le nombre de Pods.</p>
<p>Exemple :</p>
<p>Avant</p>
<pre><code>3 Pods
</code></pre>
<p>Après</p>
<pre><code>10 Pods
</code></pre>
<p>Kubernetes crée automatiquement les nouveaux Pods.</p>
<hr>
<h2 id="rolling-update">Rolling Update</h2>
<p>Permet de mettre à jour une application sans interruption.</p>
<p>Exemple :</p>
<pre><code>Version 1

↓

Création d'un Pod Version 2

↓

Suppression d'un Pod Version 1

↓

Répéter jusqu'à migration complète
</code></pre>
<p>Les utilisateurs continuent d’accéder au service pendant toute la mise à jour.</p>
<hr>
<h2 id="rollback">Rollback</h2>
<p>Si une mise à jour échoue :</p>
<pre><code>Version 2

↓

Erreur

↓

Rollback

↓

Retour Version 1
</code></pre>
<hr>
<h2 id="les-labels">Les Labels</h2>
<p>Les Labels sont des paires clé/valeur.</p>
<p>Exemple :</p>
<pre><code>app=frontend

env=production

version=v2
</code></pre>
<p>Ils servent à :</p>
<ul>
<li>organiser les ressources</li>
<li>filtrer les Pods</li>
<li>connecter les Services aux Pods</li>
</ul>
<hr>
<h2 id="les-selectors">Les Selectors</h2>
<p>Les Selectors recherchent les Pods possédant certains Labels.</p>
<p>Exemple :</p>
<pre><code>Selector

app=frontend
</code></pre>
<p>Tous les Pods ayant :</p>
<pre><code>app=frontend
</code></pre>
<p>seront sélectionnés.</p>
<hr>
<h2 id="workflow-complet">Workflow complet</h2>
<pre><code>Créer une image Docker

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
</code></pre>
<hr>
<h2 id="bonnes-pratiques">Bonnes pratiques</h2>
<ul>
<li>Ne jamais créer directement des Pods en production.</li>
<li>Utiliser des Deployments pour gérer les applications.</li>
<li>Exposer les Pods via un Service plutôt que par leur adresse IP.</li>
<li>Utiliser des Labels cohérents et explicites.</li>
<li>Garder les Pods stateless lorsque c’est possible.</li>
<li>Effectuer les mises à jour avec des Rolling Updates.</li>
<li>Vérifier régulièrement l’état du cluster et des Pods avec <code>kubectl</code>.</li>
</ul>
<hr>
<h2 id="commandes-essentielles">Commandes essentielles</h2>
<pre class=" language-bash"><code class="prism  language-bash">kubectl get pods
</code></pre>
<p>Lister les Pods.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl get deployments
</code></pre>
<p>Lister les Deployments.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl get services
</code></pre>
<p>Lister les Services.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl describe pod <span class="token operator">&lt;</span>nom<span class="token operator">&gt;</span>
</code></pre>
<p>Afficher les détails d’un Pod.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl logs <span class="token operator">&lt;</span>pod<span class="token operator">&gt;</span>
</code></pre>
<p>Consulter les journaux d’un Pod.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl apply -f deployment.yaml
</code></pre>
<p>Créer ou mettre à jour une ressource.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl delete -f deployment.yaml
</code></pre>
<p>Supprimer une ressource.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl scale deployment nginx --replicas<span class="token operator">=</span>5
</code></pre>
<p>Modifier le nombre de Pods.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl rollout status deployment/nginx
</code></pre>
<p>Suivre une mise à jour.</p>
<pre class=" language-bash"><code class="prism  language-bash">kubectl rollout undo deployment/nginx
</code></pre>
<p>Revenir à la version précédente.</p>
<hr>
<h2 id="points-clés-à-retenir">Points clés à retenir</h2>
<ul>
<li>Kubernetes orchestre des conteneurs, tandis que Docker les exécute.</li>
<li>Le Pod est la plus petite unité déployable, mais il est éphémère.</li>
<li>Le ReplicaSet garantit le nombre de Pods demandé.</li>
<li>Le Deployment est l’objet principal pour déployer et mettre à jour des applications.</li>
<li>Le Service fournit un point d’accès stable en utilisant des Labels et des Selectors.</li>
<li>Le Scaling, les Rolling Updates et les Rollbacks assurent disponibilité et évolution sans interruption.</li>
<li>Kubernetes repose sur le principe de l’état désiré : il compare en permanence l’état réel à l’état attendu et agit automatiquement pour les faire correspondre.</li>
</ul>

