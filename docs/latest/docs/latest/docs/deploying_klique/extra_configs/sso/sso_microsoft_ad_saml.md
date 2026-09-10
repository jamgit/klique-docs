# Microsoft AD SAML (Including Azure SAML)

> How to configure Microsoft Active Directory, including Azure SAML, as a SAML identity provider for Klique SSO, with group sync.

This guide explains the configuration required for connecting a Klique Server to allow authenticating users with Microsoft Active Directory (AD) using SAML.

Configuration requires two steps:

1. Register and Configure the Klique application in AD  
2. Identity service configuration in the Klique Server

## Configure AD

1. Register the Klique app with the callback URL: `<clearml_webapp_address>/callback_microsoft_ad`
1. Make sure that SSO binding is set to HTTP-Redirect
1. Make sure that the following user claims are returned to Klique app:

   ```
   emailaddress   - user.mail
   displayname    - user.displayname
   Unique user identifier - user.principalname
   ```
1. Generate the IdP metadata file and save the file and entity ID, which you will use when configuring Klique Server

## Configure Klique Server 

### docker-compose
1. Prepare the deployment with the user IdP metadata file mapped into the `apiserver`
1. Define the following environment variables:

   * `CLEARML__secure__login__sso__saml_client__microsoft_ad__entity_id=<app_entity_id>`
   * `CLEARML__secure__login__sso__saml_client__microsoft_ad__idp_metadata_file=<path to the metadata file>`
   * `CLEARML__secure__login__sso__saml_client__microsoft_ad__default_company="<company_id>"`
   * `CLEARML__services__login__sso__saml_client__microsoft_ad__claims__object_id="http://schemas.microsoft.com/identity/claims/objectidentifier"`
   * `CLEARML__services__login__sso__saml_client__microsoft_ad__claims__name="http://schemas.microsoft.com/identity/claims/displayname"`
   * `CLEARML__services__login__sso__saml_client__microsoft_ad__claims__email="emailAddress"`
   * `CLEARML__services__login__sso__saml_client__microsoft_ad__claims__given_name="givenName"`
   * `CLEARML__services__login__sso__saml_client__microsoft_ad__claims__surname="surname"`

### Kubernetes

The following should be configured in the override file:

```yaml
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
    - name: "CLEARML__secure__login__sso__saml_client__microsoft_ad__entity_id"
      value: "<app_entity_id>"
    - name: "CLEARML__secure__login__sso__saml_client__microsoft_ad__idp_metadata_file"
      value: "/opt/clearml/config/default/metadata.xml"
    - name: "CLEARML__secure__login__sso__saml_client__microsoft_ad__default_company"
      value: "<company_id>"
    - name: "CLEARML__services__login__sso__saml_client__microsoft_ad__claims__object_id"
      value: "http://schemas.microsoft.com/identity/claims/objectidentifier"
    - name: "CLEARML__services__login__sso__saml_client__microsoft_ad__claims__name"
      value: "http://schemas.microsoft.com/identity/claims/displayname"
    - name: "CLEARML__services__login__sso__saml_client__microsoft_ad__claims__given_name"
      value: "givenName"
    - name: "CLEARML__services__login__sso__saml_client__microsoft_ad__claims__surname"
      value: "surname"
    - name: "CLEARML__services__login__sso__saml_client__microsoft_ad__claims__email"
      value: "emailAddress"
```

### Customization
The Klique Server can sync user admin roles and group assignments from Microsoft AD. For this, set the following environment 
variables:

* `CLEARML__services__login__sso__saml_client__microsoft_ad__groups__enabled=true`

* `CLEARML__services__login__sso__saml_client__microsoft_ad__groups__admins=[<admin_group_name1>, <admin_group_name2>, ...]`

Group claim defaults to "groups" and can be overridden by setting `CLEARML__services__login__sso__saml_client__microsoft_ad__groups__claimenv var`

Please note that groups themselves are not auto created and need to be created manually in Klique. The group name is taken
from the first CN of the full DN path. For example the following dn `'CN=test, OU=unit, DU=mycomp'` corresponds to the 
group name `'test'`. The group name comparison between Microsoft AD and Klique is case-sensitive. To make it case-insensitive define the following:

```
CLEARML__services__login__sso__saml_client__microsoft_ad__groups__case_sensitive=false
```

To restrict login only to users whose AD groups exist in Klique, set the following environment variable:

```
CLEARML__services__login__sso__saml_client__microsoft_ad__groups__prohibit_user_signup_if_not_in_group=true
```
