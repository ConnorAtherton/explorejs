## Constructor functions

The most important thing to understand about constructor functions 
in js is that they are just like any other function! 

The only difference is that they are called with the ```new``` operator
which does a number of things.

- It creates a new object and passes it into the function as its context (ie. Its ```this``` value)
- The passed in objects ```__proto__``` property is set to the prototype ```property``` of the function.
- The objects ```constructor``` property references the function that created it.

The above list items are much easier to visualise in working code. Below is a person constructor

```
function Person(name, gender) {
 this.name = name;
 this.gender = gender;
}
'''

This is just a normal named function declaration with the small difference that its name is
uppercase. This is not a rule enforced by the language in any way but rather a consequence
of the languages dynamic nature. Enforcing that all constructors are written in uppercase
documents that the function should only ever be invoked using the ```new``` operator to avoid
unwanted side affects. These side effects will be discussed in a later section.

Back to our person example. We call this using the new operator and assign the object returned
into some variable.

```
var connor = new Person('connor', 'male');
```

Now connor is an instance of Person and contains both a name and gender property.

```
connor instanceof Person // => true
connor.name // => 'connor'
connor.gender // => 'male'
```

To gain a better understanding of what is going on here, we can actually simulate what the new
operator is doing in this example.

```
var connor = {};
connor.__proto__ = Person.prototype;
Person.call(connor, 'connor', 'male');
```

That example produces the same instance as invoking new would. First, we create a new object and assign 
to the connor variable. We then set that variables __proto__ property to the prototype object of the constructor.

Explaining in depth both the __proto__ and protoype properties would make this post far too long and will be reserved
for another post in the future. For now, we'll just say that these two properties create a prototype chain that allows
inheritance in js by saying that all instances should inherit metods and values from the constructors prototype.

Finally we invoke the Person function using ```call``` so we can manually set the context as the first variable. In this example
that context will be the object we created. In the scope of the function ```this``` will now refer to the object so saying
```this.name = name``` is the same as ```connor.name = name```. It's just normal assignment but with a specified context bound.

### Protecting misuse of new
What do you think will happen if we call the Person constructor without using new?

```
var connor = Person()
```

Probably not what you expect. The variable connor will be ```undefined```. This is because without new there is no plain object passed
in and bound to ```this``` and so nothing is implicitly returned.

We can code defensively against this possibility by checking whether the object passed in is an instance of the Constructor and call it proper;y
if it isn't;

```
function Person(name, gender) {
  if (!(this instanceof Person))
    return new Person(name, gender);

  this.name = name;
  this.gender = gender;
}
```
