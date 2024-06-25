Pour un déploiement on-premises sur un VPS avec un nom de domaine, vous devrez adapter votre configuration Kubernetes et Flux pour utiliser ce nom de domaine. Voici les étapes détaillées pour déployer MailHog dans un cluster Kubernetes on-premises avec un nom de domaine.

### Prérequis

1. **Cluster Kubernetes sur VPS**: Assurez-vous d'avoir un cluster Kubernetes fonctionnel sur votre VPS.
2. **Nom de Domaine**: Un nom de domaine configuré pour pointer vers votre VPS.
3. **Ingress Controller**: Assurez-vous d'avoir un Ingress Controller installé et configuré dans votre cluster Kubernetes (par exemple, NGINX Ingress Controller).

### Étape 1: Créer le Namespace pour MailHog

Créez un fichier `mailhog-namespace.yaml` pour définir le namespace.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: mailhog
```

### Étape 2: Déployer MailHog

Créez un fichier `mailhog-deployment.yaml` pour définir le déploiement et le service Kubernetes pour MailHog.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mailhog
  namespace: mailhog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mailhog
  template:
    metadata:
      labels:
        app: mailhog
    spec:
      containers:
      - name: mailhog
        image: mailhog/mailhog:latest
        ports:
        - containerPort: 1025
        - containerPort: 8025

---
apiVersion: v1
kind: Service
metadata:
  name: mailhog
  namespace: mailhog
spec:
  selector:
    app: mailhog
  ports:
    - protocol: TCP
      port: 1025
      targetPort: 1025
    - protocol: TCP
      port: 8025
      targetPort: 8025
```

### Étape 3: Configurer l'Ingress

Créez un fichier `mailhog-ingress.yaml` pour configurer l'Ingress afin d'exposer MailHog via votre nom de domaine.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: mailhog-ingress
  namespace: mailhog
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: mailhog.votre-domaine.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: mailhog
            port:
              number: 8025
```

Remplacez `mailhog.votre-domaine.com` par votre nom de domaine réel.

### Étape 4: Ajouter les Manifests au Dépôt Git

Ajoutez les fichiers `mailhog-namespace.yaml`, `mailhog-deployment.yaml`, et `mailhog-ingress.yaml` à votre dépôt Git surveillé par Flux.

```sh
git add mailhog-namespace.yaml mailhog-deployment.yaml mailhog-ingress.yaml
git commit -m "Add MailHog namespace, deployment, and ingress"
git push origin main
```

### Étape 5: Configurer Flux pour Surveiller les Manifests

Assurez-vous que Flux est configuré pour surveiller le dossier ou le dépôt où vous avez ajouté ces fichiers.

### Étape 6: Vérifier le Déploiement

Flux appliquera automatiquement les changements. Vous pouvez vérifier que le namespace MailHog et ses ressources sont correctement déployés en utilisant les commandes suivantes:

```sh
kubectl get namespace
kubectl get pods -n mailhog
kubectl get svc -n mailhog
kubectl get ingress -n mailhog
```

### Accéder à MailHog

Une fois que l'Ingress est configuré et appliqué, vous devriez pouvoir accéder à MailHog via votre navigateur en utilisant l'URL `http://mailhog.votre-domaine.com`.

### Conclusion

En suivant ces étapes, vous aurez déployé MailHog dans votre cluster Kubernetes on-premises avec un nom de domaine, en utilisant Flux CI pour gérer les configurations. Cette configuration permet d'exposer MailHog via un nom de domaine, facilitant l'accès et le test des emails envoyés par vos applications.
