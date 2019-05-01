# Persistence Framework Settings

|Setting|Description|
|-------|-----------|
|**Testing Settings**| |
| Boolean DebugMode |If true displays app console messages. The applicaiton should use this as well.|
| Boolean DebugBypassConnectivityTest |If set to true the app will assume connectivity without checking. For testing.|
| Boolean DebugDetailMode |Show a lower level of detail when debuging.|
 | | |
|**Server sync setting**s| |
| Boolean IdsAreGUIDs |Whether the Ids are GUIDDS. If Id's aren't GUIDs then they are assumed to be integers.|
| AuthType AuthType |This will be updated via the login process to record the current authentication type.|
| String BaseApiUrl |The connection string of the api, including the "/api" part.|
| Boolean HasDataSynced |This is used to support optimised syncing of initial data loads.|
| Boolean AlwaysCheckSyncServer |If set to true, "GetAll" model service requests will also try an HTTP requests to background sync data.|
| TimeSpan DefaultHttpTimeout |An error will be thrown if the server does not respond in this time. e.g. 20 seconds.|
| TimeSpan DefaultIsInSyncTime |The app will not check for changes if it last checked within this time. e.g. 10 seconds|
| Boolean RunServerSyncProcessing |To be able to turn off server syncing. Used in the initials stages of development before the server is available.|
| Int32 ServerSyncPeriodicityMs |Repeat server synchonising this number of milliseconds.|
| Int32 ServerSyncDelayMs |Delay initial sync after the app starts by this number of milliseconds.|
| String SessionHeaderKey |This allows the http header key to be changed for the session id. Default is "X-SESSION".|
| String ProcessedDateHeaderKey  |This allows the http header key to be changed for the syncing processed date id. Default is "X-PROCESSED-UTC".|
| String APIVersionHeaderKey |This allows the http header key to be changed for receiving the API version nubmer. Default is "X-API-VER".|
| String LastChangedDateKey  |This allows the key for the last changed date http parameter to be changed. Default is "ChangedSinceDateTimeUtc".|
 | | |
|**Database settings**| |
| String DatabaseName |Name to store the database in the file structure on the phone.|
| Int32 CurrentDbVersion |The database version hard coded into this version of the app. If an instance of the app encounters an older version it assumes the database has been restructured, deletes and rebuilds it.|
| String DatabaseEncryptionPassword |If the database is encrypted, this is the password.|
| String DatabaseEncryptionLicence |If the database is encrypted, this is the licence key.|
 | | |
|**Operational support settings**|  |
| String AppCenterIosKey  |AppCenter iOS key for crashing and analytics.|
| String AppCenterAndroidKey |AppCenter Android key for crashing and analytics.|
| Boolean TrackEventsTurnedOn |Whether to send out what pages and methods the user executed. Used for operational support.|
| | |
|**General settings** | |
| Boolean ErrorMessagesAsDialogs |Gets a value indicating whether the setting of an ErrorMessage in view models automatically the error in a dialog box.|

