# OKTA_AWS_LOGIN
A CLI tool that uses SAML assertions generated by Okta's IdP portal to obtain temporary AWS CLI credentials via AWS STS.

Written by Joe Keegan - <joe@nimbusscale.com>

Hosted https://github.com/nimbusscale/okta_aws_login

This software is distributed under the MIT license. Please see the file LICENSE.txt for terms of use and redistribution. 

Happy sharing.

# DESCRIPTION
The okta_aws_login tool, written in Python 3, was developed to help solve the issue of needing to generate and store 
long lived AWS Access and Secret Keys on systems to be able to utilize AWS's CLI tools.

Okta is a leading Identity Provider and is often used by organizations to federate user credentials and provide 
Single Sign On access to the AWS console. While using Okta resolves the issue of providing federated access to the 
AWS console it does not provide an "out-of-the-box" solution for federated access when using AWS's CLI tools.

The okta_aws_login tool will prompt the user for the necessary credentials needed to authenticate with the Okta IdP  and then utilize the SAML assertion generation by Okta to retrieve temporary AWS Access and Secret Keys and an AWS 
Session Token. The AWS credentials will then be written to the local AWS credentials file to be utilized by aws cli.

okta_aws_login.py supports both password only and Multi-Factor Authentication (MFA). The following MFA options are 
supported:

* Okta Verify 
* Google Authenticator 
* SMS Authentication 


# INSTALLATION
Installtion is simple, just place the okta_aws_login.py file in a directory that is included in your PATH. Make sure 
the file is exectuable.

okta_aws_login.py must be run using Python3 and the shebang references python3 specifically. 

okta_aws_login.py requires several third party packages. The required packages can be found in the requirements.txt 
file.

# CONFIGURATION
Configuration for okta_aws_login is done via variables set within the module. There is one variable that must be 
changed and a few others that you may want to change.

* idp_entry_url - You must enter the URL for the AWS app within Okta that is configured to provide access to AWS with 
the desired role.

* cred_profile - By default the okta_aws_login tool writes the credentials to a profile within the aws credentials 
file that matches the name of the AWS role assumed with the credentials. This can be changed from "role" to "default" 
which will cause the okta_aws_login tool to write the credentials to the default profile. Writing the default profile 
will often be the desired behavior, it is not the default behavior as it prevents any unexpected overwriting of the 
default profile credentials.

* region and output_format - These will define the region and output for the profile written to the aws credentials 
file. They default to "us-west-2" and "json". Change them to suit your needs.

# USAGE
The simplest usage is to just call okta_aws_login.py from the command line. You will be prompted for the necessary credentials, including MFA credentials.

    $ okta_aws_login.py
    Username: user@example.com
    Password for user@example.com:
    Enter your Okta Verify code: 123456

Once okta_aws_login.py successfully runs, be default, your Okta session ID is cached and will be used to retrieve 
future temporary AWS credentials. The temporary AWS credentials will typically expired in 60 minutes, while an Okta
session will, by default, expire after 120 minutes of inactivity. Simply running to tool again at any time before the 
Okta session expires will refresh your AWS credentials and extend their life for another 60 minutes. Since the 
session ID is used for authentication you will not be prompted for credentials by okta_aws_login.py.

The Okta username can be specified in two other ways, one is via a command line argument using the --username option 
or by setting the username in the OKTA_USERNAME environment variable.

Additional options exist and others may be added in the future. Running `okta_aws_login.py --help` will show you all the 
options.

# Thanks
Thanks to Quint Van Deman who published [the blog post](http://blogs.aws.amazon.com/security/post/TxU0AVUS9J00FP/How-to-Implement-a-General-Solution-for-Federated-API-CLI-Access-Using-SAML-2-0) "How to Implement a General Solution for Federated 
API/CLI Access Using SAML 2.0". Without his example code the development of okta_aws_login.py would have been way more difficult.

