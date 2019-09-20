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

### <a id="Server">11. Set the server Connection String.

The connection string for the API endpoint can be set in PersistenceSettings either as a once off or programatically.

```
public class PersistenceSettings : IPersistenceSettings
    {
        private static readonly string InitialBaseUrl = "https://xxx.xxx/api";
    }
```

### <a id="Headers">12. Check and modify the 'standard' HTTP header key values.

The http header keys for communicating the session id (and others) with the server is often server dependent. These can be set in PersistenceSettings.

```
    public class PersistenceSettings : IPersistenceSettings
    {
        public static string SessionHeaderKeyValue = "X-SESSION";
        public static string ProcessedDateHeaderKeyValue = "X-PROCESSED-UTC";
        public static string APIVersionHeaderKeyValue = "X-API-VER";
        public static string LastChangedDateKeyValue = "ChangedSinceDateTimeUtc";
    }
```

### <a id="Timeout">13. Check the http timeout parameters.

The default http timeout value is also in the PersistenceSettings:
```
public TimeSpan DefaultHttpTimeout { get { return new TimeSpan(0, 1, 30); } }
```

### <a id="ServerSync">14. Turn on server syncing.

In the PersistenceSettings you can turn on automatic server syncing and other syncing parameters. We often set different values for testing and production:
```
#if DEBUG
        public bool RunBatchProcessing { get { return true; } }
        public bool RunServerSyncProcessing { get { return true; } }
        public int ServerSyncPeriodicityMs { get { return 60 * 1000; } } // 60 seconds
        public int ServerSyncDelayMs { get { return 60 * 1000; } } // 60 seconds
#else
        public bool RunBatchProcessing { get { return true; } }
        public bool RunServerSyncProcessing { get { return true; } }
        public int ServerSyncPeriodicityMs { get { return 10 * 60 * 1000; } } // 10 minutes
        public int ServerSyncDelayMs { get { return 10 * 60 * 1000; } } // 10 minutes
#endif
```

## Operational Support

### <a id="AppCenter">15. Set the App Center keys.

```
    public class PersistenceSettings : IPersistenceSettings
    {
         private static readonly string AppCenterIosKeyValue = "put the ios key here";
        private static readonly string AppCenterAndroidKeyValue = "put the android key here";
    }
```

### <a id="DeviceInfo">16. Check what user device information is to be recorded on the server.

Understanding your users and what device they are one is important:
1. In a crash situation,
2. General understanding of what type of client uses your app, and
3. For asset management for corporate clients.

The follow information is sent to the server when a user logs in which can be changed in App.xaml.cs:
```
        private static void SetLoginHttpHeaders(HttpClient httpClient)
        {
            httpClient.DefaultRequestHeaders.Add("X-DEVICE-ID", String.Format("{0}", deviceId));
            httpClient.DefaultRequestHeaders.Add("X-MANUFACTURER", String.Format("{0}", DeviceInfo.Manufacturer));
            httpClient.DefaultRequestHeaders.Add("X-MODEL", String.Format("{0}", DeviceInfo.Model));
            httpClient.DefaultRequestHeaders.Add("X-DEVICE-NAME", String.Format("{0}", DeviceInfo.Name));
            httpClient.DefaultRequestHeaders.Add("X-OS", String.Format("{0}", DeviceInfo.VersionString));
            httpClient.DefaultRequestHeaders.Add("X-PLATFORM", String.Format("{0}", DeviceInfo.Platform));
            httpClient.DefaultRequestHeaders.Add("X-IDIOM", String.Format("{0}", DeviceInfo.Idiom));

            httpClient.DefaultRequestHeaders.Add("X-APP-VER", AppVersionHelper.Build(VersionTracking.CurrentVersion, VersionTracking.CurrentBuild));
        }
```

### <a id="Logging">17. Logging Services.

Crash logging is implemented using AppCenter using the keys above. Application event logging is implemented as follows:

1. PersistenceSettings.LogLevel indicates the level of logging to be performed. (0 = none, 9 = highest level of detail, default = 1)
2. Logging can dynamically be changed by setting the http header "X-LOG-LEVEL" when a user logs in.
3. The persistence layer automaticaly logs at log level = 1 app start, and all page navigations.
4. Logs are sent to the server via the LogDto class with the payload being a json string converted to base64, along with basic information such as deviceId, userId. You server should take these dto records and save them.
```
    public class LogDto 
    {
        public string AppAbbrev { get; set; }   // Derived from the Application name. e.g. "MineOpps Survey" = "MOS"
        public string SessionId { get; set; }
        public string DeviceId { get; set; }
        public string UserId { get; set; }
        public string JsonTextAsBase64 { get; set; }
        public double MaxMemoryUsed { get; set; }
        public DateTime? LastUpdatedTimestamp { get; set; }
    }
```

5. To implement your app specific logging such as data values, in your App.xaml.csw create a PersistenceRegistration.LogApplicationDataDelegate and register it with PersistenceRegistration.RegisterDelegates() (also in App.xaml.cs). This is designed to take in an object, you write the code to process it.
6. When you call LogServices.LogApplicationData(object) it will call LogApplicationDataDelegate() to process the passed in object. The result you return will be JSON serialized, converted to base64 and sent to the server. In your LogApplicationDataDelegate() you should not json serialise and you should not call LogServices.Log(). If you wish to log data from multiple sources, pass them in as a tuple and return the object you wish to be serialized. If your app uses GUIDs for Id's you may wish to truncate the Id's tot he first 8 characters.

7. If the app is restarted without being closed down properly (e.g. a StackOverflow crash), on restart a log message to this effect will be sent to the server.

8. If the Log Level is set to 5 or above LogServices will automatically take snapshots of memory useage at 10 second intervals and send the max memour usage in the previous mentioned untidy close down log.

The list of current logging methods include, and subject to change (all are static calls):

|Method|Description|
|------|------|
|LogNavigationEvent|Logs the page name. This is the method called automatically at LogLevel >= 1.|
|LogUICotrolAction|Logs a click on the specified UI control.|
|LogAction|A general method for logging something within a specified method within a specified class.|
|LogApplicationData|A delegate class that you can write to log applicaiton specific data.|
|UpdateMaxMemory|Updates the maximum memory used. This method is automatically called every 10 second at log level >= 5. It relies on PersistenceRegistration.Sleep() and PersistenceRegistration.Resume() being called in the OnStart() and OnResume() overrides in App.xaml.cs.|
|LogDeviceInfo|Logs a complete picture of the device. Automatically called on app initialisation, but only if PersistenceRegistration.Start() is called in OnStart() in App.xaml.cs|
|LogCrashEvent|Logs that a crash event has happened. It does not include the exception details (these are in AppCenter). This is automatically called whenever ExceptionServices is called to handle a crash.|
|TrackElapsedTime|Used to measure how long something takes to execute and logs the result. Used like this:|
||using (var itemToTrack = LogServices.TrackElapsedTime("name of thing")) { // some code that takes time }|

The following lists what the persistence layer logs are which log level value:

|Log Level|Description|
|-----|-----|
|1|Basic navigation navigation information including page name and method name.|
|1|Logs user timeout and Bad Request errors|
|1|Logs an untidy start ... i.e. an app start without a proper app close.|
|2|All failed logins|
|2|LogApplicationData() is only called to log application uspplied data on loge level 2 and above.|
|5|Keeps track of memory useage every 10 minutes|
	
## Additional How To's

### <a id="DBEncrypt">18. Encrypting your database.

To encrypt a database we use [Zetetic's SQLCipher product](https://www.zetetic.net/sqlcipher/) which when we last checked was USD$499 per developer per platform. For installation instructions please contact the supplier, but these two fields in PersistenceSettings will need to be set:
```
        public string DatabaseEncryptionPassword { get { throw new NotImplementedException("Database encryption not implemented"); } }
        public string DatabaseEncryptionLicence { get { throw new NotImplementedException("Database encryption not implemented"); } }
```

### <a id="PassingParams">19. Passing parameters between pages.

The following snippet show passing parameters when a row is selected on the example WidgetListPage:
```
        private async Task DoOnItemTappedCommand(Widget item)
        {
            var param = new NavigationParameters();
            param.Add("Widget", item);

            await _navigationService.NavigateAsync("WidgetPage", param, true, true);
        }
```

The following is a snippet from the example WidgetPage when receiving the passed Widget object:
```
        public override void OnNavigatingTo(INavigationParameters parameters)
        {
            base.OnNavigatingTo(parameters);
            if (parameters.ContainsKey("Widget"))
            {
                Widget = (Widget)parameters["Widget"];
            }
        }
```

### <a id="Splash">20. Updating your splash pages.

The simplest way to update splash pages on both Android and iOS is to replace the ```SplashScreen.jpg```. Splash screen are a bit of a pain ... chaining any of the other files may cause you problems. 

### <a id="SingleRun">21. Only execute a button push on a page once.

View models inherit from BaseViewModel which provides the method SingleRun(). Wrap this method around the code that you only want to have executed once and subsequent clicks will be ignored:

```
await SingleRun(async () => 
{ 
 --- code you only want to execute once
});
```

### <a id="Spinning">22. Place a __spinning wheel__ overlay on a page indicating a page is loading.

In the CustomControls folder you will find a StackLayout based XAML called Loading.xaml that is designed place a grey overlay on top of an existing page whenever a view model property called IsBusyLoading is set to true. To implement this:

1. define the property ```public bool IsBusyLoading {get; set;}``` in your view model.
2. add the Loading control to the bottom of your xaml page in a way that it covers the whole screen, in a grid for example:
   ```
    <Grid>
         <customcontrols:Loading/>
    </Grid>
   ```
3. add the 'customcontrol' definition in the heading of your xaml page:
   ```
    xmlns:customcontrols="clr-namespace:Cashbook.CustomControls"
   ```
4. set IsBusyLoading to true or false where required.


[go to summary](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewApp)
