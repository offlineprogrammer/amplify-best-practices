---
title: Modeling relationships - AWS Amplify Gen 2 Documentation
source: https://docs.amplify.aws/typescript/build-a-backend/data/data-modeling/relationships/
framework: typescript
lastModified: 2024-10-21T23:11:46.997Z
---

WHEN MODELING APPLICATION DATA, YOU OFTEN NEED TO ESTABLISH RELATIONSHIPS BETWEEN DIFFERENT DATA MODELS. IN AMPLIFY DATA, YOU CAN CREATE ONE-TO-MANY, ONE-TO-ONE, AND MANY-TO-MANY RELATIONSHIPS IN YOUR DATA SCHEMA. ON THE CLIENT-SIDE, AMPLIFY DATA ALLOWS YOU TO LAZY OR EAGER LOAD OF RELATED DATA.

```typescript
const schema = a
  .schema({
    Member: a.model({
      name: a.string().required(), // 1. Create a reference field    teamId: a.id(),
      // 2. Create a belongsTo relationship with the reference field
      team: a.belongsTo("Team", "teamId"),
    }),
    Team: a.model({
      mantra: a.string().required(), // 3. Create a hasMany relationship with the reference field
      //    from the `Member`s model.
      members: a.hasMany("Member", "teamId"),
    }),
  })
  .authorization((allow) => allow.publicApiKey());
```

CREATE A "HAS MANY" RELATIONSHIP BETWEEN RECORDS

```typescript
const { data: team } = await client.models.Team.create({
  mantra: "Go Frontend!",
});
const { data: member } = await client.models.Member.create({
  name: "Tim",
  teamId: team.id,
});
```

UPDATE A "HAS MANY" RELATIONSHIP BETWEEN RECORDS

```typescript
const { data: newTeam } = await client.models.Team.create({
  mantra: "Go Fullstack",
});
await client.models.Member.update({ id: "MY_MEMBER_ID", teamId: newTeam.id });
```

DELETE A "HAS MANY" RELATIONSHIP BETWEEN RECORDS
IF YOUR REFERENCE FIELD IS NOT REQUIRED, THEN YOU CAN "DELETE" A ONE-TO-MANY RELATIONSHIP BY SETTING THE RELATIONSHIP VALUE TO NULL.

```typescript
await client.models.Member.update({ id: "MY_MEMBER_ID", teamId: null });
```

LAZY LOAD A "HAS MANY" RELATIONSHIP

```typescript
const { data: team } = await client.models.Team.get({ id: "MY_TEAM_ID" });
const { data: members } = await team.members();
members.forEach((member) => console.log(member.id));
```

EAGERLY LOAD A "HAS MANY" RELATIONSHIP

```typescript
const { data: teamWithMembers } = await client.models.Team.get(
  { id: "MY_TEAM_ID" },
  { selectionSet: ["id", "members.*"] }
);
teamWithMembers.members.forEach((member) => console.log(member.id));
```

```typescript
const schema = a
  .schema({
    Cart: a.model({
      items: a.string().required().array(),
      // 1. Create reference field
      customerId: a.id(),
      // 2. Create relationship field with the reference field
      customer: a.belongsTo("Customer", "customerId"),
    }),
    Customer: a.model({
      name: a.string(),
      // 3. Create relationship field with the reference field
      //    from the Cart model
      activeCart: a.hasOne("Cart", "customerId"),
    }),
  })
  .authorization((allow) => allow.publicApiKey());
```

CREATE A "HAS ONE" RELATIONSHIP BETWEEN RECORDS
TO CREATE A "HAS ONE" RELATIONSHIP BETWEEN RECORDS, FIRST CREATE THE PARENT ITEM AND THEN CREATE THE CHILD ITEM AND ASSIGN THE PARENT.

```typescript
const { data: customer, errors } = await client.models.Customer.create({
  name: "Rene",
});

const { data: cart } = await client.models.Cart.create({
  items: ["Tomato", "Ice", "Mint"],
  customerId: customer?.id,
});
```

UPDATE A "HAS ONE" RELATIONSHIP BETWEEN RECORDS
TO UPDATE A "HAS ONE" RELATIONSHIP BETWEEN RECORDS, YOU FIRST RETRIEVE THE CHILD ITEM AND THEN UPDATE THE REFERENCE TO THE PARENT TO ANOTHER PARENT. FOR EXAMPLE, TO REASSIGN A CART TO ANOTHER CUSTOMER:

```typescript
const { data: newCustomer } = await client.models.Customer.create({
  name: "Ian",
});
await client.models.Cart.update({ id: cart.id, customerId: newCustomer?.id });
```

DELETE A "HAS ONE" RELATIONSHIP BETWEEN RECORDS
YOU CAN SET THE RELATIONSHIP FIELD TO NULL TO DELETE A "HAS ONE" RELATIONSHIP BETWEEN RECORDS.

```typescript
await client.models.Cart.update({ id: project.id, customerId: null });
```

LAZY LOAD A "HAS ONE" RELATIONSHIP

```typescript
const { data: cart } = await client.models.Cart.get({ id: "MY_CART_ID" });
const { data: customer } = await cart.customer();
```

EAGERLY LOAD A "HAS ONE" RELATIONSHIP

```typescript
const { data: cart } = await client.models.Cart.get(
  { id: "MY_CART_ID" },
  { selectionSet: ["id", "customer.*"] }
);
console.log(cart.customer.id);
```

MODEL A "MANY-TO-MANY" RELATIONSHIP
IN ORDER TO CREATE A MANY-TO-MANY RELATIONSHIP BETWEEN TWO MODELS, YOU HAVE TO CREATE A MODEL THAT SERVES AS A "JOIN TABLE". THIS "JOIN TABLE" SHOULD CONTAIN TWO ONE-TO-MANY RELATIONSHIPS BETWEEN THE TWO RELATED ENTITIES. FOR EXAMPLE, TO MODEL A POST THAT HAS MANY TAGS AND A TAG HAS MANY POSTS, YOU'LL NEED TO CREATE A NEW POSTTAG MODEL THAT RETYPESCRIPTSENTS THE RELATIONSHIP BETWEEN THESE TWO ENTITIES.

```typescript
const schema = a
  .schema({
    PostTag: a.model({
      // 1. Create reference fields to both ends of
      //    the many-to-many relationshipCopy highlighted code example
      postId: a.id().required(),
      tagId: a.id().required(),
      // 2. Create relationship fields to both ends of
      //    the many-to-many relationship using their
      //    respective reference fieldsCopy highlighted code example
      post: a.belongsTo("Post", "postId"),
      tag: a.belongsTo("Tag", "tagId"),
    }),
    Post: a.model({
      title: a.string(),
      content: a.string(),
      // 3. Add relationship field to the join model
      //    with the reference of `postId`Copy highlighted code example
      tags: a.hasMany("PostTag", "postId"),
    }),
    Tag: a.model({
      name: a.string(),
      // 4. Add relationship field to the join model
      //    with the reference of `tagId`Copy highlighted code example
      posts: a.hasMany("PostTag", "tagId"),
    }),
  })
  .authorization((allow) => allow.publicApiKey());
```

MODEL MULTIPLE RELATIONSHIPS BETWEEN TWO MODELS
RELATIONSHIPS ARE DEFINED UNIQUELY BY THEIR REFERENCE FIELDS. FOR EXAMPLE, A POST CAN HAVE SEPARATE RELATIONSHIPS WITH A PERSON MODEL FOR AUTHOR AND EDITOR.

```typescript
const schema = a
  .schema({
    Post: a.model({
      title: a.string().required(),
      content: a.string().required(),
      authorId: a.id(),
      author: a.belongsTo("Person", "authorId"),
      editorId: a.id(),
      editor: a.belongsTo("Person", "editorId"),
    }),
    Person: a.model({
      name: a.string(),
      editedPosts: a.hasMany("Post", "editorId"),
      authoredPosts: a.hasMany("Post", "authorId"),
    }),
  })
  .authorization((allow) => allow.publicApiKey());
```

ON THE CLIENT-SIDE, YOU CAN FETCH THE RELATED DATA WITH THE FOLLOWING CODE:

```typescript
const client = generateClient<Schema>();
const { data: post } = await client.models.Post.get({ id: "SOME_POST_ID" });
const { data: author } = await post?.author();
const { data: editor } = await post?.editor();
```

MODEL RELATIONSHIPS FOR MODELS WITH SORT KEYS IN THEIR IDENTIFIER
IN CASES WHERE YOUR DATA MODEL USES SORT KEYS IN THE IDENTIFIER, YOU NEED TO ALSO ADD REFERENCE FIELDS AND STORE THE SORT KEY FIELDS IN THE RELATED DATA MODEL:

```typescript
const schema = a
  .schema({
    Post: a.model({
      title: a.string().required(),
      content: a.string().required(),
      // Reference fields must correspond to identifier fields.
      authorName: a.string(),
      authorDoB: a.date(),
      // Must pass references in the same order as identifiers.
      author: a.belongsTo("Person", ["authorName", "authorDoB"]),
    }),
    Person: a
      .model({
        name: a.string().required(),
        dateOfBirth: a.date().required(),
        // Must reference all reference fields corresponding to the
        // identifier of this model.
        authoredPosts: a.hasMany("Post", ["authorName", "authorDoB"]),
      })
      .identifier(["name", "dateOfBirth"]),
  })
  .authorization((allow) => allow.publicApiKey());
```

MAKE RELATIONSHIPS REQUIRED OR OPTIONAL
AMPLIFY DATA'S RELATIONSHIPS USE REFERENCE FIELDS TO DETERMINE IF A RELATIONSHIP IS REQUIRED OR NOT. IF YOU MARK A REFERENCE FIELD AS REQUIRED, THEN YOU CAN'T "DELETE" A RELATIONSHIP BETWEEN TWO MODELS. YOU'D HAVE TO DELETE THE RELATED RECORD AS A WHOLE.

```typescript
const schema = a
  .schema({
    Post: a.model({
      title: a.string().required(),
      content: a.string().required(),
      // You must supply an author when creating the post
      // Author can't be set to `null`.
      authorId: a.id().required(),
      author: a.belongsTo("Person", "authorId"),
      // You can optionally supply an editor when creating the post.
      // Editor can also be set to `null`.
      editorId: a.id(),
      editor: a.belongsTo("Person", "editorId"),
    }),
    Person: a.model({
      name: a.string(),
      editedPosts: a.hasMany("Post", "editorId"),
      authoredPosts: a.hasMany("Post", "authorId"),
    }),
  })
  .authorization((allow) => allow.publicApiKey());
```


- THIS FILE IS TO HELP UNDERSTAND THE RELATIONSHIPS, HOW TO MODEL SCHEMAS, WHAT IS THE CORRECT WAY TO CODE FOR ACCURACY
- USE THIS TO UNDERSTAND HOW DATA SCHEMAS ARE DESIGNED.
- FOR THE DATA SCHEMAS MAKE SURE THAT YOU ALWAYS FOLLOW THESE RULES AND THIS FILE OVER ANY OTHER FILE - THIS IS THE SOURCE OF TRUTH.
- RULES

  1. DON'T USE `.PUBLIC()` WHILE SETTING UP THE AUTHORIZATION. AS AMPLIFY GEN2 ONLY SUPPORTS `.GUEST()`.
  2. `.BEONGSTO()` AND `.HASMANY()` RELATIONS SHALL ALWAYS HAVE THE RELATEDFIELD ID.
  3. `.ENUM()` DOESN'T SUPPORT `.REQUIRED()`/ `.DEFAULTVALUE()` IN ANY CONDITION, SO ALWAYS IGNORE USING IT.
  4. TO GIVE PERMISSION TO THE GROUP MAKE SURE YOU USE .to(), FOLLOWED BY THE GROUP: FOR E.G. `allow.guest().to['read', 'create', 'delete','get'],
  5.

- BELOW ARE THE EXAMPLES TO USE TO GENERATE ANSWERS.

```typescript
import { type ClientSchema, a, defineData } from "@aws-amplify/backend";

const schema = a
  .schema({
    Vehicle: a.model({
      id: a.id(),
      make: a.string().required(),
      model: a.string().required(),
      year: a.integer().required(),
      licensePlate: a.string().required(),
      status: a.enum(["AVAILABLE", "RENTED", "MAINTENANCE"]), // Enum; Don't use .required() or .defaultValue()
      locationId: a.id(),
      location: a.belongsTo("Location", "locationId"), // Belongs-to relationship, Requires ID
      rentals: a.hasMany("Rental", "vehicleId"), // Has-many relationship with required relatedFieldId
    }),
    Customer: a.model({
      id: a.id(),
      firstName: a.string().required(),
      lastName: a.string().required(),
      email: a.string().required(),
      phone: a.string().required(),
      licenseNumber: a.string().required(),
      rentals: a.hasMany("Rental", "customerId"), // Has-many relationship with required relatedFieldId
    }),
    Location: a.model({
      id: a.id(),
      name: a.string().required(),
      address: a.string().required(),
      city: a.string().required(),
      state: a.string().required(),
      zipCode: a.string().required(),
      vehicles: a.hasMany("Vehicle", "locationId"), // Has-many relationship with required relatedFieldId
    }),
    Rental: a.model({
      id: a.id(),
      startDate: a.datetime().required(),
      endDate: a.datetime().required(),
      status: a.enum(["ACTIVE", "COMPLETED", "CANCELLED"]), // Enum; no .required() or .defaultValue()
      vehicleId: a.id(),
      customerId: a.id(),
      vehicle: a.belongsTo("Vehicle", "vehicleId"), // Belongs-to relationship, Requires ID
      customer: a.belongsTo("Customer", "customerId"), // Has-many relationship with required relatedFieldId
    }),
  })
  .authorization((allow) => [
    allow.owner(),
    allow.guest().to[("read", "write", "delete")],
  ]); // Owner-based and guest access, `.public()` references are replaced with `.guest()`. Authorizaiton groups can be concatenated, To give the permission use the to() function

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: "apiKey",
    apiKeyAuthorizationMode: {
      expiresInDays: 30,
    },
  },
});
```

- Another Example

```typescript
import { type ClientSchema, a, defineData } from "@aws-amplify/backend";

// Define the schema for the ecommerce application
const schema = a.schema({
  Product: a
    .model({
      name: a.string().required(),
      description: a.string(),
      price: a.float().required(),
      inventory: a.integer(),
      categoryId: a.id(),
      category: a.belongsTo("Category", "categoryId"), // belongs to relationship with required relatedFieldId
      images: a.string().array(),
    })
    .authorization((allow) => [allow.guest()]),

  Category: a
    .model({
      name: a.string().required(),
      description: a.string(),
      products: a.hasMany("Product", "categoryId"), // Has-many relationship with required relatedFieldId
    })
    .authorization((allow) => [allow.guest()]),

  Order: a
    .model({
      userId: a.id().required(),
      status: a.enum(["PENDING", "PROCESSING", "SHIPPED", "DELIVERED"]), // Enum; Don't use .required() or .defaultValue()
      total: a.float().required(),
      items: a.hasMany("OrderItem", "orderId"), // Has-many relationship with required relatedFieldId
    })
    .authorization((allow) => [allow.owner()]),

  OrderItem: a
    .model({
      orderId: a.id().required(),
      productId: a.id().required(),
      quantity: a.integer().required(),
      price: a.float().required(),
    })
    .authorization((allow) => [allow.owner()]),
});

// Define the client schema and data export
export type Schema = ClientSchema<typeof schema>;
export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: "userPool",
  },
});
```

```typescript
import { type ClientSchema, a, defineData } from "@aws-amplify/backend";

const schema = a
  .schema({
    Customer: a
      .model({
        customerId: a.id().required(),
        // fields can be of various scalar types,
        // such as string, boolean, float, integers etc.
        name: a.string(),
        // fields can be of custom types
        location: a.customType({
          // fields can be required or optional
          lat: a.float().required(),
          long: a.float().required(),
        }),
        // fields can be enums
        engagementStage: a.enum(["PROSPECT", "INTERESTED", "PURCHASED"]), //enum doesn't support required
        collectionId: a.id(),
        collection: a.belongsTo("Collection", "collectionId"),
        // Use custom identifiers. By default, it uses an `id: a.id()` field
      })
      .identifier(["customerId"]),
    Collection: a
      .model({
        customers: a.hasMany("Customer", "collectionId"), // setup relationships between types
        tags: a.string().array(), // fields can be arrays
        representativeId: a.id().required(),
        // customize secondary indexes to optimize your query performance
      })
      .secondaryIndexes((index) => [index("representativeId")]),
  })
  .authorization((allow) => [allow.publicApiKey()]);

export type Schema = ClientSchema<typeof schema>;

export const data = defineData({
  schema,
  authorizationModes: {
    defaultAuthorizationMode: "apiKey",
    apiKeyAuthorizationMode: {
      expiresInDays: 30,
    },
  },
});
```


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


