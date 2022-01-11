# Fully Automated MultiDomain DNS Environment using CDK

We always used to manually set up our DNS, but let's try to completly have it all in code so that is completely automated and no more human involvement is required to set up our Multi Domain Architecture.

So below is our Domains Setup.

So we will have a stack in each environment (dev, staging and prod). but we will have a main environment that is responsible for the root domain stack localfarmer.com

First we followed the steps to migrate the registerar to route53. we will make the main environment be responsible for the domain registration.

## The Main Stack

- HostedZone
- Custom Resource to put the just created hosted zones name servers

It will share the delegation role to be used by each child account to update the hosted zone with required nameservers record

## The SubDomain Stack

- Hosted zone for subdomain dev.localfarmer.com for dev environment
- SSL Certificate for subdomain
- CloudFront
- for prod only - Custom Resource to create and validate multi domain certificate that includes both root domain & sub domain (prod.localfarmer.com & localfarmer.com)

## Understanding the flow that we are reflecting in CDK:

first we migrated and registered domain name localfarmer in route53 registered domain. note that after AWS automatically creates a hosted zone after registering a domain. we don't need this as our CDK code will create and maintain one. so let's go ahead and delete this hosted zone created.

Main Stack deployed to main environment. Stack will create hosted Zone for root domain localfarmer.com.
new nameservers list generated in hosted zone needs to replace nameservers in the registered domain.

Child stack deployed to dev and staging environment. Stack will create hosted Zone for sub domain dev.localfarmer.com. nameservers list generated will need to be added to the root hosed zone in the main stack. This is done using the zone account delegation. cerate Cloudfront to serve static web site. Then create an alias record in hosted zone to make sub domain point to cloudfront distribution. create SSL Certificate to be used by cloudfront.

## The Code
