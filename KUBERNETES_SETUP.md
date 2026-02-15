# Running Bhagavad Gita App with K8s Ollama

## Issue: Rancher Desktop Stops When Using K8s Ollama

### Root Cause
The phi3.5:latest model (2.2GB) requires significant memory. When accessed via port-forward, Rancher Desktop hits resource limits and crashes.

### Solution 1: Increase Rancher Desktop Resources (Recommended)

**Increase memory allocation:**

1. Open Rancher Desktop settings
2. Go to **Preferences** → **Virtual Machine** → **Hardware**
3. Increase **Memory** to at least **8GB** (currently might be 4GB)
4. Increase **CPUs** to at least **4 cores**
5. Click **Apply & Restart**

### Solution 2: Deploy Jupyter Notebook Inside K8s

Run your notebook **inside the cluster** to avoid port-forwarding entirely:

**Quick deployment:**
```bash
# Create a Jupyter pod
kubectl run jupyter -n ollama \
  --image=jupyter/scipy-notebook:latest \
  --port=8888 \
  -- start-notebook.sh --NotebookApp.token=''

# Port-forward Jupyter (lighter than Ollama)
kubectl port-forward -n ollama pod/jupyter 8888:8888

# Access at http://localhost:8888
```

**Update notebook config:**
```python
# No port-forward needed - direct K8s service access
OLLAMA_BASE_URL = "http://ollama.ollama.svc.cluster.local:11434"
OLLAMA_MODEL = "phi3.5:latest"
```

### Solution 3: Use Lighter Model

If resource constraints persist, use `deepseek-r1:1.5b` (1.1GB instead of 2.2GB):

**Pull to K8s Ollama:**
```bash
kubectl exec -n ollama $(kubectl get pods -n ollama -o name) -- ollama pull deepseek-r1:1.5b
```

**Update notebook:**
```python
OLLAMA_MODEL = "deepseek-r1:1.5b"
```

### Recommended Approach

**For your setup, I recommend:**
1. Increase Rancher Desktop memory to 8GB
2. Restart Rancher Desktop
3. Use port-forward with the increased resources
4. Your notebook will work with phi3.5:latest

**Quick check current resources:**
```bash
# Check Rancher Desktop VM stats
docker stats --no-stream
```

Would you like me to help deploy Jupyter inside K8s, or would you prefer to increase Rancher Desktop resources?
