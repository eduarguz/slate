# Eci Codes

The Electronic Commerce Indicator (ECI) indicates the level of security, in general, is the result of the attempted 3D Secure authorization.

Visa | MasterCard | Description
---------- | ------- | ------------
5 | 2 | Cardholder fully authenticated, 3D Secure applied.   
6 | 1 | Merchant attempted to authenticate but the issuer or cardholder was non-participating, or an issuer access control server was not able to respond in time (Merchant-only-authentication).
7 | 0 | Authentication did not succeed. The transaction did not use 3D Secure.
