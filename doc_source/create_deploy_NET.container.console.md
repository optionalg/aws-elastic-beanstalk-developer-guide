# Using the AWS Elastic Beanstalk \.NET Platform<a name="create_deploy_NET.container.console"></a>

AWS Elastic Beanstalk supports a number of platforms for different versions of the \.NET programming framework and Windows Server\. See Supported Platforms for a full list\.

Elastic Beanstalk provides configuration options that you can use to customize the software that runs on the EC2 instances in your Elastic Beanstalk environment\. You can configure environment variables needed by your application, enable log rotation to Amazon S3, and set \.NET framework settings\.

Platform\-specific configuration options are available in the AWS Management Console for modifying the configuration of a running environment\. To avoid losing your environment's configuration when you terminate it, you can use saved configurations to save your settings and later apply them to another environment\.

To save settings in your source code, you can include configuration files\. Settings in configuration files are applied every time you create an environment or deploy your application\. You can also use configuration files to install packages, run scripts, and perform other instance customization operations during deployments\.

Settings applied in the AWS Management Console override the same settings in configuration files, if they exist\. This lets you have default settings in configuration files, and override them with environment specific settings in the console\. For more information about precedence, and other methods of changing settings, see \.

## Configuring your \.NET Environment in the AWS Management Console<a name="dotnet-console"></a>

You can use the AWS Management Console to enable log rotation to Amazon S3, configure variables that your application can read from the environment, and change \.NET framework settings\.

**To configure your \.NET environment in the Elastic Beanstalk console**

1. Open the [Elastic Beanstalk console](https://console.aws.amazon.com/elasticbeanstalk)\.

1. Navigate to the management page for your environment\.

1. Choose **Configuration**\.

1. In the **Software Configuration** section, choose the settings icon \( ![\[Edit\]](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/images/cog.png)\)\.

### Container Options<a name="dotnet-console-framework"></a>

+ **Target \.NET runtime** – Set to `2.0` to run CLR v2\.

+ **Enable 32\-bit applications** – Set to `True` to run 32\-bit applications\.

### Log Options<a name="dotnet-console-logs"></a>

The Log Options section has two settings:

+ **Instance profile** – Specifies the instance profile that has permission to access the Amazon S3 bucket associated with your application\.

+ **Enable log file rotation to Amazon S3** – Specifies whether log files for your application's Amazon EC2 instances should be copied to your Amazon S3 bucket associated with your application\.

### Environment Properties<a name="dotnet-console-properties"></a>

The **Environment Properties** section lets you specify environment configuration settings on the Amazon EC2 instances that are running your application\. These settings are passed in as key\-value pairs to the application\.

```
NameValueCollection appConfig = ConfigurationManager.AppSettings;
string endpoint = appConfig["API_ENDPOINT"];
```

See  for more information\.

## The aws:elasticbeanstalk:container:dotnet:apppool Namespace<a name="dotnet-namespaces"></a>

You can use a configuration file to set configuration options and perform other instance configuration tasks during deployments\. Configuration options can be defined by the Elastic Beanstalk service or the platform that you use and are organized into *namespaces*\.

The \.NET platform defines options in the `aws:elasticbeanstalk:container:dotnet:apppool` namespace that you can use to configure the \.NET runtime\.

The following example configuration file shows settings for each of the options available in this namespace:

**Example \.ebextensions/dotnet\-settings\.config**  

```
option_settings:
  aws:elasticbeanstalk:container:dotnet:apppool:
    Target Runtime: 2.0
    Enable 32-bit Applications: True
```

Elastic Beanstalk provides many configuration options for customizing your environment\. In addition to configuration files, you can also set configuration options using the console, saved configurations, the EB CLI, or the AWS CLI\. See  for more information\.