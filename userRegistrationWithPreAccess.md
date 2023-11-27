### User Registration with pre-configured accesses

When user Onboarded into the System using Self Registration or through HR onboarding application,  admin need to set  few pre-requisite access for the same. This we can achieve by making that access request-able for them or grant the same once user onboarded .

There is requirement in some Organization if user is doing Self Registration then access need to 
decide for them as per the domain user's belong to .

To achieve this use case IBM Security Verify provide feature to design a process flow using Flow Designer . 
Please go through this below link for more information about it --
https://www.ibm.com/docs/en/security-verify?topic=experience-managing-flow-designer

Here we are going to use Flow Designer to achieve this use case . 

### Pre-requisites

- Application Onboarded into ISV .
- Make required entitlement request-able using Cloud Directory Group 
- Create Flow Diagram  

### Steps Admin need to perform after login into ISV 
-	Go to User Experience - > User Forms - > Create Forms
		- Provide Name of the Form and click on Start building form
<img width="793" alt="userRegistrationForm" src="https://github.com/ranvijay84hub/blog/assets/69187117/a1efc0bb-46ad-440a-ad8a-04d963bcd81f">

		- Add attribute which we is required to get Input from EndUser  
<img width="1435" alt="userReg 1" src="https://github.com/ranvijay84hub/blog/assets/69187117/75a7419d-bffb-4e0c-aa6a-6be5bea1c1af">

		- Here we have added 'preferred_username' and mark account userName 
		- After adding details Save and Publish it 	
<img width="1425" alt="userRegAfterPublish" src="https://github.com/ranvijay84hub/blog/assets/69187117/b6753702-b03a-49fb-9884-9d8823af5526">

		
-  Go to User Experience - > Flow Designer and - > Create flow 

	<img width="263" alt="flowDesigner" src="https://github.com/ranvijay84hub/blog/assets/69187117/25d75685-de5c-4c5f-8ced-f404a674ffc7">
	<img width="510" alt="createFlow" src="https://github.com/ranvijay84hub/blog/assets/69187117/5157afbf-18d2-4d8c-9b83-1e5d06bde922">
	<img width="1181" alt="afterCreateFlow" src="https://github.com/ranvijay84hub/blog/assets/69187117/58fc146d-fa9b-4c76-b489-8da1630730fc">


-  Here Admin need to write function to decide which access is required for the user who is registering into System

 	<img width="1102" alt="metodScript" src="https://github.com/ranvijay84hub/blog/assets/69187117/1f0cf194-bc09-4bbf-9ab4-2ccd9cdff472">


	Ex - 
		
statements:
- context: >
    tenant := "https://<replace-with-verify-tenant>"
- context: >
    client_id := "<replace-with-your-client-id>" 
- context: >
    client_secret := "<replace-with-client-secret>"
- context: >
    token_endpoint := "/v1.0/endpoint/default/token"
- context: >
    group_ibm := "<Group_id>"
- context: >
    group_ext := "<Group_id>"
- context: >
    emailAddress := user.emails.filter(x, x.type == "work")[0].value
- context: >
    tkn := oauth.GetBearerToken(context.tenant + context.token_endpoint, context.client_id, context.client_secret)
- context: >
    token := "Bearer " + context.tkn
- context: > 
    group_id := context.emailAddress.contains("ibm.com") ? (context.group_ibm) : (context.group_ext)
- context: >
    payload := "{\"Operations\":[{\"path\":\"members\",\"value\":[{\"type\":\"user\",\"value\":\"" + user.id + "\"}],\"op\":\"add\"}],\"schemas\":[\"urn:ietf:params:scim:api:messages:2.0:PatchOp\"]}"
- context: >
    r := hc.Patch(context.tenant + "/v2.0/Groups/" + context.group_id, {"Authorization": context.token, "Content-Type": "application/scim+json"}, context.payload)
- return: >
    context.r

###  After creating Flow Designer 

 - Go to General tab and copy Execution URL and share with Stack Holders
     https://cigtestrv.rel.vanitytst.cloudidentity.ibm.com/flows?reference=verify_group_using_email_domain  

<img width="762" alt="copyexecutionurl" src="https://github.com/ranvijay84hub/blog/assets/69187117/d92f5879-296c-470e-aa6c-040fd295dbab">

  
