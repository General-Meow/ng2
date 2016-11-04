# ng2
notes on ng2

## Commands:
npm install -g typescript					- install typescript globally
npm install -g angular-cli					- install angular cli globally
ng 											- start the angular cli program. defaults to output help. if you see an error about watchman use the following command
brew install watchman						- using brew install watchman so that file can be watched during dev
ng new <project_name>						- create a new angular 2 project with name project_name in the current directory
ng serve									- start the built in cli http server and serve the current angular app
ng generate component <component_name>		- create an angular component with a name. creates html, css, ts and test spec under the src/app directory





Component:

import { Component, OnInit } from '@angular/core';	// import from the angular/core module the Component and OnInit things and destructure them

@Component({								// declare an annotation call component with the following attributes
	selector: 'my-component',				// the css selector used to define this component
	templateUrl: './my-component.html',		// the template used relative to this file
	styleUrls: ['./my-component.css']		// an array of styles that are applied to this template
})

export class MyComponent implements OnInit {  // declare and export a class. the first class after the annotation gets the meta data from the annotation
	
	constructor()							// a class can have only one constructor
	ngOnInit()								// implement the OnInit method that runs after initialization of the component

}

Template:

<p> {{name}} </p>							// Template tags aka mustache tags are expression that are evaluated using the variable from the component