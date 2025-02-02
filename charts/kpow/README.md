# Run kPow for Apache Kafka in Kubernetes

[kPow](https://kpow.io) is the all-in-one toolkit to manage, monitor, and learn about your Kafka resources.

This Helm chart uses the [operatr/kpow](https://hub.docker.com/r/operatr/kpow) container from Dockerhub.

## Prerequisites

To run the Dockerhub container requires a license. Start a [free 30-day trial](https://kpow.io/try) of kPow today.

See [kPow on the AWS Marketplace](https://docs.kpow.io/installation/aws-marketplace) to have kPow billed automatically to your AWS account, no license required.

## Installation

You need to connect to a Kubernetes environment before you can install kPow.

The following examples demonstrate installing kPow in [Amazon EKS](https://aws.amazon.com/eks/).

### Configure Kubernetes/EKS

```bash
aws eks --region <your-aws-region> update-kubeconfig --name <your-eks-cluster-name>

Updated context arn:aws:eks:<your-aws-region>:123123123:cluster/<your-eks-cluster-name> in /your/.kube/config
```

#### Confirm Kubernetes Cluster Availability

```bash
kubectl get svc

NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   12.345.6.7   <none>        443/TCP   28h
```

### Run kPow in Kubernetes

#### Configure the kPow Helm Repository

Add the Helm Repository in order to use the kPow Helm Chart.

```
helm repo add kpow https://charts.kpow.io
```

Update Helm repositories to ensure you install the latest version of kPow.

```
helm repo update
```

#### Pull the kPow Chart

```
helm pull kpow/kpow --untar --untardir .
```

#### Update kPow Configuration

The minimum information required by kPow to operate is:

* License Details
* Kafka Bootstrap URL

Update [./kpow/templates/kpow-config.yaml](charts/kpow/templates/kpow-config.yaml) with your environment configuration.

See the [kPow Documentation](https://docs.kpow.io) for a full list of configuration options.

```bash
vi ./kpow/templates/kpow-config.yaml
```

#### Start a kPow Instance

```bash
helm install --namespace operatr-io --create-namespace my-kpow ./kpow

NAME: my-kpow
LAST DEPLOYED: Mon May 31 17:22:21 2021
NAMESPACE: operatr-io
STATUS: deployed
REVISION: 1
NOTES:
1. Get the application URL by running these commands:
  export POD_NAME=$(kubectl get pods --namespace operatr-io -l "app.kubernetes.io/name=kpow,app.kubernetes.io/instance=my-kpow" -o jsonpath="{.items[0].metadata.name}")
  echo "Visit http://127.0.0.1:3000 to use your application"
  kubectl --namespace operatr-io port-forward $POD_NAME 3000:3000
```

#### Access the kPow UI

Follow the instructions in the notes to configure port forwarding.

```bash
export POD_NAME=$(kubectl get pods --namespace operatr-io -l "app.kubernetes.io/name=kpow,app.kubernetes.io/instance=my-kpow" -o jsonpath="{.items[0].metadata.name}")
echo "Visit http://127.0.0.1:3000 to use your application"
kubectl --namespace operatr-io port-forward $POD_NAME 3000:3000
```

kPow is now available on [http://127.0.0.1:3000](http://127.0.0.1:3000).

#### Check the kPow Pod

```bash
kubectl describe pods --namespace operatr-io

Name:         my-kpow-9988df6b6-vvf8z
Namespace:    operatr-io
Priority:     0
Node:         ip-172-31-33-42.ap-southeast-2.compute.internal/172.31.33.42
Start Time:   Mon, 31 May 2021 17:22:22 +1000
Labels:       app.kubernetes.io/instance=my-kpow
              app.kubernetes.io/name=kpow
              pod-template-hash=9988df6b6
Annotations:  kubernetes.io/psp: eks.privileged
Status:       Running
```

#### View the kPow Pod Logs

```bash
kubectl logs --namespace operatr-io my-kpow-9988df6b6-vvf8z 

11:36:49.111 INFO  [main] operatr.system ? start kPow
...
```

#### Remove kPow

```bash
helm delete --namespace operatr-io my-kpow
```

### Get Help!

If you have any issues or errors, please contact support@operatr.io.

### Licensing and Modifications

This repository is Apache 2.0 licensed, you are welcome to clone and modify as required.