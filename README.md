# coupon-generator
# Global loading spinner to an Angular application

Global loading spinner to an Angular application (2023 Quick Tutorial)

So what's the quickest way to add a global loading spinner to your Angular application?

angular%20loading%20spinner%20demo

When it comes to creating a loading spinner for your Angular application there are many ways to skin this cat.

You could subscribe to Angular's router events.

You could also use an HTTP interceptor.

You can use a spinner inside your components - for example - and loading spinner on an Angular material button.

Or you could create a global spinner.

Whatever the case, here's the quickest way to add an Angular loading spinner that I know of.

Today, you're going to learn an easy way to set up a global spinning loader in Angular. This loading spinner that will be shown while your app is sending and receiving data from an API server.

First Step: Create a loader service to manage the loading state
We'll be using the Angular CLI a lot so get your terminal (or command prompt) ready.

Here's the first command we'll use to generate our loading service. This service will be used as a mini state-manager for our application.

It would be better to use a state management library to manage the loading state of our application. But since I promised to make this fast, here's how to leap-frog the sticky work of getting a state manager set up.

ng generate service loader
Here's the code for our new service.

import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class LoaderService {

  private loading: boolean = false;

  constructor() { }

  setLoading(loading: boolean) {
    this.loading = loading;
  }

  getLoading(): boolean {
    return this.loading;
  }
}
Second Step: Create a component with an overlay spinner
This is the component that will create the overlay effect and display a loading spinner in the middle of the page.

ng generate component spinner
Here's the code for our component's Typescript file.

import { Component, ViewEncapsulation } from '@angular/core';
import { LoaderService } from '../loader.service';

@Component({
  selector: 'app-spinner',
  templateUrl: './spinner.component.html',
  styleUrls: ['./spinner.component.css'],
  encapsulation: ViewEncapsulation.ShadowDom
})
export class SpinnerComponent {
  constructor(public loader: LoaderService) { }
}
As well as the code for our component's HTML file.

<div *ngIf="this.loader.getLoading()" class="cssload-container">
    <div class="cssload-speeding-wheel"></div>
</div>
And last of all, the CSS styles and animations for the loading spinner.

.cssload-container {
  position: fixed;
  width: 100%;
  left: 0;
  right: 0;
  top: 0;
  bottom: 0;
  background-color: rgba(255, 255, 255, 0.7);
  z-index: 9999;
}

.cssload-speeding-wheel {
  content: "";
  display: block;
  position: absolute;
  left: 48%;
  top: 40%;
  width: 63px;
  height: 63px;
  margin: 0 auto;
  border: 4px solid rgb(0, 0, 0);
  border-radius: 50%;
  border-left-color: transparent;
  border-right-color: transparent;
  animation: cssload-spin 500ms infinite linear;
  -o-animation: cssload-spin 500ms infinite linear;
  -ms-animation: cssload-spin 500ms infinite linear;
  -webkit-animation: cssload-spin 500ms infinite linear;
  -moz-animation: cssload-spin 500ms infinite linear;
}

@keyframes cssload-spin {
  100% {
    transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-o-keyframes cssload-spin {
  100% {
    -o-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-ms-keyframes cssload-spin {
  100% {
    -ms-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-webkit-keyframes cssload-spin {
  100% {
    -webkit-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}

@-moz-keyframes cssload-spin {
  100% {
    -moz-transform: rotate(360deg);
    transform: rotate(360deg);
  }
}
Third Step: Add our new spinner component
We will also need to add the new spinner component to the app.component.html file.

<app-spinner></app-spinner>
Fourth Step: Create an HTTP Interceptor
Our HTTP interceptor will be used to set the state of our loading spinner.

We'll begin by generating it.

ng generate interceptor loading
Then, we'll modify our HTTP interceptor to spy on all outgoing requests and flip the state to loading. As soon as all outgoing requests have completed, it will flip the loading state back to false.

Here's the code.

import { Injectable } from '@angular/core';
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor
} from '@angular/common/http';
import { Observable } from 'rxjs';
import { finalize } from 'rxjs/operators';
import { LoaderService } from './loader.service';

@Injectable()
export class LoadingInterceptor implements HttpInterceptor {

  private totalRequests = 0;

  constructor(
    private loadingService: LoaderService
  ) {}

  intercept(request: HttpRequest<unknown>, next: HttpHandler): Observable<HttpEvent<unknown>> {
    console.log('caught')
    this.totalRequests++;
    this.loadingService.setLoading(true);
    return next.handle(request).pipe(
      finalize(() => {
        this.totalRequests--;
        if (this.totalRequests == 0) {
          this.loadingService.setLoading(false);
        }
      })
    );
  }
}
We are almost done

Last Step: Register your new HTTP interceptor
All that's left to do now is to register our HTTP interceptor inside the app.module.ts file.

import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';
import { HttpClientModule, HTTP_INTERCEPTORS } from '@angular/common/http';

import { AppRoutingModule } from './app-routing.module';
import { AppComponent } from './app.component';
import { TodosComponent } from './todos/todos.component';
import { SpinnerComponent } from './spinner/spinner.component';
import { LoadingInterceptor } from './loading.interceptor';

@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    BrowserModule,
    AppRoutingModule,
    HttpClientModule
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS, useClass: LoadingInterceptor, multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
We're done!

The Final Result 👏🏻 👏🏻
And here's a demo of the result.

angular%20loading%20spinner%20demo

What do you think of this global loading spinner? Is there any way it could be improved?

Or maybe you know of an easier or quicker way?

Please let me know in the comments below.
