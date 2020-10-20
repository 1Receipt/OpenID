# 1Receipt OAuth
This documentation is for integrate 1receipt account to third party clients

### Start
##### 1. Apply for 1receipt sandbox certification
-  Fill in [apply form](https://forms.gle/Yg5k1DgTWGchDPVv5) and wait for checking application details. 
  
-  Once the checking has been past, one sandbox certification will be sent by email.
  
-  The sandbox certification contains `client_id` and `sign_in_domain` `user_pool_id` `region`for sandbox userpool.

##### 2. Generate auth URL and integrate user consent dialog
- Generate auth URL
  - Format: 
  ```
  https://<sign_in_domain>/login?client_id=<client_id>&response_type=code&scope=openid+profile&redirect_uri=<redirect_uri>
  ```
  - Example:
  ```
  https://www.1receipt.io/login?client_id=my_client_id&response_type=code&scope=openid+profile&redirect_uri=my_redirect_uri
  ```

- Integrate the user consent dialog:
  - Content:
    ```
    Do you authorise 1receipt to share your accountId and firstName with 1R OAuth 2.0 Playground?
    ```
  - Button:
    `Allow` | `Cancel`
  - Example:
    [Component Example]()
    ![Component Example Page]()

- Attention: Only allow redirect_uri submitted in apply form.

##### 3. Authenticating the user
- Open the URL generated in step 2 in browser
  ![Sign In Page]()
  
- After customer sign in with their account, this http request will redirect to the reditect_uri and contains `id_token` of this customer in query.

- Redirect request example:
  ```
  <redirect_uri>#id_token=<id_token>&expires_in=<expires_in>&token_type=Bearer
  ```

##### 4. Validating an ID token
- Confirm the Structure of the JWT
  - A JSON Web Token (JWT) includes three sections:
    1. Header
    2. Payload
    3. Signature
  - Example:
      `11111111111.22222222222.33333333333`
  - These sections are encoded as base64url strings and are separated by dot (.) characters. If your JWT does not conform to this structure, consider it invalid and do not accept it.
- Validate the JWT Signature
  - The JWT signature is a hashed combination of the header and the payload. Amazon Cognito generates two pairs of RSA cryptographic keys for each user pool. One of the private keys is used to sign the token.
  - To verify the signature of a JWT token:
    1. Decode the ID token.
         - The OpenID Foundation also maintains [a list of libraries for working with JWT tokens](https://openid.net/developers/jwt/). 
    2. Compare the local key ID (kid) to the public kid.
        - Download and store the corresponding public JSON Web Key (JWK) for your credencial: 
          - [Sandbox]()
          - [Production]()
        - Fields in jwk:
          - Key ID (kid)
            The kid is a hint that indicates which key was used to secure the JSON web signature (JWS) of the token.

          - Algorithm (alg)
            The alg header parameter represents the cryptographic algorithm used to secure the ID token. User pools use an RS256 cryptographic algorithm, which is an RSA signature with SHA-256. For more information on RSA, see RSA Cryptography.

          - Key type (kty)
            The kty parameter identifies the cryptographic algorithm family used with the key, such as "RSA" in this example.

          - RSA exponent (e)
            The e parameter contains the exponent value for the RSA public key. It is represented as a Base64urlUInt-encoded value.

          - RSA modulus (n)
            The n parameter contains the modulus value for the RSA public key. It is represented as a Base64urlUInt-encoded value.

          - Use (use)
            The use parameter describes the intended use of the public key. For this example, the use value sig represents signature.

        - Search the public JSON web key for a kid that matches the kid of your JWT.
    3. Use the public key to verify the signature using your JWT library. You might need to convert the JWK to PEM format first. This example takes the JWT and JWK and uses the Node.js library, jsonwebtoken, to verify the JWT signature:
        ```
        var jwt = require('jsonwebtoken');
        var jwkToPem = require('jwk-to-pem');
        var pem = jwkToPem(jwk);
        jwt.verify(token, pem, { algorithms: ['RS256'] }, function(err, decodedToken) {
        });
        ```
- Verify the Claims:
  - To verify JWT claims：
    1. Verify that the token is not expired.
    2. The audience (aud) claim should match your app `client_id` 
    3. The issuer (iss) claim should match your `user_pool_id` in the following format:
        ```
        https://cognito-idp.us-east-1.amazonaws.com/<user_pool_id>.
        ```
    4. Check the token_use claim, its value must be `id`.
  - You can now trust the claims inside the token.
  
##### 5. Obtaining user profile information
- In the payload of this `id_token` contains `accountId` and `name` for this customer which should be saved in your database.

##### 6. Link to the 1receipt main app
- Make a new page in application showing the barcode of customer's account id and name.
  ![1receipt card page]()

- Include both 1receipt App Store Id () and Play Store Id () which connect to the main app so the user can download the full application if they want.
  ![1receipt main app link page]()

##### 7. Request for production access
-  [Submit a ticket](https://forms.gle/j3hsG2nDk7KtXT8cA) with a screenshot and following after a Skype meeting to verify the integration.
  
- Once the checking has been past, one production certification will be sent by email.
  
- The production certification contains `client_id` and `sign_in_domain` `user_pool_id` `region`for production userpool

##### 8. Switch to production environment
- Update the `client_id` and `sign_in_domain` of the sign in url in step 2
- Update the `user_pool_id` `region` of the jwks url in step 3
