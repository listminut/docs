---
title: "JSON Web Token (JWT)"
linkTitle: "JWT"
description: >
  One way to represent claims that is URL-safe
date: 2021-06-02
---

## What is it?

One way to represent claims that is URL-safe

## Claims?

Yes, claims to be asserted in the context of authentication. Examples:

- Issuer Claim - who is claiming this? - https://appleid.apple.com
- Audience Claim - who is it meant for? - com.listminut.app or
  be.listminut.apps
- Subject Claim - who are we talking about? - Apple User ID
  001525.8272ae76b7204d28a5b4cea10f93131e.1500
- Expiration Time Claim - when will this claim expire? - 1621616256 (timestamp
  date)
- Not before Claim - when will this claim become valid? - 1621529856 (timestamp
  date)

## URL-safe?

If you use the encoded JWT in an HTTP request, it won't break it with any weird
characters.

For example if Apple sends me this, it will not break anything because of weird
characters:

```
https://listminut.be/some-endpoint?jwt=eyJraWQiOiJlWGF1bm1MIiwiYWxnIjoiUlMyNTYifQ.eyJpc3MiOiJodHRwczovL2FwcGxlaWQuYXBwbGUuY29tIiwiYXVkIjoiY29tLmxpc3RtaW51dC5hcHAiLCJleHAiOjE2MjE2MTYyNTYsImlhdCI6MTYyMTUyOTg1Niwic3ViIjoiMDAxNTI1LjgyNzJhZTc2YjcyMDRkMjhhNWI0Y2VhMTBmOTMxMzFlLjE1MDAiLCJub25jZSI6IkJXb2F2SUsxUWJvTnpMNE14U3ludlEiLCJhdF9oYXNoIjoiYnQxY1JmckdJeVhVeHBWdDBfcEpFZyIsImVtYWlsIjoiejliNDhkZms0N0Bwcml2YXRlcmVsYXkuYXBwbGVpZC5jb20iLCJlbWFpbF92ZXJpZmllZCI6InRydWUiLCJpc19wcml2YXRlX2VtYWlsIjoidHJ1ZSIsImF1dGhfdGltZSI6MTYyMTUyOTg1NSwibm9uY2Vfc3VwcG9ydGVkIjp0cnVlfQ.vJvINKvpWUVt4TSp5VxaLQ5RXpnPMc2QfKRFa0WHtAKDSMr8Fp4QKk0D1xoraEzYUFqVSOhTh0iblreSUW-sY0PXYD3ddnhzO-lpWKH0C-R3qCBOsB3iLqqvj3r1sQa5LmyNiNx8pJNQ_QM8nsPvWftYP-__Wg-Tx--Mdo4vJeLYP-wnS52yx1Zl6cKtHaqVVcY8amttjufgeaeIzBj0m7ha0ynKlXVxGostHegzZYYmVFIYWBIJnm0B-oJzeJBzUmSureB5GNFew4Jr3zHxltVCDVi_RmLr8QYafQDKLs4CXmOrDaqLLWKQnOu3z_YFBFjGykWewIhzEuhEFqErXA
```

## How does that gibberish (encoded JWT) translates to claims?

```
encoded_jwt = "eyJraWQiOiJlWGF1bm1MIiwiYWxnIjoiUlMyNTYifQ.eyJpc3MiOiJodHRwczovL2FwcGxlaWQuYXBwbGUuY29tIiwiYXVkIjoiY29tLmxpc3RtaW51dC5hcHAiLCJleHAiOjE2MjE2MTYyNTYsImlhdCI6MTYyMTUyOTg1Niwic3ViIjoiMDAxNTI1LjgyNzJhZTc2YjcyMDRkMjhhNWI0Y2VhMTBmOTMxMzFlLjE1MDAiLCJub25jZSI6IkJXb2F2SUsxUWJvTnpMNE14U3ludlEiLCJhdF9oYXNoIjoiYnQxY1JmckdJeVhVeHBWdDBfcEpFZyIsImVtYWlsIjoiejliNDhkZms0N0Bwcml2YXRlcmVsYXkuYXBwbGVpZC5jb20iLCJlbWFpbF92ZXJpZmllZCI6InRydWUiLCJpc19wcml2YXRlX2VtYWlsIjoidHJ1ZSIsImF1dGhfdGltZSI6MTYyMTUyOTg1NSwibm9uY2Vfc3VwcG9ydGVkIjp0cnVlfQ.vJvINKvpWUVt4TSp5VxaLQ5RXpnPMc2QfKRFa0WHtAKDSMr8Fp4QKk0D1xoraEzYUFqVSOhTh0iblreSUW-sY0PXYD3ddnhzO-lpWKH0C-R3qCBOsB3iLqqvj3r1sQa5LmyNiNx8pJNQ_QM8nsPvWftYP-__Wg-Tx--Mdo4vJeLYP-wnS52yx1Zl6cKtHaqVVcY8amttjufgeaeIzBj0m7ha0ynKlXVxGostHegzZYYmVFIYWBIJnm0B-oJzeJBzUmSureB5GNFew4Jr3zHxltVCDVi_RmLr8QYafQDKLs4CXmOrDaqLLWKQnOu3z_YFBFjGykWewIhzEuhEFqErXA" 
```

### The JOSE Header

The first part (until the first period) of the encoded JWT is the JOSE Header
and it can tell us what's the id of the key that was used to encrypt the JWT
and which algorithm was used

```
encoded_header = jwt.split('.').first # => eyJraWQiOiJlWGF1bm1MIiwiYWxnIjoiUlMyNTYifQ
jose_header = JSON.parse(Base64.decode64(encoded_header)) # => {"kid"=>"eXaunmL", "alg"=>"RS256"}
```

### Issuer Signing Key

Since this JWT is used for Apple authentication, they are the ones who
signed/encrypted it, so we need to go fetch their keys (URL will differ between
issuers)

```
curl https://appleid.apple.com/auth/keys
{
  "keys": [
    {
      "kty": "RSA",
      "kid": "eXaunmL",
      "use": "sig",
      "alg": "RS256",
      "n": "4dGQ7bQK8LgILOdLsYzfZjkEAoQeVC_aqyc8GC6RX7dq_KvRAQAWPvkam8VQv4GK5T4ogklEKEvj5ISBamdDNq1n52TpxQwI2EqxSk7I9fKPKhRt4F8-2yETlYvye-2s6NeWJim0KBtOVrk0gWvEDgd6WOqJl_yt5WBISvILNyVg1qAAM8JeX6dRPosahRVDjA52G2X-Tip84wqwyRpUlq2ybzcLh3zyhCitBOebiRWDQfG26EH9lTlJhll-p_Dg8vAXxJLIJ4SNLcqgFeZe4OfHLgdzMvxXZJnPp_VgmkcpUdRotazKZumj6dBPcXI_XID4Z4Z3OM1KrZPJNdUhxw",
      "e": "AQAB"
    },
  ]
}
```

As you can see there's one that matches (removed the others for readability).
I'm not going to do all the steps manually but instead will use JWT gem to make
the steps simpler. First I load Apple's key:

```
key_hash = {
  "kty" => "RSA",
  "kid" => "eXaunmL",
  "use" => "sig",
  "alg" => "RS256",
  "n" => "4dGQ7bQK8LgILOdLsYzfZjkEAoQeVC_aqyc8GC6RX7dq_KvRAQAWPvkam8VQv4GK5T4ogklEKEvj5ISBamdDNq1n52TpxQwI2EqxSk7I9fKPKhRt4F8-2yETlYvye-2s6NeWJim0KBtOVrk0gWvEDgd6WOqJl_yt5WBISvILNyVg1qAAM8JeX6dRPosahRVDjA52G2X-Tip84wqwyRpUlq2ybzcLh3zyhCitBOebiRWDQfG26EH9lTlJhll-p_Dg8vAXxJLIJ4SNLcqgFeZe4OfHLgdzMvxXZJnPp_VgmkcpUdRotazKZumj6dBPcXI_XID4Z4Z3OM1KrZPJNdUhxw",
  "e" =>"AQAB"
}
apple_jwk = JWT::JWK.import(key_hash) # => #<JWT::JWK::RSA:0x00007f8a0b611ba0 @keypair=#<OpenSSL::PKey::RSA:0x00007f8a0b611c40>, @kid="eXaunmL">
```

### Decoding the JWT

And then I can finally decode the JWT.

```
JWT.decode(encoded_jwt, apple_jwk.public_key, false, algorithm: key_hash['alg'])
=> [{"iss"=>"https://appleid.apple.com",
  "aud"=>"com.listminut.app",
  "exp"=>1621616256,
  "iat"=>1621529856,
  "sub"=>"001525.8272ae76b7204d28a5b4cea10f93131e.1500",
  "nonce"=>"BWoavIK1QboNzL4MxSynvQ",
  "at_hash"=>"bt1cRfrGIyXUxpVt0_pJEg",
  "email"=>"z9b48dfk47@privaterelay.appleid.com",
  "email_verified"=>"true",
  "is_private_email"=>"true",
  "auth_time"=>1621529855,
  "nonce_supported"=>true},
 {"kid"=>"eXaunmL", "alg"=>"RS256"}]
```

Now we're able to read who's the issuer, the intended audience, the expiration
date, subject and other extra info that Apple put there for us like the email.

But only reading the claims has not much value, so with help from JWT gem I can
request to validate if such claims are right (I just need to pass true instead
of false). And if it finds any errors it will throw an error.

## Dig deeper

- [RFC7519](https://datatracker.ietf.org/doc/html/rfc7519) Every library that
  implements JWT must follow this document. So you can learn every detail about
  it.
- [Authenticating Users with Sign in with Apple](https://developer.apple.com/documentation/sign_in_with_apple/sign_in_with_apple_rest_api/authenticating_users_with_sign_in_with_apple)
