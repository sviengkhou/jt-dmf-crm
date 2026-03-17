apiVersion: v1
kind: Pod
metadata:
  name: mxl-writer
  namespace: media
spec:
  # Bind the DRA claim created earlier
  resourceClaims:
    - name: media-accel
      source:
        resourceClaimName: mxl-writer-claim

  volumes:
    - name: mxl-domain
      emptyDir:
        medium: Memory
        sizeLimit: 512Mi

  containers:
    - name: writer
      image: ghcr.io/your-org/mxl-writer:latest

      # Consume the DRA claim in this container
      resources:
        claims:
          - name: media-accel

        # ---- Requests & limits (CPU & memory) ----
        # Choose values that match your Parameters CR hints (e.g., 2000m, 4Gi)
        requests:
          cpu: "2000m"        # 2 vCPU guaranteed
          cpu_specialized_feature: "specialized"
          memory: "4Gi"       # 4 GiB guaranteed
          network: "xxxMB"    # missing control network and media network.  Also redundancy.
          storage: "xxxMB"
          cache: "xxxkB"      # relevant to metal.
          gpu: "core"
          gpu_specialized_feature: "something"
        limits:
          cpu: "2000m"        # hard cap at 2 vCPU (throttled if exceeded)
          memory: "4Gi"       # hard cap at 4 GiB (OOM‑kill if exceeded)
          
      env:
        - name: MXL_DOMAIN
          value: /mxl/domain1
      volumeMounts:
        - name: mxl-domain
          mountPath: /mxl/domain1
``
