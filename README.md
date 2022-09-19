# Deploy AWS Web Application Firewall (WAF) with CDK

[AWS Web Application Firewall](https://aws.amazon.com/waf/) (WAF) is a web application firewall that helps protect your web 
applications or APIs against common web exploits and bots that may affect availability, compromise security, or consume 
excessive resources. It gives you control over how traffic reaches your applications by enabling you to create security rules 
that control bot traffic and block common attack patterns.

You can associate two types of rules to your WAF:
* **AWS Managed rules** - AWS offers a pre-configured set of rules to address common issues such as the OWASP top 10 security risks and automated bots
* **Custom rules** - You can also create your own managed-rules to customize access to your web applications and APIs. For example, you can
restrict traffic based on a specific list of IP addresses, or on a list of countries.

This repository illustrates how you can easily deploy WAF with [AWS Cloud Development Kit](https://aws.amazon.com/cdk/) (CDK), an open-source
software development framework to define your cloud application resources with familiar programming languages such as Python.

🚧 This code deploys the following resources:

<table>
    <thead>
        <tr>
            <th>Scope</th>
            <th>Target Resource</th>
            <th>Rules</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan=3>Regional</td>
            <td rowspan=3>API Gateway</td>
            <td rowspan=1><b>IP Match</b>: Block access for non-allowed IP adresses</td>
        </tr>
        <tr>
            <td rowspan=1><b>Geolocation Match</b>: Block requests coming from non-allowed countries</td>
        </tr>
        <tr>
            <td rowspan=1>(Optional) 6 AWS Managed rules to protect your API against common threats. These rules are details later in this document</td>
        </tr>
        <tr>
            <td rowspan=3>Global</td>
            <td rowspan=3>CloudFront</td>
            <td rowspan=1><b>IP Match</b>: Block access for non-allowed IP adresses</td>
        </tr>
        <tr>
            <td rowspan=1><b>Geolocation Match</b>: Block requests coming from non-allowed countries</td>
        </tr>
        <tr>
            <td rowspan=1>(Optional) 6 AWS Managed rules to protect your web application against common threats. These rules are details later in this document</td>
        </tr>
    </tbody>
</table>

## How to deploy WAF in your AWS account ? 🚀

#### Pre-requisites

For this deployment, you will need:

* An AWS account with sufficient permissions to deploy WAF
* aws-cdk: installed globally (`npm install -g aws-cdk`)
* git client

#### Clone the repository

Clone the GitHub repository on your machine:

```bash
git clone https://github.com/louishourcade/WAF-with-CDK
cd WAF-with-CDK
```

#### Configure your WAF rules

Edit the `app.py` file with your desired configuration

```python
aws_acccount = "AWS_ACCOUNT"
region = "AWS_REGION"
ip_list = ["CIDR_RANGE_1", "CIDR_RANGE_2"]
geo_list = ["COUNTRY_CODE_1", "COUNTRY_CODE_2"]
aws_managed_rules = True
```

* `aws_account` : AWS account number in which resources will be deployed.
* `region` : AWS region in which WAF will be deployed. The WAF for your API is **regional** and will be deployed
  in the given region. The WAF for your web application is **global** (CloudFront) and will be deployed in a CloudFormation stack in us-east-1.
* `ip_list` : List of CIDR range allowed to access your web application and API.
* `geo_list` : List of countries allowed to access your web application and API. The set of valid values is available in the [AWS documentation](https://docs.aws.amazon.com/waf/latest/APIReference/API_GeoMatchStatement.html).
* `aws_managed_rules` : If this variable is set to **True**, then the set of AWS managed rules is added to the WAF. 

List of AWS managed rule groups that are associated to your WAF is `aws_managed_rules` is set to **True**:
* [Core rule set (CRS)](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-baseline.html#aws-managed-rule-groups-baseline-crs): The Core rule set (CRS) 
  rule group contains rules that are generally applicable to web applications. This provides protection against exploitation of a wide range of vulnerabilities, 
  including some of the high risk and commonly occurring vulnerabilities described in OWASP publications such as OWASP Top 10.
* [Admin protection](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-baseline.html#aws-managed-rule-groups-baseline-admin): 
  The Admin protection rule group contains rules that allow you to block external access to exposed administrative pages.
* [Known bad inputs](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-baseline.html#aws-managed-rule-groups-baseline-known-bad-inputs):
  The Known bad inputs rule group contains rules to block request patterns that are known to be invalid and are associated with exploitation or discovery of vulnerabilities. 
* [Amazon IP reputation list](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-ip-rep.html#aws-managed-rule-groups-ip-rep-amazon)
  The Amazon IP reputation list rule group contains rules that are based on Amazon internal threat intelligence. 
  This is useful if you would like to block IP addresses typically associated with bots or other threats.
* [Linux operating system managed rule group](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-use-case.html#aws-managed-rule-groups-use-case-linux-os):
  The Linux operating system rule group contains rules that block request patterns associated with the exploitation of vulnerabilities specific to Linux, including Linux-specific Local File Inclusion (LFI) attacks.
* [SQL database managed rule group](https://docs.aws.amazon.com/waf/latest/developerguide/aws-managed-rule-groups-use-case.html#aws-managed-rule-groups-use-case-sql-db): 
  The SQL database rule group contains rules to block request patterns associated with exploitation of SQL databases, like SQL injection attacks.

#### Bootstrap your AWS account

If not already done, you need to [bootstrap your AWS environment](https://docs.aws.amazon.com/cdk/v2/guide/bootstrapping.html) before deploying this CDK application.

Run the commands below with the AWS credentials of your AWS account:

```bash
cdk bootstrap aws://<tooling-account-id>/<aws-region>
```

If your region is different from **us-east-1**, then you should also run this command:

```bash
cdk bootstrap aws://<tooling-account-id>/us-east-1
```

#### Deploy the CDK application

Now that your AWS account is bootstrapped, and that you configured your WAF deployment, you can deploy the CDK application
with the following command:

```bash
cdk deploy --all
```

Confirm the deployment in the terminal, wait until the CloudFormation stack is deployed, and you're done 🎉

## Next steps

This template only deploys a WAF with a small fraction of all possible rules that you can associate to your WAF to protect your web applications and APIs.
Have a look at the [AWS WAF documentation](https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html) to build a WAF that meets your needs.