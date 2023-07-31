## console-private-access-automation

## Overview

Official documentation - https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/console-private-access.html

The Management Console - Private Access feature enables enterprise customers to control which AWS accounts and organizations can be accessed from their corporate networks. This is accomplished by conditionally forwarding DNS queries for aws.amazon.com to a Route 53 Inbound resolver which will resolve those queries to a console VPC Endpoint in the VPC through a Private Hosted Zone, and using the endpoint policy to control the accounts/organizations that can be allowed.

* In short, implementing this feature requires
    * VPC Endpoints for console and signin PrivateLink services in each supported and required Region (us-east-1 is a must)
    * Route 53 Inbound Resolver in a VPC
    *  Route 53 Private Hosted Zone for aws.amazon.com associated to the VPC that has the Inbounc Resolver with RecordSets for all the listed records in the documentation
    * DNS Forwarding for aws.amazon.com achieved in one of two ways
        * Conditionally forward only the listed sub-domains of aws.amazon.com to the Inbound resolver from on-premises
        * Or forward all DNS queries for aws.amazon.com from on-premises to the Inbound resolver
            - This can break access to sub-domains that are not configured in the PHZ, for example docs.aws.amazon.com, health.aws.amazon.com (Refer SIM https://t.corp.amazon.com/P91231640)


**Parameters**
1. VPC - Select a VPC from the dropdown for deploying the resources
2. Subnet 1, Subnet 2 - Select two subnets from the selected VPC for deploying the VPC endpoints and Inbound resolver endpoint
3. Security Group - Select a security group for the VPC Endpoints and Inbound Resolver - This should allow TCP 443 and UDP 53 from on-premises network

**Resources created:**

|Logical ID|Type|
|-|-|
|consoleEndpoint|AWS::EC2::VPCEndpoint|
|signinEndpoint|AWS::EC2::VPCEndpoint|
|inboundResolver|AWS::Route53Resolver::ResolverEndpoint|
|hostedZone|AWS::Route53::HostedZone|
|consoleRecord|AWS::Route53::RecordSet|
|globalConsoleRecord|AWS::Route53::RecordSet|
|signinRecord|AWS::Route53::RecordSet|
|use1ConsoleRecord|AWS::Route53::RecordSet|
|use1SigninRecord|AWS::Route53::RecordSet|
|widgetConsoleRecord|AWS::Route53::RecordSet|
|nmConsoleRecord|AWS::Route53::RecordSet|
|supportconsoleRecord|AWS::Route53::RecordSet|

## About this project

The goal of this project is to provide customers with automation for deploying Management Console - Private Access using CloudFormation. This is needed because the feature is expected to incrementally add support for more Regions and services, and that will be very manual, cumbersome and error-prone for customers to keep making those additions in their implementations. This automation script will make that process faster, smoother and simpler.


## How to Test

One way to test this is by deploying a client VPN endpoint in us-east-1 with the DNS server as the Inbound resolver, thus forwarding all DNS queries to the resolver during a VPN session. The resolver will use the PHZ for resolving all queries to aws.amazon.com. However, as stated above, this will break resolution for some sub-domains such as docs.aws.amazon.com.

## Limitations

- This feature currently is supported only in a handful of Regions and a limited number of services. When using this feature, users will see the un-supported Regions and services greyed out in the console

## To-do
* This script has to be deployed as a Stack in each Region. That is because you have to provide the VPC and Subnet in each Region for the endpoints and resolver. Explore if this can be deployed as a StackSet. 
* Add all other service Interface endpoints to the script


## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

