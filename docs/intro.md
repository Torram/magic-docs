---
sidebar_position: 1
---

# Introduction

Magic inventory system for **El Bazaar TCG** shop.

## Getting Started

Get started by **download** or **clone [GitHub repository](https://github.com/AlexRuiz98/Magic)**.


### What you'll need

- **[Node.js](https://nodejs.org/en/download/)** version 16.14 or above:
  - When installing Node.js, you are recommended to check all checkboxes related to dependencies.
- **[Visual Studio Code](https://code.visualstudio.com/)** or your favorite editor:
  - After installed, you are recommended to install productivity extensions.
- **[MongoDB Compass](https://www.mongodb.com/products/compass)** for querying and test for MongoDB.
  - You can add both, production and development databases, but production should not be modified.

## Installing repository 

Once Node.js is installed, you can open a terminal window within the project folder and run the command:

```bash
npm run preinstall
```

You can type this command into Command Prompt, Powershell, Terminal, or any other integrated terminal of your code editor.

The command also installs all necessary dependencies you need to run project.

## Start client site

Run the development client:

```bash
cd magic-development
npm start
```

The `npm run start` command builds the website locally and serves it through a development server, ready for you to view at **http://localhost:3000/**.

## Start Server

### Development Server
```bash
npm run dev
```
The `npm run dev` command builds the API server linked to development Mongo database, ready to be visited at **http://localhost:5001/api**. 

### Production Server
:::danger Use with caution
:::
```bash
npm run prod
```
The `npm run prod` command builds the API server linked to production Mongo database, ready to be visited at **http://localhost:5001/api**. 