# Akash Node Installation

## Uninstall Any Prior Node Instances

**NOTE** - if no previous Akash node instances are found the following message will be received and is expected

* \``` Error: uninstall: Release not loaded: akash-node: release: not found` ``

```
helm -n akash-services uninstall akash-node
```

## **Install Akash Node as a Kubernetes Pod**

```
helm install akash-node akash/akash-node -n akash-services --set image.tag=0.16.4-rc2 --set state_sync.enabled=true
```