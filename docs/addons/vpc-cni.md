# VPC CNI Amazon EKS Add-on

The `VPC CNI Amazon EKS Add-on` adds support for `Amazon VPC Container Network Interface (CNI)` plugin.

Amazon EKS supports native VPC networking with the Amazon VPC Container Network Interface (CNI) plugin for Kubernetes. Using this plugin allows Kubernetes pods to have the same IP address inside the pod as they do on the VPC network. For more information, see [Pod networking (CNI)](https://docs.aws.amazon.com/eks/latest/userguide/pod-networking.html).

Installing VPC CNI as [Amazon EKS add-on](https://docs.aws.amazon.com/eks/latest/userguide/eks-add-ons.html) will reduce the amount of work that is needed to do in order to install, configure, and update add-ons. It includes the latest security patches, bug fixes and is validated by AWS to work with Amazon EKS. This ensures that Amazon EKS clusters are secure and stable.

Amazon EKS automatically installs VPC CNI as self-managed add-on for every cluster. So if it is already running on your cluster, you can still install it as Amazon EKS add-on to start benefiting from the capabilities of Amazon EKS add-ons.

## Prerequisite
- Amazon EKS add-ons are only available with Amazon EKS clusters running Kubernetes version 1.18 and later.

## Usage

```typescript
import * as blueprints from '@shapirov/cdk-eks-blueprint';

readonly vpcCni = new blueprints.addons.VpcCniAddOn("v1.7.5-eksbuild.2");// optionally specify image version to pull  or empty constructor

const addOns: Array<ClusterAddOn> = [ vpcCni ];

const app = new cdk.App();
new EksBlueprint(app, 'my-stack-name', addOns, [], {
  env: {
      account: <AWS_ACCOUNT_ID>,
      region: <AWS_REGION>,
  },
});
```
## Configuration Options

   - `version`: Pass in the vpc-cni plugin version compatible with kubernetes-cluster version as shown below
```bash
# Assuming cluster version is 1.19, below command shows versions of the vpc-cni add-on available for the specified cluster's version.
aws eks describe-addon-versions \
    --addon-name vpc-cni \
    --kubernetes-version 1.19 \
    --query "addons[].addonVersions[].[addonVersion, compatibilities[].defaultVersion]" \
    --output text
# Output
v1.9.0-eksbuild.1
False
v1.8.0-eksbuild.1
False
...
...
v1.7.5-eksbuild.2
True
...
...
v1.7.5-eksbuild.1
False
v1.6.3-eksbuild.2
False
```  
# Validation
To validate that vpc-cni add-on is running, ensure that the pod is in Running state.

```
$ kubectl get pods  -n kube-system|grep aws-node
NAME              READY    STATUS    RESTARTS   AGE
aws-node-xzk5n     1/1     Running   0          34d
```

```bash
# Assuming cluster-name is my-cluster, below command shows the version of vpc-cni add-on installed. Check if it is same as the version installed via EKS add-on
aws eks describe-addon \
    --cluster-name my-cluster \
    --addon-name vpc-cni \
    --query "addon.addonVersion" \
    --output text
# Output
v1.7.5-eksbuild.2
```

## Functionality

Applies VPC CNI add-on to Amazon EKS cluster.