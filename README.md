# kind-multus

Attempting to setup multus in a local kind cluster.

## Step 1: Create kind cluster

```bash
kind create cluster --config kind-config.yaml
```

## Step 2: Install Calico as the default CNI

```bash
kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
```

## Step 3: Install multus

Note, i've stubbed in the chainguard image in the manifest. Upstream doesn't
provide arm images, so wouldn't be able to test on macOS otherwise:

```bash
k apply -f multus.yaml
```

Wait until all Calico and Multus pods are running!

## Step 4: Create example NetworkAttachmentDefinition

```bash
k apply -f network-attachment.yaml
```

## Step 5: Deploy a Pod with Multus Annotation

```bash
k apply -f pod.yaml
```

Wait for the pod to be running!

## Step 6: Verify the Network Attachments

```bash
kubectl exec -it example-pod -- ip a
```

# Issues!

Pod does not start (step 5), errors in log:

```bash
% k get po
NAME          READY   STATUS              RESTARTS   AGE
example-pod   0/1     ContainerCreating   0          9m18s
```

```bash
ERRORED: error configuring pod [default/example-pod] networking: [default/example-pod/3bf8fcab-6fa7-4b5e-bb43-5796676e159d:k8s-pod-network]: error adding container to network "k8s-pod-network": DelegateAdd: cannot set "" interface name to "eth0": validateIfName: no net namespace /var/run/netns/cni-010a4a7a-533f-b302-14d7-3910ac4f526b found: failed to Statfs "/var/run/netns/cni-010a4a7a-533f-b302-14d7-3910ac4f526b": no such file or directory
```
