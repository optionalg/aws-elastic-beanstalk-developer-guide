# Deployment Policies and Settings<a name="using-features.rolling-version-deploy"></a>

AWS Elastic Beanstalk provides several options for how deployments are processed, including deployment policies \(**All at once**, **Rolling**, **Rolling with additional batch**, and **Immutable**\) and options that let you configure batch size and health check behavior during deployments\. By default, your environment uses rolling deployments if you created it with the console or EB CLI, or all at once deployments if you created it with a different client \(API, SDK or AWS CLI\)\.

With rolling deployments, AWS Elastic Beanstalk splits the environment's EC2 instances into batches and deploys the new version of the application to one batch at a time, leaving the rest of the instances in the environment running the old version of the application\. During a rolling deployment, some instances serve requests with the old version of the application, while instances in completed batches serve other requests with the new version\.

If you need to maintain full capacity during deployments, you can configure your environment to launch a new batch of instances prior to taking any instances out of service\. This option is called a **rolling deployment with an additional batch**\. When the deployment completes, Elastic Beanstalk terminates the additional batch of instances\.

**Immutable deployments** perform an immutable update to launch a full set of new instances running the new version of the application in a separate Auto Scaling group alongside the instances running the old version\. Immutable deployments can prevent issues caused by partially completed rolling deployments\. If the new instances don't pass health checks, Elastic Beanstalk terminates them, leaving the original instances untouched\.

If your application doesn't pass all health checks, but still operates correctly at a lower health status, you can allow instances to pass health checks with a lower status, such as `Warning`, by modifying the **Healthy threshold** option\. If your deployments fail because they don't pass health checks and you need to force an update regardless of health status, specify the **Ignore health check** option\.

When you specify a batch size for rolling updates, Elastic Beanstalk also uses that value for rolling application restarts\. Use rolling restarts when you need to restart the proxy and application servers running on your environment's instances without downtime\.

## Configuring Application Deployments<a name="environments-cfg-rollingdeployments-console"></a>

In the environment management console, enable and configure batched application version deployments by editing **Updates and Deployments** on the environment's **Configuration** page\.

**To configure deployments \(console\)**

1. Open the [Elastic Beanstalk console](https://console.aws.amazon.com/elasticbeanstalk)\.

1. Navigate to the management page for your environment\.

1. Choose **Configuration**\.

1. In the **Updates and Deployments** section, choose ![\[Edit\]](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/images/cog.png)\. 

1. In the **Application Deployments** section, choose a **Deployment policy**, batch settings and health check options\.

1. Choose **Apply**\.

The **Application Deployments** section of the **Updates and Deployments** page has the following options for rolling updates:

+ **Deployment policy** – Choose from the following deployment options:

  + **All at once** – Deploy the new version to all instances simultaneously\. All instances in your environment are out of service for a short time while the deployment occurs\.

  + **Rolling** – Deploy the new version in batches\. Each batch is taken out of service during the deployment phase, reducing your environment's capacity by the number of instances in a batch\.

  + **Rolling with additional batch** – Deploy the new version in batches, but first launch a new batch of instances to ensure full capacity during the deployment process\.

  + **Immutable** – Deploy the new version to a fresh group of instances by performing an immutable update\.

+ **Batch type** – Whether you want to allocate a percentage of the total number of EC2 instances in the Auto Scaling group or a fixed number to a batch of instances\.

+ **Batch size** – The number or percentage of instances to deploy in each batch, up to 100 percent or the maximum instance count in your environment's Auto Scaling configuration\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/images/environment-cfg-rollingdeployments.png)

The **Preferences** section contains options related to health checks\.

+ **Healthy threshold** – Lowers the threshold at which an instance is considered healthy during rolling deployments, rolling updates and immutable updates\.

+ **Ignore health check** – Prevents a deployment from rolling back when a batch fails to become healthy within the **Command timeout**\.

+ **Command timeout** – The number of seconds to wait for an instance to become healthy before cancelling the deployment or, if **Ignore health check** is set, to continue to the next batch\.

![\[Elastic Beanstalk Application Deployment Configuration Page\]](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/images/environment-cfg-healthchecks.png)

## How Rolling Deployments Work<a name="environments-cfg-rollingdeployments-method"></a>

When processing a batch, Elastic Beanstalk detaches all instances in the batch from the load balancer, deploys the new application version, and then reattaches the instances\. If you have connection draining enabled, Elastic Beanstalk drains existing connections from the EC2 instances in each batch before beginning the deployment\.

After reattaching the instances in a batch to the load balancer, Elastic Load Balancing waits until they pass a minimum number of Elastic Load Balancing health checks \(the **Healthy check count threshold** value\), and then starts routing traffic to them\. If no health check URL is configured, this can happen very quickly, because an instance will pass the health check as soon as it can accept a TCP connection\. If a health check URL is configured, the load balancer doesn't route traffic to the updated instances until they return a `200 OK` status code in response to an `HTTP GET` request to the health check URL\.

Elastic Beanstalk waits until all instances in a batch are healthy before moving on to the next batch\. With basic health reporting, instance health depends on the Elastic Load Balancing health check status\. When all instances in the batch pass enough health checks to be considered healthy by Elastic Load Balancing, the batch is complete\. If enhanced health reporting is enabled, Elastic Beanstalk considers several other factors, including the result of incoming requests\. With enhanced health reporting, all instances must pass 12 consecutive health checks with an OK status within 2 minutes for web server environments, and 18 health checks within three minutes for worker environments\.

If a batch of instances does not become healthy within the command timeout, the deployment fails\. After a failed deployment, check the health of the instances in your environment for information about the cause of the failure, and perform another deployment with a fixed or known good version of your application to roll back\.

If a deployment fails after one or more batches completed successfully, the completed batches run the new version of your application while any pending batches continue to run the old version\. You can identify the version running on the instances in your environment on the health page in the console\. This page displays the deployment ID of the most recent deployment that executed on each instance in your environment\. If you terminate instances from the failed deployment, Elastic Beanstalk replaces them with instances running the application version from the most recent successful deployment\.

## The aws:elasticbeanstalk:command Namespace<a name="environments-cfg-rollingdeployments-namespace"></a>

You can also use the configuration options in the `aws:elasticbeanstalk:command` namespace to configure rolling deployments\.

Use the `DeploymentPolicy` option to set the deployment type\. The following values are supported:

+ `AllAtOnce` disables rolling deployments and always deploy to all instances simultaneously\.

+ `Rolling` enables standard rolling deployments\.

+ `RollingWithAdditionalBatch` launches an extra batch of instances prior to starting the deployment to maintain full capacity\.

+ `Immutable` performs an immutable update for every deployment\.

When you enable rolling deployments, set the `BatchSize` and `BatchSizeType` options to configure the size of each batch\. For example, to deploy 25% of all instances in each batch, specify the following options and values:

**Example \.ebextensions/rolling\-updates\.config**  

```
option_settings:
  aws:elasticbeanstalk:command:
    DeploymentPolicy: Rolling
    BatchSizeType: Percentage
    BatchSize: 25
```

To deploy to five instances in each batch regardless of the number of instances running, and to bring up an extra batch of five instances running the new version prior to pulling any instances out of service, specify the following options and values:

**Example \.ebextensions/rolling\-additionalbatch\.config**  

```
option_settings:
  aws:elasticbeanstalk:command:
    DeploymentPolicy: RollingWithAdditionalBatch
    BatchSizeType: Fixed
    BatchSize: 5
```

To perform an immutable update for each deployment with a health check threshold of **Warning**, and proceed with the deployment even if instances in a batch don't pass health checks within a timeout of 15 minutes, specify the following options and values:

**Example \.ebextensions/immutable\-ignorehealth\.config**  

```
option_settings:
  aws:elasticbeanstalk:command:
    DeploymentPolicy: Immutable
    HealthCheckSuccessThreshold: Warning
    IgnoreHealthCheck: true
    Timeout: "900"
```

EB CLI and Elastic Beanstalk console apply recommended values for the preceding options\. These settings must be removed if you want to use configuration files to configure the same\. See  for details\.