### Stackery Multi-Api Monorepo Example

This repo contains three stacks:

* Users api
* Accounts api
* Top-level API Gateway custom domain

The top-level domain ties the two sub apis together using base path mappings. The users api is mounted at /users, and the accounts api is mounted at /accounnts.

The three stacks can be re-deployed independently, though the two sub-api stacks must exist before the top-level domain stack is provisioned. The sub-api IDs are exported from the sub-api stacks and imported by the top-level domain stack.

#### Deployment steps
1. Fork this repo to your own account
1. Create three Stackery stacks, one for each of the stacks in this repo
1. Create an environment in Stackery if you don't already have one
1. Add a custom domain and validation domain to the Stackery environment parameters as "domain" and "validationDomain" properties. This is a requirement to provision the full API in the end, though the sub-apis can be deployed without a domain. When provisioning the domain, emails will be sent to the contact addresses in the DNS registrar info for the validation domain. The validation domain must be the same as the domain or a parent of the domain. Example parameter values:
    ```JSON
    {
      "domain": "foo.example.com",
      "validationDomain": "example.com"
    }
    ```
    This will send validation emails to the admins of example.com to approve a certificate for foo.example.com. (See https://docs.stackery.io/docs/api/nodes/RestApi/#custom-domain for more info)
1. Deploy the two sub-api stacks first. This is easiest using our CLI and the local strategy by descending into each directory and running:
    ```
    stackery deploy --strategy local --env-name <your Stackery environment name>
    ```
    An extra configuration step is necessary if you want to do a cloud-side deployment strategy to tell Stackery which template file to use for each stack. You can find more details here: https://docs.stackery.io/docs/using-stackery/mono-repo-apps/#adding-a-mono-repo-in-the-cli
1. Deploy the top-level domain in the same manner. Make sure to check for an email sent to validate the SSL certificate. Otherwise, your deployment will just sit there for an hour until it times out and rolls back.
1. Get the custom domain CNAME address from the API Gateway console.
1. Issue a curl request to test:
    ```
    curl https://<custom domain CNAME address>/users/5 -H 'Host: <custom domain>' -v
    ```
1. You should receive an empty 200 response (none of the functions are implemented to do anything else)