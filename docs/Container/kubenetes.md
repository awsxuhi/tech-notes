# kubenetes

Pod definition

```yaml
apiVersion: V1
kind: Pod
metadata:
  name: nginx-busybox
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
  -name: busybox
  image: busybox
  command: ["/bin/sh"]
  args: ["-c", "while true; do echo hello; sleep 10; done"]
```

