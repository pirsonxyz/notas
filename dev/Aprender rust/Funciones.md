**10/5/2024**
[link](https://doc.rust-lang.org/stable/book/ch03-03-how-functions-work.html)
Las funciones son predominantes en  rust. Ya hemos visto las funciones mas importantes en el lenguaje: la función `main`, que es el punto de entrada en muchos programas. También he visto la `keyword` o palabra clave `fn`, que permite declarar nuevas funciones.

Rust usa *snake_case* como el estilo convencional para nombres de funciones y variables.

```rust
fn main() {
	println!("Hola funcion");

	another_function();
}
fn another_function() {
	println!("Hola otra funcion");
}
```

## Parámetros

Podemos hacer que las funciones tengan *parámetros*, que son variables especiales partes de la firma de una función. Cuando una función tiene parámetros, puedes proveerlos con valores concretos. Técnicamente(🤓) los valores concretos son llamados argumentos, pero en conversación casual las personas tienden a usar la palabra parámetro y argumente de forma intercambiada.

```rust
fn main() {
	another_function(5);
}
fn another_function(x: i32) {
	println!("El valor de x es: {x}");
}
```

Cuando se compila el código se ve así:

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
    Finished dev [unoptimized + debuginfo] target(s) in 1.21s
     Running `target/debug/functions`
The value of x is: 5
```

## Statements versus Expressions 

El cuerpo de las funciones esta hecho de una serie de *statements* que **podrían**  convertirse en *expressions*.  

- **Statements** son instrucciones que realizan alguna acción y no retornan un valor.
- **Expressions** evalúan el valor que resulta.

```rust
fn main() {
	let y = 6;
}
```

Aquí, `let y  = 6;` es un *statement*.

Los *statement* no retornan un valor, por lo tanto no se pueden asignar a una variable:

```rust
fn main() {
    let x = (let y = 6);
}
```

Si intentas correr el programa, encontraras este error:

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error: expected expression, found `let` statement
 --> src/main.rs:2:14
  |
2 |     let x = (let y = 6);
  |              ^^^
  |
  = note: only supported directly in conditions of `if` and `while` expressions

warning: unnecessary parentheses around assigned value
 --> src/main.rs:2:13
  |
2 |     let x = (let y = 6);
  |             ^         ^
  |
  = note: `#[warn(unused_parens)]` on by default
help: remove these parentheses
  |
2 -     let x = (let y = 6);
2 +     let x = let y = 6;
  |

warning: `functions` (bin "functions") generated 1 warning
error: could not compile `functions` (bin "functions") due to 1 previous error; 1 warning emitted
```

`let y = 6` no retorna un valor, entonces no hay nada que se le pueda asignar a `x`. Esto es diferente de otros lenguajes como C o Ruby, donde asignar retorna el valor que fue asignado. En esos lenguajes, puedes escribir `x  = y = 6`,  `x` y `y` tendrán el valor `6`; ese no es el caso en Rust.

Las *expressions* evalúan a un valor, y hacen la mayoría del  código en Rust. Considera una operación matemática, como `5 + 6`, en el cual la expresión retorna el valor `11`. Las *expressions* pueden ser parte de los *statements*, pero no viceversa.

```rust
fn main() {
	let y = {
		let x = 3;
		x + 1
	};

	println!("El valor de y es: {y}");
}
```

La *expression*:

```rust
{
	let x = 3;
	x + 1
}
```

Es un bloque, que en este caso, evalúa a `4`. Este valor se le asigna a `y` como parte del *statement* `y`. Nota que el `x + 1` no tiene un punto y  coma al final, esto es porque las *expressions* no tienen punto y coma al final. Porque si es añadido lo conviertes en un *statement*.

## Funciones que retornan valores

Las funciones pueden retornar valor al código que las llama. No llamamos valores de retorno, pero debemos declarar el tipo con una flecha (`->`). En rust, el valor del retorno de una función es sinónimo con el valor al final de la *expression* en el cuerpo de la función. Puedes retornar antes en una función usando la palabra clave `return`. Pero la mayoría de las funciones retornan el valor implícitamente. 

```rust
fn five() -> i32 {
	5
}

fn main() {
	let x = five();

	println!("El valor de x es: {x}");
}
```

Otro ejemplo:

```rust
fn main() {
	let x = plus_one(5);

	println!("El valor de x es: {x}");
}

fn plus_one(x: i32) -> i32 {
	x + 1
}
```

Correr este código va  a producir `El valor de x es: 6`. Pero si colocamos un punto y coma al final, la vamos a cambiar de una *expression* a un *statement*, obteniendo un error en el proceso:

```
$ cargo run
   Compiling functions v0.1.0 (file:///projects/functions)
error[E0308]: mismatched types
 --> src/main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: remove this semicolon to return this value

For more information about this error, try `rustc --explain E0308`.
error: could not compile `functions` (bin "functions") due to 1 previous error
```
