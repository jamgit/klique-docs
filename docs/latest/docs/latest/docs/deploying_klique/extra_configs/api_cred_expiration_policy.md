# API Credentials Expiration Policy

> How administrators restrict the maximum API credential expiration period using an apiserver environment variable.

Administrators can restrict the maximum expiration period users can set for API credentials by setting the following 
environment variable:

```
CLEARML__apiserver__auth__credentials__default_expiration_sec=7776000
```

If set to `0`, no restriction is enforced and users may choose any expiration period, including no expiration.

Admin users can create credentials without expiration, regardless of the limit. 

Non-admin users cannot set an expiration longer than the configured limit.
