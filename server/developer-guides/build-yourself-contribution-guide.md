---
description: >-
  How to build the server on your own system, or how to contribute to the
  project
---

# Build Yourself / Contribution Guide

## Pre-requisites

1. NodeJS: [https://nodejs.org/en/](https://nodejs.org/en/)
2. Yarn Package Manager: [https://yarnpkg.com/](https://yarnpkg.com/)
3. Git: [https://git-scm.com/](https://git-scm.com/)
4.  Code editor. Here are a few recommended editors:

    * [Visual Studio Code](https://code.visualstudio.com/download)
    * [Android Studio](https://developer.android.com/studio)
    * [Atom](https://atom.io/)

    Once you have a code editor installed, remember to install all of the required plugins/extensions such as the following:

    * Typescript
    * ESLint
    * Prettier

## Development

1. Clone the repository
   * `git clone git@github.com:BlueBubblesApp/BlueBubbles-Server.git`
2. Navigate into the repository on your local machine
   * `cd BlueBubbles-Server`
3. Install the server dependencies
   * `yarn`
4. Run the dev server (this will start both the renderer and server)
   * `yarn run start-dev`

### Contribution Guide

We encourage all contributions to this project! All we ask are you follow these simple rules when contributing:

1. Write clean code
2. Comment your code
3. Follow Typescript and React best practices
   * [Typescript Best Practices](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html)
   * [React Best Practices](https://reactjs.org/docs/thinking-in-react.html)

## Structure / Directory Map

### Back-end

* Backend Code: `/src/main`
* BlueBubbles Server: `/src/main/server/index.ts`
  * **Description**: This class is the main entry point to the whole backend. This classes manages the ngrok connection, the config database connection, the socket.io connection, and handles any inter-process-communications (IPC) from the "renderer" (UI).
* BlueBubbles Types: `/src/main/server/types.ts`
  * **Description**: Holds the types for the BlueBubbles server. Defines what fields are required and optional, as well as which keys are required in a request/response
* iMessage Library: `/src/main/server/api/imessage`
  * **Description**: This directory contains all of the classes and code needed to communicate with the iMessage Chat database. We use TypeORM as our decorator library for connecting to the database. This allows us to request information from the database in an object-oriented way
* iMessage Database Models: `/src/main/server/api/imessage/entity`
  * **Description**" This directory contains all of the entities within the iMessage Chat database. These are also known as database "models". They defined the columns and their types. These files determine what "properties" are associated with each entity, and what we can get from the database table
* iMessage Database Transformers: `/src/main/server/api/imessage/transformers`
  * **Description**: This directory contains what we cann "transformers". They allow us to automatically convert values that we get from the database, as well as insert into the database. These are super helpful for the iMessage database. One instance they really help is with date conversions. iMessage stores dates as seconds since 2001. This is opposed to a "normal" seconds since EPOCH. On top of that, they switched the date formats from v10.12 to v10.13. The transformers allows us to seemlessly convert those date without having to worry about it in our "fetching" code. There are also transformers for integers to booleans as well as reaction IDs to strings
* iMessage Database Listeners: `/src/main/server/api/imessage/listeners`
  * **Description**: These classes are "listeners". They allow you to listen on certain things. For instance, the MessageListener allows you to "listen" for new messages. It does this by polling the database for new information, then "emitting" that message to whoever is listening. These classes inherit the JS EventEmitter class
* Filesystem Lib: `/src/main/fileSystem`
  * **Description**: This class allows us, and helps us, interact with the MacOS filesystem. Mostly, reading/writing files to the app's directory.
* Filesystem Scripts: `/src/main/fileSystem/scripts.ts`
  * **Description**: File that holds the Apple Scripts that get executed when sending a message, creating a chat, etc.
* Server Helpers: `/src/main/helpers`
  * **Description**: Some helpers for executing actions from the client, or sending results back to the client
* Socket Server: `/src/main/services/socket`
  * **Description**: The socket server that handles all incoming requests from connected sockets. Allows clients to request for bulk information such as getting chats, messages, etc.
* FCM Server: `/src/main/services/fcm`
  * **Description**: This class will handle all communication with Google Firebase. This includes registering devices with FCM, sending notifications, etc.

### Front-end

* Frontend Code: `/src/renderer`
* Fronend Layouts: `/src/renderer/layouts`
  * **Description**: This directory contains the layouts for the frontend. In essence, these are the "containers" for all the pages. For example, the `Admin` layout in that directory is what shows the sidebar navigation, and all of its' children
* Frontend Views: `/src/renderer/views`
  * **Description**: The components in this directory are the "views" or "pages" within a layout. For instance, you'll find the configuration page here. It is a child within the Admin layout.
* Frontend Components: `/src/renderer/components`
  * **Description**: These are the re-usable components that you may use anywhere within the frontend. These may be "cards", or "buttons", or any other custom UI element.
* Frontend Entrypoint: `/src/renderer/app.tsx`
