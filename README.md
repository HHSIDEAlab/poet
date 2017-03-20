Pre-OAuth Entity Trust (POET) - DRAFT
======================================

The purpose of POET is to assert some level of confidence in 3rd-party applications accessed via OAuth2.


POET was concieved by the Blue Button API Team at the Centers for Medicare and Medicaid Services (CMS) to assist Medicare beneficiaries to distinguish between applications that are have some endorsement or have no endorsement and could be malicious. 


POET provides a technical means for another party to _vouch for_ or _endorse_ an OAuth2 application. For example, the organizations, _NATE Trust_ and _UPMC_ could endorse the application _Cardiac Risk App_. In this example, _NATE_ and _UPMC_ are both "Endorsers". An Endorser digitally signs a document, called a _JWT_, that contains information about the OAuth2 application.  Information contained within the JWT includes the application's redirect URIs and other basic information.  This JWT payload is further described below.


How Does it Work?
-----------------


1.	A developer registers his or her application with the Endorsing Body (EB). Information collected includes many of the same elements used in an oAuth client application registration.  Items include the name, host name, and redirect URLs of the application.  
2.	When approved by the EB, these values become memorialized and are embedded into a signed JWT, containing a software statement, that is given to the developer.  The EB's application registry contains one JSON document per certified application.  
3.	An OAuth2 Server (OAuth2 Provider) may obtain a list of JWTs (i.e. a manifest) from a central source such as URL. Optionally, when an application developer is registering an application in the OAuth2 server, he or she may optionally include one or more JWT to the application registration.  The endorsement badge information will be displayed to the developer upon registration.
4.	The badge and related information will be displayed to the end-user at the point of the oAuth2 client authorization.
5.	While we anticipate all 3rd party applications will contain warnings to the end-user, when one or more valid badges are present, the warning language will be lessened. In the green, yellow, red, analogy the warning would become yellow.


Example  JWT
------------

The  example signed JWT (JWS) contains infromation about the _Cardiac Risk App_ OAuth2 application and is signed by _nate-trust.org_.
The JWS is signed with a private key using the `RS256 Algorithm`.  If an x509 certificate is used for signing, then the corresponding public certificate shall be hosted at `https://nate-trust.org/.well-known/poet.pem`.  If a bare key iss used, the corresponding public key shall be hosted at `https://nate-trust.org/.well-known/poet.jwks`.  


Example Header
--------------

    {
    "alg": "RS256",
    "typ": "JWT"
    }

Example Payload
---------------

    {
    "software_id": "4NRB1-0XZABZI9E6-5SM3R",
    "iss": "nate-trust.org",
    "iat": 1455031265,
    "exp": 1549639265,
    "client_name" : "Cardiac Risk App",
    "client_uri": "https://apps-dstu2.smarthealthit.org/cardiac-risk/",
    "logo_uri" : "https://gallery.smarthealthit.org/img/apps/66.png",
    "initiate_login_uri" : "https://apps-dstu2.smarthealthit.org/cardiac-risk/launch.html",
    "redirect_uris" : [
          "https://apps-dstu2.smarthealthit.org/cardiac-risk/"
       ],
    "scope" : "openid profile patient/*.read",
    "token_endpoint_auth_method" : "none",
    "grant_types" : [ "authorization_code" ]
    }


Example Signature
-----------------

    HMACSHA256(
      base64UrlEncode(header) + "." +
      base64UrlEncode(payload),
      
    ) secret base64 encoded


Example JWT
-----------

    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJuYXRlLXRydXN0Lm9yZyIsImlhdCI6MTQ1NTAzMTI2NSwiZXhwIjoxNTQ5NjM5MjY1LCJhdWQiOiJhcHBzLWRzdHUyLnNtYXJ0aGVhbHRoaXQub3JnIiwic3ViIjoianJvY2tldEBhcHBzLWRzdHUyLnNtYXJ0aGVhbHRoaXQub3JnIiwiY2VydGlmaWNhdGlvbl91aWQiOiI5MjkyMDEwMTMxIiwiY29udGFjdHMiOlsiaW5mb0BzbWFydHBsYXRmb3Jtcy5vcmciLCJodHRwczovL2dhbGxlcnkuc21hcnRoZWFsdGhpdC5vcmciXSwiY2xpZW50X25hbWUiOiJDYXJkaWFjIFJpc2sgQXBwIiwiY2xpZW50X3VyaSI6Imh0dHBzOi8vYXBwcy1kc3R1Mi5zbWFydGhlYWx0aGl0Lm9yZy9jYXJkaWFjLXJpc2svIiwibG9nb191cmkiOiJodHRwczovL2dhbGxlcnkuc21hcnRoZWFsdGhpdC5vcmcvaW1nL2FwcHMvNjYucG5nIiwiaW5pdGlhdGVfbG9naW5fdXJpIjoiaHR0cHM6Ly9hcHBzLWRzdHUyLnNtYXJ0aGVhbHRoaXQub3JnL2NhcmRpYWMtcmlzay9sYXVuY2guaHRtbCIsInJlZGlyZWN0X3VyaXMiOlsiaHR0cHM6Ly9hcHBzLWRzdHUyLnNtYXJ0aGVhbHRoaXQub3JnL2NhcmRpYWMtcmlzay8iXSwic2NvcGUiOiJvcGVuaWQgcHJvZmlsZSBwYXRpZW50LyoucmVhZCIsInRva2VuX2VuZHBvaW50X2F1dGhfbWV0aG9kIjoibm9uZSIsImdyYW50X3R5cGVzIjpbImF1dGhvcml6YXRpb25fY29kZSJdfQ.0E4c0g4JTz2Fbr9oxp7RdMtJ5LqrVHuYOmvh7D6MHCE



Key Responsibilities of an Endorsing Body (EB)
===============================================


* To verify application owners own their domains to which they are binding applications (i.e. `whois`).
* To verify that SSL and valid certificates are in place on the application's server. For example, `https://apps-dstu2.smarthealthit.org` must have a "green light" (e.g. it may not generate common web browser warnings).
* To sign certificates with a private  key with the CN bound to the CB's domain.
* To host the corresponding public key at http(s)://{{iss}}/.well-known/poet.pem or http(s)://{{iss}}/.well-known/poet.jwks


List of All Fields in the Payload
=================================

 Field names follow RFC 7591, OAuth 2.0 Dynamic Registration. See https://tools.ietf.org/html/rfc7591 for details.


    * software_id: A string identifier for the software that comprises a client.
    * iss: A string containing a FQDN. See https://tools.ietf.org/html/rfc7591
    * iat: An integer representing the epoch of the time the JWT was signed.
    * exp: An integer representing the epoch of the time the JWT will expire.
    * client_name: See https://tools.ietf.org/html/rfc7591#section-2.2 
    * client_uri: See https://tools.ietf.org/html/rfc7591#section-2.2
    * logo_uri :  See https://tools.ietf.org/html/rfc7591#section-2.2
    * initiate_login_uri: A string containing a URI pointing to the client's login.
    * redirect_uris: See https://tools.ietf.org/html/rfc7591
    * scope : See https://tools.ietf.org/html/rfc7591
    * token_endpoint_auth_method: A string  enumeration. ["none", "client_secret_post", "client_secret_basic"]
    * grant_types : A string  enumeration.[ "authorization_code", "implicit", "password", "client_credentials", "refresh_token" ]
    
    
    
    
    


