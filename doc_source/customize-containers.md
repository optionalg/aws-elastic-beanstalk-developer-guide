# AWS Elastic Beanstalk Environment Configuration<a name="customize-containers"></a>

Elastic Beanstalk provides a wide range of options for customizing the resources in your environment, Elastic Beanstalk's behavior, and platform settings\. When you create a web server environment, Elastic Beanstalk creates several resources to support the operation of your application\.

+ **EC2 instance** – An Amazon Elastic Compute Cloud \(Amazon EC2\) virtual machine configured to run web apps on the platform that you choose\.

  Each platform runs a different set of software, configuration files, and scripts to support a specific language version, framework, web container, or combination thereof\. Most platforms use either Apache or nginx as a reverse proxy that sits in front of your web app, forwards requests to it, serves static assets, and generates access and error logs\.

+ **Instance security group** – An Amazon EC2 security group configured to allow ingress on port 80\. This resource lets HTTP traffic from the load balancer reach the EC2 instance running your web app\. By default, traffic is not allowed on other ports\.

+ **Load balancer** – An Elastic Load Balancing load balancer configured to distribute requests to the instances running your application\. A load balancer also eliminates the need to expose your instances directly to the Internet\.

+ **Load balancer security group** – An Amazon EC2 security group configured to allow ingress on port 80\. This resource lets HTTP traffic from the Internet reach the load balancer\. By default, traffic is not allowed on other ports\.

+ **Auto Scaling group** – An Auto Scaling group configured to replace an instance if it is terminated or becomes unavailable\.

+ **Amazon S3 bucket** – A storage location for your source code, logs, and other artifacts that are created when you use Elastic Beanstalk\.

+ **Amazon CloudWatch alarms** – Two CloudWatch alarms that monitor the load on the instances in your environment and are triggered if the load is too high or too low\. When an alarm is triggered, your Auto Scaling group scales up or down in response\.

+ **AWS CloudFormation stack** – Elastic Beanstalk uses AWS CloudFormation to launch the resources in your environment and propagate configuration changes\. The resources are defined in a template that you can view in the [AWS CloudFormation console](https://console.aws.amazon.com/cloudformation)\.

+ **Domain name** – A domain name that routes to your web app in the form **subdomain*\.*region*\.elasticbeanstalk\.com*\.

This topic focuses on the resource configuration options available in the Elastic Beanstalk console\. The following topics show how to configure your environment in the console, and describe the underlying namespaces that correspond to the console options for use with configuration files or API configuration options\. You can learn more about advanced configuration methods in the next chapter\. 


+ [Your AWS Elastic Beanstalk Environment's Auto Scaling Group](using-features.managing.as.md)
+ [Your AWS Elastic Beanstalk Environment's Load Balancer](using-features.managing.elb.md)
+ [Adding a Database to Your Elastic Beanstalk Environment](using-features.managing.db.md)
+ [Environment Properties and Other Software Settings](environments-cfg-softwaresettings.md)
+ [Configuring AWS X\-Ray Debugging](environment-configuration-debugging.md)
+ [Elastic Beanstalk Environment Notifications with Amazon Simple Notification Service](using-features.managing.sns.md)
+ [Streaming Logs to Amazon CloudWatch](using-features.managing.cw.md)
+ [Configuring VPC with Elastic Beanstalk](using-features.managing.vpc.md)
+ [Your Elastic Beanstalk Environment's Domain Name](customdomains.md)
+ [Updating Tags in an Elastic Beanstalk Environment](environment-configuration-tags.md)