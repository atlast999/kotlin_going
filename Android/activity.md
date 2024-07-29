# Activity

## The concept
- App entry point
- Provides the window in which the app draws its UI
## Configuring the manifest
- `<activity>`
- `<intent-filter>`
- Declare permissions: optional, other apps need matching `<uses-permission>` to be able to start.


# Lifecycle

## Callbacks
### onCreate()
- Startup logic
- `savedInstanceState`
- `onRestoreInstanceState`
- `onSaveInstanceState`
### onStart()
- Makes visible
### onResume()
- Comes to foreground, be interactive
### onPause()
- No longer in the foreground
- Could still visible (multi-window mode)
- Should be brief
### onStop()
- No longer visible (not attached to the window manager)
- For heavy-load shutdown operation
### onDestroy()
- Finishing
- Configuration change
## States
- The further state is from Resumed, the more likely to be killed.
## Saving and restoring transient state
- `onSaveInstanceState` callback for simple, lightweight state
- `savedInstanceState` in `onCreate()` or `onRestoreInstanceState()` for restoring

# Tasks and the back stack
- Activities in the stack are never rearranged

## Task lifecycle
### Back tap behavior
- Android version < 12: Finishes the activity
- Android version >= 12: Moves the activity and its task to background
### Background and foreground
- A task can be moved to the background, all the activities in the task are stopped
- When a task comes to foreground, the activity at the top of the stack resumes
### Multi-window
- Each window can have multiple tasks
## Manage tasks
### Launch modes
- Could be defined in manifest or intent flag(higher priority) when calling startActivity()
- Mode `standard`: always creates a new instance
- Mode `singleTop`: like `standard` except when the activity already exists at the top, `onNewIntent()` will be called instead of creating a new instance.
- Mode `singleTask`: If there's an activity with same affinity, call `onNewIntent()` and destroy other activities on top of it. If there's not, create a new task.
- Mode `singleInstance`: behave like `singleTask` except that it's the only member of a task, no other activities could be launched into that task.
- Mode `singleInstancePerTask`: Can only be the root activity of a task, allowed to have multiple instance(in different tasks)
- Flag `FLAG_ACTIVITY_NEW_TASK`: like `singleTask`
- Flag `FLAG_ACTIVITY_SINGLE_TOP`: like `singleTop`
- Flag `FLAG_ACTIVITY_CLEAR_TOP`: If an instance existed, destroys all on top. If not, create a new one.
