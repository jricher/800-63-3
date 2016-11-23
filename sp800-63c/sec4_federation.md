<a name="sec4"></a>

## 4. Federation

In a federation protocol, a relationship is formed between the subscriber, the identity provider (IdP), and the relying party (RP) as shown in [Figure 4-1](#63cSec4-Figure1). Depending on the specifics of the protocol, different information passes among the participants at different times. The subscriber communicates with both the IdP and the RP, usually through a web browser. In most cases, the RP and the IdP communicate with each other; this communication can happen over the *front channel* (through redirects involving the subscriber), or over the *back channel* (through a direct connection between the RP and IdP). In some cases, the subscriber may present an information bundle to the RP that is packaged and pre-signed by the IdP that is stored as part of a cached credential they retain.

<a name="63cSec4-Figure1"></a>

<div class="text-center" markdown="1">
![Figure 1: Federation](sp800-63c/media/federation.png)

**Figure 4-1: Federation**

</div>

The subscriber authenticates to the IdP as described in [SP 800-63B](sp800-63b.html), and then the result of that authentication event is asserted to the RP across the network. The IdP can also make attribute statements about the subscriber as part of this process. These attributes and authentication event information are carried to the RP through the use of an assertion, described in [Section 5](#sec5).

### 4.1. Federation Models

This section provides an overview of and requirements for common identity federation models currently in use. In each model, relationships are established between members of the federation in several different ways.

#### <a name="manual-registration"></a> 4.1.1. Manual Registration

In the manual registration model, the IdP and RP manually provision endpoint information about parties with which they expect to interoperate. Agencies SHALL individually vet each such participant to determine that they adhere to their expected security, identity, and privacy standards. Vetting of IdPs and RPs SHALL establish, as a minimum, that:

* IdP assertions of subscriber identifiers include the authentication assurance level (AAL) by which the subscriber authenticated.
* IdP assertions of attributes include the identity assurance level (IAL) by which the attributes were established.
* RPs will adhere to IdP requirements for the handling of subscriber attribute data, such as retention, aggregation, and disclosure to third parties.
* RP and IdP systems use approved profiles of federation protocols.

Parties MAY depend upon a federation authority, as described in [Section 4.1.3](#authorities) below, to perform some or all of this vetting, provided that they are satisfied that the authority has completed this process.

Depending on the protocol being used, at registration time participants SHALL securely establish any keying information they need to operate the federated relationship, including shared secrets, if any. Any symmetric keys used in this relationship SHALL be unique to a pair of federation participants. Each party SHALL maintain their own registry of other parties with which they federate.

Federation relationships SHALL specify a given maximum identity assurance level (IAL) and authentication assurance level (AAL) in connection with the federated relationship. For example, a given RP may only accept IAL 2 identity proofing from a given IdP that has only been vetted at that level.

#### <a name="dynamic-registration"></a> 4.1.2. Dynamic Registration

In the dynamic registration model of federation, it is possible for relationships between members of the federation to be negotiated at the time of a transaction. In order to establish adherence to security, identity, and privacy standards, dynamic registration of federation participants SHALL make use of a federation authority as described in [Section 4.1.3](#authorities) below.

Frequently, parties in a dynamic registration model do not know each other ahead of time. As a consequence, little information about users and systems is exchanged by default. This problem SHOULD be mitigated by a technology called *software statements*, which allow federated parties to cryptographically verify some attributes of the parties involved in dynamic registration. Software statements are lists of attributes describing the RP software, cryptographically signed by a federation authority. Because both parties trust the federation authority, that trust can be extended to the other party in the dynamic registration partnership.  This allows the connection to be established or elevated between the federating parties without relying on self-asserted attributes entirely. See [[RFC 7591]](#RFC7591) section 2.3 for more information on software statements.

#### <a name="authorities"></a> 4.1.3. Federation Authorities

Some federations use a party known as a *federation authority* in order to decrease the number of relationships that need to be individually vetted and negotiated.

Where they are used, federation authorities SHALL evaluate the criteria listed in [Section 4.1.1](#manual-registration) of each participant on behalf of federation members. Federation authorities SHALL establish the maximum operating IAL and AAL for use by relationships they facilitate.

Federation authorities MAY assist members that are trying to discover API endpoints in order to register with other federation members. If this service is not provided, federations supporting dynamic registration SHALL ensure that participants have predictable API addresses where other participants can register themselves without administrator involvement.

Most federations managed through authorities have a simple membership model: either parties are in the federation or they are not. However, more sophisticated federations MAY have multiple tiers of membership which can be used by federated parties to tell whether other parties in the federation have been more thoroughly vetted and therefore are certified to operate at a higher IAL. Federation IdPs MAY decide that certain  subscriber information is only releasable to RPs in higher tiers.

#### 4.1.4. Proxied Federation

In a proxied federation, communication between the IdP and the RP is intermediated in a way that prevents direct communication between the two parties. There are multiple methods to achieve this effect; common configurations include:
* A third party that acts as a federation proxy (or *broker*)
* A network of nodes that distributes the communications

Where proxies are used, they function to some degree as a federation IdP on one side and a federation RP on the other side. Therefore, all normative requirements that apply to IdPs and RPs SHALL apply to proxiesin their respective roles.

<a name="63cSec4-Figure1"></a>

<div class="text-center" markdown="1">
![Figure 2: Federation Proxy](sp800-63c/media/broker.png)

**Figure 4-2: Federation Proxy**
</div>

A proxied federation model can provide several benefits. Federation proxies can simplify technical integration between the RP and IdP by providing a common interface for integration. Additionally, to the extent a proxy effectively blinds the RP and IdP from each other, it can provide some business confidentiality for organizations that want to guard their subscriber lists from each other. Proxies can also mitigate some of the privacy risks of federation described in Section 4.2 below. 

While some proxy structures (typically those that exist primarily to simplify integration) MAY offer no additional subscriber privacy protection, others offer varying levels of privacy to the subscriber through a range of blinding technologies. Privacy policies may dictate appropriate use by the IdP, RP, and the federation proxy, but technical means such as blinding can increase effectiveness of these policies by making the data more difficult to obtain. It should also be noted that as the level of blinding increases, so does the technical and operational implementation complexity.

Note that even with the use of blinding technologies, it MAY still be possible for a blinded party to infer protected subscriber information through released attribute data or metadata such as analysis of timestamps, attribute bundle sizes, or attribute signer information.
 
#### 4.1.5. <a name="runtime-decisions"></a>Runtime Decisions

The fact that parties have federated SHALL NOT be interpreted as permission to pass information in all circumstances. Federated parties MAY establish whitelists of other federated parties who may authenticate subscribers or pass information about them without runtime authorization from the subscriber. Federated parties MAY also establish blacklists of other federated parties who may not be allowed to pass information about subscribers at all. Every party that is not on a whitelist or a blacklist SHALL be placed by default in a gray area where runtime authorization decisions will be made by an authorized party, often the subscriber.

### 4.2. Privacy Requirements

Federation involves the transfer of personal attributes from a third party, the IdP, that is not otherwise involved in a transaction. Federation also potentially gives the IdP broad visibility into subscriber activities. Accordingly, there are specific privacy requirements associated with federation. 

Communication between the RP and the IdP MAY reveal to the IdP where the subscriber is conducting a transaction. Communications with multiple RPs allow the IdP to build a profile of subscriber transactions that would not have existed absent federation. This aggregation MAY enable new opportunities for subscriber tracking and use of profile information that do not align with the privacy interests of subscribers. 

The IdP SHALL NOT disclose information on subscriber activities at an RP to any party, nor use the information for any purpose other than federated authentication, to comply with law or legal process, or in the case of a specific user request for the information. The IdP SHOULD employ technical measures, such as the use of pairwise pseudonymous identifiers described in Section 5.2.5, to provide unlinkability and discourage subscriber activity tracking and profiling.

An IdP MAY disclose information on subscriber activities to other RPs within the federation for security purposes such as communication of compromised subscriber accounts.

The following requirements apply specifically to agencies:

1.  The agency SHALL consult with their Senior Agency Official for Privacy to conduct and analysis to determine whether the agency that is acting as either an IdP or an RP in an identity federation triggers the requirements of the Privacy Act.

2. The agency SHALL publish or identify coverage by a System of Records Notice as applicable.

3. The agency SHALL consult with their Senior Agency Official for Privacy to conduct an analysis to determine whether the agency that is acting as either an IdP or an RP in an identity federation triggers the requirements of the E-Government Act.

4. The agency SHALL publish or identify coverage by a Privacy Impact Assessment as applicable.

