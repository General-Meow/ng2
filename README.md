# ng2
notes on ng2

## Commands:
- npm install -g typescript	                    - install typescript globally
- npm install -g angular-cli                    - install angular cli globally
- ng 											- start the angular cli program. defaults to output help. if you see an error about watchman use the following command
- brew install watchman							- using brew install watchman so that file can be watched during dev
- ng new <project_name>							- create a new angular 2 project with name project_name in the current directory
- ng serve										- start the built in cli http server and serve the current angular app
- ng generate component <component_name>		- create an angular component with a name. creates html, css, ts and test spec under the src/app directory
- npm install -g tsun							- install the typescript REPL
- tsun											- run the typescript REPL


## Files:
- angular-cli.json 								- create by angular cli projects with ng new <name> it contains config for this app and specifies a main file (main.ts)
- main.ts 										- the main entry point for this application. it bootstraps the application by importing and loading the angular module
- app.module.ts									- The file used to bootstrap the application. its whats called the AppModule. it specifies the component that used as the top level component aka AppComponent. it contains the @NgModule annotation that applies to the class right after it. YOU NEED TO DECLARE YOUR OWN COMPONENTS HERE BEFORE YOU CAN USE THEM.
- app.component.ts 								- The component that contains the selector for the main app on the index page.




## Component:
```
import { Component, OnInit, Input } from '@angular/core';	// import from the angular/core module the Component and OnInit things and destructure them
```

```
@Component({								// declare an annotation call component with the following attributes
	selector: 'my-component',				// the css selector used to define this component
	templateUrl: './my-component.html',		// the template used relative to this file
	template: `<xxx></xxx>`					// template can be used instead of templateUrl. use back ticks here to have multiline
	styleUrls: ['./my-component.css'],		// an array of styles that are applied to this template
	host: [
		class: 'row',						// add a class to the host tag. the my-component tag will now have an addition row class applied
	],
})

export class MyComponent implements OnInit {  // declare and export a class. the first class after the annotation gets the meta data from the annotation
	@Input() name: string					// Input annotation that allows this component to accept input from another component
	constructor()							// a class can have only one constructor
	ngOnInit()								// implement the OnInit method that runs after initialization of the component
	runAction(nameField: HTMLInputElement) : boolean 	// the method to run when clicking on the form button, see below form. Actual element is sent, use the value method on it to get the actual value in the input field
}
```

## Template:
```
<p> {{name}} </p>							// Template tags aka mustache tags are expression that are evaluated using the variable from the component

<div *ngFor="let user of users"> {{user.name}} </div>	// for loop directive that will create the divs multiple times for each user in the users array. will create a user variable per iteration which can be referenced in template tags. make note of the *

<outer-component *ngFor="let user of users">				// Iterate over users create this tag (the outer one) for every user in the list
	<inner-component [name]="user.name"></inner-component> //To inject data from one component to annother, you use this syntax with the @Input annotation. The variable in the square brackets is the variable annotated with Input while the value in the quotes is the value from the outer component your sending
</outer-component>

<form>
	<input name='myName' #nameField/>						//using hash will bind a name to this HTMLInputElement (so it is now an object in the dom) so we can refer to it in the runAction method, so the element is sent

	<button (click)="runAction(nameField)">a button</button>	//bind the click action event to the runAction method on the class for this component. You need to ensure that the method returns false at the end of it so that the click action doesn't bubble up to parent components
</form>
```

## How Angular works

- Angular 2 is all about components. Components belong to modules and these components can be composed of other components
- Annotations in angular 2 are also known as decorators. They decorate the immediate class with meta data to provide it with additional functionality
- Templates can use template binding syntax aka mustache brackets {{}} to bind controller properties to the view
- The binding syntax can be used to evaluate expressions too {{ x + 1 }} or run methods {{ getSomeProperty() }}
- To input into a component from a view template use square brackets e.g. [ctrlProperty]="outerControllerProperty"
- To handle output from a component from a view template use round brackets e.g. (click)="ctrlFunction()"
- To use [input] and (output) from components, you could use the @Input() and @Output() annotations and decorate the property or event emitter
- OR you could use the @Component annotations input and output keys to define an array of properties
- To use input, the value in the square brackets need to be a property on the controller
- To use output, you need an event, an event emitter for the event and a function that is called when that event is thrown.

```
@Component({
	inputs: ['myShares'],
	outputs: ['boughtShare']
})
export class Shares{
	myShares: Shares;	
}
```

- You can remap inputs if the view properties dont exactly match the controller properties. If you can't change with you can remap by:

```
<a-component [cannot-change-this]="valueFromOutter"/>

@Component({
	inputs: ['myInternalValue: cannot-change-this'],			//Remap the cannot-change-this to an internal property of the component
})
export class MyComponent{
	myInternalValue: string;
}
//You do not need to do this for all properties if its just the one input, ie. inputs: ['blah', 'blag', 'internal: cantchage']
```

- You dont have to you basic primitive types for inputs, you can also use complex custom class types too
- To emit custom events you need to define the event in the output key array in Component

```
<button (click)="myFunc()" [class.myClass]="someFunc()"></button>	//myClass class will be applied if someFunc() returns true
@Component({
	selector: 'zzz',
	output: ['blah'],
})
export class MyInnerComponent{
	blahEmitter: EventEmitter<string>;
	constructor(){
		this.blahEmitter = new EventEmitter();
	}
	myFunc(): void {
		this.blahEmitter.emit('IM A BIG BLAH');
	}
}

@Component({
	selector: 'zzz',
	template: `
	<div>
		<zzz (blah)='outerFunc($event)'></zzz>  			//this is the event that is thrown from the inner comonent, in this case its the string IM A BIG BLAH
	</div>`
})
export class Outer{
	outerFunc(event: string){
		console.log(event);
	}
}
```

- When using url's for images don't use the template binding syntax to set the src property. Instead use the [src] input method. This ensures that if angular get loaded after the template gets rendered, you don't get a flash of mustache templating. e.g.

```
<img [src]='product.imgUrl'/>
```

- You can get the index of a *ngFor loop with the following syntax
*ngFor="let blah of blahz; let i=index"
- In order to use custom component in angular YOU HAVE TO load it as a declaration in the root module. either that or you need to assign it to a module and import the module
- Ahead of time compilation is when you compile the application before loading it. By default angular apps bui;lt with angular cli doesn't have this and is built using just in time. its done during bootstraping i.e. platformBrowserDynamic().bootstrapModule(MyModule);
- To get data from one component to another, it is considered one way data binding to be the best way in angular 2. to do this you emit an event to the top component which will filter down to the rest of the components. This is better than using service singletons as they are much more difficult to do in angular. also in angular 1 they did it with 2 way data binding but that proved to be difficult because it could cause cascading affects.


## Built in angular components and directives

- <div *ngIf='<expression>'> 						- used to display something if the expression holds true. Unlike NG1 there is no ngShow
- <div [ngSwitch]="myVariable">						- switch case, like java each case will fall through to the next even if they match
	<div *ngSwitchCase="'A'">its a</div>
	<div *ngSwitchDefault>default val</div> 		- optional
  </div>
- <div [style.background-color]="yellow"/>			- ngStyle used to set tag styles in the form of style.<the css style>='<css value>'
- <div [ngStyle]="{'font-color':'blue', 'display':'block'}"/>		- multiple style syntax for ngStyle 
- <div [style.font-size.px]="size"/>				- for styles that are messured in units you can state the unit of messure in the key
- <div [ngClass]="{bordered: <exp>}"/>				- ngClass used to apply a class if the exp hold true. any classes already applied will still stay applied
- <div *ngFor="let person of persons; let i=index"/> - iterate this element with all elements in the persons collection. the index bit is optional, creates an index variable
- <div ngNonBindable/>								- ngNonBindable tells angular not to compile this part of the template
- 


## Forms in angular

- Forms in angular can be done in two ways. Template driven or model driven. 
- Template driven means that you decorate the template so that angular automatically creates forms, form controls, validation etc for you, this uses the NgForm and NgModel directives and other attributes like required and max length. This leaves the backing component pretty much free of any logic. 
- Use template driven if you are writing a simple form with basic validation or migrating from ng1 to ng2.
- If you use template driven forms, you can only test them using end to end testing methods
- You will need to import the FormsModule into the root module if you want to use template driven forms
- Model driven forms define the form and its controls in the backing component then bind them to the template using [formControl] and [formGroup] input directives.
- Use model driven if your form is complicated, needs validation code tested using unit tests.  
- You will need to import the ReactiveFormsModeul into the root module if you want to use model driven forms.
- You can mix both methods but its best to choose one as it can get messy.
- You can represent a form element in the controller as a FormControl type and a group of form elements i.e. the form itself as a FormGroup
- Both FormControl and FormGroup extend the AbstractControl.
- They both have methods that will tell you if the form element or form is valid, dirty, errors and get the value
- Create a form control by: let myInput = new FormControl();
- To bind this form control to a view element you use the [formControl]='<NAME>' attribute:
	<input type='text' [formControl]='myInput'/>
- Create a form group by sending an object of key to FormControls to the constructor:
	let myWholeForm = new FormGroup({
		name: new FormControl("paul"),
		postcode: new FormControl('se14')
	});
- Using form groups allows you to check properties of all the controls on the form e.g. myWhole.value //will output { name: 'paul', postcode: 'se14' }
- To use these types you need to import the FormsModule or ReactiveFormsModule from '@angular/forms' into the root module e.g.
import { FormsModule, ReactiveFormsModule } from '@angular/forms';
@NgModule({
	imports: [FormsModule, ReactiveFormsModule]
})

- The FormModule gives us template directives such as 'ngModel' and 'NgForm'
- The ReactiveFormsModule gives us directives such as 'formControl' and 'ngFormGroup' and many more

```
html
<form #f='ngForm' (ngSubmit)='onSubmitFunc(f.value)'>
	<input type='text' name='sku' ngModel/> //the name attribute is REQUIRED when using ngModel
</form>
```
- Whenever we import directives and make them available, they will automatically attach to any tags that have the correct selector.
- ** There are effectively 2 ways to build forms, use ngForm and ngModel to implicitly (automatically) build forms and controls OR use [formGroup] and [formControl] to manually build and bind forms - the later is usually for complex forms or forms with validation.
- In this case the NgForm directive has a component select on 'form' so it will automatically attach to any forms on the page and create its own FormGroup
- KEEP THIS IN MIND as you may not expect it to bind on your forms
- The directive gives us the ngForm and ngSubmit
- In the above form, we create a local variable called 'f' with the #f and binds it with the FormGroup ngForm
- Also the function onSubmitFunc is executed when the form is submitted. It sends the FormGroup's value output i.e. the object of key to FormControls
- The input tag has the ngModel directive applied. Applying this directive on a tag means we want to bind this tag to a FormControl that will be autmactically attached for the forms FormGroup with the name 'sku' because of the name attribute.
- We can apply ngModel in 2 ways. By itself like above without any values, or with values with two way binding banana syntax i.e. [(ngModel)]='<component property>'
- When you use ngModel without any values, your essentially setting it up with one way databinding. 
- When we use ngModel a formControl is created using the name attribute value as the variable name. In this case sku is used.
- Using ngModel will bind the new formControl to the formGroup automatically.
- When using ngModel with a value property like [(ngModel)]='componentProperty' you are setting it up to be two way databinding. This means that any changes to the input form will propegate out as an event and any elements using the model will update automatically
- If you dont want two way data binding then just using [ngModel] is fine, you can still use a form with form initialization as follows:

```
html
<input type='text' name='sku' [ngModel]='product.sku'/> 
```

```
ts
@Component(...)
export class MyProductComponent{
	public product: Product;
}
```

- The class named FormBuilder, does just that, it builds forms. It makes it slightly easier as building them can be tedious
- You have to inject the FormBuilder into the constructor of the controller if you wish to use it
- Like FormControl and FormGroup it comes from the @angular/forms package
- The FormBuilder has the methods, control and group
- group takes an object of a key (string) value (array) pair which make form controls
formBuilder.group({
	name: ['paul'], // the arrays can take additional values which will be options like validation rules but a single value will be used as the default value of the control
	postcode: ['se14'],
})
- form creates a simple form control
- Creating a set of form controls and groups in the controller wont do anything until you bind it to the view. You do so by using the [formGroup] directive e.g.
```
html
<form [formGroup]='myCreatedFormGroup'/>
```
- By doing this, it will stop NgForm from automatically binding to this form as we're manually binding our own form group to this form
- If we manually bind our own formGroup to the form we'll need to change the binding of the input tag to bind to the controls of that group using [formControl] directive
<input type='text' [formControl]='myCreatedFormGroup.controls["postcode"]'/>
- Validation is done via the Validators module.
- To make a field required, you use the Validators.required method and assign it to a formControl object. You can do this when constructing formContols or with formBuilder

let postcode = new FormControl('postcode', Validators.required);
//or
formBuilder.group({
	'postcode': ['', Validators.required]
});

- With these formControls defined in the component class now, we need to bind them to the view. there are 2 ways to do so.
	- assign the formControl to a property of the component class giving access to it easier. Best for easy short forms
	- access the formControl via the formGroup which will be part of the component property, which makes it more verbose in the view. Best for long complex forms as you dont need to define every form control element on the component class as a property

- As stated earlier, FormControls and FromGroups have a valid method. this method can be used to display messages on the view
<div *ngIf='!postcode.valid'>My error message here</div>
<div *ngIf='postcode.hasError("required")'>My error message here</div> //this looks for a specific type of validation error key

<div [class.error]='!postcode.valid && postcode.touched'>

- You can look up form fields on the form group level
<div *ngIf="formGroup.hasError('valid','postcode')">postcode error message here</div>

- If your using a FormGroup only on the component class and not setting FormControls as properties then you will need to change the view to interrigate the form group instead like the following
<div *ngIf='!formGroup.control["postcode"].valid'>My error message here</div>

- Custom validation is done by creating a class or a function that takes in a FormControl, does the validation and returns a StringMap<String, boolen> where the string key is the error code and the boolean is true when theres a fault.

function validateName(formControl: FormControl): {[key: string]: boolean} {
	if(formControl.value == ''){
		return { required: true };
	}
}

- To use a custom validator you can assign it just like the other validators.
- If you need to use mulitple validators on a form control you can use the method Validators.compose that takes an array to group them

let postcode = new FormControl('', Validators.compose([
	Validators.required, validateName
]));

- To watch for changes on a form, we can subscribe functions for event emittors on the formControls or formGroups like so

this.postcodeControl.valueChanges.subscribe(
	(value: string) => { console.log(value); }
);

- valueChanges is an emitter that emits strings

## Http stuff
- Asynchronous http has always been difficult in javascript. The ways in which you do async code is with Callbacks, Promises, Observables.
- Angular 2 prefers to use observables to do async calls
- The module to use when using HTTP is the HttpModul from the '@angular/http' package
- The classes of interest when using HTTP is Http, Response, RequestOptions, Headers
- You would need to import that module in the root module before you can use it

```
@NgModule({
	import : [HttpModule]
})
```

- You have to inject http in order to use it
- Use the request method to make a http request. It will return an Observable which you can use subscribe on
- To make a request and process a response you do:
- Use the subscribe method to define a function that takes a repsonse.
this.http.request('http://someurl.com').subscribe((response: Response) => { console.log(response); } );
- http has methods such as get, post, head, delete, patch
- all of these http methods take an optional parameter of type RequestOptions, these encapsulate, method, headers, body, mode, credentials, cache, url and search
- Injection can be setup by defining an arrary of objects and setting that into the provider key in the root module e.g.

```
export var myInjectables: Array<any> = [ 					//You can export functions and variables as well as classes
	{provide: MY_USERNAME, useValue: 'username'},			//basic types can be injected
	{provide: MY_PASSWORD, useValue: 'password'},
	{provide: CartService, useClass: DefaultCartService}	//classes can be injected
];

import {myInjectables} from 'some file';
@NgModule({
	providers: [ myInjectables ],
	...
})
```

You can inject the values by...

```
export class Derp{
	constructor(@Inject('MY_USERNSME') private username: string){}
}
```

- You can inject a wrapper representing the component view into the class the type if ElementRef

```
export class Derp{
	constructor(private el: ElementRef)
}
```

## Routing

- Hash based routing is what was done in that past where we would use named tags and links with hashes to move the browser to a certain part of a page without loading a new page.
- Html 5 provided a way to change browser history, with this it would change the url without a reload of the page. This allowed for moving to a different page without reloading.
- ng2 defaults to using history push state.
- not all browsers support html 5 routing 
- the server needs to support html5 based routing - this means that pages like /contactus will not actually exist, so the server will need to direct to the index page to get the main app before routing to /contactus
- In  Angular 2 you need to defined paths and map them to components that will handle them
- In order to use the router module you need to do the following: create some routes in an array of path to component objects, insert that array into the router module when importing the router module. Define a <router-outlet/> as a placeholder element on a page to where component in the router will be displayed. Use the RouterLink directives on links to activate a route.
- example

app.ts 
```
import { RouterModel, Routes } from '@angular/router';
import { HomeComponent, ContactUsComponent } from './myComponents';

const routes: Routes = [		//create a const of all routes that the application will map to
	{ path: '', redirectTo: 'home', pathMatch: 'full', pathMatch: 'full' }, 		//redirect is optional
	{ path: 'home', component: HomeComponent },
	{ path: 'contactus', component: ContactUsComponent },
	{ path: 'contact', redirectTo: 'contactus'}
	{ path: 'allProducts', component: 'AllProductsComponent' }
	{ path: 'product/:id', component: 'ProductComponent' } 							//route with request parameter
	{ path: 'myAccount', component: 'MyAccountComponent', canActivate: [LoggedInGuard] }	//route thats been locked down unless the LoggedInGuard is satisfied
];

@NgModule({
	declarations: ['HomeComponent', 'ContactUsComponent'],
	imports: [ 
		BrowserModule,
		RouterModule.forRoot(routes);			//configure the router module with our routes
	],
	providers: [
		{ provide: LocationStrategy, useClass: HashLocationStrategy }, 	//configure the router module to use the hashing strategy
																		//can be PathLocationStrategy to use html push history
		{ provide: APP_BASE_HREF, useValue: '/'}						// this tell angular to make all paths base off application context path, this can also be 
																		// configured using a html tag <base href='/'/> in the main index of your app
	]
})

```

```
<div>
	<nav>
		<a [routerLink]="['home']">Home</a>
		<a [routerLink]="['allProducts']">All Products</a>
		<a [routerLink]="['contactus']">Contact Us</a>
	</nav>

	<section>
		<router--outlet></router--outlet>
	</section>
</div>

```

```
export class ProductComponent {
	private productId: string;
	constructor(private route: ActivatedRoute){
		this.productId = route.params.subscribe(params => { this.prouductId = params['id']; }) //the id key here maps to the paths route request param in the mappings
	}
}
```

- The [routerLink] directive is used to tell angular that these links correspond to links in the router module and that they should not cause a page reload
- [routerLink] value is an array of strings, the first string is the name of the path of the configured path, there are other parameters that we can send
- Routes with dynamic parameters can be defined with :<id> in the path
- To get the parameter from the route, you need to use the ActivatedRoute objects params object. activatedRoute.params.subscribe(params => { params['id'] })
- Query params, params after the ? in the url can be accessed via the queryParams method on the activated route object. 
- activatedRoute.queryParams.subscribe(params => { this.sortQuery = params['sortOrder'];});
- To programatically change page use the Route classes navigate method. route.navigate(['product'])
- route.navigate(['search'], {queryParams: {query: 'xxxx'}}).then(_ => this.search()) // go to the search page with query params then once success run the search method
- localStorage is a implicite variable available that allows access to the local storage of the browser
- localStorage has the methods, getItem, setItem, removeItem
- You can lock down routes so that users cannot view them unless the class defined in the canActivate are all satisfied

```
js
{ path: 'myAccount', component: 'MyAccountComponent', canView: [LoggedInGuard] }
```






