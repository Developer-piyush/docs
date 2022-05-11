# Provider Attributes and Pricing Adjustments

## Storage Class Types <a href="#storage-class-types" id="storage-class-types"></a>

In the subsequent section persistent storage attributes will be defined.  Use the chart below to determine your provider's storage class.

| Class Name | Throughput/Approx matching device |
| ---------- | --------------------------------- |
| beta1      | hdd                               |
| beta2      | ssd                               |
| beta3      | NVMe                              |
| default    | Provider defined default class    |

## Attribute Adjustments

* Conduct the steps in this section on the Kubernetes master from which the provider was configured in prior steps
* Adjust the following key-values pairs as necessary:
  * Update the value of the `capabilities/storage/2/class` key to the correct storage class such as `"beta2"`
  * Update the region value from current `us-west` to an appropriate value such as `us-east` OR `eu-west`

```
helm upgrade akash-provider akash/provider -n akash-services \
  --set keyringbackend="test" \
  --set from="$KEY_NAME" \
  --set key="$(cat ./key.pem | base64)" \
  --set keysecret="$(echo $KEY_PASSWORD | base64)" \
  --set domain="$DOMAIN" \
  --set chainid="akashnet-2" \
  --set image.tag="$AKASH_VERSION" \
  --set gas=auto \
  --set gasadjustment=1.25 \
  --set gasprices=0.025uakt \
  --set withdrawalperiod=24h \
  --set bidpricestoragescale="0.00016,beta3=0.00016" \
  --set node="http://akash-node-1:26657" \
  --set attributes[0].key="region" \
  --set attributes[0].value="us-west" \
  --set attributes[1].key="host" \
  --set attributes[1].value="akash" \
  --set attributes[2].key="tier" \
  --set attributes[2].value="community" \
  --set attributes[3].key="capabilities/storage/1/class" \
  --set attributes[3].value="default" \
  --set attributes[4].key="capabilities/storage/1/persistent" \
  --set attributes[4].value="true" \
  --set attributes[5].key="capabilities/storage/2/class" \
  --set attributes[5].value="<beta1|beta2|beta3>" \
  --set attributes[6].key="capabilities/storage/2/persistent" \
  --set attributes[6].value="true"
```

#### Example Completed Attributes Section

```
cd ~

helm uninstall akash-provider -n akash-services

helm repo update

helm install akash-provider akash/provider -n akash-services \
  --set keyringbackend="test" \
  --set from="$KEY_NAME" \
  --set key="$(cat ./key.pem | base64)" \
  --set keysecret="$(echo $KEY_PASSWORD | base64)" \
  --set domain="$DOMAIN" \
  --set chainid="akashnet-2" \
  --set image.tag="$AKASH_VERSION" \
  --set gas=auto \
  --set gasadjustment=1.25 \
  --set gasprices=0.025uakt \
  --set withdrawalperiod=24h \
  --set bidpricestoragescale="0.00016,beta3=0.00016" \
  --set node="http://akash-node-1:26657" \
  --set attributes[0].key="region" \
  --set attributes[0].value="us-east" \
  --set attributes[1].key="host" \
  --set attributes[1].value="akash" \
  --set attributes[2].key="tier" \
  --set attributes[2].value="community" \
  --set attributes[3].key="capabilities/storage/1/class" \
  --set attributes[3].value="default" \
  --set attributes[4].key="capabilities/storage/1/persistent" \
  --set attributes[4].value="true" \
  --set attributes[5].key="capabilities/storage/2/class" \
  --set attributes[5].value="beta2" \
  --set attributes[6].key="capabilities/storage/2/persistent" \
  --set attributes[6].value="true"
```

## Update Provider Attributes

### Pricing Adjustments

* Use the AKASH\_BID\_PRICE\_STORAGE\_SCALE environment variable to set provider pricing detail
* Use the values as indicated in the sample for initial testing

### Enact Attribute Updates

```
cd ~/helm-charts/charts

helm upgrade akash-provider ./akash-provider -n akash-services \
     --set from="$ACCOUNT_ADDRESS" \
     --set key="$(cat ~/key.pem | base64)" \
     --set keysecret="$(echo $KEY_SECRET | base64)" \
     --set domain="$DOMAIN" \
     --set node="$NODE" \
     --set chainid="akashnet-2" \
     --set image.tag="$AKASH_VERSION" \
     --set bidpricestoragescale="0.0001\,beta2=0.002"
```

#### Expected Result

```
Release "akash-provider" has been upgraded. Happy Helming!
NAME: akash-provider
LAST DEPLOYED: Mon Mar  7 17:16:20 2022
NAMESPACE: akash-services
STATUS: deployed
REVISION: 2
TEST SUITE: None
```

## **Restart Provider Pod**

### **Capture the Name of Provider Pod**

```
kubectl get pods -n akash-services
```

#### Expected Provider Status

* Note - the Helm upgrade will spawn a new provider pod
* Possible the prior provider pod may show with a status of deleting on initial view and then would eventually disappear from output

```
root@node1:~/helm-charts/charts# kubectl get pods -n akash-services

NAME                                 READY   STATUS    RESTARTS   AGE
akash-provider-6bf9986cdc-btvlg      1/1     Running   0          3m13s
```