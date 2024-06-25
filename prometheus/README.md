# Port forwarding in Prometheus

Pour transférer les métriques de Prometheus d'un cluster Kubernetes sur un VPS vers votre machine locale en utilisant `kubectl`, vous pouvez configurer le port forwarding. Voici les étapes détaillées pour y parvenir :

### Étape 1: Identifier le Service Prometheus

1. **Lister les services Prometheus**:
    - Utilisez `kubectl` pour lister les services dans le namespace où Prometheus est déployé. Ce namespace est souvent appelé `monitoring` ou `prometheus`.

    ```sh
    kubectl get svc -n <namespace>
    ```

    Remplacez `<namespace>` par le nom réel du namespace.

2. **Identifier le service Prometheus**:
    - Recherchez le service ayant un nom comme `prometheus-server` ou similaire. Prenons comme exemple que le service s'appelle `prometheus-server`.

### Étape 2: Configurer le Port Forwarding

1. **Configurer le port forwarding**:
    - Utilisez la commande `kubectl port-forward` pour transférer le port du service Prometheus de votre cluster Kubernetes vers votre machine locale. 

    ```sh
    kubectl port-forward svc/<service-name> -n <namespace> 9090:9090
    ```

    Par exemple, si le service s'appelle `prometheus-server` et le namespace est `monitoring` :

    ```sh
    kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
    ```

    Cette commande transférera le port 9090 du service Prometheus à votre machine locale sur le port 9090.

### Étape 3: Accéder aux Métriques Prometheus

1. **Accéder via le navigateur**:
    - Ouvrez votre navigateur et accédez à `http://localhost:9090`. Vous devriez voir l'interface web de Prometheus.

2. **Utiliser des outils en ligne de commande**:
    - Vous pouvez utiliser des outils comme `curl` pour interroger l'API de Prometheus directement depuis votre machine locale.

    ```sh
    curl http://localhost:9090/api/v1/query?query=up
    ```

### Étape 4: Configurer Grafana pour Utiliser Prometheus Localement

1. **Ouvrir Grafana**:
    - Ouvrez Grafana dans votre navigateur (par exemple, `http://localhost:3000`).

2. **Ajouter une source de données Prometheus**:
    - Allez dans **Configuration** > **Data Sources**.
    - Cliquez sur **Add data source** et choisissez **Prometheus**.
    - Dans le champ **URL**, entrez `http://localhost:9090`.
    - Cliquez sur **Save & Test** pour vérifier la connexion.

### Résumé des Commandes

Voici un résumé des commandes que vous utiliserez :

1. Identifier le service Prometheus :

    ```sh
    kubectl get svc -n <namespace>
    ```

2. Configurer le port forwarding :

    ```sh
    kubectl port-forward svc/prometheus-server -n monitoring 9090:9090
    ```

3. Accéder à l'interface web de Prometheus :

    - Naviguez vers `http://localhost:9090`.

4. Interroger l'API de Prometheus avec `curl` :

    ```sh
    curl http://localhost:9090/api/v1/query?query=up
    ```

En suivant ces étapes, vous pourrez transférer les métriques de Prometheus de votre cluster Kubernetes sur un VPS vers votre machine locale, et les consommer en utilisant des outils comme Grafana ou directement via l'API Prometheus.
