# Certificate Pinning

Pinning is the process of associating a host with their expected X509 certificate or public key. Once a certificate or public key is known or seen for a host, the certificate or public key is associated or 'pinned' to the host. If more than one certificate or public key is acceptable, then the program holds a pinset. In this case, the advertised identity must match one of the elements in the pinset.

A host or service's certificate or public key can be added to an application at development time, or it can be added upon first encountering the certificate or public key. The former - adding at development time - is preferred since preloading the certificate or public key out of band usually means the attacker cannot taint the pin.

You should pin anytime you want to be relatively certain of the remote host's identity.

## Pinning Methods

### Certificate

The certificate is easiest to pin. You can fetch the certificate out of band for the website, have the IT folks email your company certificate to you, use openssl s_client to retrieve the certificate etc. At runtime, you retrieve the website or server's certificate in the callback. Within the callback, you compare the retrieved certificate with the certificate embedded within the program. If the comparison fails, then fail the method or function.
There is a downside to pinning a certificate. If the site rotates its certificate on a regular basis, then your application would need to be updated regularly. For example, Google rotates its certificates, so you will need to update your application about once a month (if it depended on Google services). Even though Google rotates its certificates, the underlying public keys (within the certificate) remain static.

### Public Key

Public key pinning is more flexible but a little trickier due to the extra steps necessary to extract the public key from a certificate. As with a certificate, the program checks the extracted public key with its embedded copy of the public key.
There are two downsides to public key pinning. First, it's harder to work with keys (versus certificates) since you must extract the key from the certificate. Extraction is a minor inconvenience in Java and .Net, buts it's uncomfortable in Cocoa/CocoaTouch and OpenSSL. Second, the key is static and may violate key rotation policies.

### Hashing
While the three choices above used DER encoding, its also acceptable to use a hash of the information. In fact, the original sample programs were written using digested certificates and public keys. The samples were changed to allow a programmer to inspect the objects with tools like dumpasn1 and other ASN.1 decoders.

Hashing also provides three additional benefits. First, hashing allows you to anonymize a certificate or public key. This might be important if you application is concerned about leaking information during decompilation and re-engineering. Second, a digested certificate fingerprint is often available as a native API for many libraries, so its convenient to use.

Finally, an organization might want to supply a reserve (or back-up) identity in case the primary identity is compromised. Hashing ensures your adversaries do not see the reserved certificate or public key in advance of its use. In fact, Google's IETF draft websec-key-pinning uses the technique.

### What Pinning won't do
* Will not stop users intercepting their own traffic.
* Will not stop reverse engineers & local bypass.
* Will not help if device is rooted/jailbroken.

* Absense of Certificate Pinning is not a vulnerability, only an extra control.
* However, a broken pinning implementation is a vulnerability.

## Pinning to your servers cert
* + Tiny attack surface (the host, rather than the CA has to be compromised)
* + No 3rd parties invloved
* + Easily self signed
* + No need for chain validation
* - Highly fragile
* - Requires maturity

## Pinning to Intermediate CA identity
* + More flexible (you don't need to update your pins all the time)
* - Chain vlaidation bugs
* - Not easily self-signed
* - Intermediate CA may change
* - No guarantees pinned ICA is used

## Pinning to root CA identity
* + Most flexible
* - Very wide attack surface
* - Chain validation bugs
* - Avoid cross-cerifieds roots

## Pin to Full Certificate or Public key
* Full Certificate
* Public Key
* SPKI (Subject Public Key Information)

### Full Certificate Pinning
* Commonly Used
* Easy pin creation
* Only option for some pinning implementations
* Only option for internal CA pinning
* Brittle
  * CA certificates often reissued or rotated
  * CA's may use multiple certs

### Public Key/SPKI Pinning
* Trickier to get pins (public key needs to be extracted from the cert)
* Flexible as it uses key continuity
* Anonymized - pin hashes (of the public key)
* Downside is you can't pin to internal self-signed CA

## How to handle Compromise
* You must handle two things:
  * The security of the end users
  * The usability of the mobile app
* Create an action plan
  * Fallback certificates
    * Maintain an extra cert for each host offline
    * Include fallback pin in app
  * Enforce app updates for all users
    * Limit available functionality for older apps

## How to handle Key Rotation
* Keep track of your app's endpoints & pins
* Create cert rotation schedule
  * Issue new certs long before rotation
  * Do scheduled app updates & review pins as part of update process
* Coordinate between PKI servers/mobile teams
* Practise Key continuity
  * Try rotate the certifcate without rotating the key to make it easier for everyone

## How to handle pin failures
* Hard-fail - Do not establish the channel
  * Common, easy & secure.
  * Inflexible, user experience issues, danger of self induced DoS.
* Soft-fail - retry without pinning
  * Keep the app UX alive
  * Tricky to get right and a very custom approach
  * Must limit app functionality, lower trust mode

## How to deploy the pins
1. Preloading
  * App ships with hardcoded pin List
  * Common
  * Easy to implement
  * Complex to operate
    * You have to maintain an app version/pin map & force app updates
  * Requires app updates
    * To revoke pins
    * To rotate pins
    * Insecurity Window
    * Self-induced DoS


2. Trust on first use
  * The idea is that you connect to the server, you get a certifcate and you use that certificate as a pin going forward.
  * Used in the HPKP protocol
  * East to roll out
  * Fairly complex to design (You need to be able to define when to renew certificates before they expire etc)
  * Pin expiration - adds an attack window
  * Good for not-so critical or unknown endpoints


3. Over The Air
  * Over the Air provisioning, you connect to a pin server and you download the pins. Allows for smooth deployments.
  * Central management of pins for different applications.
  * Very flexible
  * Easy to deploy
  * Easy to get wrong
    * Complexity, custom protocol, expirations
  * Still have to pin the 'pin server'
  * Still have to manage the pins


## HPKP
HTTP Public Key Pinning, or HPKP, is a security policy delivered via a HTTP response header much like HSTS and CSP. It allows a host to provide information to a user agent about which cryptographic identities it should accept from the host in the future. This can protect a host website from a security compromise at a Certificate Authority where rogue certificates may be issued for your hostname.


### Why Do We Need HPKP?
Any one of the Certificate Authorities (CAs) in your trust store can issue a certificate for your hostname and the browser will trust it implicitly. This is great in the sense that you can obtain a certificate from any CA of your choosing, but not so great when one gets compromised.

[More Infomation](https://scotthelme.co.uk/hpkp-http-public-key-pinning/)

