# JumpCloud SAML

> How to configure JumpCloud as a SAML identity provider for Klique SSO, including IdP metadata and Klique Server environment variables.

This guide explains how to configure JumpCloud as a SAML identity provider for Klique Single Sign-On (SSO):

1. Register and Configure the Klique application in JumpCloud  
2. Identity service configuration in the Klique Server  
3. Optionally, configure automatic user creation by assigning a default company.

## Configure JumpCloud 
1. Go to the JumpCloud Admin console and define Klique app as a **Generic SAML Service Provider** with the following parameters:
   * Entity ID - `clearml`
   * Callback URL - `<the_url_of_klique_webapp_in_your_deployment>/callback_jumpcloudsaml`
1. Configure the SAML response attributes:
   * `emailaddress` - User email
   * `objectidentifier` - User ID in the IdP 
   * `displayname` - Username for the display
1. Generate the IdP metadata file and save the file and entity ID, which you will use when configuring Klique Server

## Configure Klique Server

1. Prepare the deployment with the user IdP metadata file mapped into the `apiserver`.

1. Set the following environment variables:

   * `CLEARML__SECURE__LOGIN__SSO__SAML_CLIENT__JUMPCLOUDSAML__ENTITY_ID=clearml`
   * `CLEARML__SECURE__LOGIN__SSO__SAML_CLIENT__JUMPCLOUDSAML__IDP_METADATA_FILE=<path to the metadata file>`
   * `CLEARML__SECURE__LOGIN__SSO__SAML_CLIENT__JUMPCLOUDSAML__DEFAULT_COMPANY=<company_id>`
