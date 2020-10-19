# 1Receipt OAuth
This documentation is for integrate 1receipt account to third party clients

### Start
##### 1. Apply for 1receipt sandbox certification
-  Fill in [apply form](http://docs.1receipt.io) and wait for checking application details. 
  
-  Once the checking has been past, one sandbox certification will be sent by email.
  
-  The sandbox certification contains `client_id` and `sign_in_domain` `user_pool_id` `region`for sandbox userpool.

##### 2. Generate sign in URL
- Format: 
  https://<sign_in_domain>/login?client_id=<client_id>&response_type=code&scope=openid+profile&redirect_uri=<redirect_uri>

- Example:
  https://1receipt_domain/login?client_id=my_client_id&response_type=code&scope=openid+profile&redirect_uri=my_redirect_uri

- Attention: Only allow redirect_uri submitted in apply form.

##### 3. Get customer 1receipt account id and name
- Open the URL generated in step 2 in browser
  
- After customer sign in with their account, this http request will redirect to the reditect_uri and contains `id_token` of this customer in query.
  
- [Verfy `id_token` signature](https://docs.aws.amazon.com/cognito/latest/developerguide/amazon-cognito-user-pools-using-tokens-verifying-a-jwt.html#amazon-cognito-user-pools-using-tokens-step-3).
  
- In the payload of this id_token contains `accountId` and `name` for this customer which should be saved in your database.

- Redirect request example:
  <redirect_uri>#id_token=<id_token>&expires_in=<expires_in>&token_type=Bearer

##### 4. Integrate 1receipt card page
- Make a new page in application showing the barcode of customer's account id and name.

- Include both 1receipt App Store Id () and Play Store Id () which connect to the main app so the user can download the full application if they want.

##### 5. Apply for 1receipt production certification
-  [Submit a ticket]() with a screenshot and following after a Skype meeting to verify the integration.
  
- Once the checking has been past, one production certification will be sent by email.
  
- The production certification contains `client_id` and `sign_in_domain` `user_pool_id` `region`for production userpool

##### 6. Switch to production environment
- Update the `client_id` and `sign_in_domain` of the sign in url in step 2
- Update the `user_pool_id` `region` of the jwks url in step 3
