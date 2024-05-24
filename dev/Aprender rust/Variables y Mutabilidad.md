**8/5/2024**
[link](https://doc.rust-lang.org/stable/book/ch03-01-variables-and-mutability.html)

```rust
fn main() {
	let x = 5;
	println!("El valor de x es: {x}");
	x = 6;
	println!("El valor de x es: {x});
}
```
esto no compila debido a que la variables no es marcada como ```mut``` 
### Para que compile hay que hacer esto
```rust
fn main() {
	let mut x = 5;
	println!("El valor de x es: {x}");
	x = 6;
	println!("El valor de x es: {x});
}
```
### Shadowing
**shadowing** es cuando se declara una variable diferente con el mismo nombre, por ejemplo:
```rust
fn main() {
    let x = 5;

    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");
}
```
Output:
```
$ cargo run
   Compiling variables v0.1.0 (file:///projects/variables)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31s
     Running `target/debug/variables`
The value of x in the inner scope is: 12
The value of x is: 6

```

### Cuando una variable es declarada como mutable su tipo no puede cambiar, con shadowing si.

```rust
let spaces = "   ";
let spaces = spaces.len();
```
Esto compila porque estamos haciendo **shadowing**.
#### En cambio

```rust
let mut spaces = "   ";
spaces = spaces.len();
```
Esto no compila, porque se esta cambiando el signo.