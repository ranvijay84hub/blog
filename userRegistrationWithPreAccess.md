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
		- Add attribute which we is required to get Input from EndUser  
		- Here we have added 'preferred_username' and mark account userName 
		- After adding details Save and Publish it 		
		
-  Go to User Experience - > Flow Designer and - > Create flow 
-  Here Admin need to write function to decide which access is required for the user who is registering into System

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
