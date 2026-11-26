# 1. Crear el clúster
kind create cluster --name teemii-lab

# 2. Cargar imágenes locales SOLO si las construiste tú
# kind load docker-image teemii-frontend:latest --name teemii-lab
# kind load docker-image teemii-backend:latest --name teemii-lab
# Si usas las de Docker Hub (dokkaner/*), Kind las descarga solo.

# 3. Desplegar el chart
helm install mi-teemii ./teemii-chart

# 4. Verificar
kubectl get pods -w
kubectl get pvc

# 5. Opción A: port-forward rápido
kubectl port-forward svc/mi-teemii-frontend 8080:80

# 5. Opción B: Ingress (teemii.local)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/kind/deploy.yaml
kubectl wait --namespace ingress-nginx --for=condition=ready pod --selector=app.kubernetes.io/component=controller --timeout=180s
echo "127.0.0.1 teemii.local" | sudo tee -a /etc/hosts
# abre http://teemii.local en tu navegador

# 6. Limpiar (opcional)
helm uninstall mi-teemii && kind delete cluster --name teemii-lab
