> [!Important] 
> This is a first version of the chart, which will deploy a basic privacyIDEA stack. 
> Do not use for production!

---

### **Prerequisites**
1. **Install Minikube**: Follow the [official Minikube installation guide](https://minikube.sigs.k8s.io/docs/start/).
2. **Install Helm**: Follow the [official Helm installation guide](https://helm.sh/docs/intro/install/).
3. **Start Minikube**: Start a Minikube cluster.
   ```bash
   minikube start
   ```
4. **Enable Ingress (Optional)**: If you want to expose PrivacyIDEA using an Ingress, enable the Ingress addon in Minikube.
   ```bash
   minikube addons enable ingress
   minikube addons enable ingress-dns
   ```

---

### **Steps to Deploy the Helm Chart**

#### **1. Clone or Create the Helm Chart**
If you haven't already created the Helm chart, follow the steps in the previous responses to create the `privacyidea-chart` directory with the necessary files.

#### **2. Install the Helm Chart**
Navigate to the directory containing the Helm chart and install it:
```bash
helm install privacyidea ./privacyidea-chart
```

This will deploy the following components:
- PrivacyIDEA application
- MariaDB database
- Redis cache

#### **3. Verify the Deployment**
Check if the pods are running:
```bash
kubectl get pods
```
You should see pods for PrivacyIDEA, MariaDB, and Redis.

#### **4. Access PrivacyIDEA**
By default, the PrivacyIDEA service is exposed as a `ClusterIP`. To access it, you can use one of the following methods:

##### **Method 1: Use `kubectl port-forward`**
Forward a local port to the PrivacyIDEA service:
```bash
kubectl port-forward svc/privacyidea-privacyidea 8080:80
```
Now, you can access PrivacyIDEA in your browser at:
```
http://localhost:8080
```

##### **Method 2: Use Minikube Service**
Expose the service using Minikube:
```bash
minikube service privacyidea-privacyidea
```
This will open the PrivacyIDEA application in your default browser.

##### **Method 3: Use Ingress (Optional)**
If you enabled the Ingress addon, you can create an Ingress resource to expose PrivacyIDEA. Enable ingress in the `values.yaml` file:

```yaml
...
# Enable or disable ingress
ingress:
  enabled: true
```

Configure ingress in `templates/ingress.yamnl`


Add the following line to your `/etc/hosts` file to map `privacyidea.local` to the Minikube IP:
```bash
echo "$(minikube ip) privacyidea.local" | sudo tee -a /etc/hosts
```

Now, you can access PrivacyIDEA at:
```
http://privacyidea.local
```

---

### **5. Clean Up**
When you're done testing, you can delete the Helm release:
```bash
helm uninstall privacyidea
```

To stop Minikube:
```bash
minikube stop
```

To delete the Minikube cluster:
```bash
minikube delete
```

---

### **Troubleshooting**
1. **Pods not starting**: Check the logs for the failing pod:
   ```bash
   kubectl logs <pod-name>
   ```
2. **Database connection issues**: Ensure that the MariaDB pod is running and that the connection string in the `privacyidea.cfg` file is correct.
3. **Persistent Volume issues**: If you encounter issues with persistent volumes, ensure that Minikube's storage class is configured correctly. You can check the persistent volumes and claims:
   ```bash
   kubectl get pv
   kubectl get pvc
   ```

---

By following these steps, you can successfully deploy the PrivacyIDEA stack with MariaDB and Redis on Minikube using the Helm chart. This setup is ideal for local development and testing.
