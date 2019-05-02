# Creating a new app Using the Persistence Framework

To create an app using the Persistence Framework you need to firstly:
* Decide on your app name.
* Decide on the models that it will use and how they relate to each other.
* Decide on the pages that need to be created.
* Decide on the interactions required with the server.

Armed with this information you can create an app in one of two ways:
1. Data first, or
2. View first (prototyping)

Our preference is data first because we are IT people and we always define our data, but an equally valid approach is prototyping. Prototyping is:
1. Follow the Views and ViewModels instruction below but don't have any bindings to show data (use mocked up text).
2. Add the ViewModels but within these only include commands (e.g button pusses) and navigation.
3. At a later time follow the Database, Models and ModelServices instructions below and progressively add them to your app.
4. Update your pages one at a time until your app "becomes alive".

The following instructions are assuming a data first approach.

## Database, Models and ModelServices

1. [Create a copy of the PersistenceFrameworkTemplate using with you app name.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#CreateAppProject)
2. For each model, create classes for 

2.1. [DTOs](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

2.2. [Models](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#Models) and

2.3. [ModelServices.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#Models)

3. [Add the CreateTable and DropTable methods.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
4. [Add a method to create test data](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs) (if required).
5. [Instruct the Persistence Framework to rebuild the database schema.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)


## Views and ViewModels

Note: leave the login page until last.
For each page:

{:start="6"}

6. Create the xaml page and code behind inheriting from BaseContentPage.
6.1. [if the page includes a ListView control, use WidgetListPage as a template.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
6.2. [otherwise use WidgetPage as a template.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
7. [Create a ViewModel using one of WidgetListPageViewModel or WidgetPageViewModel as templates.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
8. [Add each as a navigatable page.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
9. [For each page you want on the tab bar update TabPage.xaml.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
10. [For each page you want on the menu update Menu Definitions.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

## Server Interactions

Note: this assumes that you are using a REST interface between your server and app. If you are not then talk to us ... advice on what to do is site specific.

{:start="11"}
11. [Set the server Connection String.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
12. [Check and modify the 'standard' HTTP header key values.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
13. [Check the http timeout parameters.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
14. [Check the API and APP http header keys.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
15. [Turn on server syncing.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
16. [Set the auto server sync parameters.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
17. [Update what has to be automatically synced.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

## Operational Support

{:start="18"}
18. [Set the App Center keys.
19. [Check what user device information is to be recorded on the server.

## Additional How To's

{:start="20"}
20. [Encrypting your database.(https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
21. [Passing parameters between pages.(https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
22. [Updating your splash pages.(https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
23. [Only execute a button push on a page once.(https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
24. [Place a __spinning wheel__ overlay on a page indicating a page is loading.(https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
24. [Optimising user experience on very first app load that has a large amount of data.(https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

