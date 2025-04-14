# Kubernetes Assignment Guide

## Step-by-Step Instructions

The Kubernetes assignment that consists of three main tasks:

1. Creating a Nginx web server Pod YAML definition
2. Creating a Pod with a logging sidecar container
3. Learning essential kubectl commands

## Step 1: Set Up Your Working Directory

1. Create a directory for your assignment files:

```
mkdir -p kubernetes-assignment
cd kubernetes-assignment
```

## Step 2: Create the Nginx Pod YAML Definition (Question 1)

1. Create a file named `nginx-pod.yaml` using your preferred text editor
2. Add the following content to the file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
          protocol: TCP
```

3. Save the file

## Step 3: Create the Pod with Logging Sidecar (Question 2)

1. Create a file named `sidecar-pod.yaml` using your preferred text editor
2. Add the following content to the file:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: app-with-sidecar
  labels:
    app: web-application
spec:
  containers:
    - name: main-app
      image: busybox
      args:
        - /bin/sh
        - -c
        - >
          while true;
          do
            echo "$(date): Application log entry" >> /var/log/app/app.log;
            sleep 5;
          done
      volumeMounts:
        - name: log-volume
          mountPath: /var/log/app

    - name: log-sidecar
      image: busybox
      args:
        - /bin/sh
        - -c
        - >
          tail -f /var/log/app/app.log
      volumeMounts:
        - name: log-volume
          mountPath: /var/log/app

  volumes:
    - name: log-volume
      emptyDir: {}
```

3. Save the file

## Step 4: Test Your YAML Files and kubectl Commands

1. Start your Kubernetes environment if it's not already running:

   - Docker Desktop: Ensure Kubernetes is enabled in settings
   - Minikube: Run `minikube start`

2. Verify connectivity to your Kubernetes cluster:

```
kubectl cluster-info
```

3. Create the Nginx Pod using kubectl:

```
kubectl apply -f nginx-pod.yaml
```

4. Check that the pod is running:

```
kubectl get pods
```

You should see `nginx-pod` with Status `Running`

5. Get detailed information about the pod:

```
kubectl describe pod nginx-pod
```

This command shows detailed information including events, status, and configurations

6. Create the Pod with the sidecar container:

```
kubectl apply -f sidecar-pod.yaml
```

7. Verify both pods are running:

```
kubectl get pods
```

You should see both `nginx-pod` and `app-with-sidecar` with Status `Running`. The `app-with-sidecar` pod should show `2/2` in the READY column, indicating both containers are running.

8. Check the logs from the sidecar container:

```
kubectl logs app-with-sidecar -c log-sidecar
```

You should see timestamped log entries that are being written by the main container and read by the sidecar.

## Step 5: Explain the Volume Sharing Mechanism (For Question 2)

For your assignment, include the following explanation about how the sidecar container shares a volume with the main container:

### Volume Sharing Explanation

In the `sidecar-pod.yaml` configuration:

1. **Volume Definition**:

   - An `emptyDir` volume named `log-volume` is created when the Pod starts
   - This volume exists as long as the Pod is running and is shared across all containers
   - It provides a common storage space accessible to both containers

2. **Volume Mounting**:

   - Both containers mount the same volume at the identical path `/var/log/app`
   - This creates a shared directory that both containers can access

3. **Log Flow Process**:

   - The main application container writes logs to `/var/log/app/app.log` in the shared volume
   - The sidecar container reads from this same file in real-time using `tail -f`
   - Changes to the log file are immediately visible to the sidecar container

4. **Benefits**:
   - Separation of concerns: Application logic in the main container, log processing in the sidecar
   - The sidecar container can be updated independently of the main application
   - This pattern enables centralized log processing, filtering, or forwarding to external systems

## Step 6: Document the Required kubectl Commands (For Question 3)

For the third part of your assignment, include these kubectl commands with explanations:

### a. Create a Pod using a YAML file

```
kubectl apply -f nginx-pod.yaml
```

This command creates or updates Kubernetes resources defined in the YAML file. It's idempotent, meaning you can run it multiple times without error.

### b. Get the list of Pods in the cluster

```
kubectl get pods
```

This command lists all pods in the current namespace, showing their names, ready status, status, restarts, and age.

### c. Describe a specific Pod

```
kubectl describe pod nginx-pod
```

This command provides detailed information about the specified pod, including events, conditions, container statuses, and configuration details.

## Step 7: Clean Up Resources (Optional)

When you're done with testing, you can remove the pods:

```
kubectl delete pod nginx-pod
kubectl delete pod app-with-sidecar
```

## Additional Tips

- If you encounter issues with pod creation, check your YAML syntax and indentation
- Use `kubectl get events` to see cluster events that might help troubleshoot problems
- For more information on specific kubectl commands, use `kubectl <command> --help`

## Conclusion

By completing these steps, you will have successfully:

1. Created a Nginx web server Pod definition
2. Designed a Pod with a main container and logging sidecar that share a volume
3. Learned and demonstrated essential kubectl commands

Remember to take screenshots of your terminal commands and their outputs for documentation in your assignment submission.
