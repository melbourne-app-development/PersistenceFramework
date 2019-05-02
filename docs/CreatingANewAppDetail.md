# Creating a new app using the Persistence Framework (detailed)
[go to summary](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewApp)

## <a id="CreateAppProject">1. Creating a new project

Note: this will eventually become a Visual Studio extension.

* Copy PersistenceFrameworkTemplate.zip to a folder.
* Unzip it
* Rename the folder “PersistenceFrameworkTemplate” to your project name.
* Rename the contents of the folder to your project name by:

   Windows:	Double click “RenameTemplateWin.ps1” and enter your project name.
   Mac:	Double click “RenameTemplateMac.command” and enter your project name.

When you open the solution in Visual Studio set iOS or Android as Startup Project.

* The nugget source code is in PersistenceFrameworkPlugin.zip


## 2. Database, Models and ModelServices

Fore each model, create:

### <a id="DTOs">2.1 DTOs

Note: We create a single definition for Data Transfer Objects (DTOs) and Database record definitions, suffixed "Dto".

If the name is "Widget":
* Create new WidgetDto by right clicking the “Dtos” folder.
* Inherit from BaseDto.
* Add its properties (excluding audit fields like "Id" and "LastUpdatedTimestamp" - these are in the BaseDto).

e.g.
```
public class WidgetDto : BaseDto 
{ 	public string Name  	{get; set;}
	public string Description 	{get; set;}
}
```

If you wish to exclude a property from being saved to the database, add the attribute [Ignore] on the line before.
If you wish to exclude a property from being send to the server, add the attribute [JsonIgnore] on the line before.

### <a id="Models">2.2 Models

If the name is "Widget":
* Create a new class in the Models folder called Widget.
* Inherit from BaseModel with Widget and WidgetDto as its referenced types:
```
public class Widget : BaseModel<Widget, Widget Dto> 
{
}
```
* Add the same properties as the Dto.
* Create the FromDtoMapping() and ToDtoMapping() methods.

```
    public class Widget : BaseModel<Widget, WidgetDto>
    {
        public string Name { get; set; }
        public string Description { get; set; }
 
        public Widget() : base()
        {
        }

        protected override void FromDtoMapping(WidgetDto dto)
        {
            Name = dto.Name;
            Description = dto.Description;
        }

        protected override void ToDtoMapping(WidgetDto dto)
        {
            dto.Name = Name;
            dto.Description = Description;
        }
    }
```

* For each of the models children types, add a list of children:

If a Widget has a WidgetPart as its child then add:
```
        public List<WidgetPart> WidgetPartList { get; set; }
```

Populate the children lists by overriding the following method:
```
        protected override async Task<bool> GetChildrenFromDatabaseAsync(WidgetDto dto)
        {
            WidgetPartList = await _widgetPartServices.GetAllForParentAsync(this);
            if (WidgetPartList == null)
                return false;
            return true;
        }
```

Which means you'll need to add any model services (see next section) as dependency injections in the constructor:
```
        private readonly IWidgetPartServices _widgetPartServices;
        public Widget() : base()
        {
            _widgetPartServices = _iocContainer.Resolve<IWidgetPartServices>();
        }
```

* For each of the models parent or foreign keys, add the parent object to model.
Each parent object should be 'lazy loaded' - I.e. only build the object if there is 'get' for it.

Widget hasn't been defined as having a parent record but say to had a UserId property then it would like like this:
```
        public string UserId { get; set; }
        public string UserLocalId { get; set; }

        private User _user { get; set; } = null;
        public User User
        {
            get
            {
                if (_user == null)
                {
                    Task.Run(async () =>
                    {
                        _user = await _userServices.GetAsync(UserId, UserLocalId);
                    }).Wait();
                }
                return _user;
            }

            set
            {
                _user = value;
            }
        }
```


### <a id="ModelServices">2.3 ModelServices.

If the name is "Widget":
* Create a new class in the ModelServices folder called WidgetServices.
* Inherit from BaseModelServices with Widget and WidgetDto as its referenced types.
* Create an interface above it exposing the methods you want to make available.

```
    public interface IWidgetServices
    {
        Task<Widget> GetAsync(string id, int? localId = null, bool getChildren = true);
        Task<List<Widget>> GetAllAsync(bool ignoreCache = false, bool getChildren = true);
     }

    public class WidgetServices : BaseModelServices<Widget, WidgetDto>, IWidgetServices
    {
        public WidgetServices() : base()
        {
        }
    }
```

HINT: to get the signature of a method from BaseModelService that you wish to expose:
	 Type in 'override' and chose the method from intellisense.
	 Copy the signature pattern to the interface and delete the the code.

* Register each model services as a singleton in App.xaml.cs RegisterTypes():
```
        protected override void RegisterTypes(Prism.Ioc.IContainerRegistry containerRegistry)
        {
            containerRegistry.RegisterSingleton<IWidgetServices, WidgetServices>();
        }
```


### <a id="CreateDrop">3. Add the CreateTable and DropTable methods.

Go to the App.xaml.cs CreateTables() and DropTables() and add each table.

```
        private static void CreateTables(SQLiteConnection _connection)
        {
            DataServices.CreateTable<WidgetDto>(_connection);
            DataServices.CreateTable<UserDto>(_connection);
        }

        public static void DropTables(SQLiteConnection _connection)
        {
            try { _connection.DropTable<WidgetDto>(); } catch { };
            try { _connection.DropTable<UserDto>(); } catch { };
        }
```

### <a id="TestData">4. [Add a method to create test data](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs) (if required).

Go to App.xaml.cs and add any test data.
```
        public static void TestDataSetup(SQLiteConnection _connection)
        {
             WidgetDto widgetDto = new WidgetDto();

            widgetDto.Name = "Aqua one";
            widgetDto.Description = "This is a aqua widget";
            widgetDto.WidgetType = (int)WidgetType.Aqua;
            _connection.Insert(widgetDto);

            widgetDto.Name = "Black one";
            widgetDto.Description = "This is a black widget";
            widgetDto.WidgetType = (int)WidgetType.Black;
            _connection.Insert(widgetDto);
```

### <a id="CurrentDBVersion">5. [Instruct the Persistence Framework to rebuild the database schema.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

By incrementing the CurrentDbVersionValue property in the PersistenceSettings class the database will be dropped and recreated with the schema.
```
        private static readonly int CurrentDbVersionValue = 4;        // Change this to force rebuild of database.
```

## Views and ViewModels

For each page:

### <a id="CreateView">6. Create the xaml page and code behind.

Note: manually create a XAML page that inherits from a base page is a a bit tricky to explain. Cloning the WidgetPage or WidgetListPage and changing it's name is easier. Use whichever of WidgetPage or WidgetListPage is the closest to your new page format:

* Create a Forms XAML page with code behind in the normal way, using your new page name.
* Copy and paste the code behind of WidgetPage to you new code behind, changing "WidgetPage" to your new name.
* Copy and paste the WidgetPage.xaml code to your new page, changing "WidgetPage" to your new name. 

Add your unique design to the XAML.

blah

### <a id="ViewModel">7. [Create a ViewModel.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

For each page:

* Create a view model in the ViewModels folder with the same name as your view, except with 'ViewModel' as a suffix. eg
```WidgetPage.xaml`` would have a view model ```WidgetPageViewModel```

* Clone either WidgetPageViewModel or WidgetListPageViewModel, global change the name to you page name, and modify the properties, commands and parameter passing as required.

## <a id="RegisterForNavigation">8. Add each as a navigatable page.

For each page, update the App.xaml.cs RegistrationTypes() method and register each page for navigation:
```
        protected override void RegisterTypes(Prism.Ioc.IContainerRegistry containerRegistry)
        {
            containerRegistry.RegisterForNavigation<WidgetPage>();
        }
```

### <a id="TabPage">9. For each page you want on the tab bar update TabPage.xaml.
```
	<local:WidgetListPage />
	<local:AboutPage />
```

### <a id="Menu">10. Add a page to the menu.

Open the class:
```Views/Menu/MenuDefinitions.cs``` and change the InitializeMenu() method.

## Server Interactions

Note: this assumes that you are using a REST interface between your server and app. If you are not then talk to us ... advice on what to do is site specific.

### <a id="ViewModel">11. Set the server Connection String.

[to be updated]

### <a id="ViewModel">12. Check and modify the 'standard' HTTP header key values.

[to be updated]

### <a id="ViewModel">13. Check the http timeout parameters.

[to be updated]

### <a id="ViewModel">14. Check the API and APP http header keys.

[to be updated]

### <a id="ViewModel">15. Turn on server syncing.

[to be updated]

### <a id="ViewModel">16. Set the auto server sync parameters.

[to be updated]

### <a id="ViewModel">17. Update what has to be automatically synced.

## Operational Support

### <a id="ViewModel">18. Set the App Center keys.

[to be updated]

### <a id="ViewModel">19. Check what user device information is to be recorded on the server.

## Additional How To's

### <a id="ViewModel">20. Encrypting your database.

[to be updated]

### <a id="ViewModel">21. Passing parameters between pages.

[to be updated]

### <a id="ViewModel">22. Updating your splash pages.

[to be updated]

### <a id="ViewModel">23. Only execute a button push on a page once.

[to be updated]

### <a id="ViewModel">24. Place a __spinning wheel__ overlay on a page indicating a page is loading.

[to be updated]

### <a id="ViewModel">24. Optimising user experience on very first app load that has a large amount of data.

[go to summary](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewApp)
