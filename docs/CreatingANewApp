# Creating a New App

## Project settup 
Note: A Marketplace Template will be available soon.

1. Contact [support@melbappdev.com](mailto:support@melbappdev.com) to get a copy of the template.
2. Unzip the template
3. Rename the PersistenceFrameworkTemplate folder name to your new app name.
4. Run the RenameTemplateMac or RenameTemplateWin script to rename the namespaces and projects to your new app name.
5. Build and run.

## Models, dataabase records and data transfer objects
For each model in the system create the following using Widget.cs, WidgetDto.cs and WidgetServices.cs as templates:
* A database record / data transfer object called Dtos/__name__Dto.cs using WidgetDto.cs as a template
* A model called Models/__name__.cs using Widget.cs as a template
* A model services class called ModelServices/__name__Services.cs using WidgetServices.cs as a template

Note:
* Each model needs to encapsulate its parents using lazy loading and its children for maximum code reuse.
* Each model services class will be implemented as a singlton so register them in App.xaml.cs  ->  RegisterTypes()

Once done, update the CreateTables(), DropTables() and TestDataSetup() methods in App.xaml.cs.
Increment the PersistenceSettings.CurrentDbVersionValue to cause the database scheme to be rebuilt.

## Views and ViewModels
For each page create views and viewmodels using the WidgetListPage and WidgetPage as templates.
New pages need to be registered in App.xaml.cs  ->  RegisterTypes()

## Configuration
There are a number of possible configuration points:
* PersistenceSettings.cs implement the IPersistenceSettings interface for things like your connection string and AppCenter keys.
* HTTP header information about the user's device can be set in App.xaml.cs  ->  SetLoginHttpHeaders().
* Styles can be set in App.xaml in the normal Xamarin Forms manner.
* Replace SplashScreen.jpg and icon.png in both projects to personalise your app.
