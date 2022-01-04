---
services: azure-active-directory-B2C
platforms: nodejs
author: soumi
level: 200
sdk: MSAL-Node
client: NodeJs WebApp
endpoint: Microsoft Identity Platform
---

# A NodeJS WebApp signing-in users with the Microsoft Identity Platform in Azure AD B2C.

[![Build status](https://identitydivision.visualstudio.com/IDDP/_apis/build/status/AAD%20Samples/.NET%20client%20samples/ASP.NET%20Core%20Web%20App%20tutorial)](https://identitydivision.visualstudio.com/IDDP/_build/latest?definitionId=819)

## Scenario

This sample shows how to build a NodeJS Express Web app that uses [MSAL-Node](https://www.npmjs.com/package/@azure/msal-node) to implement OpenID Connect to sign in users in **Azure AD B2C**. It assumes you have some familiarity with **Azure AD B2C**. If you'd like to learn all that B2C has to offer, start with our documentation at [https://aka.ms/aadb2c](https://aka.ms/aadb2c).


This sample demonstrates a [confidential client application](../../../lib/msal-node/docs/initialize-confidential-client-application.md) registered on Azure AD B2C. It uses:

1. [OIDC Connect protocol](https://docs.microsoft.com/azure/active-directory-b2c/openid-connect) to implement standard B2C [Custom Policies](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-policy-overview) to:

    - sign-up/sign-in a user
    - reset/recover a user password
    - edit a user profile

2. [Authorization code grant](https://docs.microsoft.com/azure/active-directory-b2c/authorization-code-flow) to acquire an [Access Token](https://docs.microsoft.com/azure/active-directory-b2c/tokens-overview) to call a [protected web API](https://docs.microsoft.com/azure/active-directory-b2c/add-web-api-application?tabs=app-reg-ga) (also on Azure AD B2C)

## Registration

1. [Create an Azure Active Directory B2C tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)
2. [Register a web application in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications?tabs=app-reg-ga)
3. Use the Custom Policies from the `policies` folder.


### Step 1: Clone or download this repository

From your shell or command line:

```powershell
git clone https://github.com/souravmishra-msft/B2C-MSAL-Node-WebApp.git
```

Navigate to the `"B2C-MSAL-Node-WebApp-main"` folder

 ```Sh
  cd "B2C-MSAL-Node-WebApp-main"
  ```

### Step 2: Download node.js for your platform

To successfully use this sample, you need a working installation of Node.js.

### Step 3: Install NPM modules

Next, install the NPM.

From your shell or command line:
* `$ npm install`

### Step 4: Get your own Azure AD B2C tenant

If you don't have an Azure AD B2C tenant yet, you'll need to create an Azure AD B2C tenant by following the [Tutorial: Create an Azure Active Directory B2C tenant](https://azure.microsoft.com/documentation/articles/active-directory-b2c-get-started).

### Step 5: Create your own Web app

Now you need to [register your web app in your B2C tenant](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#register-a-web-application), so that it has its own Application ID.

Your web application registration should include the following information:

- Enable the **Web App/Web API** setting for your application.
- Set the **Reply URL** to `https://www.yourcustomdomain.com/auth/openid/return`.
- Copy the Application ID generated for your application, so you can use it in the next step.

### Step 8: Configure the sample with your app coordinates

1. Open the sample in Visual Studio Code.
1. In `policies.js`, we create a `b2cPolicies` object to store authority strings for initiating each user-flow. The object may look like the following:

  ```javascript
  const b2cPolicies = {
    names: {
        signUpSignIn: "B2C_1A_EMBEDDEDSIGNIN_SIGNUP_SIGNIN",
        resetPassword: "B2C_1A_EMBEDDEDSIGNIN_PASSWORDRESET",
        editprofile: "B2C_1A_EMBEDDEDSIGNIN_PROFILEEDIT",
    },
    authorities: {
        signUpSignIn: {
            authority: "https://login.yourcustomdomain.com/yourcustomdomain.com/B2C_1A_EMBEDDEDSIGNIN_SIGNUP_SIGNIN",
        },
        resetPassword: {
            authority: "https://login.yourcustomdomain.com/yourcustomdomain.com/B2C_1A_EMBEDDEDSIGNIN_PASSWORDRESET",
        },
        editprofile: {
            authority: "https://login.yourcustomdomain.com/yourcustomdomain.com/B2C_1A_EMBEDDEDSIGNIN_PROFILEEDIT",
        },
    },
    authorityDomain: "yourcustomdomain.com",
    destroySessionUrl: "https://login.yourcustomdomain.com/yourcustomdomain.com/oauth2/v2.0/logout?p=B2C_1A_EMBEDDEDSIGNIN_SIGNUP_SIGNIN" + "&post_logout_redirect_uri=https://yourcustomdomain.com/"
}
```
3. In `index.js` (can be found inside the `config` folder), we setup the configuration object expected by MSAL Node `confidentialClientApplication` class constructor:

  ```javascript
  const confidentialClientConfig = {
      auth: {
          clientId: "ENTER_CLIENT_ID",
          authority: policies.authorities.signUpSignIn.authority, //signUpSignIn policy is our default authority
          clientSecret: "ENTER_CLIENT_SECRET",
          knownAuthorities: [policies.authorityDomain], // mark your tenant's custom domain as a trusted authority
          redirectUri: "http://localhost:3000/redirect",
      },
      system: {
          loggerOptions: {
              loggerCallback(loglevel, message, containsPii) {
                  console.log(message);
              },
              piiLoggingEnabled: false,
              logLevel: msal.LogLevel.Verbose,
          }
      }
  };
  ```
4. Find the instances of `yourcustomdomain.com` and replace the value with your Azure AD B2C domain name. For example, `constoso.com`
5. Find the assignment for `ENTER_CLIENT_ID` and replace the value with the Application ID from Step 4.


### Step 9: Run the sample

1. To locally run the sample, you can use `npm run start` or in dev environment you can use `npm run start-dev`.
1. You can also upload this sample as an App Service and configure the App Service to use HTTPS.
1. If you don't have an account registered on the **Azure AD B2C** used in this sample, follow the sign up process. Otherwise, input the email and password for your account and click on **Sign in**.



>Note: There are other alternatives here which we can do to accomplish the same logic, one being creating your own SignIn action which includes generating your own sign-in challenge and using the *state* parameter of the sign-in request using OpenIdConnect Events to indicate the redirect url. However, for the purposes of demo, this JavaScript snippet will do.



> Did the sample not work for you as expected? Did you encounter issues trying this sample? Then please reach out to us using the [GitHub Issues](../../../../issues) page.
> [Consider taking a moment to share your experience with us.](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRz0h_jLR5HNJlvkZAewyoWxUNEFCQ0FSMFlPQTJURkJZMTRZWVJRNkdRMC4u)



