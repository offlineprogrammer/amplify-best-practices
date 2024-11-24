
### README File

# Amplify Best Practices and Reference Guide

This repository provides a comprehensive guide(**`Amplify_Reference_and_Best_Practices.md`**) designed to be used as context for Amazon Q Developer Chat in your IDE via the `@workspace` command. By integrating this guide, you can enhance Amazon Q's capabilities and accuracy in generating AWS Amplify Gen 2 code. The guide includes detailed instructions, examples, and best practices for:

- Data modeling techniques (e.g., one-to-many, one-to-one relationships)
- Schema rules for accurate configuration
- Authentication setup with external providers and custom attributes

Using this guide as context ensures that Amazon Q has access to Amplify Gen 2 best practices, enabling it to provide more relevant and accurate code suggestions.



## How to Use

Below is an example of how to integrate the `Amplify_Reference_and_Best_Practices.md` file into your project and use it for Amazon Q workspace context.

### Step 1: Create a New App

1. Run the following command to use Vite to create a React application::

   ```bash
   npm create vite@latest amplify_sample_app   -- --template react-ts -y 
   cd amplify_sample_app
   npm install
   ```

2. Open the project in Visual Studio Code:

   ```bash
   code . -r
   ```

---

### Step 2: Add the Markdown File to Your Project

1. Add AWS Amplify to your project by running the following command in your project root:

```bash
npm create amplify@latest -y
```

This will set up Amplify in your project and initialize the required configuration files.

1. Copy the Amplify_Reference_and_Best_Practices.md file into the root folder of your project. The project structure should look like this:

![project-structure](project_folders.png)

This ensures that the markdown file is accessible and that Amplify is integrated into the project for any further development or configuration.

---

### Step 3: Enable Workspace Indexing and Verify File Indexing in Amazon Q

1. Open the Amazon Q Developer Chat in your IDE.

2. Type `@workspace` to enable workspace indexing. Amazon Q will guide you through enabling indexing for your project's directory. 


---

### Step 4: Use the File in Queries

After successful indexing, reference the markdown file content in your queries to Amazon Q. Examples:

```bash

@workspace follow Amplify_Reference_and_Best_Practices.md, Design a backend schema for a coworking space booking system using Amplify Gen 2. Include models for users, spaces, bookings, and amenities.demonstrate how to get a list of spaces

```



---

