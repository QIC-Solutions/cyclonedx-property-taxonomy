# QiC Solutions CycloneDX Property Taxonomy

Version 1.1.

This document describes the `qic` namespace for [CycloneDX properties](https://cyclonedx.org/use-cases/cyclonedx-properties/), used by **|QS>EDR**, the quantum-risk assessment and post-quantum migration platform of [QiC Solutions](https://www.qic-solutions.com).

|QS>EDR produces Cryptography Bills of Materials (CBOM, CycloneDX 1.7) from source code, container images, TLS and SSH endpoints, certificates and keystores, HSMs, cloud key management services, network captures and third-party scanner reports. The properties below carry what the CycloneDX schema has no field for. Where the schema has a field, |QS>EDR fills that field instead.

The namespace is administered by [QIC Solutions](https://github.com/QIC-Solutions).

## Conventions

- Every value is a string, as required by CycloneDX properties. Numbers, dates and lists are serialised as described in each table.
- Enumerated values are lower-case English tokens. Free-text values (warnings, reasons) are in the user's language.
- Dates are ISO 8601. Lists are joined with `, `.
- *metadata* means `metadata.properties`, *metadata.component* means `metadata.component.properties`, and component means `components[].properties`.

## Provenance

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:engine` | component | QiC engine that found the asset. | One of `qodescan`, `cbomkit`, `patterns`, `qic` (QiC rules), `regex` (rules written by the organisation), `llm`, `inventory` (imported inventory), `third-party` (see qic:provider). |
| `qic:provider` | component | External source the asset comes from. | One of `tenable`, `qualys`, `venafi`, `keyfactor`, `digicert`, `crt.sh`, `certspotter`. |
| `qic:vector` | component | Discovery vector of the scan that contributed the component (consolidated inventories). | One of `REPOSITORY`, `IMAGE`, `TLS`, `CERTIFICATE`, `SBOM`, `FILESYSTEM`, `CLOUD`, `CONFIG`, `NETWORK`, `SSH`, `TRUSTSTORE`, `PCAP`, `CT`, `THIRDPARTY`, `HOST`, `SAP`, `MAINFRAME`, `ZERT`. |
| `qic:primitive` | component | The primitive as detected, when it has no exact equivalent in the CycloneDX enumeration (which then holds the closest value or `other`). | Free text, for example `keyagree`, `kex`. |
| `qic:derived-from` | component | How a synthesised component was obtained. | One of `same-line-merge` (a parameter merged with the algorithm on the same line), `generating-call` (key material deduced from the call that generates it). |
| `qic:cbom-entry-id` | component | Identifier of the asset in the \|QS>EDR inventory (consolidated inventories). | Opaque identifier. |

## Location and context

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:system` | component, *metadata.component* | Logical system where the asset lives. | Free text: source label, application, or cloud service and region. |
| `qic:application` | component | Application the asset belongs to, as mapped by the user. | Free text. |
| `qic:criticality` | component, *metadata.component* | Business criticality of the system. | One of `LOW`, `MEDIUM`, `HIGH`, `CROWN`. |
| `qic:repository-url` | component, *metadata.component* | Source of the analysed code. Never carries credentials. | `<url>[@<branch>][ (<subfolder>)]`, `smb://<server>/<share>/<path>`, `probe-inbox:<name>` (file on the scanning probe) or `upload:<name>` (uploaded file). |
| `qic:endpoint` | component, *metadata.component* | Network endpoint where the asset was observed. | `host:port` or `host`. |
| `qic:endpoints` | component | All endpoints where it was observed, when more than one. | List of `host:port`. |
| `qic:endpoint-count` | component | Number of endpoints where it was observed. | Integer. |
| `qic:scope` | component | Scope of the occurrences. | `test` (every occurrence is in test code). |
| `qic:test-occurrences` | component | Number of occurrences in test code, when production and test occurrences are mixed. | Integer. |
| `qic:evidence` | component | What backs the component. | `dependency-manifest` (the only evidence is a dependency manifest: the component is a library, not a use of the algorithm). |
| `qic:key-origin` | component | Where key material comes from. | `generated-in-code`. |
| `qic:reason` | component | Why a configuration line is a finding. | Free text. |
| `qic:warning` | component, *metadata* | Something the scan could not cover, or a correction applied to a component. One property per warning. | Free text. |

## Certificates

Validity dates and issuer go in the native `certificateProperties` fields (`notValidBefore`, `notValidAfter`, `issuerName`).

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:pem` | component | The certificate, or the full chain, for import into a certificate lifecycle manager. | Base64 of the PEM text. |
| `qic:keystore` | component | Type of the keystore the certificate came from. | One of `JKS`, `P12`. |
| `qic:signature-algorithm` | component | Signature algorithm, as reported by a third-party source, when there is no component to reference. | Free text. |

## Network, TLS and SSH

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:role` | component | Role of the algorithm in the protocol where it was observed. | One of `tls-version`, `tls-cipher-suite`, `tls-group`, `tls-key-exchange`, `tls-authentication`, `ssh-host-key`, `ssh-key-exchange`, `ssh-cipher`, `ssh-mac`, `certificate-signature`. |
| `qic:ssh-banner` | component | SSH server banner. | Text, for example `SSH-2.0-OpenSSH_9.6`. |
| `qic:flag` | component | Posture flag. | One of `obsolete`, `weak`, `no-pfs` (no forward secrecy), `pqc-hybrid`, `null-cipher`, `no-authentication`. |

## HSM and PKCS#11

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:hsm-object` | component | PKCS#11 object classes that share the key. | Classes joined with `+`, for example `Private Key+Public Key`. |
| `qic:pkcs11-slot` | component | PKCS#11 slot. | Integer. |
| `qic:pkcs11-id` | component | PKCS#11 object identifier. | Hexadecimal. |

## Cloud key management

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:cloud-provider` | component | Cloud service. | One of `aws-kms`, `aws-acm`, `azure-keyvault`, `gcp-kms`. |
| `qic:cloud-id` | component | Identifier of the resource in the provider. | AWS key ID or ARN, Azure Key Vault key URL, or GCP `projects/…/cryptoKeys/…`. |

## Crypto-agility

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:algorithm-source` | component | Where the algorithm name comes from at the component's occurrences, as traced by the code analyser. A list of the distinct sources, joined with `, `. | One of `literal` (written in the call itself), `constant` (a named constant), `parameter` (a parameter of the enclosing function), `configuration` (read from configuration or the environment), `return-value` (the return value of another function), `unknown` (the analyser could not trace it). |
| `qic:key-size-source` | component | Same as `qic:algorithm-source`, for the key size. | One of `literal` (written in the call itself), `constant` (a named constant), `parameter` (a parameter of the enclosing function), `configuration` (read from configuration or the environment), `return-value` (the return value of another function), `unknown` (the analyser could not trace it). |
| `qic:hardcoded-at` | component | Occurrences where the algorithm or the key size is a literal or a constant: what has to change to switch algorithm. | List of `path:line`. |
| `qic:configurable-calls` | *metadata*, *metadata.component* | Cryptographic calls whose algorithm comes from configuration, so the analyser cannot name it. Zero with many hard-coded sites means there is no fallback. | Integer. |

## Server inspection

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:host-setting` | component | Which part of an inspected server's configuration the component comes from (vector HOST: read inside the server with a read-only account). | One of `ssh-server` (the effective sshd configuration (`sshd -T`) or `sshd_config`), `crypto-library` (what the system cryptographic library supports (`openssl list`)), `crypto-package` (an installed cryptographic package and the algorithms it brings), `system-crypto-policy` (the system-wide crypto policy (`update-crypto-policies`)), `tls-configuration` (a TLS directive in a web server or proxy configuration file), `java-security` (the JDK security properties), `certificate-store` (a system certificate store), `schannel` (Windows SChannel protocols, cipher suites and groups), `dotnet-tls` (the .NET Framework strong-crypto settings), `kerberos` (the Kerberos encryption types allowed by policy), `smb` (the SMB server encryption and signing settings), `rdp` (the Remote Desktop security layer), `disk-encryption` (the disk encryption method (BitLocker)), `key-storage-provider` (a registered key storage provider (Windows CNG KSP, or a non-default CryptoAPI CSP)), `iis` (an IIS HTTPS binding, with the certificate HTTP.sys serves on it), `efs` (the Encrypting File System algorithm and recovery agent certificates), `certificate-authority` (the signing key, hash and key archival cipher of an AD CS certification authority), `certificate-template` (an AD CS certificate template published by the inspected CA). |
| `qic:key-provider` | component | Provider that holds the private key: the Windows CNG key storage provider or CryptoAPI CSP of a certificate's key, of a certification authority, or the default one of a certificate template. | Free text, for example `Microsoft Software Key Storage Provider`. |
| `qic:key-storage` | component | Where the private key lives, as deduced from its provider. | One of `software` (a file protected by the operating system), `tpm` (the machine's Trusted Platform Module), `smart-card` (a smart card or token), `hsm` (a hardware security module), `windows-hello` (Windows Hello for Business (Passport) keys), `unknown` (a provider QiC does not recognise). |
| `qic:key-exportable` | component | Whether the private key can be exported (for a certificate template: whether the keys it issues can be). | One of `true`, `false`. |
| `qic:binding` | component | Web server binding where the asset is served (IIS site and binding). On a certificate, every binding that uses it, joined with `, `. | `<site> https/<IP>:<port>:<host name>`. |
| `qic:certificate-template` | component | AD CS certificate template the component describes. | The template's common name, for example `WebServer`. |
| `qic:inspection-source` | component | Where on the inspected servers the value was read. | A command (`sshd -T`), a file path or a registry key; several joined with `, `. |

## SAP inspection

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:sap-setting` | component | Which part of an inspected SAP system the component comes from (vector SAP: read with display-only accounts through SAPControl, ADT and HANA SQL). | One of `instance-parameter` (an instance profile parameter (`ssl/ciphersuites`, `snc/*`…) read with SAPControl `ParameterValue`), `access-point` (a port the instance listens on, from SAPControl `GetAccessPointList` or `icm/server_port_*`), `snc` (Secure Network Communications: whether it is on and its protection level), `ssf` (the hash and encryption algorithms of an SSF application (`SSFARGS`)), `pse` (a certificate of a Personal Security Environment, as presented over TLS), `rfc-destination` (RFC and HTTP destinations (`RFCDES`), grouped by type and protection), `password-hash` (the ABAP password hash algorithm and its downward compatibility), `crypto-library` (the cryptographic library the instance loads (CommonCryptoLib)), `hana-encryption` (HANA data, log and backup encryption at rest (`M_ENCRYPTION_OVERVIEW`)), `hana-sql-tls` (HANA SQL TLS settings (`sslEnforce`, `sslProvider`, `sslMinProtocolVersion`)), `hana-password-hash` (HANA `password_hash_methods`), `hana-certificate` (a certificate stored in HANA (`CERTIFICATES`)). |
| `qic:sap-pse` | component | The PSE (or HANA store) a certificate belongs to. | Free text naming the PSE and where it was seen, for example `SAPSSLS del ICM (44300)`. |

## Mainframe inspection

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:zos-setting` | component | Which part of an inspected z/OS system the component comes from (vector MAINFRAME: read with a read-only RACF account through z/OSMF or SSH). | One of `icsf-key-dataset` (a CKDS, PKDS or TKDS (`D ICSF,KDS`)), `icsf-master-key` (an ICSF master key (`D ICSF,MKS`)), `icsf-coprocessor` (a Crypto Express coprocessor and its CCA level (`D ICSF,CARDS`)), `racf-certificate` (a certificate listed by `RACDCERT LIST`), `racf-keyring` (a key ring listed by `RACDCERT LISTRING`), `at-tls` (an AT-TLS policy setting (`pasearch -t` or the policy file)), `dataset-encryption` (DATASET profiles with or without a key label (`LISTDSD … DFP`)). |
| `qic:racf-keyring` | component | RACF key ring of the component, or the key rings a certificate is connected to. | `OWNER/RING`; several joined with `, `. |
| `qic:zert-protection` | component | Cryptographic protection of the connections that z/OS Encryption Readiness Technology (zERT, SMF 119 subtypes 11 and 12) recorded. | One of `tls` (TLS or SSL), `ssh`, `ipsec`, `none` (cleartext: no cryptographic protection detected). |
| `qic:connection-count` | component | Number of connections that use the component, as counted by zERT. | A non-negative integer. |

## Libraries (SBOM analysis)

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:library` | component | Cryptographic library. | Free text, for example `OpenSSL`. |
| `qic:packages` | component | Packages that bring the library in. | List of `package@version` (just `package` when the version is unknown). |
| `qic:detected-crypto-libs` | *metadata.component* | Packages recognised as cryptographic libraries. Omitted when there are none. | List. |

## Remediation (|QS>RESPOND exports)

Severity and the recommendation text go in the native `vulnerabilities[]` fields (`ratings`, `recommendation`).

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:recommended-algorithm` | component | Recommended target algorithm. | Free text. |
| `qic:recommended-library` | component | Recommended library. | Free text, may be empty. |
| `qic:sources` | component | Detection methods that found the finding. | List joined with `,`. |

## Document metadata

Engine versions go in the native `metadata.tools`, and the organisation in `metadata.supplier`.

| Property | Applies to | Description | Value |
| --- | --- | --- | --- |
| `qic:discarded-code-patterns` | *metadata* | Code patterns discarded as assets, with counts. | `Name: n, …` |
| `qic:consolidated-scans` | *metadata* | Number of scans in a consolidated inventory. | Integer. |
| `qic:inventory-assets` | *metadata* | Number of assets in the inventory. | Integer. |
| `qic:assets-without-evidence` | *metadata* | Assets emitted from the inventory without occurrences. | Integer. |

## Schema conformance

When a source document carries a field that the target CycloneDX version does not accept in that position, |QS>EDR moves it to a property so the document validates, and moves it back when reading. The value is the original value as a string.

| Namespace | Description |
| --- | --- |
| `qic:cryptoProperties:<field>` | A field of `cryptoProperties` that the target schema version does not accept in that position. The name after the namespace is the CycloneDX field name. |
| `qic:algorithmProperties:<field>` | Same, for `cryptoProperties.algorithmProperties`. |
| `qic:certificateProperties:<field>` | Same, for `cryptoProperties.certificateProperties`. |

## Sensitive values

CBOMs describe an organisation's infrastructure. Consumers should treat these properties as confidential:

- `qic:system`
- `qic:repository-url`
- `qic:endpoint`
- `qic:endpoints`
- `qic:pem`
- `qic:ssh-banner`
- `qic:cloud-id`
- `qic:hardcoded-at`
- `qic:binding`
- `qic:inspection-source`
- `qic:packages`
- `qic:detected-crypto-libs`

## Names before version 1.0

Documents produced before version 1.0 may carry these names. |QS>EDR reads them as the current ones.

| Before 1.0 | Version 1.0 |
| --- | --- |
| `qic:ambito` | `qic:scope` |
| `qic:apariciones-en-pruebas` | `qic:test-occurrences` |
| `qic:clase` | `qic:evidence` |
| `qic:aviso` | `qic:warning` |
| `qic:parametro` | `qic:derived-from` |
| `qic:patrones-de-codigo` | `qic:discarded-code-patterns` |
| `qic:recommendedAlgorithm` | `qic:recommended-algorithm` |
| `qic:recommendedLibrary` | `qic:recommended-library` |
| `qic:network-target` | `qic:endpoint` |
| `qic:tls-endpoint` | `qic:endpoint` |
| `qic:ssh-target` | `qic:endpoint` |
| `qic:ssh-endpoints` | `qic:endpoint-count` |
| `qic:thirdparty-endpoints` | `qic:endpoint-count` |
| `qic:thirdparty-seen-at` | `qic:endpoints` |
| `qic:ssh-role` | `qic:role` |
| `qic:pcap-role` | `qic:role` |
| `qic:thirdparty-role` | `qic:role` |
| `qic:ct-source` | `qic:provider` |
| `qic:from-package` | `qic:packages` |
| `qic:valid-to`, `qic:valid-from`, `qic:issuer` | `certificateProperties` fields |
| `qic:engine-version:<engine>` | `metadata.tools` |
| `qic:severity`, `qic:company` | `vulnerabilities[].ratings`, `metadata.supplier` |
| `qic:usage` | `algorithmProperties.cryptoFunctions` (and `qic:key-origin`) |
| `qic:<site>.<field>` | `qic:<site>:<field>` |
