
# 1. Service
- Run in the main thread
- Does not run in a separate process unless specify otherwise

## 1.1 Types of services

### Foreground services
- Noticeable to the user, must display a Notification
- WorkManager is able to run jobs as foreground services
### Background services
- Operations aren't directly noticed by the user
- API >= 26 imposes restrictions on running background services
### Bound services
- Offers client-server interface
- Supports interprocess communication (IPC)
- Runs as long as there's at least one component bound to it

## 1.2 A Service or a thread
- Service: component run in the background, even when the user is not interacting with the app
- Thread: for works performed while the user is interacting with the app
- Remember: service run in the same process and the main thread of the app by default

## 1.3 The basics
- onStartCommand: called when a component requests that the service be started. The service then run in the background indefinitely
- onBind: called when a component wants to bind with the service, return an IBinder for communication
- onCreate: called to perform ont-time set up.
- onDestroy: called when the service is about to be destroy, implement this to clean up
- The system could kill service when memory is low and restarts it as soon as resources become available

### Declaring a service
- Service needs to be declared in manifest file
- Service name should be left unchanged
- Ensure that service is available to only the app by set the `exported` attribute
- Provide `description` so users wouldn't accidentally kill service

## 1.4 Creating a started service
- Component call `startService()`, when started, the service run in the background indefinitely.
- The service runs even if the component that started it is destroy, stops when a component call `stopService` or by calling `stopSelf()`
- The intent is received in `onStartCommand()`

### Extending the Service class
- `onStartCommand` returns an integer that describe how the system would do in the event the system kills it
- START_NOT_STICKY: do not recreate the service
- START_STICKY: recreate the service but do not redeliver the last intent
- START_REDELIVER_INTENT: recreate the service with the last delivered intent
### Starting a service
- API >= 26, when the app is in background, a service can be started using `startForegroundService()`
- In that case, the service must call its `startForeground()` within 5 seconds.
- The service can send a result back using `PendingIntent`
### Stopping  a service
- Other component can call `stopService()`
- The service can call `stopSelf()`, with optional `startId` to avoid kill service when there is pending task

## 1.5 Creating a bound service
- Allow to interact with the service from other components
- Expose some of app's functionality to others through ICP
- Must define `IBinder`, the communication interface

## 1.6 Sending notifications to the user
- Running service can notify user using `snackbar notifications` or `status bar notifications`

## 1.7 Managing the lifecycle of a service
- Can also bind to a started service
- In such case, `stopService()` doesn't stop the service until all of the clients unbind
- Additional lifecycler callbacks:
+ onUnbind(): All clients have unbound 
+ onRebind(): A client is binding to the service with `bindService()` after `onUnbind()` has already been called


# 2. Foreground services

## 2.1 Overview
- To perform a task that is noticeable by the user (through a notification)
- Consider purpose-built APIs instead of foreground services

## 2.2 User can dismiss notification by default
- Starting from API 33, users can dismiss the notification associated with a service
- To avoid this, pass `true` into the `setOngoing()` when creating notification

## 2.3 Services that show a notification immediately
- The service is associated with a notifation that includes action buttons
- The service has a `foregroundServiceType` of `mediaPlayback`, `mediaProjection`, or `phoneCall`
- The service relates to phone calls, navigation, or media playback as defined in the notification's category
- The service set `FOREGROUND_SERVICE_IMMEDIATE` into `setForegroundServiceBehavior()` when setting up the notification
> When notification permission is denied, users still see notices in Task Manager

## 2.4 Declare foreground services in manifest
- use `android:foregroundServiceType` to declare what kind of work the service does
> API >= 29: Must declare all foreground services that use location information
> API >= 30: Must declare all foreground services that use camera or microphone
> API >= 34: Must declare all foreground services with types

## 2.5 Request the foreground service permissions
- API >= 28: Need to request the `FOREGROUND_SERVICE` permission in manifest.
- API >= 34: Need also to request the appropriate permission type

## 2.6 Foreground service prerequisites
- API >= 34: Check for specific prerequisites based on service type -> through `SecurityException`
> Must confirm that the required prerequisites are met before starting a foreground service

## 2.7 Start a foreground service
- `startForegroundService()` and then inside a service, call ServiceCompat.startForeground()
> The notification must usea priority of `PRIORITY_LOW` or higher or the system will add a message to the drawer

## 2.8 Remove a service from the foreground
- call `stopForeground()`, could also indicates whether to remove the notification
> If the service is stoped, its notification is removed

## 2.9 Handle user-initiated stopping of apps running foreground services
- User can completely stop app along with its services using Task Manager
> There's no callback for such case, check for `REASON_USER_REQUESTED` in `ApplicationExitInfo` API

## 2.10 Use purpose-built APIs instead of foreground services
- Check for existed APIs for use cases of services

## 2.11 Restrictions on starting a foreground service from the background
- target API >= 30 cannot start foreground services while the app is in background, except for a few special cases
> If a service is from another app, the restriction apply only if both target API >= 30

### Exemptions from background start restrictions
https://developer.android.com/develop/background-work/services/foreground-services#background-start-restriction-exemptions

### Restrictions on starting foreground services that need while-in-use permissions
- API >= 34 has while-in-use permission, the app in background (not in-use) cannot start a foreground service.
> Lower API allows to create a service but that service cannot access needed resources.

### Exemptions from restrisctions on while-in-use permissions
https://developer.android.com/develop/background-work/services/foreground-services#wiu-restrictions-exemptions

## 2.12 Foreground service types
//TODO


# 3. Bound services

## 3.1 Overview
- Allows other components to bind, send request, receive response, and perform IPC

### Bind to a started service
- 