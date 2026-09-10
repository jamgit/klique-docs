# Deployment with Self-Signed Certificates

> Configuring the AI Application Gateway and Klique Orchestrator to use self-signed or custom CA certificates.

This guide covers how to configure the [AI Application Gateway](../../whats_klique/appgw.md) and [Klique Orchestrator](../../orchestrator/deployment_k8s.md#installing-the-orchestrator) 
to use self-signed or custom CA certificates. 

## AI Application Gateway and Klique Orchestrator

To configure certificates, update the applicable overrides file:
* For AI Application Gateway: `gateway-values.override.yaml` file
* For Klique Orchestrator: `orchestrator-values.override.yaml` file

```yaml
# -- Custom certificates
customCertificates:
  # -- Override system crt certificate bundle. Mutual exclusive with extraCerts.
  overrideCaCertificatesCrt:
  # -- Extra certs usable in case of needs of adding more certificates to the standard bundle, Requires root permissions to run update-ca-certificates. Mutual exclusive with overrideCaCertificatesCrt.
  extraCerts:
     - alias: certificateName
       pem: |
         -----BEGIN CERTIFICATE-----
         ###
         -----END CERTIFICATE-----
```

You have two configuration options:

- [**Replace**](#replace-entire-ca-certificatescrt-file) the entire `ca-certificates.crt` file
- [**Append**](#append-extra-certificates-to-the-existing-ca-certificatescrt) extra certificates to the existing `ca-certificates.crt`

### Replace Entire ca-certificates.crt File

To replace the whole ca-bundle, provide a concatenated list of all trusted CA certificates in `pem` format as 
they are stored in a standard `ca-certificates.crt`.

```yaml
# -- Custom certificates
customCertificates:
  # -- Override system crt certificate bundle. Mutual exclusive with extraCerts.
  overrideCaCertificatesCrt: |
    -----BEGIN CERTIFICATE-----
    ### CERT 1
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
    ### CERT 2
    -----END CERTIFICATE-----
    -----BEGIN CERTIFICATE-----
    ### CERT 3
    -----END CERTIFICATE-----
   ...
```

### Append Extra Certificates to the Existing ca-certificates.crt

You can add certificates to the existing CA bundle. Each certificate must have a unique `alias`.

```yaml
# -- Custom certificates
customCertificates:
  # -- Extra certs usable in case of needs of adding more certificates to the standard bundle, Requires root permissions to run update-ca-certificates. Mutual exclusive with overrideCaCertificatesCrt.
  extraCerts:
     - alias: certificate-name-1
       pem: |
         -----BEGIN CERTIFICATE-----
         ###
         -----END CERTIFICATE-----
     - alias: certificate-name-2
       pem: |
         -----BEGIN CERTIFICATE-----
         ###
         -----END CERTIFICATE-----
```

### Klique Orchestrator: Add Certificates to Task Pods

If your workloads need access to these certificates (e.g., for HTTPS requests), configure the orchestrator to inject them into pods:

```yaml
customCertificates:
  propagateToTasks: true
```

### Apply Changes

To apply the changes, run the update command:
* For AI Application Gateway:

   ```bash
   helm upgrade -i <RELEASE_NAME> -n <WORKLOAD_NAMESPACE> oci://docker.io/kliqueai/gateway --version <CHART_VERSION> -f gateway-values.override.yaml
   ```

* For Klique Orchestrator: 

   ```bash
   helm upgrade -i -n <WORKER_NAMESPACE> orchestrator oci://docker.io/kliqueai/orchestrator -f orchestrator-values.override.yaml
   ```
  
## Klique Applications (Apps Agent)

Klique Application instances are executed by a dedicated orchestrator instance (the Apps Agent), which creates pods for each application 
instance.

To make custom certificates available to these pods, update the control-plane `control-plane-values.override.yaml` file under 
the `applications` section. 

The `basePodTemplate` defines the base pod specification applied to all application pods created by the Apps Agent.

Example configuration:

```yaml
applications:
  basePodTemplate:
    initContainers:
      - command:
        - /bin/sh
        - -c
        - update-ca-certificates
        image: clearml/clearml-enterprise-agent-k8s-base:<AGENT-VERSION-AVAILABLE-ON-REPO>
        imagePullPolicy: IfNotPresent
        name: init-task
        volumeMounts:
          - name: etc-ssl-certs
            mountPath: "/etc/ssl/certs"
          - name: klique-extra-ca-certs
            mountPath: "/usr/local/share/ca-certificates"
    env:
      - name: REQUESTS_CA_BUNDLE
        value: "/etc/ssl/certs/ca-certificates.crt"
      - name: SSL_CERT_FILE
        value: "/etc/ssl/certs/ca-certificates.crt"
    volumeMounts:
      - name: etc-ssl-certs
        mountPath: "/etc/ssl/certs"
    volumes:
      - name: etc-ssl-certs
        emptyDir: {}
      - name: klique-extra-ca-certs
        projected:
          defaultMode: 420
          sources:
          # LIST HERE CONFIGMAPS CREATED BY THE APPS AGENT IN THE CONTROL PLANE CHART, THE CARDINALITY DEPENDS ON THE NUMBER OF CERTS PROVIDED.
          - configMap:
              name: control-plane-apps-custom-ca-0
          - configMap:
              name: control-plane-apps-custom-ca-1
  customCertificates:
    # -- Override system crt certificate bundle. Mutual exclusive with extraCerts.
    overrideCaCertificatesCrt:
    # -- Extra certs usable in case of needs of adding more certificates to the standard bundle, Requires root permissions to run update-ca-certificates. Mutual exclusive with overrideCaCertificatesCrt.
    extraCerts:
      []
      # - alias: certificateName
      #   pem: |
      #     -----BEGIN CERTIFICATE-----
      #     ###
      #     -----END CERTIFICATE-----
```
