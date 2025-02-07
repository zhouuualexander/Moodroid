# Moodroid

An app to log and view other peoples mood's. 

## Demo video
https://youtu.be/EuzAD_DIGV0
## Initial Startup

1. ensure you setup a macro to automatically format on save: https://freakycoder.com/android-studio-tips-tricks-3-save-format-the-code-e8ea291fa84f
2. Setup the UML plugin we are going to be using if you want to visualize the structure: https://stackoverflow.com/questions/17123384/how-to-generate-class-diagram-uml-on-android-studio-intellij-idea/36823007#36823007
3. Optionally install the Javadoc plugin to document your code, otherwise use the Javadoc specifications: https://binfalse.de/2015/10/05/javadoc-cheats-sheet/ We will be autogenerating documentation for our code base on commits.

## Notes

- We should really look into using something like [Dagger 2](https://github.com/google/dagger) for dependency injection to make life easier in the future.
- We should look into [Jackson](https://www.baeldung.com/jackson-object-mapper-tutorial) for data serialization between Firestore and our Models

## Project Structure (From an architecture level)

We are attempting to create a highly decoupled application to allow simple testing, and simple development in the future. Because of doing this, we have a multi-tiered architecture in respect to the code base, using some techniques in the MVC pattern.

The most important tiers we have are:

1. Design/Fragment layer (AKA controllers, control the flow of the application and user input/output and communication to services)
2. Service Layer (Public facing business logic for use case implementation of the controller and manipulation of the models)
3. Model Layer (Public facing concrete object manipulation/serialization layer)
4. Repository Layer (protected object storage layer of models and integration layer with external services)

This allows the design implementation to only know about how to talk with services and what it expects back (because of interface contracts) so backend and frontend developers can work seemlessly without worrying about breaking on-anothers code.

## Dependency Injection

We use dependency injection in this project so we can easily test our service layer. 

To use services inside Activities/Fragments you need to do the following. Lets say we created a new activity ViewFriends

1. Add your inject(ClassName className) entry into the di/ServiceComponent.java file:
```java
    void inject(ViewFriends viewFriends);
```

2. In your `onCreate()` method, ensure you inject from the DI source:
```java
    protected void onCreate(Bundle savedStateBundle) {
        super.onCreate(savedStateBundle);
        ContextGrabber.get().di().inject(ViewFriends.this);
        ...
        Log.i("DI/TEST", auth.getUsername());
    }
```

3. Now above in your properties, you can easily inject dependencies:
```java
class ViewFriends extends AppCompatActivity {

    @Inject
    AuthenticationService auth;


    protected void onCreate(Bundle savedStateBundle) {
        super.onCreate(savedStateBundle);
        ContextGrabber.get().di().inject(ViewFriends.this);
        // this will get the auth instance (which is a singleton) and output the username
        Log.i("DI/TEST", auth.getUsername());
    }

}
```


## Terminology

- **mood event**: an entry of a mood by a specific user
- **model**: a concrete construct of an object in firestore
- **repository**: an implementation of C(reate)R(ead)U(pdate)D(elete) against some sort of persistent storage, in our case most likely Firestore
- **service**: a concrete implementation of business logic to be utilized by the controllers/views/fragments/activities in our application
- **friend follow request**: a user can send a request that is sent to the recipient and they can then decide whether to accept or decline the request
- **friends mood event**: most recent mood event from friends who the user follows
- **user**: a person who operates the application
