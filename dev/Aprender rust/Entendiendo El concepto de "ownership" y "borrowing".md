La "ownership" es una de las características mas importantes de rust y tiene implicaciones profundas en el resto del lenguaje. Permite a rust hacer afirmaciones de seguridad de memoria sin necesitar un recolector de basura, entonces es importante entender como funciones. Algunas de los temas son: **borrowing, slices y como rust escribe la data en la memoria**.

## Que es ownership?

La *ownership* es un conjunto de reglas que gobiernan como los programas de rust manejan la forma de la computadora de usar la memoria. Algunos lenguajes tienen recolección de basura que regularmente busca por memoria no usada mientras el programa es ejecutado; en otros lenguajes, el programador tiene que *allocate* y *free* la memoria de forma explicita. Rust usa una tercera forma: la memoria es manejada mediante un sistema de *ownership* con un conjunto de reglas que el compilador revisa. Si alguna de estas reglas son violadas, el programa no compilara. Ninguna de las características de el sistema de *ownership* hará que tu programa sea mas lento al ejecutarse.

## El stack y el heap

Muchos lenguajes de programación no requieren pensar sobre sobre el *stack* y el *heap* frecuentemente. Pero en el are de programación de sistemas, cuando un valor esta en el *stack* o en el *heap* afecta como el lenguaje se comporta y cuando tienes que hacer ciertas decisiones. Partes del sistema de *ownership* van a describir la relación entre el *stack* y el *heap*.

Tanto el *stack* como el *heap* son partes de la memoria disponibles a tu código durante el *runtime*, pero están estructuradas de forma diferente. 
### Stack

El *stack* guarda los valores en el **orden que los obtiene** y remueve valores en el **orden opuesto**. Esto es referido como *last in, first out*. Piensa del *stack* como platos: cuando agregas mas platos, los pones en el tope de la pila, y cuando quieres un plato lo sacas también del tope. Añadir o remover platos del medio o de abajo no funcionaria bien, Añadir data se llama *pushing onto the stack*, y remover data se llama *popping off the stack*. Toda la data en el stack tiene que tener un tamaño conocido y fijo. Data con un tamaño desconocido al tiempo de compilación o un tamaño que podría cambiar debe ser guardada en el *heap*.
![[Pasted image 20240512181859.png]]
![[Pasted image 20240512181923.png]]

### Heap

El heap es menos organizado: cuando pones data en el *heap*, pides una cierta cantidad espacio. El asignado de memoria encuentra un espacio vació en el *heap* que es lo suficientemente grande, lo marca en uso y retorna un *pointer*, que es la dirección de donde la memoria fue asignada. Este proceso se llama *allocating on the heap* y algunas veces es abreviado solo como *allocating* (*pushing values onto the stack* no es considerado como *allocating*). Porque el *pointer* al *heap* es de un tamano conocido y fijo, lo puedes poner en el *stack*, pero cuando quieras la *data* que ese *pointer* apunta a debes seguir al *pointer*. Piensa de esto como si estuvieras sentado en un restaurante. Cuando entras, dices el numero de personas en tu grupo, y el anfitrión encuentra una mesa vacía en la cual tu grupo pude entrar. Si alguien de tu grupo viene tarde, pueden pedir donde te has sentado para encontrarte.
[Direcciones de memoria](https://es.wikipedia.org/wiki/Direcci%C3%B3n_de_memoria)

![[Pasted image 20240512183245.png]]
![[Pasted image 20240512183217.png]]
![[Pasted image 20240512183232.png]]


### Stack versus Heap

*Pushing* al stack es mas rápido que *allocation* en el *heap* porque el *allocator*  de memoria nunca tiene que buscar por un lugar en donde guardar la data; Esa ubicación siempre va a estar en el tope del *stack*. Comparativamente, *allocating* espacio en el *heap* requiere mas trabajo debido a que el *allocator* tiene que primero encontrar un espacio lo suficientemente grande para contener la data, y lo luego realizar contabilidad para preparar para la siguiente *allocation*.

Acceder data en el *heap* es mas lento que acceder data en el *stack* porque tiene que seguir in *pointer* para llegar ahí. Contemporáneamente los procesadores son mas rápidos si saltan menos en la memoria. Continuando con la analogía, considera  un mozo en el restaurante que esta tomando todas las ordenes que puede. Es mas eficiente conseguir todas las ordenes de una mesa antes de seguir a la otra mesa. Apuntando una orden de la mesa A, luego de la mesa B,  y luego de la Mesa C, y de nuevo de la mesa B seria un proceso mas lento. De la misma forma, un procesador puede hacer su trabajo de forma mas eficiente si trabaja en data que esta mas cerca a otra data (como es en el *stack*) en vez de data que esta lejos entre si (como puede ser en el *heap*).

Cuando tu programa llama a una función, los valores pasados a esa función (incluyendo, potencialmente, *pointers* a la data en el *heap*) y las variables locales de la función serán *pushed onto the stack*. Cuando la función se acaba, esos valores van a ser *popped of the stack*.

Manteniendo cuenta de que partes del código están usando que data en el heap y que data en el stack, minimizando la cantidad de memoria duplicada en el *heap*, y limpiando la data que no se usa en *heap* para que no te quedes sin espacio son problemas que el sistema de *ownership* resuelve. Cuando entiendes el sistema de *ownership*, no tendrás que pensar  tanto en el *stack* o en el *heap*, pero sabiendo que el propósito del sistema de *ownership* es manejar data en el *heap* pude ayudar a explicar porque funciona de la forma que funciona. 

## Reglas de ownership

Las reglas son:

- Cada valor en rust tiene un *dueño*.
- Solo puedo haber *un* dueño a la vez.
- Cuando el dueño sale de *scope* el valor sera *dropped*.

### Alcance de las variables

Como un primer ejemplo de *ownership*, vamos a ver el alcance de algunas variables. El alcance, también llamado **scope** es el rango donde dentro del programa donde un ítem es valido.

```rust
let s = "hello";
```

La variable `s` se refiere a una *string literal*, donde el valor es *hardcoded* a texto en el programa. La variable es valida desde el punto donde es declarada hasta el fin del *scope*.

```rust
{                  // s no es valida aqui, porque todavia no ha sido declarada 
	let s = "hola";   // s es valida desde aqui

	// Hacer cosas con s
}                     // este scope ya se acabo, y s no es valido
```

En otras palabras, hay dos puntos importantes en este tiempo:
- Cuando `s` entra en *scope*, es valido.
- Se mantiene valido hasta que salga de *scope*.

En este punto, la relación entre *scopes* y cuando la variable es valida es similar a otros en otros lenguajes de programación.

### El tipo String

Para ilustrar las reglas de *ownership*, necesitamos un tipo de data que es mas compleja que las primitivas. Los tipos cubiertos previamente son de un tamaño conocido, pueden ser guardadas en el *stack* y *popped of the stack* cuando el *scope* se acaba, y pueden ser copiadas rápida y trivialmente para crear una instancia nueva e independiente si otra parte del código necesita usar el mismo valor en un *scope* diferente. Pero queremos ver a la data que esta guardada en el *heap* y explorar como rust sabe cuando limpiar la data, y el tipo `String` es un gran ejemplo.

Vamos a concentrarnos en las partes del `String` que se relacionan con *ownership*. Estos aspectos van a aplicar a otros tipos de data complejos, independientemente de que sean proveídos por la librería estándar o creados. 

Ya hemos visto las *string literals*, donde un string es el valor *hardcoded* en nuestro programa. Las *string literals* son convenientes, pero no para todas las situaciones en las que quisiéramos usar texto, Una razón es porque son inmutables. Otra razón es por el tamaño del tipo de *string literals* cuando escribimos nuestro código: Por ejemplo: que tal si quisiéramos guardar el input de un usuario? Para estas situaciones rust  tiene un segundo tipo, `String`. Este tipo maneja la data asignada en el *heap* y es capaz the guardar una cantidad de texto que no es conocida por nosotros en el tiempo de compilación. Puedes crear un `String` de una *string literal* con la función `from`:

```rust
let s = String::from("hola");
```

El operador de doble dos puntos `::` nos permite acceder al *namespace* de la función particular `from` dentro del tipo `String` en vez de `string_from`.
Este tipo de string *puede* ser mutado:

```rust
let mut s = String::from("hola");
s.push_str(", world!); // push_str() hace append de una literal a un string
println!("{}", s); // Esto va a imprimir `hola, world`
```

Entonces, cual es la diferencia? Porque un `String` puede ser mutado pero las literales no? La diferencia esta en como estos dos tipos manejan la memoria.

### Memoria y Asignación

En el caso de una *string literal*, sabemos el contenido al tiempo de compilación, el texto es *hardcoded* directamente en el ejecutable final. Esto es porque las *string literals* son rápidas y eficientes(como el *stack*). Pero estas propiedades solo existen por la inmutabilidad de las *string literals*. No podemos poner una gota de memoria en el binario por cada pieza de texto que su tamaño no es conocido en el tiempo de compilación y tal vez pude cambiar mientras se corre el programa.

Con el tipo `String`, para poder apoyar a una pieza de texto mutable y que puede crecer, debemos asignar una cantidad de memoria en el *heap*, no conocida en el tiempo de compilación, para poder sostener los contenidos esto significa que: 

- La memoria debe ser pedida al *allocator* de memoria durante el *runtime*.
- Necesitamos una forma de retornar esta memoria al *allocator* cuando acabamos con nuestro `String`.

La primera parte es hecha por nosotros: cuando llamamos `String::from`, esta implementación pide memoria cuando la necesita. Esto es prácticamente universal en todos los lenguajes de programación. 

Pero, la segunda parte es diferente, En lenguajes con *garbage collector (GC)*, el GC mantiene cuenta y limpia la memoria que ya no es usada, causando que no tengamos que pensar sobre eso. En la mayoría de los lenguajes sin GC, en nuestra responsabilidad identificar cuando la memoria ya no es usada y escribir en el programa para de forma explicita liberarla, igualmente cuando la queremos pedir. Haciendo esto de forma correcta ha sido un problema difícil en la historia de la programación. Si nos olvidamos, vamos a malgastar memoria. Si lo hacemos muy temprano, tendríamos una variable invalida. Si lo hacemos dos veces, eso es un *bug* también. Tenemos que juntar exactamente un `allocate` con exactamente un `free`.

Rust toma un camino diferente: la memoria es automáticamente retornada cuando la variables que la *owns* sale de *scope* . Esta es la versión usando un `String` en vez de una *string literal*.

```rust
{
	let s = String::from("hola"); // S es valido desde este punto
	// hacer cosas con s
							// este scope se acabo y s ya no 
							// Es valido
}
```

Hay un punto natural en el cual podemos retornar la memoria que nuestro `String` necesita al *allocator*: cuando `s` sale de *scope*. Cuando una variables sale de *scope*, Rust llama una función especial por nosotros. Esta función se llama `drop`, y es donde el autor del `String` puede hacer que el código retorne la memoria. Rust llama `drop` automáticamente al  final del *scope*

Nota: En C++, este patrón de *deallocating* recursos  al final de la vida de un ítem aveces se llama _Resource Acquisition Is Initialization (RAII)_. La función `drop`  en Rust es familiar si has usado patrones RAII.

Este patrón tiene un impacto profundo en la forma que el código en Rust  es escrito. Puede parecer simple ahora, pero la forma de ser del código puede ser inesperada en situaciones mas complicadas cuando queremos que múltiples variables usen la data que hemos asignado en el *heap*.

#### Variables y la Data interactuando con move

Múltiples variables pueden interactuar con la misma data en múltiples formas en Rust.

```rust
let x = 5;
let y = x;
```

Probablemente podemos adivinar que esta haciendo: "asigna el valor `5` a `x`; luego has una copia de este valor en `y`" Ahora tenemos 2 variables `x` y `y`, y las dos son igual a `5`. Esto es lo que realmente esta pasando, porque los enteros son valores simples con un valor arreglado y simple, y estos dos `5` son *pushed onto the stack*.

