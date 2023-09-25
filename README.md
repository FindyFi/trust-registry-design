# Trust registry design

## Functionality (genaralized)
- Who are the trusted issuers of a credential type (within an ecosystem)?
    - verifier queries a trust registry
    - verifier can ask for a proof based on a credential issued by a trusted issuer
- Who are the trusted verifiers of a credential type in an ecosystem?
    - a wallet queries the registry in order to inform the user about the status of a verifier requesting a proof or a set of proofs
        - soft limit: the wallet shows a warning if the verifier is not in the list of trusted verifiers
        - hard limit: the wallet refuses to present any information to the verifier (_"[the list says no](https://github.com/WebOfTrustInfo/rwot11-the-hague/blob/master/final-documents/verifiable-issuers-and-verifiers.pdf)"_)
    - is a verifier authorized to request a specific attribute in the credential?
        - support for selective disclosure
        - _good example here?_
- Who are the trusted verifiers of a credential?
    - a wallet queries the registry in order to inform the user about the status of a verifier requesting a proof or a set of proofs
    - the validity of a verifier is based on the credential instance itself - not on the credential _type_
    - see the boarding pass use case below
    
## Use cases (specialized)
1. **Trusted issuer of travel tickets** A user is buying flight tickets from an online shop. Before payment, the user can verify that the travel agent is an accredited travel agent.

2. **Trusted issuer of a boarding pass** An airline issues a boarding pass to a passenger. Only the airline themselves and the security check and border control on an airport are verified verifiers of the boarding pass. Other airlines are not verified issuers of that credential even though they can issue and verify their own boarding passes (the same credential type based on the same schema). The boarding pass credential itself contains pointers to the issuer airline's identifier in a trust registry and trust registries for trusted security check and border control instances. (?)

3. **Trusted issuers: cross-border diplomas** When a student moves from Germany to Finland, a Finnish university wants to check the courses the student has passed in a German university. The Finnish university only acknowledges courses offered by accredited universities. The Finnish university can provide a proof request requesting courses and degrees based on a commonly-agreed diploma schema. When the student presents their credentials, the university queries a trust registry to verify that the issuer of a credential is an accredited university.

4. **Trusted verifiers for healthcare credentials.** In a healthcare ecosystem, the participants agree that electronic health records issued to a holder's wallet can only be seen by verified healthcare providers. The holder's wallet must consider this a strict requirement and decline to present any information from a health record credential to unauthorized verifiers.

## Implementation

### Smart registry
- contains the ecosystem's rulebook (ecostystem gorvernance framework, EGF) as machine-readable code (or smart contracts?)
- ToIP trust registry protocol seems to be built on top of a smart registry (although e.g., TRAIN can be used underneath)
 - an API with specific query endpoints, e.g., "get authorizations of an organization" or "is org A an authorized verifier of credential type X"
 - the governance rules must be maintained in a registry
 - wallets and other agents must know how to call each API query
 - an agent must be able to know (based on credential content?) which is the valid trust registry (source of truth) for each credential
    - _"my ecosystem governance framework is specified in the registry X"_

### Dumb registry
- just a list or a collection of lists with identifiers
- the governance rules must be specified somewhere (within a credential?)
    - _"my issuer claims to be listed in registry Y, see for youself"_
    - _"the registry for authorized verifiers for me is registry Z"_ ([`aud:`](https://www.w3.org/TR/vc-data-model/#jwt-encoding) header?)
 - an agent must be able to know (based on credential content?) which is the valid trust registry (source of truth) for each credential 
- a side note: it seems that EU/EAA Trusted Lists have some knowledge about services offered by trusted entities but not governance framework rules

## Deployment
- hierarchy needed in many cases (e.g., EU diplomas)
    - a Finnish trust list maintained by a Finnish authority containing all valid universities in Finland
    - a German trust list maintained by a German authority containing all valid universities in Germany
    - a European trust list containing pointers to both Finnish and German trust lists

### Centralized
- "ecosystem master"
- an agent (a client, e.g., a holder wallet or a verifier agent) queries a single trust registry - that registry can make (or redirect the client to make) queries to other registries

### Decentralized
- anyone can set up a trust list (trust registry)
- the credential can specify its relevant registries
- the verifier can specify the registries it trusts for each credential type it is configured to handle
- a holder can set up trusted registries in their wallet
