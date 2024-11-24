

### README File:

# Amplify Best Practices and Reference Guide

This repository provides a comprehensive guide(**`Amplify_Reference_and_Best_Practices.md`**) designed to be used as context for Amazon Q Developer Chat in your IDE via the `@workspace` command. By integrating this guide, you can enhance Amazon Q's capabilities and accuracy in generating AWS Amplify Gen 2 code. The guide includes detailed instructions, examples, and best practices for:

- Data modeling techniques (e.g., one-to-many, one-to-one relationships)
- Schema rules for accurate configuration
- Authentication setup with external providers and custom attributes

Using this guide as context ensures that Amazon Q has access to Amplify Gen 2 best practices, enabling it to provide more relevant and accurate code suggestions.


## How to Use

Here’s the updated **How to Use** section with the requested changes:

---

## How to Use

Below is an example of how to integrate the `Amplify_Reference_and_Best_Practices.md` file into your project and use it for Amazon Q workspace context.

### Step 1: Create a New Vite App with TypeScript
1. Install Vite using the following command:
   ```bash
   npm create vite@latest my-vite-app -- --template react-ts
   cd my-vite-app
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Test the app to ensure it's running correctly:
   ```bash
   npm run dev
   ```
   Open the development server link provided (e.g., `http://localhost:5173`) to confirm the app works.

---

### Step 2: Add the Markdown File to Your Project
1. Copy the `Amplify_Reference_and_Best_Practices.md` file into your Vite project. Place it under the `src/docs` directory:
   ```
   src/docs/Amplify_Reference_and_Best_Practices.md
   ```
2. (Optional) If you want to render the markdown file in your app, install a markdown loader for Vite:
   ```bash
   npm install vite-plugin-md
   ```
3. Use the markdown file in your app by importing it into a component:
   ```tsx
   import markdownContent from './docs/Amplify_Reference_and_Best_Practices.md';

   const App = () => {
     return (
       <div>
         <h1>Amplify Guide</h1>
         <pre>{markdownContent}</pre>
       </div>
     );
   };

   export default App;
   ```

---

### Step 3: Enable Workspace Indexing in Amazon Q
1. Open the Amazon Q Developer Chat in your IDE or browser.
2. Type `@workspace` to enable workspace indexing. Amazon Q will guide you through enabling indexing for your project's directory. Follow the prompts.

---

### Step 4: Verify File Indexing
1. Once indexing is enabled, test that the markdown file is included by running a query in Amazon Q Developer Chat:
   ```bash
   @workspace search "Schema rules for enums"
   ```
2. If the file is not indexed, ensure it is in the correct directory and indexing has completed successfully.

---

### Step 5: Use the File in Queries
After successful indexing, reference the markdown file content in your queries to Amazon Q. Examples:
```bash
@workspace Explain the relationships in src/docs/Amplify_Reference_and_Best_Practices.md
```
```bash
@workspace What are the authentication rules in Amplify_Reference_and_Best_Practices.md?
```

---

### Step 6: Keep the File Updated
If you make changes to the markdown file, refresh the workspace index in Amazon Q to reflect the updates:
```bash
@workspace refresh
```

