# Google SAML

> How to configure Google as a SAML identity provider for Klique SSO, including docker-compose and Kubernetes setup.

This guide explains how to configure Google as a SAML identity provider (IdP) for Klique Single Sign-On (SSO):

1. Register and Configure the Klique application in Google  
2. Identity service configuration in the Klique Server

##  Configure Google as SAML Identity Provider

1. Register Klique app with the callback URL: `<klique_webapp_address>/callback_google_saml`
1. Set SSO binding to HTTP-Redirect
1. Make sure that the following user claims are returned to the Klique app: 
   * `objectidentifier` - User ID
   * `displayname` - Full username
   * `emailaddress` - User's email address

1. Generate the IdP metadata file and save the file and entity ID, which you will use when configuring Klique Server

## Configure Klique Server

1. Make sure the IdP metadata file (from Step 4 above) is placed in a folder that’s volume-mounted into the Klique 
`apiserver` container so it can be accessed during deployment.  

1. Set the following environment variables:

   * `CLEARML__secure__login__sso__saml_client__google_saml__entity_id=<app_entity_id>`
   * `CLEARML__secure__login__sso__saml_client__google_saml__idp_metadata_file=<path to the metadata file>` 
   * `CLEARML__secure__login__sso__saml_client__google_saml__default_company="<company_id>"`

### Configure Klique Server - Kubernetes Deployment
In the override file, configure the metadata and environment variables:

```
apiserver:
  additionalConfigs:
    metadata.xml: |
      <?xml version="1.0"?>
      <test>
        <rule id="tst">
            <test_name>test</test_name>
        </rule>
      </test>
  extraEnvs:
    - name: "CLEARML__secure__login__sso__saml_client__google_saml__entity_id"
      value: "<app_entity_id>"
    - name: "CLEARML__secure__login__sso__saml_client__google_saml__idp_metadata_file"
      value: "/opt/clearml/config/default/metadata.xml"
    - name: "CLEARML__secure__login__sso__saml_client__google_saml__default_company"
      value: "<company_id>"
```
