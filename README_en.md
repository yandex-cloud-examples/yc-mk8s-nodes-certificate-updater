# `DaemonSet` for updating certificates on Managed K8s nodes

## Description 
`DaemonSet` will do the following: 

1. Use a Bash script to continuously monitor nodes for the required CA certificates.
2. Copy the certificates from the secret if they are missing and update the existing ones.
3. Restart `containerd` and `dockerd`.

`DaemonSet` supports nodes running both Docker and `containerd` runtimes.

## General way to run it

1. Create a dedicated namespace for the DaemonSet:

```
kubectl apply -f certificate-updater-ns.yaml
```

1. Use `kubectl` to create a basic secret with multiple files from multiple sources within the new namespace:

```
kubectl create secret generic crt --from-file=num1.crt --from-file=num2.crt --from-file=num3.crt --from-file=num4.crt --from-file=num5.crt --namespace="certificate-updater"
```

Make sure the DaemonSet refers to a certificate named `crt`.

3. Create `DaemonSet`:

```
kubectl apply -f certificate-updater-ds.yaml
```

After you create `DaemonSet`, you can monitor its state: when certificates are updated, `dockerd` and `containerd` will restart.

### Updating certificates

Use the following command: 

```kubectl get secret crt -o yaml```

It gives us a nearly ready-to-reuse configuration. To add data to the secret as is, first encode the file using the base64 command, add the relevant content to the YAML file, and re-apply it.
