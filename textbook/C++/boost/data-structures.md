<!---
Fix examples, only put in the part of the examples in the writeup which are relevent to that part
Split up examples (ex: split up any into multiple example files)
--->
#Boost Libraries- Optional, Any, Variant
This tutorial will focus on `boost/optional`, `boost/any`,and `boost/variant`. The point of these libraries is to take the statically typed language C++ and make it similar to dynamically typed languages.

Statically Typed: The type of a variable is known at runtime. The programmer must specify the type in the code. Languages C, C++, and Java are statically typed.

Dynamically Typed: The type of a variable is interpretted at runtime, thus not specified by the programmer. Languages JavaScript, Objective-C, and Python are dynamically typed.
<!---
Why should people care
--->
##How to Use
We first need to look at our [optional_temp.cpp](https://github.com/baileyherms/rshell/blob/master/src/optional_temp.cpp) file which includes the boost libraries and uses the boost namespace.
```
#include <boost/optional.hpp>
#include <iostream>

using namespace std;
using namespace boost;
```
For the above just replace `<boost/optional.hpp>` with whatever boost type you're using.

##Boost.Optional
<!---
Begin with an example then explain it
--->
Allows for optional return types.
This is done because optional initializes a variable as empty but allows for a possible type change later.
This can be useful to use if you want to check if a function returns a valid value.

<!---
Change the below to an example
--->
For example `-1` is sometimes considered valid (say if you are measuring temperature), but `-1` is also used to say when a function has failed. 
In the temperature example, we want `-1` to be a possible passing return value, so we'll use Boost.Optional to know when the function hasn't done the task we want it to.

Boost.Optional allows you to initialize your variable in the temperature function to empty, and if nothing is returned, then you know that you have an error. Let's look at our [optional_temp.cpp](https://github.com/baileyherms/rshell/blob/master/src/optional_temp.cpp) example.

<!---
Replace this example with something else
--->
```
double deg;
boost::optional<double> degrees()
{
	if(deg)
	{
		cout << "The temperature is " << deg << " degress." << endl;
		return optional<double>{};
	}
	else
	{
		cout << "There is no temperature reported" << endl;
		return optional<double>{};
	}
}

int main()
{
	degrees();
	deg = 43;
	degrees();
	return 0;
}
```
This will output:

```
$ g++ -std=c++11 optional_temp.cpp -o optional_temp
$ ./optional_temp
There is no temperature reported.
The temperature is 43 degrees.
```
<!---
Show commands to compile and run code
--->
<!---
is_initialized() -> will check to see if the boost::optional variable has been initialized. Can use it to know if it is possible to output.

Constructor takes two params. First is a conditional statement, second is a value. Ex/ return optional<int>{i%2 == 0, i}; if false will return optional<int>{}

make_optional() makes a new optional var. Similar to new

boost::optional get_value_or( , )    boost::get_optional_value_or( , ) if first param is initialized then will return that value else will return a default value specified as the second param
--->
##Boost.Any
<!---
You need to talk about the difference between dynamic and static typing. In particular, you should never use Any when it is possible to use auto.
--->
Uses library `<boost/any.hpp>`

It allows for a variable that can be changed to any type in any portion of its scope.
<!---
Explain what variables it cannot use
-->
<!---
This is useful to use in place of `auto` because `auto` will assign a type to that variable from that point on, and Boost.Any will not. 
This takes up less memory (but the variable itself will take up more memory) because it enables you to reuse variables as different types instead of having to initialize new variables.
--->
<!--
and why
-->
For example, our [any_simple.cpp](https://github.com/baileyherms/rshell/blob/master/src/any_simple.cpp) file shows:
```
any var = 4;
var = false;
var = 3.25678;
var = string("hello world");
```
This is valid and var would be assigned `hello world`.
But if you tried to do this:
```
any var = 4;
var = false;
var = 3.25678;
var = string("hello world");
cout << var << endl;
```
You would get:
```
$ g++ -std=c++11 any_simple.cpp -o any_simple
any_simple.cpp: In function ‘int main()’:
any_simple.cpp:15:10: error: cannot bind ‘std::ostream {aka std::basic_ostream<char>}’ lvalue to ‘std::basic_ostream<char>&&’
  cout << var << endl;
```
This error is because Boost.Any does not support `<<`, so you cannot use `cout` with anything involving `Any`.

So what do you need to do to output a Boost.Any variable?
You would need to use `boost::any_cast` 
<!--
(explain how var is currently set to a string and that it cannot `cout << boost::any_cast<int>(var) << endl;`)
-->
```
boost::any var = 4;
var = false;
var = 3.25678;
var = string("hello world");
cout << boost::any_cast<string>(var) << endl;
```
This outputs 
```
$ g++ -std=c++11 any_simple.cpp -o any_simple
$ ./any_simple
hello world
```

You can even output multiple Boost.Any variables.
These variables will output a value based on their type.
This can be seen in the [any_mult.cpp](https://github.com/baileyherms/rshell/blob/master/src/any_mult.cpp) example
<!--
(explain different types)
(explain better, change wording)
-->
```
boost::any var = 4;
cout << boost::any_cast<int>(var) << endl;
var = false;
cout << boost::any_cast<bool>(var) << endl;
var = 3.25678;
cout << boost::any_cast<double>(var) << endl;
var = string("hello world");
cout << boost::any_cast<string>(var) << endl;
```
This will output:
```
$ g++ -std=c++11 any_mult.cpp -o any_mult
$ ./any_mult
4
0
3.25678
hello world
```
Note: You will have to change the cast type depending on what type `var` is currently set to.
The cast assigns a type to `var` so the computer knows how to handle the variable.
The cast is necessary because Boost.Any assigns values to `var` at runtime so the code needs to be told what type it is dealing with beforehand since values can be interpreted in different ways.
<!--
Link to something explaining cast type
-->

For example, we can modify the previous example to become [any_add.cpp](https://github.com/baileyherms/rshell/blob/master/src/any_add.cpp):
```
any var = 4;
cout << any_cast<int>(var) + any_cast<int>(var) << endl;
var = string("ho");
cout << any_cast<string>(var) + any_cast<string>(var) + any_cast<string>(var) << endl;
```
This will now output:

```
$ g++ -std=c++11 any_add.cpp -o any_add
$ ./any_add
8
hohoho
```
As you can see, the cast is very important in how the code ends up being executed.

Another important part of `Any` is `type_info` which can show what the type of a variable is which can be seen in the [any_1.cpp](https://github.com/baileyherms/rshell/blob/master/src/any1.cpp) example.

```
void type(any a)
{
	if(!a.empty())
	{
		const type_info &ti = a.type();
		cout << "type: " << ti.name() << endl;
		if(a.type() == typeid(int))
			cout << any_cast<int>(a) << endl;
		else if(a.type() == typeid(double))
			cout << any_cast<double>(a) << endl;
		else
			cout << "The type of a is not an int or a double" << endl;
	}
}
int main()
{
	any a = 1;
	type(a);
	a = 3.65;
	type(a);
	return 0;
}
```
This outputs:
```
$ g++ -std=c++11 any1.cpp -o any1
$ ./any1
type: i
1
type: d
3.65
```

Now, you might be thinking that it would be easier to create separate variables so you don't have to worry about casting the types, so let's use Boost.Any with vectors.

As shown in [any2.cpp](https://github.com/baileyherms/rshell/blob/master/src/any2.cpp) one Boost.Any `vector` can hold multiple variables of different types.
<!--
Should change wording
-->

For example:
```
vector<any> vect;
for(unsigned i = 0; i < 2; i++)
{
	int in;
	double d;
	char c;
	cout << "Insert an integer: ";
	cin >> in;
	vect.push_back(in);
	cout << "Insert a double: ";
	cin >> d;
	vect.push_back(d);
	cout << "Insert a char: ";
	cin >> c;
	vect.push_back(c);
}
```
`vect` now holds two integers, doubles, and chars all assigned by the user.
```
for(unsigned i = 0; i < vect.size(); i++)
{
	if(vect.at(i).type() == typeid(int))
		cout << any_cast<int>(vect.at(i)) << endl;
	else if(vect.at(i).type() == typeid(double))
		cout << any_cast<double>(vect.at(i)) << endl;
	else if(vect.at(i).type() == typeid(char))
		cout << any_cast<char>(vect.at(i)) << endl;
}
```
When the above is run:
```
$ g++ -std=c++11 any2.cpp -o any2
$ ./any2
Insert an integer: 8
Insert a double: 3.3 
Insert a char: b
Insert an integer: 9
Insert a double: 3.4
Insert a char: a
8
3.3
b
9
3.4
a
```

A useful application combining the above examples can be seen in the [any3.cpp](https://github.com/baileyherms/rshell/blob/master/src/any3.cpp) file:
```
for(unsigned i = 0; i < v.size(); i++)
{
	cout << "Username: " << any_cast<string>(v.at(i)) << " ";
	i++;
	cout << "SID: " << any_cast<int>(v.at(i)) << " ";
	i++;
	cout << "GPA: " << any_cast<double>(v.at(i)) << endl;
}
```
Here the `vector<any> v` is filled by the user (as seen in the [any3.cpp](https://github.com/baileyherms/rshell/blob/master/src/any3.cpp)) file and the below is output:
```
$ g++ -std=c++11 any3.cpp -o any3
$ ./any3
How many students do you have? 2
Student 1's username: jsmit089
Student 1's SID: 861035962
Student 1's GPA: 2.1
Student 2's username: lskyw001
Student 2's SID: 861325987
Student 2's GPA: 3.4
List of students:
Username: jsmit089 SID: 861035962 GPA: 2.1
Username: lskyw001 SID: 861325987 GPA: 3.4
```

##Boost.Variant
Uses library `<boost/variant.hpp>`

With a Boost.Variant variable, you can create a list of possible types (ex: `int`, `double`, etc.) that the variable can be assigned to.

Remember: At least one parameter must be specified.
<!--
Link to a site that explains
-->
<!---
Why should he care
--->

One reason to use Boost.Variant rather than Boost.Any is that when outputting a variant value, you do not have to specify the type since you define the possible types the value can be when initializing the value.

For example:
```
boost::variant<int, double, char, string> var;
var = 4;
cout << var << endl;
var = 3.98;
cout << var << endl;
var = 'V';
cout << var << endl;
var = "hello world"
cout << var << endl;
```
This will output:
```
$ g++ -std=c++11 variant.cpp -o variant
$ ./variant
4
3.98
V
hello world
```
<!---
Why should he care (add variant.cpp example here)
--->
If you wanted to use other operators on the values other than just stream, then you'd have to do something similar to the cast used in Boost.Any.
The way to do that is to use `boost::get<type>(var)` where `<type>` is the variable type you want `var` to be interpreted as. 
So to add two `ints` or to concatenate two `strings`, you'd have to change the code to:
```
boost::variant<int, double, char, string> var, var2;
var = 4;
cout << boost::get<int>(var) + boost::get<int>(var) << endl;
var = "hello";
var2 = " world";
cout << boost::get<string>(var) + boost::get<string>(var2) << endl;
```
This will output:
```
$ g++ -std=c++11 variant.cpp -o variant
$ ./variant
8
hello world
```
If you had attempted to do `var + var` in the previous example without using `boost::get<type>` then the computer wouldn't know which of the types in the original variable declaration you would want the variable to be interpreted as.

Another thing that Boost.Variant can do is send a variable to a function.
<!--
to a function, or...
-->
When the variable is sent to a function, it can have different actions done to the variable depending on its type.
This is used with `boost::apply_visitor()` that takes two arguments within its parenthesis.
<!--
(link to something explaining how to use boost::apply_visitor())
-->

The first argument is called a visitor functional object and the second parameter is a Boost.Variant variable.
The visitor functional object is a struct that must overload the `operator()` for each possible type defined for your variant variable.

For example:
```
struct func : public boost::static_visitor<>
{
	void operator()(int i) const
		cout << "integer " <<  i << endl;
	void operator()(double d) const
		cout << "double " <<  d << endl;
	void operator()(char c) const
		cout << "character " <<  c << endl;
	void operator()(string s) const
		cout << "string " << s << endl;
};
int main()
{
	boost::variant<int, double, char, string> var;
	var = 4;
	boost::apply_visitor(func{}, v);
	var = 3.98;
	boost::apply_visitor(func{}, v);
	var = 'V';
	boost::apply_visitor(func{}, v);
	var = "hello world"
	boost::apply_visitor(func{}, v);
	return 0;
}
```
This will output:
```
$ g++ -std=c++11 variant.cpp -o variant
$ ./variant
integer 4
double 3.98
character V
string hello world
```

`apply_visitor` works like a switch statement.
The variant variable is the test case evaluated for the switch, and each overloaded `operator()` is a different case.
When the variant variable type is the same as the `operator()(type)` then that portion of the code is executed. 
<!--
(explain how apply_visitor works)
--->
Since there are multiple possible results based on the visitor functional object that are passed into `apply_visitor`, we are able to do different types of outputs without needing to know specifically which type of variable we are dealing with.
If we wanted to choose to add two `ints` or concatenate two `strings`, then we could use `apply_visitor` to do that for us.

```
struct func : public boost::static_visitor<>
{	
	void operator()(int i) const
		cout << "integer " <<  i + i << endl;
	void operator()(string s) const 
		cout << "string " << s + s << endl;
};
int main()
{    
	boost::variant<int, string> var;
	var = 4;
	boost::apply_visitor(func{}, var);
	var = "hello ";
	boost::apply_visitor(func{}, var);
	return 0;
}
```
This will output:
```
$ g++ -std=c++11 variant.cpp -o variant
$ ./variant
integer 8
string hello hello
```
Note: If you wanted to have a Boost.Variant variable and cast it to another type to use in the `apply_visitor` function to make the `int` act like a string as we did with Boost.Any, then you'd have to use the [boost::lexical_cast](http://theboostcpplibraries.com/boost.lexical_cast) library to do so.
The main function of the previous example would need to become:
```
boost::variant<int, string> var;
var = 4;
boost::apply_visitor(func{}, var);
var = boost::lexical_cast<string>(var);
boost::apply_visitor(func{}, var);
```
This will now output:
```
$ g++ -std=c++11 variant.cpp -o variant
$ ./variant
integer 8
string 44
```
