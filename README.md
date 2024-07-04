apiVersion: apps/v1
kind: Deployment
metadata:
  name: vertexai-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vertexai
  template:
    metadata:
      labels:
        app: vertexai
    spec:
      serviceAccountName: gke-sa      
      containers:
      - name: vertexai
        image: gcr.io/disearch-vertexai/vertexai:latest
        ports:
        - containerPort: 5000
        envFrom:
        - configMapRef:
            name: disearch-vertexai-configmap

---

apiVersion: v1
kind: Service
metadata:
  name: vertexai-deployment-service
  annotations:
        networking.gke.io/load-balancer-type: "Internal"
spec:
  selector:
    app: vertexai
  ports:
    - protocol: TCP
      port: 80
      targetPort: 5000
  type: LoadBalancer

---

apiVersion: v1
kind: ConfigMap
metadata:
  name: disearch-vertexai-configmap
  namespace: default
data:
  OPENAI_API_KEY: ""
  status_cloud_fn: "https://us-central1-disearch-vertexai.cloudfunctions.net/document-status"
  image_summary_cloud_fn: "https://us-central1-disearch-vertexai.cloudfunctions.net/image-processing"
  update_metadata_fn: "https://us-central1-disearch-vertexai.cloudfunctions.net/update_metadata_ingested_document"
  schema: "disearch_search"


Apply: 

  kubectl apply -f <filename>

Command to verify env that has been assigned to deployment:

  kubectl exec -it vertexai-deployment-7f6db69598-d7j75 -- env


