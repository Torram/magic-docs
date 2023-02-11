---
sidebar_position: 1
---

# Introduction

This is the documental website for the "Magic" inventory system for **El Bazaar TCG** shop, developed by Fractal. The navigation tree of this document, is the same as the folder structure of the "Magic" inventory system.

## About "Magic"
Magic is a web app to control the internal inventory of **El Bazaar TCG** shop, and updates directly to its [**Shopify**](https://bazaartcg.cards/) e-commerce. Magic is a _monorepo_, that includes client and server side repos. 

- Client was developed as a typical **React** project, created with the **_create-react-app_** command. **React** is an open source **JavaScript library** for building interfaces, made by _Facebook_ and based on components. For more details visit the React web site: **[https://reactjs.org/](https://reactjs.org/)**.

- The server side was developed with **Express**. **Express** is a web framework **Node.js**, used for fast and easy development of web applications, APIs and other frameworks; made by **[OpenJS Foundation](https://openjsf.org/)**. For a detailed reference, visit Express web site **[https://expressjs.com/](https://expressjs.com/)**.

Both projects were developed in Javascript and were later migrated to **[Typescript](https://www.typescriptlang.org/)** for better maintenability.

## Getting Started

Get started by **download** or **clone [GitHub repository](https://github.com/AlexRuiz98/Magic)**.

This is the tree folder structure: 
``` ts
MAGIC
│   .gitignore
│   LICENSE
│   package-lock.json
│   package.json
│   README.md
│
├───.idea
│       .gitignore
│       Magic.iml
│       modules.xml
│       prettier.xml
│       vcs.xml
│
├───.vscode
│       launch.json
│       settings.json
│
├───client
│   │   .eslintrc.json
│   │   .prettierrc
│   │   LICENSE
│   │   package-lock.json
│   │   package.json
│   │   tsconfig.json
│   │
│   ├───.idea
│   │   │   .gitignore
│   │   │   client.iml
│   │   │   modules.xml
│   │   │   prettier.xml
│   │   │   vcs.xml
│   │   │
│   │   └───codeStyles
│   │           codeStyleConfig.xml
│   │           Project.xml
│   │
│   ├───public
│   │       asset-manifest.json
│   │       favicon.ico
│   │       index.html
│   │       manifest.json
│   │
│   └───src
│       │   App.css
│       │   App.tsx
│       │   index.css
│       │   index.tsx
│       │   react-app-env.d.ts
│       │
│       ├───atoms
│       │       orderItemsAtom.ts
│       │
│       ├───components
│       │   ├───Auth
│       │   │       AuthenticationGuard.tsx
│       │   │       SignIn.tsx
│       │   │       SignUp.tsx
│       │   │
│       │   ├───Boxes
│       │   │       BoxActions.tsx
│       │   │       BoxLink.tsx
│       │   │       CreateBoxButton.tsx
│       │   │
│       │   ├───Cards
│       │   │       BoxCard.tsx
│       │   │       CardsAutoComplete.tsx
│       │   │       CardsBox.tsx
│       │   │       CardsBoxFetch.tsx
│       │   │       CardsList.tsx
│       │   │       CardsListFetch.tsx
│       │   │       ListCard.tsx
│       │   │       UpdatePriceForCardsInListButton.tsx
│       │   │       UpdatePricesForCardsInSetButton.tsx
│       │   │       UpdateShopifyValuesForListButton.tsx
│       │   │       UpdateShopifyValuesForSetButton.tsx
│       │   │
│       │   ├───Inventory
│       │   │       InventoryItem.tsx
│       │   │
│       │   ├───InventoryLogs
│       │   │       InventoryLog.tsx
│       │   │
│       │   ├───Lists
│       │   │       AddCardToListModal.tsx
│       │   │       CreateListModal.tsx
│       │   │       ListActions.tsx
│       │   │       ListLink.tsx
│       │   │       ListRow.tsx
│       │   │       OrderItem.tsx
│       │   │       SaveOrderItemsButton.tsx
│       │   │
│       │   ├───Sets
│       │   │       SetRow.tsx
│       │   │       SetsList.tsx
│       │   │       UpdateSetsButton.tsx
│       │   │
│       │   ├───shared
│       │   │       ContentCenterDiv.tsx
│       │   │       ErrorIndicator.tsx
│       │   │       LoadingAndErrorCentered.tsx
│       │   │       LoadingButton.tsx
│       │   │       LoadingIndicator.tsx
│       │   │       NavBar.tsx
│       │   │       
│       │   └───ShopifyLogs
│       │           ShopifyLog.tsx
│       │           UpdateDatabaseFromShopifyButton.tsx
│       │
│       ├───config
│       │       basePath.tsx
│       │
│       ├───functions
│       │       sharedHeaders.ts
│       │
│       ├───hooks
│       │   ├───Auth
│       │   │       useSignIn.ts
│       │   │       useSignUp.ts
│       │   │       useUser.ts
│       │   │
│       │   ├───Boxes
│       │   │       useAllBoxes.ts
│       │   │       useBox.ts
│       │   │       useBoxesForSet.ts
│       │   │       useCreateBoxForSet.ts
│       │   │
│       │   ├───Cards
│       │   │       useAllCards.ts
│       │   │       useAutoCompleteCards.ts
│       │   │       useCardsForList.ts
│       │   │       useCardsForSet.ts
│       │   │       usePaginateCardsInList.ts
│       │   │       useUpdatePricesForCardsInList.ts
│       │   │       useUpdatePricesForCardsInSet.ts
│       │   │
│       │   ├───Inventory
│       │   │       useCreateInventoryItemForCard.ts
│       │   │       useInventoryItemForCard.ts
│       │   │       useInventoryItemsForCards.ts
│       │   │       useUpdateInventoryItem.ts
│       │   │       
│       │   ├───InventoryLogs
│       │   │       useInventoryLogs.ts
│       │   │
│       │   ├───Lists
│       │   │       useCreateList.ts
│       │   │       useList.ts
│       │   │       useLists.ts
│       │   │       useOrderItemsForCardsInList.ts
│       │   │       useSaveOrderItemsForList.ts
│       │   │       useUpdateList.ts
│       │   │       useUpdateShopifyValuesForList.ts
│       │   │
│       │   ├───Sets
│       │   │       useSets.ts
│       │   │       useUpdateSets.ts
│       │   │       useUpdateShopifyValuesForSet.ts
│       │   │
│       │   └───Shopify
│       │           useShopifyLogs.ts
│       │           useUpdateDatabaseFromShopify.ts
│       │
│       ├───screens
│       │       Authentication.screen.tsx
│       │       Box.screen.tsx
│       │       Dashboard.screen.tsx
│       │       InventoryLogs.screen.tsx
│       │       List.screen.tsx
│       │       Lists.screen.tsx
│       │       Loading.screen.tsx
│       │       Sets.screen.tsx
│       │       Settings.screen.tsx
│       │       ShopifyLogs.screen.tsx
│       │
│       ├───tokens
│       │       checkToken.ts
│       │       getRefreshToken.ts
│       │       getToken.ts
│       │       localStorageItems.ts
│       │       saveRefreshTokenInLocalStorage.ts
│       │       saveTokenInLocalStorage.ts
│       │       tokenErrors.ts
│       │
│       └───types
│               BoxType.ts
│               CardOptionType.ts
│               CardPositionType.ts
│               CardType.ts
│               index.ts
│               InventoryItemType.ts
│               InventoryLogType.ts
│               ListType.ts
│               OrderItemType.ts
│               SetType.ts
│               ShopifyLogType.ts
│               UserDataType.ts
│               UserType.ts
│
└───server
    │   .eslintignore
    │   .eslintrc.json
    │   .prettierrc
    │   index.ts
    │   package-lock.json
    │   package.json
    │   tsconfig.json
    │
    ├───.idea
    │   │   .gitignore
    │   │   modules.xml
    │   │   prettier.xml
    │   │   server.iml
    │   │   vcs.xml
    │   │
    │   └───codeStyles
    │           codeStyleConfig.xml
    │           Project.xml
    │
    ├───config
    │       config.ts
    │
    ├───environments
    │       development.env
    │       production.env
    │
    ├───src
    │   ├───constants
    │   │       constants.ts
    │   │       
    │   ├───controllers
    │   │       AuthController.ts
    │   │       BoxesController.ts
    │   │       CardsController.ts
    │   │       ErrorController.ts
    │   │       InventoryController.ts
    │   │       InventoryLogController.ts
    │   │       ListsController.ts
    │   │       MailerController.ts
    │   │       MTGController.ts
    │   │       ScryfallController.ts
    │   │       SetsController.ts
    │   │       ShopifyController.ts
    │   │       SoupsController.ts
    │   │       StarCityController.ts
    │   │       TokenController.ts
    │   │       WebhooksEventsController.ts
    │   │
    │   ├───data
    │   │       starCitySetCodes.ts
    │   │
    │   ├───interfaces
    │   │       serverInterfaces.ts
    │   │
    │   ├───jobs
    │   │       updateShopify.job.ts
    │   │
    │   ├───models
    │   │       Box.ts
    │   │       Card.ts
    │   │       InventoryItem.ts
    │   │       InventoryLog.ts
    │   │       List.ts
    │   │       Price.ts
    │   │       Set.ts
    │   │       ShopifyLog.ts
    │   │       User.ts
    │   │       WebhookEvent.ts
    │   │
    │   ├───routers
    │   │       ApiRouter.ts
    │   │       AuthRouter.ts
    │   │       BoxesRouter.ts
    │   │       CardsRouter.ts
    │   │       InventoryLogsRouter.ts
    │   │       InventoryRouter.ts
    │   │       ListsRouter.ts
    │   │       SetsRouter.ts
    │   │       ShopifyRouter.ts
    │   │
    │   └───types
    │           ApiCardType.ts
    │           BoxType.ts
    │           CardRequestType.ts
    │           CardType.ts
    │           index.ts
    │           InventoryItemType.ts
    │           InventoryLogType.ts
    │           ListType.ts
    │           OrderItemType.ts
    │           PriceType.ts
    │           SetType.ts
    │           ShopifyLogType.ts
    │           UpdateCardInformationRequestType.ts
    │           UpdateInventoryLevelRequestType.ts
    │           UserType.ts
    │           WebhookEventType.ts
    │           WebhookResponseType.ts
    │
    └───types
            jssoup.d.ts
            nodeFetchRetry.d.ts
```

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

This project is a monorepo, containing the client and the server files, with their own individual configuration, so,the **npm run preinstall** command installs both projects.
The command also installs all necessary dependencies you need to run project.

## Development Environments

To start to work in this repo, you need to move to the client or server folder in your terminal, to run the corresponding development environment.

### Start client site

To work in the development client React server, you need to run the following commands in the terminal:

```bash
cd client
npm start
```

The `npm run start` command builds the website locally and serves it through a development server, ready for you to view at **http://localhost:3000/**.

### Start Development Server
To start the development server, you need to run the following commands in the terminal:

```bash
cd server
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