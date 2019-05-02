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
* Follow the Views and ViewModels instruction below but don't have any bindings to show data (use mocked up text).
* Add the ViewModels but within these only include commands (e.g button pusses) and navigation.
* At a later time follow the Database, Models and ModelServices instructions below and progressively add them to your app.
* Update your pages one at a time until your app "becomes alive".

The following instructions are assuming a data first approach.

__Database, Models and ModelServices__

1. Create a copy of the PersistenceFrameworkTemplate using with you app name.
2. For each model, create classes for 
2.1 [DTOs](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail.html)
2.2 Models and 
2.3 ModelServices.
3. Add the CreateTable and DropTable methods.
4. Add a method to create test data (if required).
5. Instruct the Persistence Framework to rebuild the database schema.


__Views and ViewModels__

Note: leave the login page until last.
For each page:

6. Create the xaml page and code behind inheriting from BaseContentPage.
	6.1 if the page includes a ListView control, use WidgetListPage as a template.
	6.2 otherwise use WidgetPage as a template.
7. Create a ViewModel using one of WidgetListPageViewModel or WidgetPageViewModel as templates.
8. Add each as a navigatable page.
9. For each page you want on the tab bar update TabPage.xaml.
10. For each page you want on the menu update Menu Definitions.

__Server Interactions__

Note: this assumes that you are using a REST interface between your server and app. If you are not then talk to us ... advice on what to do is site specific.

11. Set the server Connection String.
12. Check and modify the 'standard' HTTP header key values.
13. Check the http timeout parameters.
14. Check the API and APP http header keys.
15. Turn on server syncing.
16. Set the auto server sync parameters.
17. Update what has to be automatically synced.

__Operational Support__

16. Set the App Center keys.
17. Check what user device information is to be recorded on the server.

__Additional How To's__

18. Encrypting your database.
19. Passing parameters between pages.
20. Updating your splash pages.
21. Only execute a button push on a page once.
22. Place a __spinning wheel__ overlay on a page indicating a page is loading.
23. Optimising user experience on very first app load that has a large amount of data.

