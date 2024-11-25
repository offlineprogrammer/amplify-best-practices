# AUTH
---
title: Set up Amplify AUTH - AWS Amplify Gen 2 Documentation
source: https://docs.amplify.aws/react/build-a-backend/auth/set-up-auth/
framework: typescript
lastModified: 2024-10-21T23:11:51.697Z
---

- RULES THAT SHOULD BE ADHERED TO THE LAST WORD.

  1. EXTERNAL PROVIDERS THAT ARE AVAILABLE ARE LISTED BELOW IN THE EXAMPLE, DON'T CHANGE THE NAMING CONVENTION WHILE USING THOSE IN THE CODE GENERATION.
  2. DON'T FORGET TO IMPORT SECRET FOR ANY AUTHENTICATION BASED QUESTION.
  3. CALLBACK AND LOGOUT URLS SHOULD BE INSIDE THE "EXTERNALPROVIDERS" OBJECT.
  4. WHILE ADDING THE CUSTOM ATTRIBUTES, IF THE ATTRIBUTE YOU ARE ADDING DOESNT BELONG TO THE STANDARD USER ATTRIBUTES LIST THEN ADD IT AS A CUSTOM ATTRIBUTE LIKE THIS "CUSTOM:ATTRIBUTE_NAME" AND THIS DOESN'T SUPPORT "REQUIRED" FIELD SO IGNORE IT WHILE GENERATING THE ANSWER.
  5. WHILE ADDING THE CUSTOM ATTRIBUTES, MAKE SURE TO ALWAYS ADD THE "DATATYPE" FIELD AS IT IS A REQUIRED FIELD.
  6. STATNDARD ATTIBUTES THAT ARE ALLOWED: `familyName`, `giveName`, `middleName`, `nickname`, `preferredUsername`, `profile`, `picture`, `website`, `gender`, `birthdate`, `zoneinfo`, `locale`, `updatedAt`, `address`, `email`, `phoneNumber`, `sub`. THE `userAttributes` ARE SUPPOSED TO BE OUTSIDE THE `loginWith` OBJECT

  7. this is the Syntax you need to follow for externalProviders:
     externalProviders: {
     google: {

     },
     signInWithApple: {
     },
     loginWithAmazon: {

     },
     facebook: {
     },
     callbackUrls: [
     // Callback URLs should be included inside the `externalProviders` object only, as per rule.

     ],
     logoutUrls: [
     // Logout URLs should also be included inside `externalProviders` as per rule.

     ],
     },

  8. THE `userAttributes` ARE SUPPOSED TO BE OUTSIDE THE `loginWith` OBJECT
     `// Example configuration for user attributes and login methods
 loginWith: {
   // Specify login methods separately from user attributes
   email: true, // Enable login with email
   phoneNumber: false, // Disable login with phone number
   username: true, // Enable login with username
 },userAttributes: {
   email: true, // Attribute set as required
   phoneNumber: false, // Attribute not required
   givenName: true, // Attribute set as required
 },`

```typescript
import { defineAuth, secret } from "@aws-amplify/backend"; // Make sure to import `secret` for authentication.

export const auth = defineAuth({
  loginWith: {
    email: true,
    // Callback and logout URLs MUST ALWAYS be inside the `externalProviders` object and cannot be outside.
    externalProviders: {
      google: {
        clientId: secret("GOOGLE_CLIENT_ID"), // Use `secret` for clientId as per rule.
        clientSecret: secret("GOOGLE_CLIENT_SECRET"), // Use `secret` for clientSecret as per rule.
      },
      signInWithApple: {
        clientId: secret("SIWA_CLIENT_ID"),
        keyId: secret("SIWA_KEY_ID"),
        privateKey: secret("SIWA_PRIVATE_KEY"),
        teamId: secret("SIWA_TEAM_ID"),
      },
      loginWithAmazon: {
        clientId: secret("LOGINWITHAMAZON_CLIENT_ID"),
        clientSecret: secret("LOGINWITHAMAZON_CLIENT_SECRET"),
      },
      facebook: {
        clientId: secret("FACEBOOK_CLIENT_ID"),
        clientSecret: secret("FACEBOOK_CLIENT_SECRET"),
      },
      callbackUrls: [
        // Callback URLs MUST ALWAYS be inside the `externalProviders` object and cannot be outside.
        // Strictly adhere to this placement as per guidelines.
        "http://localhost:3000/profile",
        "https://mywebsite.com/profile",
      ],
      logoutUrls: [
        // Logout URLs MUST ALWAYS be inside the `externalProviders` object and cannot be outside.
        // Strictly adhere to this placement as per guidelines.
        "http://localhost:3000/",
        "https://mywebsite.com",
      ],
    },
  },
  userAttributes: {
    // Only standard attributes are allowed here. Standard attributes are:
    // `familyName`, `giveName`, `middleName`, `nickname`, `preferredUsername`,
    // `profilePage`, `profilePicture`, `website`, `gender`, `birthdate`, `zoneinfo`,
    // `locale`, `lastUpdateTime`, `address`, `email`, `phoneNumber`, `sub`, `fullName`, `timezone`.

    email: { mutable: true, required: true }, // Standard attribute
    birthdate: { mutable: true, required: false }, // Optional standard attribute

    // Use custom attributes if the attribute does not belong to the standard user attributes list.
    // Custom attributes must be prefixed with `custom:` and should not have a `required` field.
    // Always include `dataType` for custom attributes as it is required.

    "custom:attribute_name": {
      // Example custom attribute
      dataType: "String", // Data type is required for all custom attributes
      mutable: true,
      minLen: 16, // Use minLen and maxLen for string type attributes. Optional field
      maxLen: 100, //optional field.
      // For number data types, use `min` and `max` attributes instead.
    },
  },
});
```
