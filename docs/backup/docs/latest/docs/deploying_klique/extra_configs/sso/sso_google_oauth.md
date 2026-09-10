# Google OAuth

> How to register Klique as a Google OAuth client and configure the Klique Server environment variables for SSO.

This guide explains how to configure Google as an OAuth provider for Klique Single Sign-On (SSO).

Configuration requires two steps:

1. Register and Configure the Klique application in Google  
1. Identity service configuration in the Klique Server

## Configure Google as IdP

1. In the [Google Cloud Console](https://console.cloud.google.com/), go to the **APIs & Services** **>** **Credentials section** (`https://console.cloud.google.com/apis/credentials?project=\<project-id\>`)  

1. Click **Create credentials** > **OAuth Client ID**

   * Application type: Web application
   * Authorized redirect URIs: Add `<klique_webapp_address>/callback_google`
   * Note the Client ID and Client Secret, they will be used when configuring the Klique Server.

## Configure Klique Server

1. Define the following environment variables in your secret manager or runtime environment:  

   * `GOOGLE_AUTH_CLIENT_ID`
   * `GOOGLE_AUTH_CLIENT_SECRET`

1. Set the following environment variables for the `apiserver`:

   * `KLIQUE__secure__login__sso__oauth_client__google__client_id="${GOOGLE_AUTH_CLIENT_ID}"`
   * `KLIQUE__secure__login__sso__oauth_client__google__client_secret="${GOOGLE_AUTH_CLIENT_SECRET}"`

1. To allow the identity provider to automatically create new users in Klique without requiring them to be whitelisted 
   in advance, set the following environment variable:  

   ```
   KLIQUE__secure__login__sso__oauth_client__google__default_company="<company_id>"
   ```
