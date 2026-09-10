# Ping Identity OAuth

> How to configure Ping Identity as an OAuth identity provider for Klique SSO, including group and admin role sync.

This guide explains how to configure **Ping Identity** as an OAuth provider for Klique Single Sign-On (SSO):

1. Register and Configure the Klique application in Ping Identity  
2. Identity service configuration in the Klique Server  
3. Optionally, map group membership and admin roles from Ping into Klique.

## Configure Ping Identity 

1. Register a new Klique app with the callback URL: `<klique_webapp_address>/callback_ping`

1. Select the following scopes:
   * `openid`
   * `profile` 
   * `email`
1. Make sure that the claims representing `user_id`, `email`, and `username` are returned in the token

1. Note the Client ID, Client secret, Auth URL, and Access token URL. They will be used in the Klique Server configuration

## Configure Klique Server

1.  Define the following environment variables in your secret manager or runtime environment:  
    * `PING_AUTH_CLIENT_ID`  
    * `PING_AUTH_CLIENT_SECRET ` 

1. Set the following environment variables for the `apiserver`:

   * `KLIQUE__services__login__sso__oauth_client__ping__authorize_url=<authorization endpoint>`
   * `KLIQUE__services__login__sso__oauth_client__ping__access_token_url=<token endpoint>`
   * `KLIQUE__secure__login__sso__oauth_client__ping__client_id="${PING_AUTH_CLIENT_ID}"`
   * `KLIQUE__secure__login__sso__oauth_client__ping__client_secret="${PING_AUTH_CLIENT_SECRET}"`

1. If you enabled the PKCE for the Klique app in Ping Identity, add the following environment variables:

   * `KLIQUE__services__login__sso__oauth_client__ping__code_challenge=true`
   * `KLIQUE__services__login__sso__oauth_client__ping__code_challenge_method=S256`

1. To allow the identity provider to automatically create new users in Klique without requiring them to be whitelisted 
   in advance, set the following environment variable: 

   ```
   KLIQUE__secure__login__sso__oauth_client__ping__default_company="<company_id>"
   ```
   
1. To troubleshoot the integration between Ping and Klique, you can enable SSO debug information in Klique server logs: 

   ```
   KLIQUE__services__login__sso__oauth_client__ping__debug=true`
    ```

### User Group Integration

The Klique Server can sync user admin roles and group assignments from Ping.

#### In Ping Identity

Make sure that group memberships are included in the SSO token.

#### In Klique Server

Set the following environment variables:
* `KLIQUE__services__login__sso__oauth_client__ping__groups__enabled=true`
* `KLIQUE__services__login__sso__oauth_client__ping__groups__claim=<the name of the groups claim as returned from Ping>`

:::note 
Klique groups are not created automatically and must be manually created. The group name comparison between Ping and 
Klique is case-sensitive by default. To make it case-insensitive, set the following:

```
KLIQUE__services__login__sso__oauth_client__ping__groups__case_sensitive=false
```
:::

##### Setting Klique Administrators 
If you want the members of a particular Ping group to be admins in Klique, add the following environment variable (the Ping group
does not have to exist Klique):
```
KLIQUE__services__login__sso__oauth_client__ping__groups__admins=["<the name of admin group from Ping>"]
```

##### Restrict User Signup

To restrict log in only to users whose Ping groups exist in Klique, set the following environment variable:

```
KLIQUE__services__login__sso__oauth_client__ping__groups__prohibit_user_signup_if_not_in_group=true
```
