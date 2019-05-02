# Creating a new app Using the Persistence Framework - Detailed
[go to summary](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewApp)

## Database, Models and ModelServices

blah blah


### <a id="CreateAppProject">Create a copy of the PersistenceFrameworkTemplate using with you app name.

blah blah


### <a id="CreateAppProject">For each model, create classes for: 
#### <a id="DTOs">DTOs

blah blah

#### <a id="Models">Models

blah blah

#### <a id="Models">ModelServices.

blah blah

### <a id="CreateAppProject">Add the CreateTable and DropTable methods.

blah blah



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

[go to summary](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewApp)
