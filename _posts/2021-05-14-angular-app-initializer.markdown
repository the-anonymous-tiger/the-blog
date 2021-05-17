---
layout: single
title: "What is Angular's APP_INITIALIZER token?"
date: 2021-05-14 19:57:23 -0400
categories: angular
---

**tldr** The `APP_INITIALIZER` token allows to provide functions that will be injected at application startup and executed during app initialization.

Let's talk about one of Angular's great features, the APP_INITIALIZER token.

What is this token? 

Well based on Angular's official [documentation](https://angular.io/api/core/APP_INITIALIZER) it allows: "The provided functions are injected at application startup and executed during app initialization."

This might not make a lot of sense if you aren't familiar with Angular's dependency injection (DI). Simply put DI is a "A design pattern and mechanism for creating and delivering some parts of an application (dependencies) to other parts of an application that require them.", for more information you can check out Angular's [dependency injection guide](https://angular.io/guide/dependency-injection). Now the APP_INITIALIZER is a DI token, meaning it's a token used to associate a dependency to a provider.

That's a complicated way to say the APP_INITIALIZER allows you to execute code during the application bootstrap. One of the main reasons to use this is so that data required by the application is available on startup.

Let's take a look at a few examples to better understand the APP_INITIALIZER's capabilities and how to use it.

The following example shows the syntax to use the APP_INITIALIZER token for DI. Here, the `initializeApp` function will execute on application bootstrap and simply render "In initialization". The following example only shows one function being injected but it's possible to inject multiple.

```typescript
// Function to run on application bootstrap
function initializeApp(): void {
  console.log('In initialization');
}

@NgModule({
 imports: [BrowserModule],
 declarations: [AppComponent],
 bootstrap: [AppComponent],
 providers: [{
   provide: APP_INITIALIZER,
   useFactory: () => initializeApp,
   multi: true
  }]
 })
export class AppModule {}
```

Admittedly, this example doesn't provide much value. In most cases you will want to use the token to pull data from a server or execute operations required by other part of the application. For this reason, if any of the functions returns a Promise or an Observable, the initialization will not complete until the Promise is resolved or the Observable is completed.

We can see how this would look like with the following example. Here you can see our function returns a Promise that will resolve after 5 seconds.

```typescript
function initializeApp(): Promise<any> {
    setTimeout(() => {
      console.log('In initialization');
      resolve();
    }, 5000);
  });
}
```

The last two examples were aimed at understading the syntax of of APP_INITIALIZER functions and how it behaves. Now let's have a look at a use case where you might want to use the APP_INITIALIZER token.

One of the main reasons to use the APP_INITIALIZER token is to load data or configurations that need to be available right after the applications bootstrap. The following code demonstrates how to load data from a server during the application bootstrap. The `HttpClient` was added to the "deps" section of the provider object iin order for us to be able to use it in our function.

```typescript
function initializeApp(httpClient: HttpClient): Observable<any> {
 return httpClient.get("https://yourApi.com/api/user")
   .pipe(
      tap(user => { ... })
   )
}

@NgModule({
 imports: [BrowserModule],
 declarations: [AppComponent],
 bootstrap: [AppComponent],
 providers: [{
   provide: APP_INITIALIZER,
   useFactory: initializeApp,
   deps: [HttpClient]  // Dependencies for the "initializeApp" function
   multi: true
  }]
 })
export class AppModule {}
```

I hope you now have a better understanding of Angular's APP_INITIALIZER token for more information you can check out Angular's official [documentation](https://angular.io/api/core/APP_INITIALIZER) or feel free to reach out.

the-anonymous-tiger

