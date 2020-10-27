# SpringBootKeycloak
Case study: CS20180505
Name: Navneet Kumar

High level system architecture:
API Gateway provides a tiered pricing model for API requests. Which has auto scaling capabilities as per load 

List of APIs and rationale behind the design: The API’s servers(Resources) shall be as follows:
/atms
/branches
/personal-current-accounts
/business-current-accounts
/unsecured-sme-loans
/insurance
/mortgage
/credit
/debit




The implementation of one non functional requirement:

In my product the whole application shall ask the Keycloak server to authenticate a user for them. After a successful login, the application will receive an identity token and an access token. The identity token contains information about the user such as username, email, and other profile information. The access token is digitally signed by the realm and contains access information (like user role mappings) that the application can use to determine what resources the user is allowed to access on the application.
 



Steps:
Create an initial admin username and password in keycloak. Login using the admin credentials 


Keycloak configure these endpoints

http://localhost:8180/auth/realms/master/protocol/openid-connect/auth
http://localhost:8180/auth/realms/master/protocol/openid-connect/token
http://localhost:8180/auth/realms/master/protocol/openid-connect/token/introspect
http://localhost:8180/auth/realms/master/protocol/openid-connect/userinfo
http://localhost:8180/auth/realms/master/protocol/openid-connect/certs
http://localhost:8180/auth/realms/master/protocol/openid-connect/logout
http://localhost:8180/auth/realms/master/clients-registrations/openid-connect

Health ping URL:
http://localhost:8180/auth/realms/SpringBootKeycloak/.well-known/openid-configuration
Creating a Realm
A  successful login will take us to the console and open up the default Master realm for us. Here we'll focus on creating a custom realm. Let's navigate to the upper left upper corner to discover the Add realm button:

After clicking the Create button, a new realm will be created and we'll be redirected to it. All the operations in the next sections will be performed in this new danske-app-clients realm.

Creating a Client
Now we'll navigate to the Clients page. As we can see in the image below, Keycloak comes with Clients that are already built-in:

But we need to add a new client to our application, so we'll click Create. We'll call the new Client login-app:

In the next screen, for this tutorial, we'll be leaving all the defaults except the Valid Redirect URIs field. This field should contain the application URL(s) that will use this client for authentication:

Later on, we'll be creating a Spring Boot Application running at the port 8081 that'll use this client. Hence we've used a redirect URL of http://localhost:8081/* above.
Creating a Role and a User
Keycloak uses Role-Based Access. Therefore, each user must have a role.
To do that, we need to navigate to the Roles page:

Then, we'll add the user role:

Now we've got a role that can be assigned to users, but there are no users yet. So let's go the Users page and add one:
 

We'll add a user named user1:

Once the user is created, a page with its details will be displayed:

We can now go to the Credentials tab. We'll be setting the initial password to Mig@jal_14

Finally, we'll navigate to the Role Mappings tab. We'll be assigning the user role to our user1:
 


Click the Add selected button.






Application work flow:

1.	mvn clean spring-boot:run
On visiting http://localhost:8081 we see:
 
Now we click customers to enter the intranet, which is the location of sensitive information.


2. 	We can see that we've been redirected to authenticate through Keycloak to see if we're authorized to view this content:

http://localhost:8180/auth/realms/SpringBootKeycloak/protocol/openid-connect/auth?response_type=code&client_id=login-app&redirect_uri=http%3A%2F%2Flocalhost%3A8081%2Fcustomers&state=bfd5bd7c-18a6-4506-9b5b-cd6d5612859d&login=true&scope=openid

3.	Once we log in as user1, Keycloak will verify our authorization – that we have the user role – and we'll be redirected to the restricted customers page:

Now we've finished the set up of connecting Spring Boot with Keycloak and demonstrating how it works.
As we can see, the entire process of calling the Keycloak Authorization Server was handled seamlessly by Spring Boot for us. We did not have to call the Keycloak API to generate the Access Token ourselves, or even send the Authorization header explicitly in our request for protected resources.

3. Current Limitation:
API Gateway has a limit of 10,000 RPS (requests per second), which might not be enough for some cases. Whereas Load Balancers or Cloud prodded ALB, on the other hand, is virtually unlimited. In fact, Some of the cloud provider specifies no limits in terms of connections per second or concurrently. Though For Serverless applications, API Gateway was the only way to go until recently, when Cloud providers announced the integration of ALB with Lambda functions.
 
API Gateways are not offering rule-based routing mechanisms. Apart from supporting a URL path-based approach.

