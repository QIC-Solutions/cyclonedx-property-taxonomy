# QiC Solutions CycloneDX Property Taxonomy


Version 1.0.

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
| `qic:vector` | component | Discovery vector of the scan that contributed the component (consolidated inventories). | One of `REPOSITORY`, `IMAGE`, `TLS`, `CERTIFICATE`, `SBOM`, `FILESYSTEM`, `CLOUD`, `CONFIG`, `NETWORK`, `SSH`, `TRUSTSTORE`, `PCAP`, `CT`, `THIRDPARTY`. |
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
