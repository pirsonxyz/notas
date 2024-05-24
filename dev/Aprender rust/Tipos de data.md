**8/5/2024**
[link](https://doc.rust-lang.org/stable/book/ch03-02-data-types.html)
Cada valor en rust tiene un cierto tipo de data, lo cual le dicen a rust que tipo de data es especificado.

```rust
let guess: u32 = "42".parse().expect("Failed to parse");
```
Si no se anota el ```u32``` pasa este error:

```
$ cargo build
   Compiling no_type_annotations v0.1.0 (file:///projects/no_type_annotations)
error[E0284]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^        ----- type must be known at this point
  |
  = note: cannot satisfy `<_ as FromStr>::Err == _`
help: consider giving `guess` an explicit type
  |
2 |     let guess: /* Type */ = "42".parse().expect("Not a number!");
  |              ++++++++++++

For more information about this error, try `rustc --explain E0284`.
error: could not compile `no_type_annotations` (bin "no_type_annotations") due to 1 previous error
```

## Tipos Escalares
Un tipo escalar representa **4** valores: **enteros, números flotantes, bools y chars.** 
### Números enteros
| Cantidad | Signo | Sin signo |
| -------- | :---: | --------: |
| 8 bits   |  i8   |        u8 |
| 16 bits  |  i8   |        u8 |
| 32 bits  |  i32  |       u32 |
| 64 bits  |  i64  |       u64 |
| arch     | isize |     usize |
### Literales enteras

| Literal           |   Ejemplo   |
| ----------------- | :---------: |
| Decimal           |   69_420    |
| Hex               |    0xff     |
| Octal             |    0o77     |
| Binario           | 0b1111_0000 |
| Byte(solo ``u8``) |    b'A'     |
Como sabes que tipo usar? Si no estas seguro, Los predeterminados son generalmente buenos lugares para empezar: tipos enteros son `i32` por default. La situación principal en la cual se usa `isize` o `usize` es cuando se indexa algún tipo de colección.
### Números flotantes
Rust tiene 2 tipos primitivos de números flotantes, los cuales son números decimales. Los tipos son `f32` y `f64`, los cuales son 32 bit y 64 bits de tamaño respectivamente.
Ejemplo:

```rust
fn main() {
	let x = 2.0; //f64
	let y: f32 = 3.0; //f32
}
```

Los números flotantes son representados acordando al estándar IEEE-754. El `f32` es de una precisión y el `f64` es de doble precisión. 
### Operaciones numéricas
Rust apoya las operaciones matemáticas básicas que se espera de todos los tipos numéricos: adición, resta, multiplicación, división y modulo:

```rust
fn main() {
	let sum = 5 + 10;
	let diff = 95.5 - 4.3;
	let product = 4 * 30;
	let quotient = 56.7 / 32.2;
	let trunc = -5 / 3; // -1
	let rem = 43 % 5; 
}
```

Cada expresión en estos predicados usan operadores matemáticos y se evalúan a un solo valor , el cual es luego puesto en una variable.
### El tipo Bool
Como en la mayoría de los lenguajes de programación, un tipo bool en rust tiene 2 tipos de valores `true` o `false`. Los bool son de 1 bite de tamaño, por ejemplo:

```rust
fn main() {
	let t = true;

	let f: bool = false; // Con anotacion de tipo
}
```

La forma principal de usar valores bool es mediante las condicionales, como una expresión `if` .
### El tipo Carácter
El tipo `char` en rust es el tipo alfabético mas primitivo. Ejemplos:

```rust
fn main() {
	let c = 'z';
	let Z: char = 'Z';
	let pirson = '👽'
}
```

Nota: las literales `char` se declaran con `''` y no con `""` como el tipo `String` y es de 4 bites de tamaño y representa un valor `unicode` escalar, lo cual quiere decir que puede representar mas que ASCII, como tildes u otros idiomas.
## Tipos compuestos
Los tipos compuestos pueden tener múltiples valores dentro del mismo tipo. Rust tiene **2** tipos compuestos primitivos: **tuples o tuplas y arrays o listas**.
### El tipo tupla
Un `tuple` es una forma general de agrupar números de valores con una variedad de tipos en un solo tipo compuesto. los `tuples` tienen un tamaño arreglado: una vez declarados no pueden hacerse mas grandes o mas pequeños.
Se puede crear un `tuple` escribiendo una lista de valores separados por comas dentro de paréntesis. Cada posición en el `tuple` tiene un tipo, y los tipos de diferentes posiciones no tiene que ser los mismos.

```rust
fn main() {
	let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

La variable `tup` ata el `tuple` entero debido a que es considerado como un elemento compuesto. Para conseguir los valores individuales de un `tuple`, podemos usar coincidencia de patrones para reestructurarlo:

```rust
fn main() {
	let tup = (500, 6.4, 1);
	let (x,y,z) =  tup;

	println!("El valor de y es: {y}");
}
```

Este programa primero crea un `tuple` atado a la variable `tup`. Luego usa un patrón con `let` para agarrar `tup` y convertirlo en 3 variables se pardas, `x`, `y`, y `z`. Esto es llamado *reestructurar* porque rompe cada valor del `tuple` en tres partes. Finalmente, el programa imprime el valor de `y`, el cual es `6.4`.
También se puedo acceder a un elemento del `tuple` usando un punto(`.`) seguido por el indice que queremos acceder . Por ejemplo:

```rust
fn main() {
	let x: (i32, f64, u8) = (500, 6.4, 1);
	let quinientos = x.0;
	let seis_punto_cuatro = x.1;
	let uno = x.2;
}
```

Este programa crea la tupla `x` y luego accede cada elemento de la tupla utilizando sus indices respectivos. Como en la mayoría de los lenguajes de programación, el primer indice es 0.
Una tupla sin valores sin valores tiene un nombre especial, `unit`. Este valor y sus correspondientes tipos son escritos los dos con `()` y representan un valor vacío si no retornan otro valor.

### El tipo Lista
Otra forma de tener una colección de múltiples valores es con un `array`. Diferente al `tuple`, cada elemento del `array` tiene que tener el mismo tipo. Rust tiene `arrays` de tamaño fijo.
Escribimos los valores de un `array` como comas separadas dentro de square brackets:

```rust
fn main() {
	let a = [1,2,3,4,5];
}
```

Las listas son de ayuda cuando quieres de la información sea alocada en el `stack` en vez del `heap`. O cuando te quieres asegurar que siempre va a haber un numero fijo de elementos. Un `array` no es tan flexible como el tipo `vector`. Un *vector* es un tipo de colección similar proveído por la librería estándar que es *permitida* a crecer o hacerse pequeños. Si no estas seguro si utilizar un `array` o un `vector`, deberías usar un `vector`.
Aunque, las listas son mas útiles cuando sabes que el numero de elementos no va a cambiar. Por ejemplo, si usaras los nombres de los meses en un programa, deberías usar un `array` y no un `vector` porque sabrás que contendrá 12 elementos:

```rust
let months = ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio", "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"];
```

Escribes el tipo de una lista usando square brackets con el tipo de cada elemento, un punto y coma, y luego el numero de elementos en la lista: 

```rust
let a: [i32;5] = [1, 2, 3, 4, 5];
```

Aquí,  `i32` es el tipo de cada elemento. Después del punto y coma, el numero `5` indica que la lista contiene cinco elementos.
Puedes también iniciar un `array` que contiene el mismo valor por cada elemento especificando el valor inicial, seguido por un punto y coma, y luego el tamaño del `array` en square brackets.

```rust
let a = [3;5];
```

El `array` llamado `a` va a contener `5` elementos que tendrán el valor `3` inicialmente. Esto es lo mismo que escribir `let a = [3, 3, 3, 3, 3];` pero en una manera mas concisa.

#### Acceder elementos de una lista
Una lista es un pedazo de memoria del cual se conoce y su tamaño es estático, y que puede ser asignada en el `stack`. Puedes acceder los elementos de un `array` usando indices: 

```rust
fn main() {
	let a = [1, 2, 3, 4, 5];

	let first = a[0];
	let second = a[1];
}
```

En este ejemplo, la variable llamada `first` tendrá el valor `1` porque ese es el valor en el indice `[0]` en el `array`. La variable llamada `second` tendrá el valor `2` del indice `[1]` en el `array`.

#### Acceder elementos de forma invalida
Vamos a ver que pasa cuando se intenta acceder de forma invalida un elemento de un `array`. Vamos a decir que ejecutas este código para conseguir el indice del usuario:

```rust
use std::io;

fn main() {
	let a = [1, 2, 3, 4, 5];
	println!("Entra un indice");

	let mut index = String::new();

	io::stdin()
		.read_line(&mut index)
		.expect("No se pudo leer la linea");
	let index: usize = index
		.trim()
		.parse()
		.expect("Tienes que entrar un numero");
	let element = a[index];

	println!("El valor del elemento en el index {index} es: {element});
}
```

Este código compila bien. Si corres este código usando `cargo run` y entras `0`, `1`, `2`, `3`, o `4`, el programa va a imprimir el valor correspondido al indice del `array`. Pero si entras un numero después del fin del `array`, como `10`, veras esto:

```
thread 'main' panicked at src/main.rs:19:19:
index out of bounds: the len is 5 but the index is 10
note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Este programa resulta en un error de *runtime* al punto de usar un valor invalido durante la operación de indexar. El programa sale con un mensaje de error y no ejecutara la declaración de `println!`. Cuando intentas acceder a un elemento, rust va a revisar que el indice que especificaste sea menos que el tamaño del `array`. Si el indice es mayor o igual, rust va a hacer `panic!`. Esto pasa en el *runtime*, especialmente en este caso porque el compilador no puede saber que valor el usuario va a entrar cuando corran el código.

Este es un ejemplo de la seguridad de memoria en rust. En muchos lenguajes de bajo nivel, esta revisión no pasa. Cuando se provee un indice incorrecto,  la memoria se accederá inválidamente. Rust protege de estos tipos de errores mediante saliendo inmediatamente en vez de permitir el acceso a la memoria y continuar. 
