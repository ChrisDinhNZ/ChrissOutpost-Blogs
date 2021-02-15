![Blog Header Image](assets/create-and-publish-an-angular-library-to-npm-package-registry.png "Blog Header Image")

# Create and publish an Angular library to NPM package registry

Creating an Angular library is a great way to break an application's logic into modules with specific functionalities. It is also less coupled with the applications that consumes it therefore provides better code reuse and software maintainability.

In this blog we will work through the following:
* Create library
* Publish library
* Import and use library

### Create library

To create this library we will need [Angular CLI](https://cli.angular.io), which I have already installed.

The following command will create a "workspace" for the library. The `--create-application=false` flag tells the CLI not to scaffold the default Angular application since we are going to create a library.

```shell
ng new SMoS-npm --create-application=false
```

The previous command will also initialise a git repository, which is good because I preferably have my work version controlled. We are just going to go ahead and push what we have so far to GitHub (note that not everything under the `SMoS-npm` folder will be push to the remote repository as files and folders defined in `.gitignore` will not be included).

```shell
cd SMoS-npm
git remote add origin https://github.com/ChrisDinhNZ/SMoS-npm.git
git branch -M main
git push -u origin main
```

The following command creates the `projects/smos-js` folder, which contains a component and a service inside an NgModule.

```shell
ng generate library smos-js
```

Since we only intends for the library to provide a service, we can get rid of `smos-js.component.ts` and any reference to it.

We will add a simple method to the service. This method just returns the value of `SMOS_HEX_STRING_MIN_LENGTH`. The service `smos-js.service.ts` should look as follow.

```javascript
import { Injectable } from '@angular/core';

export enum SMoSDefinitions {
  /* SMoS minimum Hex string length (i.e. when byte count is 0) */
  SMOS_HEX_STRING_MIN_LENGTH = 11,
}

@Injectable({
  providedIn: 'root'
})
export class SmosJsService {

  constructor() { }

  /* Returns the value of SMOS_HEX_STRING_MIN_LENGTH, this can be useful
     when receiving data across the serial link one char at a time. */
  public GetSMoSMinimumHexStringLength(): number {
    return SMoSDefinitions.SMOS_HEX_STRING_MIN_LENGTH;
  }
}
```

### Publish library

To publish the library to NPM package registry we will need to have an [npmjs.com](https://www.npmjs.com) account.

The following command will build the library and place the output in the `dist/smos-js` folder.

```shell
ng build smos-js --prod
```

Now navigate to the build output folder and publish the library, note that the package name must be globally unique when publishing to NPM package registry.

```shell
cd dist/smos-js
npm publish
```

### Import and use library

We will also create an Angular app which we will use to demostrate the importing and usage of the library.

```shell
ng generate application smos-js-demo
```

Run the scaffolded app just to verify things are working as expected.

```shell
cd smos-js-demo
npm install
ng serve -o
```

The `ng serve -o` should launch the browser which shows the landing page like below:

![Scaffold Angular App Landing Page.png](assets/scaffold_angular_app_landing_page.png "Scaffold Angular App Landing Page")

What we are going to do is remove the boilerplate content and replace the landing page with just a simple button which when clicked, will show the value of SMOS_HEX_STRING_MIN_LENGTH (i.e 11).

```shell
npm install smos-js
```

File: app.component.html

```html
<style>...
</style>

<div class="content">
  <button (click)="OnClickMe()">Click me!</button>
</div>
```

File: app.component.ts

```javascript
import { Component } from '@angular/core';
import { SmosJsService } from 'smos-js'

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html'
})
export class AppComponent {

  constructor(private smosService: SmosJsService) {
  }

  OnClickMe() {
    let result = this.smosService.GetSMoSMinimumHexStringLength();

    alert(result);
  }
}
```

Serving the demo app above will result in the following:

![smos-js-demo.gif](assets/smos-js-demo.gif "smos-js usage demo")

That's it, hope you 'll find that useful.

Chris.