# 7 - JavaScript Object Literals

##  Overview
JavaScript is an *Object-Oriented* language, but traditional classes have not been supported until ES6. Now we will look at the powerful **JavaScript Object Literal**, and see how powerful and flexible it is.

In you want to create an object in most computer languages, you first need to create a *template* (aka a *class*) that describes what the instance variables and methods of the class are. You then us the class to create *instances* of each object. In most languages it is impossible to add new properties and methods to an existing class - to do so you will have to create a new class, and sub-class the old class.

Creating objects in JavaScript can be much easier, you can skip step #1 (creating a template), and move directly to step #2 (creating an instance).

## I. What is a *literal value*?
In programming, a literal value is *a notation for representing a fixed value in source code*, or *a value written exactly as it's meant to be interpreted*.

Here are some literals in the JavaScript Language:

```
let a = "abcd";                         // a literal String
let b = 1234;                           // a literal Number
let c = true;                           // a literal Boolean
let d = [];                             // a literal Array
let e = arg => return arg * 2;          // a literal Function
let f = {};                             // an object literal!
```

NOT literals:

```
let g = Array();
let h = Date();
let i = Math.random();
let h = b * 10; // '10' is a literal but 'b' is not
```


## II. Creating Object Literals
Here we are going to create an object literal named `car1`, and attach a few properties to it. The way we do this is to specify a *key* and a *value* (in this form - `key:value`)

### objects-1.html

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <title>Objects-1</title>
</head>
<body>
<script>

// 1 - Create an object literal with properties
let car1 = {
  make: "Ford",
  model: "Pinto",
  cylinders: 4
};

// 2 - Add properties to existing objects
car1.color = "Red"; // add properties to objects with dot syntax
car1["year"] = 1972; // or with brackets

// 3 -  Delete properties from an object with the delete operator
delete car1["color"];
delete car1.year;

// 4 - access property values with dot syntax, or square brackets
console.log(car1.make); // Ford
console.log(car1["model"]); // Pinto

</script>
</body>
</html>
```

### A. Explanation
- note that we can easily add a property to an existing object. This can lead to odd errors.
Imagine this:

```
// 1- below we misspell cylinders, so 'a' is now 'undefined'
let a = car1.clyinders; 

// 2- below we misspelled .cylinders, and created a new property named "clyinders"
car1.clyinders = 10; 

// 3- Object.seal() to the rescue!
// Object.seal() will ensure we can't add new properties to an object
Object.seal(car1);
car1.newproperty = 1000; // will fail quietly or throw an error, depends on browser
console.log(car1.newproperty); // undefined
```

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/seal
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze

## III. Iterating over object properties
The `for...in` loop is commonly used to iterate over object properties.

Add the following to **objects-1.html**:

```
// 5 - iterate over keys of object with for...in
for (key in car1){
  console.log(`key=${key}`); // make,model,cylinders
}

// 6 - here we are using the keys to get the values
for (key in car1){
  console.log(car1[key]); // Ford,Pinto,4
}

// 7 - Object.keys() is a recent addition, it returns an array of the keys of an object
console.log(Object.keys(car1)); // ["make", "model", "cylinders"]


// 8 - the fancy ES6 way to iterate over object properties
Object.keys(car1).map(key => console.log(`key=${key}  value=${car1[key]}`));

/*
key=make  value=Ford
key=model  value=Pinto
key=cylinders  value=4
*/

```

## IV. Adding behavior to our objects

Objects usually have *state* (properties or instance variables) AND *behavior* (methods).

Can we add behavior to our JavaScript object literals? You betcha!

In JavaScript objects, *methods are properties whose values are functions*.

### objects-2.html

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8" />
	<title>Objects-2</title>
</head>
<body>
<script>

// 1 - Create an object literal with properties AND methods

let car1 = {
	make: "Ford",
	model: "Pinto",
	cylinders: 4,
	maxSpeed: 100,
	speed: 10,
	speedUp: function(howMuch){
// 2- we need to use "this" whenever we refer to a property of the current object
		this.speed += howMuch; 
		if(this.speed > this.maxSpeed){
			this.speed = maxSpeed;
		}
	},
	stop: function(){
		this.speed = 0;
	}
};

console.log(car1.speed); // 10
car1.speedUp(50); 		// car1.speed is now 60
car1.stop(); 			// car1.speed is now 0


</script>
</body>
</html>
```

### A. Explanation
- Here we are adding functions as the values of properties - which makes them *methods* of the object they are attached to.
- Note that we have to use the `this` keyword when we refer to a property of the current object. 

## V. Creating "object factories"
What if we had a game that needed to have 50 cars driving around? Creating 50 object literals - `car1 = {...} ,car2 = {...}, car3 = {...}` would be both tedious and error prone.
Instead, we can write a function to create these cars for us.

### objects-3.html
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8" />
	<title>Objects-3</title>
</head>
<body>
<script>

// 1 - create two functions that we will later use as a "method"
let speedUp = function(howMuch){
		this.speed += howMuch; 
		if(this.speed > this.maxSpeed){
			this.speed = maxSpeed;
		}
}

let stop = function(){ this.speed = 0; }


// 2 - Here is our "factory" function
function makeCar(make,model,cylinders=4,speed=10, maxSpeed=100){
	let car = {
		make: make,
		model: model,
		cylinders: cylinders,
		speed: speed,
		maxSpeed: maxSpeed,
		speedUp: speedUp,
		stop: stop
	};
	
	// 3 - seal it so that no new properties may be added
	Object.seal(car);
	return car;
}

// 4 - make some cars and log them to the console
let car1 = makeCar("Toyota","Corolla");
console.log(car1);

let car2 = makeCar("Toyota","Camry",6);
car2.stop();
console.log(car2);

let car3 = makeCar("Toyota","Tundra",8,50,200);
car3.speedUp(100);
console.log(car3);


// 5 - add the cars to an array
console.log("----- now loop through cars -----");
let cars = [car1,car2,car3];
for (car of cars){
   car.speedUp(10);
   console.log(car);	
}
</script>
</body>
</html>
```

### A. Explanation
- In #1 above, we create two functions that we will later add to our cars as methods. We are creating these functions *outside* of our factory function because we want the cars to *share* a single copy of each function, rather than for each instance of car to have their own copy of the function. This will reduce the overall memory footprint of our car instances - which is a good thing!
- In #2 above we declare `makeCar()` and give it 5 parameters, 3 of which are optional in that we have provided default values.
- In #3 we use `Object.seal()` to prevent new properties from being added to an object.
- In #4 and #5 we test our factory factory methods, and see how we can loop through an array of cars and call methods on them.

## VI. ES6 Object Literal syntax
In ES6, the Object literal syntax gives the developer more concise ways to declare properties and values with *property value shortcuts*, and a more compact way of defining object methods. See below:

### objects-4.html
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="utf-8" />
	<title>Objects-4</title>
</head>
<body>
<script>

// NEW ES6 Object Literal Feature

// 1- property value "shortcuts"

// 1A - old way
function makeCar1(make,model,cylinders){
	return {
		make: make,
		model: model,
		cylinders: cylinders
	};
}

// 1B - new ES6 way works as long as the property name and value use the same identifier
function makeCar2(make,model,cylinders){
	return {
		make,
		model,
		cylinders
	};
}

console.log(makeCar1("Subaru","Brat",4));
console.log(makeCar2("Plymouth","Champ",4));


// 2 - new and more concise way to specify object methods

// 2A - old way
let car1 = {
	speed: 0,
	speedUp: function(){
		this.speed ++;
	},
	stop: function(){
		this.speed = 0;
	}
};


// 2B - new ES6 way
let car2 = {
	speed: 0,
	speedUp(){
		this.speed ++;
	},
	stop(){
		this.speed = 0;
	}
};
</script>
</body>
</html>
```

## IX. Review Questions
1. In programming, what is a *literal* value?
1. What does `Object.seal()` do?
1. What does `Object.freeze()` do?
1. What is wrong with the following code?
```
var ship={
  x: 0,
  y: 0,
  speed: 10,
    move: function(){
      x += speed;
      y += speed;
    }
}
```

## X. Review Exercise


**[Previous Section <- JavaScript Events](web-apps-6.md)**
