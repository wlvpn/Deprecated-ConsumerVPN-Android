# Diagnostics Gathering

This document will describe how you can gather diagnostics to collect and debug information from the 
SDK.

## `Timber library`

This document explains how to use [Timber][1] as tool to gather diagnostics

This is a logger with a small, extensible API which provides utility on top of Android's normal 
Log class.

### `Usage`

- Add the dependency to the project

In your project root level `build.gradle` file, add this line to your `allprojects` block:

```groovy
allprojects {
    repositories {
        mavenCentral()
    }
}
```

Edit your module-level `build.gradle` file and add your new dependency:

```groovy
dependencies {
    implementation 'com.jakewharton.timber:timber:5.0.1'
}
```

After the dependency has been added you only need 2 simple steps to use the logger:
- Install any Tree instances you want in the onCreate of your application class.

```kotlin
class ExampleApp : Application() {
    fun onCreate() {
        super.onCreate()
        if (BuildConfig.DEBUG) {
            plant(DebugTree())
        } else {
            Timber.plant(CrashReportingTree())
        }
    }

    /** A tree which diagnostics important information for crash reporting.  */
    private class CrashReportingTree : Tree() {
        override fun log(priority: Int, tag: String?, @NonNull message: String, t: Throwable?) {
            if (priority == Log.VERBOSE || priority == Log.DEBUG) {
                return
            }
            FakeCrashLibrary.log(priority, tag, message)
            if (t != null) {
                if (priority == Log.ERROR) {
                    FakeCrashLibrary.logError(t)
                } else if (priority == Log.WARN) {
                    FakeCrashLibrary.logWarning(t)
                }
            }
        }
    }
}

//FakeCrashLibrary
/** Not a real crash reporting library!  */
class FakeCrashLibrary private constructor() {
    init {
        throw AssertionError("No instances.")
    }

    companion object {
        fun log(priority: Int, tag: String?, message: String?) {
            // TODO add log entry to circular buffer.
        }

        fun logWarning(t: Throwable?) {
            // TODO report non-fatal warning.
        }

        fun logError(t: Throwable?) {
            // TODO report non-fatal error.
        }
    }
}
```

- Call [Timber's][1] static methods everywhere throughout your app. 

```kotlin
//Sample usage of Timber library
class DemoActivity : Activity(), View.OnClickListener {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: DemoActivityBinding = DemoActivityBinding.inflate(layoutInflater)
        setContentView(binding.getRoot())
        Timber.tag("TestTag")
        Timber.d("Activity Created")
        binding.hello.setOnClickListener(this)
        binding.hey.setOnClickListener(this)
        binding.hi.setOnClickListener(this)
    }

    fun onClick(v: View) {
        val button = v as Button
        Timber.i("A button with ID %s was clicked to say '%s'.", button.id, button.text)
        Toast.makeText(this, "Check logcat for a greeting!", LENGTH_SHORT).show()
    }
}
```

### `Lint`

By default [Timber][1] comes with a set of Lint rules that allows you to detect problems with your 
app. Some examples are added next:

- TimberArgCount (Error) - Detects an incorrect number of arguments passed to a Timber call for the 
specified format string.

```kotlin
Timber.d("Hello %s %s!", firstName)
```
```logcatfilter
Example.java:35: Error: Wrong argument count, format string Hello %s %s! requires 2 but format call 
supplies 1 [TimberArgCount]
```

- TimberArgTypes (Error) - Detects arguments which are of the wrong type for the specified format 
string.

```kotlin
Timber.d("success = %b", taskName)
```
```logcatfilter
Example.java:35: Error: Wrong argument type for formatting argument '#0' in success = %b: conversion 
is 'b', received String (argument #2 in method call) [TimberArgTypes]
```

- TimberTagLength (Error) - Detects the use of tags which are longer than Android's maximum length 
of 23.

```kotlin
Timber.tag("TagNameThatIsReallyReallyReallyLong").d("Hello %s %s!", firstName, lastName)
```
```logcatfilter
Example.java:35: Error: The logging tag can be at most 23 characters, was 35 
(TagNameThatIsReallyReallyReallyLong) [TimberTagLength]   
```

- LogNotTimber (Warning) - Detects usages of Android's Log that should be using Timber

```kotlin
Log.d("Greeting", "Hello " + firstName + " " + lastName + "!")
```
```logcatfilter
Example.java:35: Warning: Using 'Log' instead of 'Timber' [LogNotTimber]
```

You can also reference [Timber][1] documentation and sample app and explore the lint error provided
in more dept.

Class: [KotlinLintActivity.kt][2]


[1]: https://github.com/JakeWharton/timber
[2]: https://github.com/JakeWharton/timber/blob/trunk/timber-sample/src/main/java/com/example/timber/ui/KotlinLintActivity.kt