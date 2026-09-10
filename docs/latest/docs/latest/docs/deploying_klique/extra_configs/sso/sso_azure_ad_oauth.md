# Azure AD OAuth

> How to register Klique as an Azure AD OAuth application and configure the Klique Server environment variables for SSO.

This guide explains how to configure Azure Active Directory (Azure AD) as an OAuth provider for Klique Single Sign-On (SSO).

Configuration requires two steps:

1. Register and Configure the Klique application in Azure AD  
1. Identity service configuration in the Klique Server

## Configure Azure AD
1. Register the Klique app with the redirect URI: `<klique_webapp_address>/callback_azure_ad`
1. Select **Access tokens** to be returned from the authorization endpoint, and make sure that following claims are 
   returned in the access tokens: `email`, `preferred_username`, `upn`
1. Add client secret for the app and save the following app info:
   * Client ID
   * Client Secret 
   * OAuth 2.0 authorization endpoint (v2)
   * OAuth 2.0 token endpoint (v2)

## Configure Klique Server

1.  Define the following environment variables in your secret manager or runtime environment:  
    * `AD_AUTH_CLIENT_ID`  
    * `AD_AUTH_CLIENT_SECRET`

1. Set the following environment variables for the `apiserver`:
   * `CLEARML__services__login__sso__oauth_client__azure_ad__authorize_url=<authorization endpoint>`
   * `CLEARML__services__login__sso__oauth_client__azure_ad__access_token_url=<token endpoint>`
   * `CLEARML__secure__login__sso__oauth_client__azure_ad__client_id="${AD_AUTH_CLIENT_ID}"`
   * `CLEARML__secure__login__sso__oauth_client__azure_ad__client_secret="${AD_AUTH_CLIENT_SECRET}"`

To allow the identity provider to automatically create new users in Klique without requiring them to be whitelisted in 
advance, set the following environment variable:  

```
CLEARML__secure__login__sso__oauth_client__ad_oauth__default_company=${DEFAULT_COMPANY:?err}
```
