|   SIP-Number |  |
|         ---: | :--- |
|        Title | Attestation registry |
|  Description | Open standard for attesters to share signals about packages or modules |
|       Author | Sidestream Labs https://labs.sidestream.tech |
|       Editor |  |
|         Type | Informational |
|      Created | 2025-02-17 |
| Comments-URI |  |
|       Status |  |
|     Requires | N/A |

## Abstract

Open standard for attesters to share signals about packages or modules.

## Motivation

Currently, there is no straightforward way for the users to ensure safety of a particular package they plan to interact with. This document proposes to create a central registry that will allow:
    1. Security professionals to provide various attestations for packages or modules
    2. Regular Sui users to easily discover relevant attestations for a specific package

## Specification

We propose to develop and deploy an official Sui/Move package with:
- A permissionless "attestation type" registry. It will allow anyone to create new discoverable attestation types
    - Once created, the attestation will be frozen
    - Each attestation type will have required base qualities
        - `id` — UID of the attestation type
        - `type_name` — `std::type_name::get<T>()` of the `attestation.data` type
            - Enforced to be unique to prevent two attestation types with the same `type_name`
        - `is_revocable` — whether or not the attestations of this type can be revoked
    - Each attestation type can introduce a custom arbitrary logic implemented in an external module that defines `<T>` type (for example, the whitelisting logic of who can issue attestations can be implemented this way)
    - The sender would be required to create Display type for their attestation type
    - Created Display type will be immutable to prevent misleading updates or renames
- A permissionless attestation registry. It will allow anyone to create new attestations using any existing attestation types
   - Once created, the attestation can not be transferred or deleted
   - Each attestation will have obligatory set of fields
       - `id` — UID of the attestation
       - `receiver` – the address which is being attested
       - `created_by` — the address who created the attestation
       - `data` — the extra fields stored in the specific “attestation type”
   - Created attestation will be transferred to the attested package or module
   - Attestation creation function will return `RevokeCap` object, which can be used to revoke original attestation

## Rationale

The main target of this proposed standard is the end user interacting with packages and modules on Sui blockchain. Therefore, explorers and wallets should be able to index created attestations and provide contextual information to the users.

We wish to enable the Sui community to define exact attestation types suitable for their needs, therefore the proposal makes attestations and attestation types fully permissionless and extensible. Although we would also plan to define first attestation types and provide intended example usage for common use-cases like security audits.

## Backwards Compatibility

There are no issues with backwards compatibility, as this proposal focuses on establishing a new standard.

## Reference Implementation

To be provided by Sidestream Labs.

## Security Considerations

As the ownership of the Attestation objects will be used as a way to define “attested” address, transferability of the Attestation objects should be restricted and stay restricted. Likely the package upgradability will need to be sacrificed to avoid possibility of its misuse.
