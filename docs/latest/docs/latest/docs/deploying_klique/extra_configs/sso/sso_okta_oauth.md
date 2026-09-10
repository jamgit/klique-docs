# Okta OAuth

> How to configure Okta as an OAuth identity provider for Klique SSO, including Klique Server environment variable setup.

This guide explains how to configure **Okta** as an OAuth provider for Klique Single Sign-On (SSO). 

Configuration requires two steps:

1. Register and Configure the Klique application in Okta  
2. Identity service configuration in the Klique Server

## Configure Okta

1. Register the Klique Okta app with the callback URL: `<klique_webapp_address>/callback_okta`

1. Make sure that the following claims are configured to be returned: "sub", "email", "name", "email_verified"

1. Make sure that the relevant users are allowed under the Klique Okta app configuration

1. Send the "client_id", "client_secret", "authorize_url" and "access_token_url". These can be obtained by accessing 
  `https://${OktaDomain}/oauth2/${authorizationServerId}/.well-known/openid-configuration` and retrieving "authorization_endpoint" and "token_endpoint"

## Configure Klique Server 

1.  Define the following environment variables in your secret manager or runtime environment:  
    * `OKTA_AUTH_CLIENT_ID`  
    * `OKTA_AUTH_CLIENT_SECRET`  

1. Define the following environment variables:

   * `CLEARML__services__login__sso__oauth_client__okta__base_url=https://${yourOktaDomain}/oauth2/${authorizationServerId}/`
   * `CLEARML__services__login__sso__oauth_client__okta__authorize_url=<authorization endpoint>`
   * `CLEARML__services__login__sso__oauth_client__okta__access_token_url=<token endpoint>`
   * `CLEARML__secure__login__sso__oauth_client__okta__client_id="${OKTA_AUTH_CLIENT_ID}"`
   * `CLEARML__secure__login__sso__oauth_client__okta__client_secret="${OKTA_AUTH_CLIENT_SECRET}"`
