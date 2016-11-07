# ng2
notes on ng2

## Commands:
- npm install -g typescript						- install typescript globally
- npm install -g angular-cli					- install angular cli globally
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
- To use input and output from components, you could use the @Input() and @Output() annotations and decorate the property or event emitter
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
<button (blah)="myFunc()"></button>
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









