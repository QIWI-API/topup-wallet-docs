# SSL Authorization {#auth}

###### Last update: 2017-07-27 | [Edit on GitHub](https://github.com/QIWI-API/topup-wallet-doc/blob/master/_authorization_en.html.md)

Besides identifier/password authorization, two other authorization types may be also used:

* Client certificate authorization
* Digital signature authorization

## Using digital signature {#sign}

To use digital signature, the Agent has to create a public/private key pair using RSA.

<aside class="notice">Requirements for a key: 2048 bit and Base64-encoded.</aside>

You may use OpenSSL:

* Generate private key:

`openssl genrsa -out private.key 2048`

* Obtain public key:

`openssl rsa -in private.key -pubout -out public.key`

Thus, obtained key corresponds to the requirements.

The Agent has to give the public key to QIWI representative and use the private key in a request signing:

1. Calculate hash on the text of an XML request using MD5 or SHA1 algorithm, sign it by saved private key and make a Base64-encode. A digital sign of the request is obtained as a result.
2. Transfer the following parameters in HTTP header with the XML request to authorize and check its data integrity:
    * `X-Digital-Sign` – digital sign created on the previous point.
    * `X-Digital-Sign-Alg` – algorithm of the digital sign calculation. Specify one of the following values corresponding to the algorithm used on the previous point:
        * `MD5withRSA` - if MD5 used,
        * `SHA1withRSA` - if SHA1 used.

<aside class="notice">When authorizing by digital signature, <extra name="password"></extra> tag is not required.</aside>


## Using client certificate {#cert}
	 
To authorize requests by client certificate, you should send them to 

`https://private-api.qiwi.com/xml/topup.jsp`

To use client certificate, the Agent has to give request for a certificate and public key to QIWI representative:

* Request for a certificate (OpenSSL example):

`openssl req -new -nodes -batch -subj "/C=RU/ST=Russia/L=Moscow/O=QIWI/emailAddress=mail@qiwi.ru" -newkey rsa:2048 -keyout private_key.pem -out cert_request.csr`

  Specify your data in the request: language, country, city, name of your organization and e-mail address. In the example, QIWI data is used.

* Make a corresponding public key (OpenSSL example):

`openssl rsa -in private_key.pem -pubout -out public.key`

* Give obtained public key and the request for a certificate to QIWI representative. 
* CA server certificate and client certificate to use in requests are returned in response.

