# TYPES
/\*\*

- AWS Cognito User Pool Attribute Configuration
- This file defines standard and custom attributes for user management
- Reference: https://docs.aws.amazon.com/cognito/latest/developerguide/user-pool-settings-attributes.html
  \*/

/\*\*

- Standard Attributes Configuration
- Defines the structure for built-in Cognito attributes
- Implementation Guide:
- 1.  Choose required attributes carefully
- 2.  Consider which attributes should be mutable
- 3.  Plan for identity provider integration
      \*/
      export interface StandardAttributes {
      /\*\*
      - User's postal address
      - Format: String
      - Use Case: Shipping/Billing information
        \*/
        readonly address?: StandardAttribute;

/\*\*

- User's birthday
- Format: ISO 8601:2004
- Use Case: Age verification, personalization
  \*/
  readonly birthdate?: StandardAttribute;

/\*\*

- User's email address
- Format: RFC 5322
- Use Case: Primary communication, account verification
  \*/
  readonly email?: StandardAttribute;

/\*\*

- User's family name/surname
- Use Case: User identification
  \*/
  readonly familyName?: StandardAttribute;

/\*\*

- User's gender
- Use Case: Personalization, demographics
  \*/
  readonly gender?: StandardAttribute;

/\*\*

- User's given name/first name
- Use Case: User identification
  \*/
  readonly givenName?: StandardAttribute;

/\*\*

- User's locale
- Format: BCP47 language tag
- Use Case: Internationalization
  \*/
  readonly locale?: StandardAttribute;

/\*\*

- User's middle name
- Use Case: Complete user identification
  \*/
  readonly middleName?: StandardAttribute;

/\*\*

- User's full display name
- Use Case: UI display, communications
  \*/
  readonly fullname?: StandardAttribute;

/\*\*

- User's nickname
- Use Case: Casual identification
  \*/
  readonly nickname?: StandardAttribute;

/\*\*

- User's phone number
- Use Case: 2FA, communications
  \*/
  readonly phoneNumber?: StandardAttribute;

/\*\*

- User's profile picture URL
- Use Case: UI personalization
  \*/
  readonly profilePicture?: StandardAttribute;

/\*\*

- User's preferred username
- Use Case: Alternative identification
  \*/
  readonly preferredUsername?: StandardAttribute;

/\*\*

- URL to user's profile page
- Use Case: Social features
  \*/
  readonly profilePage?: StandardAttribute;

/\*\*

- User's timezone
- Use Case: Time-sensitive features
  \*/
  readonly timezone?: StandardAttribute;

/\*\*

- Last update timestamp
- Use Case: Data synchronization
  \*/
  readonly lastUpdateTime?: StandardAttribute;

/\*\*

- User's website URL
- Use Case: Professional profiles
  \*/
  readonly website?: StandardAttribute;
  }

/\*\*

- Standard Attribute Configuration
- Controls how individual attributes behave
-
- Implementation Notes:
- - Set mutable=true for attributes that sync with identity providers
- - Required attributes must be provided during registration
    \*/
    export interface StandardAttribute {
    /\*\*
    - Mutability Configuration
    - true: Value can be changed
    - false: Value is immutable after setting
      \*/
      readonly mutable?: boolean;

/\*\*

- Requirement Configuration
- true: Must be provided during registration
- false: Optional attribute
  \*/
  readonly required?: boolean;
  }

/\*\*

- Custom Attribute Interface
- For defining attributes beyond standard Cognito attributes
-
- Usage:
- 1.  Implement this interface for new attribute types
- 2.  Define data type and constraints
- 3.  Configure mutability
      \*/
      export interface ICustomAttribute {
      bind(): CustomAttributeConfig;
      }

/\*\*

- Custom Attribute Configuration
- CloudFormation configuration for custom attributes
-
- Setup Guide:
- 1.  Choose appropriate data type
- 2.  Define constraints
- 3.  Set mutability
      \*/
      export interface CustomAttributeConfig {
      readonly dataType: string;
      readonly stringConstraints?: StringAttributeConstraints;
      readonly numberConstraints?: NumberAttributeConstraints;
      readonly mutable?: boolean;
      }

/\*\*

- Base Custom Attribute Properties
- Common properties for all custom attributes
  \*/
  export interface CustomAttributeProps {
  readonly mutable?: boolean;
  }

/\*\*

- String Attribute Constraints
- Define limitations for string attributes
  \*/
  export interface StringAttributeConstraints {
  readonly minLen?: number;
  readonly maxLen?: number;
  }

/\*\*

- String Attribute Properties
- Combined interface for string attributes
  \*/
  export interface StringAttributeProps extends StringAttributeConstraints, CustomAttributeProps {}

/\*\*

- String Attribute Implementation
- Use for text-based custom attributes
  \*/
  export class StringAttribute implements ICustomAttribute {
  private readonly minLen?: number;
  private readonly maxLen?: number;
  private readonly mutable?: boolean;

constructor(props?: StringAttributeProps) {
this.minLen = props?.minLen;
this.maxLen = props?.maxLen;
this.mutable = props?.mutable;
}

bind(): CustomAttributeConfig {
return {
dataType: 'String',
stringConstraints: {
minLen: this.minLen,
maxLen: this.maxLen,
},
mutable: this.mutable,
};
}
}

/\*\*

- Number Attribute Constraints
- Define limitations for numeric attributes
  \*/
  export interface NumberAttributeConstraints {
  readonly min?: number;
  readonly max?: number;
  }

/\*\*

- Number Attribute Properties
- Combined interface for numeric attributes
  \*/
  export interface NumberAttributeProps extends NumberAttributeConstraints, CustomAttributeProps {}

/\*\*

- Number Attribute Implementation
- Use for numeric custom attributes
  \*/
  export class NumberAttribute implements ICustomAttribute {
  private readonly min?: number;
  private readonly max?: number;
  private readonly mutable?: boolean;

constructor(props?: NumberAttributeProps) {
this.min = props?.min;
this.max = props?.max;
this.mutable = props?.mutable;
}

bind(): CustomAttributeConfig {
return {
dataType: 'Number',
numberConstraints: {
min: this.min,
max: this.max,
},
mutable: this.mutable,
};
}
}

/\*\*

- Boolean Attribute Implementation
- Use for true/false custom attributes
  \*/
  export class BooleanAttribute implements ICustomAttribute {
  private readonly mutable?: boolean;

constructor(props?: CustomAttributeProps) {
this.mutable = props?.mutable;
}

bind(): CustomAttributeConfig {
return {
dataType: 'Boolean',
mutable: this.mutable,
};
}
}

/\*\*

- DateTime Attribute Implementation
- Use for timestamp custom attributes
  \*/
  export class DateTimeAttribute implements ICustomAttribute {
  private readonly mutable?: boolean;

constructor(props?: CustomAttributeProps) {
this.mutable = props?.mutable;
}

bind(): CustomAttributeConfig {
return {
dataType: 'DateTime',
mutable: this.mutable,
};
}
}

/\*\*

- Client Attributes Management
- Handles read/write attribute sets
-
- Usage:
- 1.  Create attribute sets
- 2.  Configure standard attributes
- 3.  Add custom attributes
      \*/
      export class ClientAttributes {
      private attributesSet: Set<string>;

constructor() {
this.attributesSet = new Set();
}

withStandardAttributes(attributes: StandardAttributesMask): ClientAttributes {
// Implementation for standard attributes
return this;
}

withCustomAttributes(...attributes: string[]): ClientAttributes {
// Implementation for custom attributes
return this;
}

attributes(): string[] {
return Array.from(this.attributesSet);
}
}
