# Akamai CLI for Request Control Cloudlet
Provides a way to interact real-time with your Request Control Cloudlet via Open APIs and without manually having to go into the Luna Portal. Provides various functionality such as viewing current policies, current status, rule details, and the ability to make changes.

*NOTE:* This tool is intended to be installed via the Akamai CLI package manager, which can be retrieved from the releases page of the [Akamai CLI](https://github.com/akamai/cli) tool.

### Local Install, if you choose not to use the akamai package manager
* Python 2.7
* pip install edgegrid-python

### Credentials
In order to use this configuration, you need to:
* Set up your credential files as described in the [authorization](https://developer.akamai.com/introduction/Prov_Creds.html) and [credentials](https://developer.akamai.com/introduction/Conf_Client.html) sections of the Get Started pagegetting started guide on developer.akamai.com, the developer portal.  
* When working through this process you need to give grants for the property manager API and the User Admin API (if you will want to move properties).  The section in your configuration file should be called 'papi'.

### Overview

```
$ akamai request-control help
usage: akamai request-control help [command] [--version]  ...

Akamai CLI for Request Control

optional arguments:
  --version       show program's version number and exit

Commands:
  
    help          Show available help
    setup         Initial setup to cache policy list information
    list          Lists policies from the account information
    retrieve      Download the policy version rules json. (Optionally, use --output-file to specify location
                  of outputfile)
    create-version
                  Create a new policy version using a local file with name
                  <policy>.json
    activate      Activate a specific policy version
    add-rule      Add a new rule to a specific version in the specified
                  policy.
    modify-rule   Updates attributes of an existing rule within a policy
                  version.
	

```

## cli-cloudlet-request-control
Main program that wraps this functionality in a command line utility:
* [setup](#setup)
* [list](#list)
* [retrieve](#retrieve)
* [create-version](#create-version)
* [add-rule](#add-rule)
* [modify-rule](#modify-rule)
* [activate](#activate)


### setup
Does a one time download of Request Control Cloudlet policyIds and groupIds and stores them in /cache/policies_IG folder for faster local retrieval. This command can be run anytime and will refresh the /policies_IG folder based on the current list of policies. 

```bash
%  akamai request-control setup
```

### list
List all current Request Control Cloudlet policy names for all groups for the specific account

```bash
%  akamai request-control list
```

### retrieve
Download the raw policy rules for a specified version in json format for local editing if desired.

```bash
%  akamai request-control retrieve --policy samplePolicyName --version 87
%  akamai request-control retrieve --policy samplePolicyName --version 71 --output-file savefilename.json
```

The flags of interest for download are:

```
--policy <policyName>     Specified Request Control Cloudlet policy name
--version <version>       Specific version number for that policy name
--output-file <filename>  Filename to be saved as (optional) 

```

### create-version
Create a new policy version from a raw json file

```bash
%  akamai request-control create-version --policy samplePolicyName
%  akamai request-control create-version --policy samplePolicyName --file filename.json 
%  akamai request-control create-version --policy samplePolicyName --file filename.json --force 
```

The flags of interest for create-version are:

```
--policy <policyName>   Specified Request Control Cloudlet policy name
--file <file>           Filename of raw .json file to be used as policy details. (optional)
--force                 Use this flag if you want to proceed without confirmation if description field in json has not been updated (optional)
```


### add-rule
Add a new rule to a specific version in the specified policy.

```bash
%  akamai request-control add-rule --policy samplePolicyName --version 6 --allow-ip '1.2.3.4/30'
%  akamai request-control add-rule --policy samplePolicyName --version 7 --rule 'ruleName' --deny-country 'US DE'
%  akamai request-control add-rule --policy samplePolicyName --version 6 --rule 'ruleName' --country-branded-response 'IN'
%  akamai request-control add-rule --policy samplePolicyName --version 9 --file filename.json
```

The flags of interest for add-rule are:

```
--policy <policyName>         Specified Request Control Cloudlet policy name
--version <version>           Specific version number for that policy name
--rule <ruleName>             Name of rule in policy that should be added. Use single quotes ('') in case rule name has spaces. (optional)
--index <index>               Index for the rule (optional)
--file <file>                 Filename of raw .json file to be used as rules details. (optional)
--allow-ip                    List of IPs or CIDR blocks to be allowed separated by commas(,) within single quotes('') 
--deny-ip                     List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes('') 
--allow-country               List of country codes(case-insensitive) to be allowed separated by commas(,) within single quotes('') 
--deny-country                List of country codes(case-insensitive) to be blocked separated by commas(,) within single quotes('') 
--ip-branded-response         List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes('') 
--country-branded-response    List of country codes(case-insensitive) to be blocked separated by commas(,) within single quotes('') 

```

### modify-rule
Add a new rule to a specific version in the specified policy. ruleID can be obtained by downloading the policy rules.

```bash
%  akamai request-control modify-rule --policy samplePolicyName --version 8 --rule-id 896fghk236eef056 --file filename.json
%  akamai request-control modify-rule --policy samplePolicyName --version 7 --rule-id dg4j5dod70eb5pa2 --deny-country 'IN'
%  akamai request-control modify-rule --policy samplePolicyName --version 9 --rule-id hj7j8keb5pa678g2 --ip-branded-response '1.2.3.4 5.6.7.8'
%  akamai request-control modify-rule --disable --policy samplePolicyName --version 10 --rule-id 'rule-id' --country-branded-response 'VI' --disable
```

The flags of interest for modify-rule are:

```
--policy <policyName>         Specified Request Control Cloudlet policy name
--version <version>           Specific version number for that policy name
--rule-id <rule ID>           ID associated with that particular rule
--rule <ruleName>             Name of rule in policy that should be added. Use single quotes ('') in case rule name has spaces. (optional)
--file <file>                 Filename of raw .json file to be used as rules details. (optional)
--allow-ip                    List of IPs or CIDR blocks to be allowed separated by commas(,) within single quotes('')
--deny-ip                     List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes('')
--allow-country               List of country codes(case-insensitive) to be allowed separated by commas(,) within single quotes('') 
--deny-country                List of country codes(case-insensitive) to be blocked separated by commas(,) within single quotes('') 
--ip-branded-response         List of IPs or CIDR blocks to be blocked separated by commas(,) within single quotes(''). This will replace the current IP/CIDR list if any. 
--country-branded-response    List of country codes(case-insensitive) to be blocked separated by commas(,) within single quotes(''). This will replace the current country list if any. 
--enable                      Enables the rule in the policy 
--disable                     Disables the rule in the policy
```


### activate
Activate a specified version for a policy to the appropriate network (staging or production)

```bash
%  akamai request-control activate --policy samplePolicyName --version 87 --network staging
%  akamai request-control activate --policy samplePolicyName --version 71 --network production
```

The flags of interest for activate are:

```
--version <version>     Specific version number for that policy name
--network <network>     Either staging or production
--policy <policyName>   Specified Request Control Cloudlet policy name 

```


## Caveats/Info

* Before we can execute these commands, please ensure we have at least one Request Control Cloudlet policy and version already created in the Luna Portal.
* You can find more about Request Control Cloudlet in here - https://control.akamai.com/dl/customers/Cloudlets/RequestControlQuickRef.pdf
https://control.akamai.com/dl/customers/Cloudlets/RequestControlGuide.pdf
* You can find more about Cloudlets APIs in here - https://developer.akamai.com/api/luna/cloudlets/overview.html
