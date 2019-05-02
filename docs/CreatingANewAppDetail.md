# Creating a new app - detailed
[go to summary](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewApp)

## <a id="CreateAppProject">1. Creating a new project

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


### <a id="Models">2.3 ModelServices.

blah

## <a id="CreateAppProject">3. Add the CreateTable and DropTable methods.

blah




blah

## <a id="CreateAppProject">4. [Add a method to create test data](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs) (if required).

blah

## <a id="CreateAppProject">5. [Instruct the Persistence Framework to rebuild the database schema.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)


## Views and ViewModels

Note: leave the login page until last.
For each page:

{:start="6"}

blah

## <a id="CreateAppProject">6. Create the xaml page and code behind inheriting from BaseContentPage.

blah

### <a id="CreateAppProject">6.1. [if the page includes a ListView control, use WidgetListPage as a template.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

blah

### <a id="CreateAppProject">6.2. [otherwise use WidgetPage as a template.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)

blah

## <a id="CreateAppProject">7. [Create a ViewModel using one of WidgetListPageViewModel or WidgetPageViewModel as templates.](https://melbourne-app-development.github.io/PersistenceFramework/CreatingANewAppDetail#DTOs)
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
