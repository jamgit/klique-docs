# Duo SAML

> How to configure Duo as a SAML identity provider for Klique SSO, including IdP metadata and Klique Server environment variables.

This guide explains how to configure Duo as a SAML identity provider for Klique Single Sign-On (SSO):

1. Register and Configure the Klique application in Duo  
2. Identity service configuration in the Klique Server  
3. Optionally, configure automatic user creation by assigning a default company.

## Configure Duo 
1. Go to the Duo Admin console and define Klique app as a **Generic SAML Service Provider** with the following parameters:
   * Entity ID - `clearml`
   * ACS URL - `<the_url_of_klique_webapp_in_your_deployment>/callback_duo`
1. Configure the SAML response attributes:
   * `emailaddress` - User email
   * `objectidentifier` - User ID in the IdP 
   * `displayname` - Username for the display
1. Generate the IdP metadata file and save the file and entity ID, which you will use when configuring Klique Server

## Configure Klique Server

1. Prepare the deployment with the user IdP metadata file mapped into the `apiserver`.

1. Set the following environment variables:

   * `CLEARML__secure__login__sso__saml_client__duo__entity_id=clearml`
   * `CLEARML__secure__login__sso__saml_client__duo__idp_metadata_file=<path to the metadata file>`
   * `CLEARML__secure__login__sso__saml_client__duo__default_company=<company_id>`
