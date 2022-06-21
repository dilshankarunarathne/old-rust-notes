# Introduction to Rust

```rust
fn main() {
	println!("Hello World!");
}
```

# Loops

```rust
fn loop_to_10() {
	let mut n = 0;
	loop {
		n += 1;
		println!("Hello");
		if n >= 10 {
			return;
		}
	}
}
```

```rust
fn loop_to_10() {
	for n in 0..10 {
		println!("Hello {}", n);
	}
}
```

This 0..10 creates a range object. 

```rust
fn loop_to_10() {
	while n < 10 {
		n += 1;
		println!("Hello");
	}
}
```

# Vectors

```rust
fn array_loop() {
	let mut v = Vec::new();
	v.push(4);
	v.push(6);
	v.push(8);
	v.push(9);
	for n in v {
		println!("{}", n);
	}
}
```

All that to create a vector seems too much, so rust have given us a macro.

```rust
fn array_loop() {
	let v = vec![4,6,8,9];
	for n in v {
		println!("{}", n);
	}
}
```

In rust, we can use continue and break keywords inside loops. 

# Nested Loops

If we wanted to break out of the outer loop instead of the inner loop, we need to create a label. Single quote mark is used to define a label.

```rust
fn array_loop() {
	let v = vec![4,6,8,9];
	'outer: for i in 0..10 {
		for n in &v {
			if i+n == 11 {
				break 'outer;
			}
			println!("{}", n);
		}
	}	
}
```

The & sign before the vector is used to make a pointer.

# Strings in Rust

```rust
fn main() {
	let s = String::from("Hello World");
	for c in s.chars() {
		println!(s);
	}
	for c in s.bytes() {
		println!(s);
	}
}
```

Just "Hello world" without the String:from is called an &str which is just a pointer to a slice of string whereas a string is equivalant to a vector. These two have subtle differences. The vector ownes the array. But the &str pointer just points to it.  

To take a pointed string to another string, it has to copy all that the data. When we use an &str it will be only used as a pointer to the primary memory inside the binaries. The data will last as long as the lifecycle of the program. We cannot change it. We will not be able to push another few characters or a slice of a string to &str. But we can do that to a mutable String object.  

If we check the length of a String object using the len method, we will get the number of bytes, not the number of characters. If we wanted to loop through the characters.  
If we want to know what the index is, we can iterate through char_indices().

```rust
fn main() {
	let mut s = String::from("Hello World");
	println!("S len = {}", s.len());
	for (i,c) in s.chars().enumerate() {
		println!("{} = {}", i, c);
	}
	let p = String::from("Hello Rust");
	for (i,c) in s.char_indices() {
		println!("{} = {}", i, c);
	}
}
```

Whenever we want to use a string in read-only, we can use an &str. Because it will not consume the string, it will only allow us to work with it without working with it. Any string can be use as an &str. We can use a String object as an &str by putting a & sign before the variable name of the string. 

```rust
let s = String::from("Hello world");
println!("Result = {}", count_l(&s));
fn count_l(s:&str)->i32 {
	let mut res = 0;
	for c in s.chars() {
		if c == 'l' {
			res += 1;
		}
	}
	res
}
```

# Struct and Methods

We can use the keyword pub infront of the struct keyword to make a struct public, so we can use it outside the project. 

```rust
#[derive(Debug)]
struct User {
	name:String,
	age:i32,
	shoesize:i32,
	height:i32,
}
fn main() {
	let u = User{
		name:"Matt".to_string(),
		age:33,
		height:250,
		shoesize:10,
	};
	println!("User is {:?}", u);
}
```

```rust
struct User {
	name:String,
	age:i32,
	shoesize:i32,
	height:i32,
}
impl User {
	fn simple_string(&self)->String {
		format!("{} - {} - {}cm - shoe:{}", self.name, self.age, self.height, self.shoesize);
	}
	fn grow(&mut self, h:i32) {
		self.height += h;
	}
}
fn main() {
	let mut u = User{
		name:"Matt".to_string(),
		age:33,
		height:250,
		shoesize:10,
	};
	println!("User is {}", u.simple_string());
	u.grow(20);
	println!("User is {}", u.simple_string());
}
```

# Enums and Pattern Matching

Enums in Rust are used extensively throughout the standard library. As long as all the members in a struct or an enum derives Debug, we can derive the struct or the enum from Debug.

```rust
#[derive(Debug)]
pub struct Bed {
	size:i32;
	count:u32;
}
#[derive(Debug)]
pub enum Room {
	Kitchen(i32),
	Bedroom(Bed),
	Lounge,
}
fn main() {
	let t = Room::Kitchen(4);
	println!("Hello from the {:?}", t);
	match t {
		Room::Kitchen(n) => println!("The room is a kitchen with {} rooms.", n),
		d => println!("{:d}", d), 
	}
}
```

By using an enum, we can use it's members without having to state what the enum is every single time. 

```rust
fn main() {
	use self::Room::*;
	let t = Bedroom(Bed{size:50,count:2});
	println!("Hello from the {:?}", t);
	let v = match t {
		Kitchen(n) => n,
		_ => 0, 
	};
}
```

# Result and Option types

Result and Option are both enums. Result lets us return something and Option gives us a chance to choose if the returned result contains something or not, and what to do in either case.

```rust
use std::collections::HashMap;
fn main() {
	let mut hm = HashMap::new();
	hm.insert(3, "Hello");
	hm.insert(5, "World");
	let r = match hm.get(&3) {
		Some(v) => v,
		_ => "Nothing",
	};
	println!("{}", r);
}
```

The get function asks for a pointer. The r came back as an option.  
Option types have a wide varity of functions we could use. The unwrap method, will return the Some value, if it have a Some value, but if we get a None, the program will panic. But we can use unwrap_or function to stop getting a panic. 

```rust
use std::collections::HashMap;
fn main() {
	let mut hm = HashMap::new();
	hm.insert(3, "Hello");
	hm.insert(5, "World");
	let r = match hm.get(&4).unwrap_or(&"NoString");
	println!("{}", r);
}
```

# Errors

```rust
match "3".parse::<f32>() {
	Ok(v) => println!("Ok - {}", v),
	Err(e) => println!("Error - {}", e),
};
```

Err is also an enum. The same methods exists in this. So we could use unwrap or unwrap_or.

```rust
use std::env::args;
fn get_arg(n:usize) ->Result<String,String> {
	for (i,a) in args().enumerate() {
		if (i == n) {
			return Ok(a)
		}
	}
	Err("Not enough Args".to_string());
}
```

# Traits

---------------------------------------------------------------------------------------------------
use std::ops::Add;
#[derive(Debug)]
pub struct Point {
	x:i32,
	y:i32,
}
impl Add for Point {
	type Output=Point;
	fn add(self, other:Point)->Self::Output {
		Point {
			x:self.x + other.x;
			y:self.y + other.y;
		}
	}
}
fn main() {
	let a = Point{x:3, y:5};
	let b = Point{x:30, y:50};
	let c = a + b ;
	println!("c = {:?}", c);
}
---------------------------------------------------------------------------------------------------



