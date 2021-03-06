#  Upstream CA "vault" Plugin
The vault plugin requests to sign intermediate certificate to Vault PKI Engine as the Upstream CA. The Spire Server generates SVID as intermediate CA.

## Configuration

The plugin accepts the following configuration options:

| key | type | required | description | default |
|:----|:-----|:---------|:------------|:--------|
| vault_addr  | string |   | A URL of Vault server. (e.g., https://vault.example.com:8443/) | `${VAULT_ADDR}` |
| pki_mount_point  | string |  | Name of mount point where PKI secret engine is mounted | pki |
| ca_cert_path     | string |  | Path to a CA certificate file that the client verifies the server certificate. PEM and DER is supported. | `${VAULT_CACERT}` |
| ttl              | string |  | Request to issue a certificate with the specified TTL (Go-Style time duration value e.g., 1h)  | |
| tls_skip_verify  | string |  | If true, vault client accepts any server certificates | false |
| cert_auth_config | struct |  | Configuration parameters to use TLS cert auth method | |
| token_auth_config | struct | | Configuration parameters to use Token auth method | |
| approle_auth_config | struct | | Configuration parameters to use AppRole auth method | |

The Plugin now supports **TLS certificate**, **Token** and **AppRole** authentication method.
**TLS certificate** method authenticates to Vault using the TLS client certificate, **Token** method authenticates to Vault using the token in the HTTP Request header. **AppRole** method authenticates to Vault using RoleID and SecretID that are issued from Vault.

**cert_auth_config**

| key | type | required | description | default |
|:----|:-----|:---------|:------------|:--------|
| tls_auth_mount_point | string |  | Name of mount point where TLS auth method is mounted | cert |
| client_cert_path | string | | Path to a client certificate file. PEM and DER is supported. | `${VAULT_CLIENT_CERT}` |
| client_key_path  | string | | Path to a client private key file PEM and DER is supported. | `${VAULT_CLIENT_KEY}` |

```hcl
    UpstreamCA "vault" {
        plugin_cmd = "vault-upstream-ca binary"
        plugin_checksum = "(SHOULD) sha256 of the plugin binary"
        plugin_data {
            vault_addr = "https://vault.example.org/"
            pki_mount_point = "test-pki"
            ca_cert_path = "/path/to/ca-cert.pem"
            cert_auth_config {
                tls_auth_mount_point = "test-tls-auth"
                client_cert_path = "/path/to/client-cert.pem"
                client_key_path  = "/path/to/client-key.pem"
            }
        }
    }
```
**token_auth_config**

| key | type | required | description | default |
|:----|:-----|:---------|:------------|:--------|
| token | string | | Token string to set into "X-Vault-Token" header | `${VAULT_TOKEN}` |


```hcl
    UpstreamCA "vault" {
        plugin_cmd = "vault-upstream-ca binary"
        plugin_checksum = "(SHOULD) sha256 of the plugin binary"
        plugin_data {
            vault_addr = "https://vault.example.org/"
            pki_mount_point = "test-pki"
            ca_cert_path = "/path/to/ca-cert.pem"
            token_auth_config {
               token = "<token>" // or specified by environment variables
            }
        }
    }
```
**approle_auth_config**

| key | type | required | description | default |
|:----|:-----|:---------|:------------|:--------|
| approle_auth_mount_point | string | | Name of mount point where AppRole auth method is mounted | approle |
| approle_id |string | | An identifier of AppRole | `${VAULT_APPROLE_ID}` |
| approle_secret_id | string | | A credential of AppRole | `${VAULT_APPROLE_SECRET_ID}` |

```hcl
    UpstreamCA "vault" {
        plugin_cmd = "vault-upstream-ca binary"
        plugin_checksum = "(SHOULD) sha256 of the plugin binary"
        plugin_data {
            vault_addr = "https://vault.example.org/"
            pki_mount_point = "test-pki"
            ca_cert_path = "/path/to/ca-cert.pem"
            approle_auth_config {
               approle_auth_mount_point = "my-approle-auth"
               approle_id = "<Role ID>" // or specified by environment variables
               approle_secret_id = "<Secret ID>" // or specified by environment variables
            }
        }
    }
```