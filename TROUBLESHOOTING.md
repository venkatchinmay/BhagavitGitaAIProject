# Troubleshooting Guide

## Issue: Model "phi3.5:latest" not found

### Root Cause
The **port-forward to Ollama has stopped running**. Even though the service exists in Kubernetes, your local notebook can't reach it without an active port-forward.

### Solution: Restart Port-Forward

Open a **new terminal** and run:

```bash
kubectl port-forward -n ollama svc/ollama 11434:11434
```

Keep this terminal **running** while you use the notebook. You should see:
```
Forwarding from 127.0.0.1:11434 -> 11434
Forwarding from [::1]:11434 -> 11434
```

### Verify Connection

After starting port-forward, test the connection:

```bash
# Check if Ollama is accessible
curl -s http://localhost:11434/api/version

# Should return: {"version":"0.5.7"} or similar
```

```bash
# List available models
curl -s http://localhost:11434/api/tags | python3 -m json.tool

# Should show phi3.5:latest in the models list
```

### Then Re-run Your Notebook Cell

Once port-forward is active, re-run the cell in your notebook that calls `solve_problem_with_gita()`.

---

## Common Issues

### Port-forward Stopped
**Symptom**: `model not found` or connection refused errors

**Solution**: The port-forward process may have been killed. Restart it as shown above.

### Wrong Model Name
**Issue**: Model name must include the tag

**Fix**: Use `phi3.5:latest` not just `phi3.5`

### Port Already in Use
**Symptom**: `bind: address already in use`

**Solution**: 
```bash
# Find and kill the process using port 11434
lsof -ti:11434 | xargs kill -9

# Then restart port-forward
kubectl port-forward -n ollama svc/ollama 11434:11434
```

### Running from Different Terminal
If you're running Jupyter from a different terminal than where port-forward is active, make sure both are on the same machine and can access localhost:11434.

---

## Alternative: Run Jupyter Inside Cluster

If you deploy your Jupyter notebook inside the Kubernetes cluster, you can skip port-forwarding entirely:

```python
# In notebook configuration cell, use:
OLLAMA_BASE_URL = "http://ollama.ollama.svc.cluster.local:11434"
```

This connects directly to the Kubernetes service without port-forwarding.
