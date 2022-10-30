# Tutorial de Python

*Python* es probablemente el lenguaje más popular hoy día. Su rotundo éxito se debe a su increíble versatilidad (desde aplicaciones *web* hasta aplicaciones móviles o de escritorio, pasando por la creación de *plugins* para distintas aplicaciones), su sintaxis sencilla (a pesar de sus numerosas capacidades), o el hecho de tratarse de un *software* libre. Además, está respaldado por una gran comunidad dispuesta a crear nuevas funcionalidades y dar soporte o aclarar dudas a cualquier usuario.

A pesar de ser un lenguaje interpretado, es relativamente rápido en su ejecución. Si la velocidad resultara un problema, también permite fácilmente la creación de módulos en lenguajes compilados como *C*.

*Python* es un lenguaje imperativo, de tipado dinámico y con pleno soporte para objetos.

A continuación se verá un resumen de los aspectos más relevantes del tutorial oficial del lenguaje. Se han añadido ampliaciones o aclaraciones donde se ha estimado necesario dar más claridad al texto.

## 2. USING THE PYTHON INTERPRETER

(Usar el intérprete de *Python*.)

### 2.1 Invoking the Interpreter

(Invocar el intérprete.)

Para iniciar una sesión interactiva del intérprete *Python* desde línea de comandos:

```
python3
```

Es posible que el comando necesario sea, por ejemplo, `python3.10`, dependiendo de las distintas versiones de *Python* que estén instaladas en nuestro sistema.

Para salir de la sesión, `Ctrl-D` (`Ctrl-W` en entorno *Windows*), o con la orden `quit()`. Si se le pasa como argumento un nombre de archivo, ejecuta ese *script*. También es posible ejecutar un módulo *Python* mediante:

```
python3 -m <mod>
```

En este caso, el módulo ***mod*** debe estar desarrollado para ser ejecutado como un *script*.

#### 2.1.1 Argument passing

(Paso de argumentos.)

Los argumentos son recogidos en `sys.argv`. En este caso, si se le ha pasado un nombre de *script* al intérprete, `sys.argv[0]` es un *string* con el nombre de dicho *script*. Si no se ha especificado *script*, es un *string* vacío.

#### 2.1.2 Interactive mode

(Modo interactivo.)

En este modo, el intérprete actúa como un terminal que acepta órdenes *Python*.

### 2.2 The Interpreter and Its Environment

(El intérprete y su entorno.)

#### 2.2.1 Source Code Encoding

(Codificación del código fuente.)

La codificación por defecto de un *script* es *UTF-8*. Si se codifica el *script* de otro modo, hay que indicarlo en la **primera línea del archivo**, de esta forma:

```
# -*- coding: <codificación> -*-
```

Donde `<codificación>` es el nombre de la codificación concreta (códec válido soportado por *Python*). Esta línea podría ser la segunda si la primera es la línea *shebang* de los *scripts* ejecutables (véase apéndice).

## 3. AN INFORMAL INTRODUCTION TO PYTHON

(Una introducción informal a *Python*.)

*Python* utiliza el carácter `#` para indicar comentarios. El comentario va desde dicho carácter hasta el fin de la línea física. Dentro de literales *string* no tiene esta función.

En los ejemplos, `>>>` y `...` indican el *prompt* del intérprete, y por tanto, introducción de código. Su ausencia indica salida (resultados).

### 3.1 Using Python as a Calculator

(Usar *Python* como una calculadora.)

#### 3.1.1 Numbers

(Números.)

En modo interactivo, entrar una expresión la evalúa y muestra su valor.

El símbolo `/` es el operador división de punto flotante (retorna siempre un *float*); la división entera es `//` (retorna un entero con el valor inferior, o *floor*); resto de la división (módulo) es `%`. Para potencias se usa `**`. Suma es `+`, resta `-`, y asignación `=` (para dar valor a variables).

Las expresiones con enteros y *floats*, convierten los enteros a *float* antes de evaluar.

En modo interactivo, la variable guión bajo (***\_***) se refiere al resultado anterior:

```python
>>> 11 * 3.5
38.5
>>> 5 + _
43.5
>>> round(_,2)  # función redondeo
43.5
```

Tipos numéricos: `int`, `float` y `complex`. Estos últimos usan el sufijo `j` (o `J`) para la parte imaginaria (p.e. ***3+5j***).

#### 3.1.2 Strings

(Cadenas.)

Pueden ir entre comillas simples o dobles. En el primer caso, si hay comillas simples en el interior, deberán ir *escaped* (precedidas de ***\\***). Lo mismo para las dobles.

En modo interactivo, cuando se presenta como salida un *string*, aparece entre comillas simples, a no ser que haya comillas simples en el interior (y ninguna doble), en cuyo caso lo presentará con comillas dobles. Presenta los caracteres *escaped* cuando procede. La función `print()` los presenta sin entrecomillar y sin *escape characters*.

Un *raw string* toma los caracteres tal cual, no entiende de *escaped characters*:

***r'Hola,\\niño'*** (o ***r"Hola,\\niño"***) incluye el carácter '***\\***' entre la coma y la '***n***'.

Para *multiline strings* podemos usar *triple-quoted strings* (*string* de triple comilla): ***\"\"\"...\"\"\"*** o ***\'\'\'...\'\'\'*** y todos los *newlines* que tecleemos quedarán también definidos en el *string*.

De todas formas, si el último carácter de una línea de código es '***\\***', la línea se concatena con la siguiente, aunque estemos dentro de un *triple-quoted string*. Es la forma de definir una línea lógica, compuesta por dos o más líneas físicas.

El operador ***+*** se puede usar para concatenar *strings*, y ***\**** para multiplicarlos (duplicar, triplicar, etc.), etc.:

```python
>>> letter = 'A'
>>> word = 'Help' + letter
>>> word
'HelpA'
>>> '<' + word * 5 + '>'
'<HelpAHelpAHelpAHelpAHelpA>'
```

Los literales *string* contiguos separados por cualquier cantidad de espacios (incluyendo saltos de línea) son concatenados automáticamente.

Si ***st*** es una variable que contiene un *string*, `st[0]` será el primer carácter, `st[1]` el segundo, etc. `st[-1]` será el último, `st[-2]` el penúltimo, etc. También funciona el llamado *slicing:* `st[0:2]` son los caracteres desde el 0 (incluido) al 2 (excluido), `st[2:5]` del 2 al 4, etc. El primero va siempre incluido y el segundo excluido, ya que de ese modo, `st[:i]` + `st[i:]` es el *string* entero. La omisión del primer número se refiere a 0 (principio del *string*), y la omisión del segundo, al número de elementos del *string* (final del *string*).

Tanto un índice como un *slice* devolverán un *string* (si es un índice, un *string* de longitud 1, no existe el tipo carácter).

Un índice negativo contará desde la derecha: -1 es el último carácter, -2 el penúltimo, etc.

En el *subscripting* (`st[N]`), especificar un índice fuera de límites (*out of bounds*) producirá un error, tanto si es positivo como negativo. En *slicing* nunca da error:

```python
>>> word = 'Python'
>>> word[4:42]
'on'
>>> word[42:]
''
```

Los *strings* son inmutables, es decir, no podemos cambiar su contenido. Si queremos otro contenido hay que crear un *string* nuevo.

*Python* incluye la función incorporada (*built-in function*) `len(s)`, que da la longitud del *string* (número de caracteres) que se le pasa como argumento (en el ejemplo, ***s***).

#### 3.1.3 Lists

(Listas.)

Las listas pueden contener elementos de tipos distintos. Para acceder a los elementos de una lista, se puede indexar o usar *slicing* (como en todos los tipos de tipo **secuencia**). También se pueden concatenar listas mediante ***+***, o multiplicar con ***\****.

> Un tipo **secuencia** es, por definición, un tipo que se puede indexar o usar *slicing*, y del cual puede extraerse su longitud mediante la función `len()`.

Un las secuencias, un índice devuelve un elemento de la lista, mientras que un *slice* devolverá una lista, aunque sea de un solo elemento, o una *shallow copy* (copia superficial, véase más abajo) de la lista original en el caso de ***lista[:]***.

Las listas son mutables, con lo que se puede cambiar su contenido. Otra cosa sería cambiar el contenido de sus elementos, que solo será posible si el elemento en cuestión es, a su vez, mutable:

```python
>>> letters = ['a', 'b', 'c', 'd', 'e', 'f', 'g']
```

Para añadir elementos al final:

```python
>>> letters.append('h')  # append() es un método propio
                         # del tipo lista
>>> letters
['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
```
Cambiar elementos:

```python
>>> letters[0] = ['A']
>>> letters[2:5] = ['C', 'D', 'E']
>>> letters
['A', 'b', 'C', 'D', 'E', 'f', 'g', 'h']
```

Eliminar elementos:
```python
>>> letters[2:5]=[]
>>> letters
['A', 'b', 'f', 'g', 'h']
```

La *built-in function* `len()` también se aplica a listas, devolviendo el número de elementos de primer nivel (un solo elemento lista con varios elementos a su vez, contará como uno). Las listas son, pues, anidables.

```python
>>> len(letters)
5
```

Borrar todos los elementos:

```python
>>> letters[:] = []
>>> letters
[]
```

Para ver si un elemento ***e*** existe en una lista ***L***, `e in L` retorna ***True*** (verdadero) si es cierto y ***False*** (falso) de otro modo.

### Aclaraciones adicionales

#### Índices y *slices*

`letters[0]` se refiere al primer **elemento**; `letters[0:1]` se refiere a una **sublista** que contiene únicamente el primer elemento:

```python
>>> letters = ['a', 'b', 'c', 'd', 'e']
>>> letters[2] = [1, 2, 3]  # letters[2] era 'c'
>>> letters
['a', 'b', [1, 2, 3], 'd', 'e']
>>> letters = ['a', 'b', 'c', 'd', 'e']
>>> letters[2:3] = [1, 2, 3]  # letters[2:3] era ['c']
>>> letters
['a', 'b', 1, 2, 3, 'd', 'e']
```

Si queremos borrar un solo elemento, `letters[2] = []` sustituirá el elemento por una lista vacía; en cambio `letters[2:3] = []` elimina el elemento.

Para añadir uno o más elementos a una lista sin usar `append()`, se puede hacer con cualquier *slice* que empiece *out of bounds* por la derecha:

```python
>>> a = [1, 2, 3]
>>> a[len(a):] = [4, 5]  # len(a) es 3; a[3] está fuera
                         # de límites
>>> a
[1, 2, 3, 4, 5]
```
Para sustituir ***n*** elementos por ***m*** elementos, se deben seleccionar los ***n*** elementos a sustituir en el *slice*, y asignarle la nueva lista de ***m*** elementos. Por ejemplo, para sustituir 2 elementos (3º y 4º) por uno solo:

```python
>>> a = [1, 2, 3, 4, 5]
>>> a[2:4] = ['tres y cuatro']
>>> a
[1, 2, 'tres y cuatro', 5]
```

Para insertar uno o más elementos en la posición ***n*** sin eliminar ningún elemento existente, se debe insertar una lista en un *slice* que inicie en la posición deseada (*n*) y termine antes de esa, por ejemplo mediante el *slice* ***[n:n]***:

```python
>>> a = [1, 2, 3, 4, 5]
>>> a[3:3] = [3.25, 3.5, 3.75]
>>> a
[1, 2, 3, 3.25, 3.5, 3.75, 4, 5]
```

Para insertar uno o más elementos por el principio, se debe usar un *slice* que inicie en el primero o antes, y termine antes del primero:

```python
>>> a = [1, 2, 3]
>>> a[:0] = ['a', 'b', 'c']  # o a[0:0] = ...
>>> a
['a', 'b', 'c', 1, 2, 3]
```

Aunque no tenga nada que ver con índices y *slices*, existen otras formas de añadir elementos a una lista. Aprovechando que la suma de dos listas produce a su vez una lista:

```python
>>> a =  [3, 4, 5]
>>> a = a + [6, 7, 8]  # añadir elementos al final
>>> a
[3, 4, 5, 6, 7, 8]
>>> a = [1, 2] + a  # al principio
>>> a
[1, 2, 3, 4, 5, 6, 7, 8]
```

#### Organización interna de los datos

*Python* trata las variables como apuntadores, o referencias a su valor. Este valor se halla en una tabla *hash*, o cache de memoria. El valor al que **referencia** una variable es siempre un **objeto** en memoria.

> Se puede entender una variable como un apuntador a un objeto, en el más puro estilo *C*.

En *Python*, cada **objeto** tiene su propio identificador (ID), que es el "código" utilizado para acceder a él en memoria (ya sea por número *hash*, dirección de memoria, o lo que el intérprete *Python* estime conveniente). En todo caso es un número único por cada objeto.

Para conocer el ID de un objeto, podemos usar la *built-in function* `id()`, pasándole como argumento la variable que lo referencia. Cuando dos objetos ***A*** y ***B*** tienen el mismo ID, la expresión `A is B` retorna ***True***, y significa que ambas variables referencian (apuntan) al mismo objeto en memoria.

Algunos objetos *inmutables* (números, *strings*, etc.) pueden tener un ID fijo, utilizado en un *hash* en *Python* para ahorrar memoria.

El operador asignación (`=`) simplemente asigna un ID o referencia a la variable.

```python
>>> a = [[1, 2, 3], [11, 22, 33], [111, 222, 333]]
>>> b = a
>>> a is b
True
```

Ambos objetos apuntan al mismo lugar, por tanto:

```python
>>> b[0] = 99  # afectará también al objeto a
>>> a
[99, [11, 22, 33], [111, 222, 333]]
```

Si queremos que cada variable sea una referencia a un objeto distinto, debemos hacer copias del objeto original.

#### Copia superficial (*shallow copy*) y copia profunda (*deep copy*)

Siguiendo con el ejemplo anterior, y una vez definido ***a***, podemos hacer una *shallow copy* en ***b***, de varias formas distintas:

- `b = a[:]` - si el objeto *sliced* está a la izquierda del operador asignación, no se crea una *shallow copy*, sino que sirve solamente para manipular los elementos de la lista a los que se refiere, como hemos visto antes.
- `b = list(a)`
- `b = a.copy()`
- `b = copy.copy(a)` (se debe importar el módulo ***copy*** de la biblioteca estándar).

Después de ejecutar una de estas acciones:

```python
>>> a is b
False
```

Ahora ***a*** y ***b*** son objetos distintos. Sin embargo al ser una copia superficial, a pesar de ser dos listas en zonas distintas de memoria, los **elementos** de estas sí comparten referencias. Si esos elementos son mutables, entonces cambiar su contenido en uno de ellos afectará al otro objeto. Veamos un ejemplo con una lista anidada:

```python
>>> a = [[1, 2, 3], [11, 22, 33], [111, 222, 333]]
>>> b = a[:]
>>> a is b
False
>>> b[0] = 99  # no afectará al objeto a
>>> a
[[1, 2, 3], [11, 22, 33], [111, 222, 333]]
>>> a[0] is b[0]  # recordemos que b[0] es 99 y a[0] no
False
>>> a[1] is b[1]
True
>>> a[2] is b[2]
True
>>> b[1][0]=99  # sí afectará al 2º elemento de a
>>> a
[[1, 2, 3], [99, 22, 33], [111, 222, 333]]
```

Si lo que queremos es hacer una copia profunda (*deep copy*), es decir, crear copias no solo de los elementos de primer nivel, sino de todos ellos, solo hay una forma:

```python
>>> b = copy.deepcopy(a)
>>> a is b
False
>>> a[0] is b[0]
False
```

A no ser que el elemento sea inmutable, en cuyo caso podría devolver ***True*** (depende del intérprete). Esto se ve fácilmente con este ejemplo:

```python
>>> a = 33
>>> b = 33
>>> a is b  # ambos objetos apuntan al hash del número 33
True
```

> Dependiendo de cómo se calcule el ID de los objetos inmutables, el anterior ejemplo podría retornar ***False***. Sin embargo, en la implementación oficial del lenguaje (*CPython*), retorna ***True***.

### 3.2 First Steps Towards Programming

(Primeros pasos hacia la programación.)

Veamos un ejemplo de cómo podríamos calcular la serie de Fibonacci (la suma de dos elementos define el siguiente elemento):

```python
>>> a, b = 0, 1
>>> while b < 10:
...     print(b)
...     a, b = b, a+b
...
1
1
2
3
5
8
```

Podemos ver la asignación múltiple (*multiple assignment*), la forma de agrupar sentencias en *Python* (mediante la indentación), y el uso de la función `print()`, la cual toma un número indeterminado de parámetros, y los envía a la salida separados por un espacio; al final imprime un salto de línea (a no ser que le demos valor al argumento `end`).

> La asignación múltiple es la combinación del empaquetado de tuplas (*tuple packing*) con el desempaquetado de secuencias (*sequence unpacking*). Se verá más adelante.

La sentencia `while` ejecutará reiteradamente el código asociado (todo lo que esté indentado), hasta que la condición (`b < 10`) sea falsa.

Las expresiones de asignación múltiple **a la derecha** del operador asignación (`=`) se evalúan de izquierda a derecha. Solo cuando están todas evaluadas empiezan a asignarse a las variables a la izquierda del `=`.

En cuanto a la evaluación de condiciones, cualquier entero distinto de 0 es verdadero (***True***), mientras que cero es falso (***False***), como en lenguaje *C* (y otros). Cualquier secuencia con más de cero elementos se evalúa a verdadero, mientras que si está vacía se evalúa a falso.

## 4. MORE CONTROL FLOW TOOLS

(Más herramientas de control de flujo.)

### 4.1 if Statements

(Sentencias `if`.)

```python
>>> x = int(input("Introduce un entero: "))
Introduce un entero: 42
>>> if x < 0:
...     x = 0
...     print('Negativo cambiado a cero')
... elif x == 0:
...     print('Cero')
... elif x == 1:
...     print('Uno')
... else:
...     print('Más')
...
More
```

El código asociado a la sentencia `if` se ejecuta solo si la condición (en este caso `x < 0`) es cierta. De lo contrario se irán evaluando las sentencias `elif` (puede haber cero o más de ellas), y si alguna de las condiciones es cierta se ejecutará. La sección `else`, opcional (solo puede haber una) se ejecutará si todas de las condiciones anteriores se han evaluado como falsas. Solo una de las secciones se ejecutará (ya sea la `if`, `elif` o `else`).

### 4.2 for Statements

(Sentencias `for`.)

La sentencia `for` itera sobre una secuencia:

```python
>>> a = ['gato', 'ventana', 'defenestrar']
>>> for x in a:
...     print(x, len (x))
...
gato 4
ventana 7
defenestrar 11
```

En este caso, la variable `x` irá tomando los valores de la secuencia `a`.

Un código que modifique la secuencia original dentro de las iteraciones, puede ser muy difícil de depurar. En ese caso es mejor iterar sobre una copia de la secuencia o crear una nueva secuencia sobre la que realizar las operaciones.

Veamos un ejemplo con un diccionario (se verá este concepto más adelante):

Itera sobre una copia:

```python
>>> for user, status in users.copy().items():
...     if status == 'inactivo':
...         del users[user]
```

Usa una secuencia nueva:

```python
>>> active_users = {}  # el nuevo diccionario
>>> for user, status in users.items():
...     if status == 'activo':
...         active_users[user] = status
```

### 4.3 The range() Function

(La función `range()`)

Genera una secuencia con una progresión aritmética:

`range(10)` genera los valores 0, 1, 2, 3, 4, 5, 6, 7, 8, 9.

`range(5, 10)` genera los valores 5, 6, 7, 8, 9.

`range(0, 10, 3)` genera los valores 0, 3, 6, 9 (*step* 3).

`range(-10, -100, -30)` genera los valores -10, -40, -70.

El valor retornado por la función se comporta como una lista, pero no lo es. Es un **objeto iterable** que no crea los elementos en memoria, ahorrando así espacio. Para crear una lista a partir de cualquier iterable, se usa la función `list()` (función que construye una lista a partir de un iterable):

```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

### 4.4 break and continue Statements, and else Clauses on Loops

(Sentencias `break` y `continue`, y cláusulas `else` en bucles.)

`break` sale del bucle envolvente `while` o `for` más inmediato.

`continue` pasa a la siguiente iteración del bucle envolvente `while` o `for` más inmediato.

El bucle `while` y `for` pueden tener una cláusila `else`, que se ejecutará después del terminar la última iteración del bucle, pero no se ejecuta si salimos con `break` (de forma similar a la cláusula `else` de la sentencia
`try`, que se ejecuta solo si no se produce una excepción, como se verá más adelante).

### 4.5 pass Statements

(Sentencias `pass`.)

La sentencia `pass` no hace nada: para bucles infinitos (vacíos), clases vacías, como *placeholder* de una función todavía no implementada, etc.

### 4.6 match Statements

(Sentencias `match`.)

La sentencia toma un valor, y lo va comparando con sucesivos patrones hasta que halla una coincidencia.

```python
match estado:
    case 400:
        print("Bad request")
    case 404:
        print("Not found")
    case 418:
        print("I'm a teapot")
    case _:
        print("Ha habido un error")
```

En este ejemplo, el valor de la variable ***estado*** se irá comparando con los sucesivos patrones literales (400, 404, 418) hasta hallar una coincidencia, en cuyo caso ejecutará la cláusula `case` correspondiente, dando por terminada la ejecución de la sentencia `match`. En caso de no hallarse ninguna coincidencia, el guión bajo (***\_***) sirve como comodín: coincide con todo. Su inclusión es opcional, y debería, lógicamente, ser la última cláusula `case`.

Por otro lado, se pueden agrupar varias cláusulas `case` en una sola, usando el operador **o lógico** (***|***):

```python
match estado:
    case 400 | 404 | 418:
        print("No permitido")
    case _:
        print("Ha habido un error")
```

Las cláusulas `case` funcionan de forma distinta si en lugar de literales utilizamos variables. En ese sentido, se realizan las asignaciones necesarias en las variables indicadas cuando se produzca una coincidencia. Veamos un ejemplo:

```python
# La variable 'punto' es una secuencia con dos valores
# numéricos:
match punto:
    case (0, 0):
        print("Origen")
    case (0, y):
        print(f"Y={y}")
    case (x, 0):
        print(f"X={x}")
    case (x, y):
        print(f"Cualquier otro punto")
    case _:
        print("No es un punto")
```

- El primer caso (0, 0), es una expresión literal, no hay variables, por lo que funciona como en el ejemplo anterior.
- El segundo caso (0, ***y***) funciona así: si `punto[0]` es 0, entonces hay coincidencia, y se asigna el valor `punto[1]` a la variable ***y***.
- De forma similar, el tercer caso (***x***, 0) funciona así: si `punto[1]` es 0, entonces hay coincidencia, y se asigna el valor de `punto[0]` a la variable ***x***.
- Suponiendo que ***punto*** sea efectivamente una secuencia con dos elementos, y en caso de no haber coincidencia con ninguno de los tres anteriores casos, la habría seguro con el cuarto caso (***x***, ***y***), ya que no hay literales en el patrón. En este caso, se haría la asignación múltiple `x, y = punto`.
- En el último caso (***\_***) solo se ejecutaría en el caso que ***punto*** no fuese una secuencia con exactamente dos elementos, ya que no coincidiría con ningún caso anterior.

Supongamos que queremos que el patrón sea de una clase concreta. Por ejemplo, si tenemos una clase ***Punto*** que tiene dos atributos, ***x*** e ***y*** (se verán las clases más adelante), y que la variable ***punto*** es de esa clase:

```python
match punto:
    case Punto(x=0, y=0):
        print("Origen")
    case Punto(x=0, y=y):
        print(f"Y={y}")
    case Punto(x=x, y=0):
        print(f"X={x}")
    case Punto():
        print("Cualquier otro punto")
    case _:
        print("No es un punto")
```

Analicemos cada caso:

- `Punto(x=0, y=0)` es como un literal. Sería como un "literal objeto", aunque en *Python* no existe tal cosa. En este caso, solo habrá coincidencia cuando la variable sea de tipo ***Punto*** y sus dos atributos tengan valor 0.
- En el caso `Punto(x=0, y=y)` existe coincidencia cuando el atributo ***x*** del objeto tiene valor 0. Por otro lado, también captura el valor del atributo ***y*** en una variable llamada (también) ***y***. Para evitar confusión, se podría haber escrito con nombres diferentes. Por ejemplo, `Point(x=0, y=vary)`, habría capturado (asignado) el valor del atributo ***y*** del punto a una variable ***vary***. Téngase en cuenta el orden, ya que `y=vary` puede parecer bastante contraintuitivo (ya que la variable asignada está a la derecha del igual).
- `Punto(x=x, y=0)` es como el caso anterior, pero la coincidencia se produce cuando el atributo ***y*** del punto es cero. En este caso, se captura el valor del atributo ***x*** en una variable ***x***.
- En `Punto()` existe coincidencia con todos los valores del tipo punto. En este caso no existe ninguna captura de variables. Si queremos las mismas coincidencias, pero capturando los valores del punto, podríamos escribir `Point(x=x, y=y)`.
- El caso por defecto `_` coincide con cualquier valor, incluso los que no son del tipo ***Punto***.

Las clases de *Python* no definen, por defecto, un orden en los argumentos, con lo que los patrones no aceptan argumentos posicionales (más sobre esto posteriormente). Pero algunas clases (como las *data classes*) sí lo hacen, con lo que sí serían permitidos los argumentos posicionales (siempre antes que los *keyword arguments*).

Los patrones pueden incluir secuencias, y tener cualquier tipo de anidamiento. Ejemplos de patrones:

- `case [ Point(x=0, y=vary) ]` coincidiría con secuencia (lista o tupla) que tuviera un solo elemento, y que ese elemento fuese un punto con el atributo ***x*** a cero. Se captura el valor del atributo ***y*** en la variable ***vary***. Es equivalente a `case ( Point(x=0, y=vary) )` a todos los efectos.
- `case [ Point(x=0, y=vary), _ ]` coincidiría con secuencia (lista o tupla) que tuviera **dos** elementos, siendo el primero un punto con cualquier valor (no hay captura). Es equivalente a `case ( Point(x=0, y=vary), _ )` a todos los efectos.

Los patrones pueden incorporar los llamados *guards* (guardias), que limitan la coincidencia a ciertos valores. Se hace usando una expresión tras la palabra clave `if`. Por ejemplo:

```python
match punto:
    case Punto(x=xvar, y=yvar) if xvar == yvar:
        print(f"Y=X en {xvar}")
    case Punto():
        print(f"No está en la diagonal")
```

El primer caso coincide con todos los puntos donde los dos atributos tienen el mismo valor (línea diagonal). El segundo coincide con todos los puntos. Obsérvese que para la expresión del *guard* podemos utilizar las variables capturadas.

La forma de indicar una secuencia en un patrón puede hacerse indistintamente de tipo lista (con corchetes) o de tipo tupla (con paréntesis), ya que coincidirán con cualquier secuencia (no *strings* ni iteradores).

Los patrones admiten *unpacking* usando asterisco en el último elemento de una secuencia. Por ejemplo, `case [a, b, *resto]` coincidirá con una secuencia que contenga 2 o más elementos. El primer elemento será asignado a la variable ***a*** y el segundo a la ***b***. El resto será asignado a una lista en la variable ***resto***: si la secuencia original tenía solo dos elementos, ***resto*** será una lista vacía.

Supongamos que solo deseamos capturar el valor del segundo elemento. Podría hacerse así: `case [_, b, *_]`.

En cuanto a los patrones de los objetos *mapping* (diccionarios, se verán más adelante), podemos indicarlo así: `case { "anchura": a, "altura": 20 }`. En este caso, coincidirá cuando la clave ***altura*** tenga el valor 20. Además, en la variable ***a*** se capturará el valor de la clave ***anchura***. Si queremos asegurarnos de la presencia de una clave, podemos hacer `case { "anchura": a, "altura": 20, "profundidad": _ }`. En este caso solo coincidirá si existe la clave ***profundidad***, independientemente de su valor, y sin capturar dicho valor. En todo caso, el resto de claves no mencionadas (si existen) son simplemente ignoradas. Si queremos capturarlas en un diccionario: `case { "anchura": a, "altura": 20, **resto }`, en cuyo caso, el diccionario ***resto*** recogerá el resto de elementos (si no hay más, será un diccionario sin elementos). Indicar `**_` es redundante, con lo que no está permitido.

Se puede capturar el valor de un elemento del patrón usando `as`: `case (Punto(x1, y1), Punto(x2, y2) as p2)` capturará el valor del segundo elemento de la secuencia en la variable ***p2***.

Los *dotted names* (nombres con punto) se considerarán constantes, y no variables donde capturar un valor:

```python
p = Punto(25, 50)  # punto con coordenada (25, 50)
coordx = input("Introduce coordenada X: ")
match coordx:
    case p.x:
        print("Coordenada X es 25.")
    case _:
        print("Coordenada X no es 25.")
```

En este ejemplo, ***p.x*** no se usa para capturar el valor de ***coordx***, ya que es un *dotted name*. En su lugar se usará el valor que tiene (25) como si fuese un literal entero.

### 4.7 Defining Functions

(Definir funciones.)

```python
>>> def fib(n):  # escribe la serie de Fibonacci hasta n
...     """Print a Fibonacci series up to n."""  # docstring
...     a, b = 0, 1
...     while a < n:
...         print(a, end=' ')
...         a, b = b, a+b
...     print()
...
>>> fib(2000)
0 1 1 2 3 5 8 13 21 34 55 89 144 233 377 610 987 1597
```

Vemos una definición (`def`) de una función, con nombre ***fib*** y lista de parámetros formales (*formal parameters*) o simplemente parámetros (en este caso, uno solo: ***n***). En la llamada a dicha función, se denominan argumentos (*arguments* o *actual parameters*), en este caso, el valor 2000.

> Denominamos **parámetros** a las variables indicadas entre paréntesis en la definición de la función, mientras que los valores utilizados en la **llamada** (o **invocación**) de dicha función se denominan **argumentos**.

La *docstring* (literal *string* como primera sentencia de la función) es opcional, aunque es una buena práctica: algunas herramientas la usan para generar documentación.

En la función vemos una asignaciones múltiples y un bucle `while`, por ejemplo. Tras la definición observamos una llamada a esa función con un valor de 1000 para el argumento.

Durante la ejecución de la función, se crea una nueva tabla de nombres local. **Todas** las asignaciones (con `=`) se hacen en la tabla local. Cuando se hace *referencia* a un nombre (de función u otro tipo de objeto), se busca primero en esa tabla local, luego en la tabla de la función envolvente inmediate (*enclosing function*), ya que está permitido el *nesting* (definir funciones dentro de otras funciones). Si tampoco se encuentra, se busca en la siguiente función envolvente (si existe), etc., hasta llegar a la tabla global; finalmente se busca en los nombres incorporados (*builtin names*).

Como hemos dicho, una *asignación* se hace directamente en la tabla local. Así, no podemos asignar valor a una variable global desde una función, pero sí usar su valor. Si queremos asignarle valor, debemos declarar, en la función, ese nombre como `global` para una variable global, o `nonlocal` para una variable en una *enclosing function*.

Al pasar una variable como argumento, se pasa el valor de esa variable; como ese valor es, de hecho un ID, es decir, una referencia al objeto, en la práctica es un paso por referencia: los cambios hechos al objeto (siempre que sea mutable) dentro de la función, son visibles al llamador. En todo caso, el valor de esta referencia (ID) se almacena en la tabla local de la función mientras esta se ejecuta. A parte de poder cambiar el valor del objeto referenciado por este parámetro, también podríamos cambiar el valor de la variable, es decir hacer que referenciara a otro objeto (esto no afectaría a la variable del módulo llamador).

Cuando el valor de una variable es accesible desde un fragmento de código determinado (en una función, por ejemplo), se dice que tal variable está en el ámbito (*scope*) de esa función.

La definición de una función almacena al identificador de la misma en la tabla de nombres actual (como si fuese una asignación). Es decir, el identificador es una referencia a un objeto función.

```python
>>> fib
<function fib at 10042ed0>
>>> f = fib
>>> f(100)  # o fib(100)
0 1 1 2 3 5 8 13 21 34 55 89
```

Todas las funciones retornan un valor. Si no tiene sentencia `return` (o retorna con `return` sin argumento), devuelve el valor vacío ***None*** (valor *built-in*).

La sintaxis de la sentencia de retorno es `return <valor>`, o simplemente `return`.

### 4.8 More on Defining Functions

(Más acerca de definir funciones.)

#### 4.8.1 Default Argument Values

(Valores de argumento por defecto.)

En la definición de la función, puede haber uno o más parámetros con valor por defecto (*default parameter*), pero no puede haber ninguno de estos **antes** de un parámetro sin valor por defecto (*non-default parameter*).

```python
def pregunta(prompt, reintentos=4, mensaje='¡Sí or no, por favor!'):
    # definición de la función
```

La función define un *non-default parameter* y dos *default parameters*. En este caso podemos pasar 1, 2 o 3 argumentos **en la llamada**. Si le damos más de uno, irá asignando en orden de aparición, es decir, el segundo argumento se asignará a ***reintentos*** y el tercero a ***mensaje***.

Los valores por defecto de los parámetros pueden ser (a parte de expresiones literales) constantes o variables, que deben estar definidas en el ámbito (*scope*) donde realizamos la definición de la función.

El valor por defecto se evalúa una sola vez. Si ese valor es mutable, irá acumulando las modificaciones entre llamadas:

```python
def f(a, L=[]):
    L.append(a)
    return L
    ```

En este caso, se irían añadiendo valores a la lista vacía inicial en las llamadas a la función. Si lo que queremos es que cada vez se empiece con una lista vacía, podríamos hacer algo así:

```python
def f(a, L=None):
    if L is None:
        L = []
    L.append(a)
    return L
```

Inicializamos la variable a una lista vacía cada vez que no le pasamos valor a ***L***; en el resto de ocasiones mantiene su valor.

#### 4.8.2 Keyword Arguments

(Argumentos de palabra clave.)

A la hora de llamar a una función, podemos simplemente pasar los argumentos en orden, o podemos especificar a qué parámetro corresponde uno o más de nuestros argumentos. Supongamos la función:

```python
def lorito(voltaje, estado='rígido', acción='bum', tipo='azulado'):
    # ...
```

**En la llamada**, podemos usar *keyword arguments* (del tipo `nombre=valor`) o argumentos posicionales (*non-keyword arguments*, sin nombre), o una mezcla de ambos, pero **no puede haber argumentos posicionales tras un** ***keyword argument*** (la posición de estos últimos entre sí no es importante). Hay que tener en cuenta que usemos el modo que usemos, debemos dar valor a todos los *non-default parameters* definidos en la función. Llamadas válidas:

```python
lorito(1000)  # el valor se asigna a voltaje
lorito(acción='BUUUUM', voltaje=1000000)
lorito('mil', estado='criando malvas')
lorito('un millón', 'despojado de toda vida', 'saltar')
```

Estas son incorrectas:

```python
lorito()    # falta voltaje
lorito(voltaje=5.0, 'dead')  # posicional sigue a keyword
lorito(110, voltaje=220)    # voltaje dos veces
lorito(actor='John Cleese')    # ¿qué es 'actor'?
```

**En la definición**, se puede incluir opcionalmente un *final formal parameter*, del tipo ***\*\*parm*** (tiene que ser el último de todos los parámetros), el cual recoge **en un diccionario** todos los *keyword arguments* que se pasen **en la llamada** y que no correspondan a ningún parámetro de la definición. El orden en el que estarán en el diccionario será el mismo que en la llamada.

De forma similar, también podemos incluir **en la definición** un parámetro del tipo ***\*parm***, que recoge **en una tupla** todos los *non-keyword arguments* **de la llamada** que no correspondan a ningún parámetro de la definición.

Si hay un parámetro **definido** después de ***\*parm***, no puede recibir valor en la llamada a través de un *non-keyword argument*, ya que todos estos irán a parar a ***\*parm***. Si dicho parámetro es un *default parameter*, obtendría siempre su valor por defecto, lo cual es un poco absurdo; y si no es *default parameter*, recibiríamos error de ejecución, ya que no sería posible darle valor. Por lo tanto, todos los parámetros que **en la definición** estén después de ***\*parm*** deben indicarse **en la llamada** como *keyword arguments*.

#### 4.8.3 Special parameters

(Parámetros especiales.)

Los **parámetros** definidos en una función son, por defecto, *positional-or-keyword* (posicional o con nombre), es decir, podemos darles valor mediante *keyword arguments* o *non-keyword arguments*, como deseemos (siempre que demos valor a todos los *non-default parameters*). Pero es posible restringir eso desde la definición de la función.

Hay dos tipos de parámetros formales que no se corresponden con un parámetro en sí (no se les da valor en la llamada), pero son indicaciones de cómo proceder desde la llamada.

Si se define el parámetro especial `/`, todos los parámetros anteriores a ese solo pueden tomar valor de *non-keyword arguments*. Se trata de los llamados *positional-only parameters*.

Por otro lado, si existe el parámetro `*`, los parámetros posteriores a ese son *keyword-only parameters*, lo cual significa que solo pueden recibir valor de *keyword arguments* en la llamada.

Si ambos parámetros especiales están definidos, `/` debe ser anterior a `*` y los parámetros que figuren entre ellos en la definición (si los hay) son *positional-or-keyword parameters*, es decir, pueden recibir valor de *keyword arguments* o de *non-keyword arguments* en la llamada, indistintamente.

Por ejemplo, si hacemos lo siguiente:

```python
def foo(nombre, **parms):
    pass

foo('Pepe', nombre='Paco')
```

La llamada producirá un error, ya que asignamos valor dos veces al parámetro ***nombre***. Pero si lo hacemos así:

```python
def foo(nombre, /, **parms):
    pass

foo('Pepe', nombre='Paco')
```

Esa llamada ya no da error, puesto que el parámetro ***nombre*** es un *positional-only parameter*, es decir, solo puede recibir valor de un *non-keyword argument*. En la llamada solo hay un *non-keyword argument*, con el valor ***Pepe***. El segundo argumento es un *keyword argument*, con lo cual pasa a dar valor directamente a ***\*\*parms***.

#### Resumen de tipos de argumento y parámetro

En la definición (parámetros), en cuanto a su valor por defecto:

- Parámetro por defecto (*default parameter*): no es obligatorio darle valor en la llamada, ya que tiene un valor por defecto definido.
- Parámetro sin valor por defecto (*non-default parameter*): es obligatorio darle valor en la llamada, ya que no tiene valor por defecto.

En la llamada (argumentos):

- Argumento sin nombre (*non-kewyord argument*): se indica solamente un valor.
- Argumento con nombre (*kewyord argument*): se indica el valor junto al nombre del parámetro al que corresponde dicho valor.

En la definición (parámetros), en cuanto cómo se deben indicar los valores:

- Parámetro solo posicional (*positional-only parameter*): solo puede recibir valor de un *non-keyword argument* en la llamada.
- Parámetro solo con nombre (*keyword-only parameter*): solo puede recibir valor de un *keyword argument* en la llamada.
- Parámetro posicional o con nombre (*positional-or-kewyord parameter*): puede recibir valor de un *kewyord argument* o de un *non-keyword argument* en la llamada, indistintamente.

> Aunque sean técnicamente incorrectos, es frecuente encontrar términos como *positional-only argument*, o *keyword-only argument*. Cuando encontremos estos nombres sabemos que se refieren a *positional-only parameters*, *keyword-only parameters*, etc.

#### 4.8.4 Arbitrary Argument List

(Lista arbitraria de argumentos.)

El parámetro formal ***\*parm*** puede utilizarse para definir funciones que acepten un número arbitrario de argumentos *non-keyword*. Si queremos aceptar un número argumentos arbitrarios con nombre, deberemos usar un parámetro de tipo ***\*\*parm*** (este debe ser el último de los parámetros en la definición).

#### 4.8.5 Unpacking Argument Lists

(Listas de desempaquetado de argumentos.)

Se utiliza en la *llamada* a una función.

Podemos utilizar listas o tuplas que serán interpretadas como una secuencia de argumentos *non-keyword*, utilizando el operador `*`. Por ejemplo, si ***L*** es una lista o tupla de 3 elementos, entonces `foo(*L)` pasa 3 *non-keyword* arguments a la función ***foo***.

Análogamente, se pueden usar diccionarios para desempaquetar sus elementos como argumentos con nombre, usando el operador `**`. Si ***D*** es un diccionario con 5 elementos, `foo(**D)` le pasa 5 *keyword arguments* a ***foo***.

Se pueden combinar ambos métodos, incluso varias veces, e incluso combinarse con otro tipo de argumentos, siempre teniendo en cuenta los requisitos de llamada (dar valor a todos los *non-default parameters*, no repetir valor para un parámetro, etc.).

#### 4.8.6 Lambda Expressions

(Expresiones lambda.)

Es una manera de crear objetos función de forma muy simple sintácticamente. Son pequeñas funciones sin nombre. Debe limitarse a una sola expresión, y es mucho más sencillo de definir que con `def`.

```python
foo = lambda x, y: 3 * (x + y)
```

Equivale a:

```python
def foo(x, y):
    return 3 * (x + y)
```

Las expresiones lambda retornan un objeto función, y tienen acceso a las variables de su ámbito contenedor:

```python
>>> def fabrica_incrementador(n):
...     return lambda x: x + n
...
>>> f = fabrica_incrementador(42)
>>> f(0)
42
>>> f(1)
43
```

#### 4.8.7 Documentation Strings

(*Strings* de documentación.)

Convenio sobre las *docstrings*: deberían ser *triple-quoted* y estar en la primera línea. La primera línea debería ser un breve sumario de la función, sin incluir nombres; si hay más cosas que explicar, se debe dejar la segunda línea en blanco y a continuación una descripción con el convenio de llamada (*calling convention*), valor de retorno, parámetros, efectos secundarios, etc.

La primera línea no está indentada, pues suele empezar tras la triple comilla. La primera línea con contenido (tras la línea en blanco, si la hay, que debería) define la cantidad de espacios iniciales que se eliminarán del resto de líneas (y de sí misma) para las aplicaciones que extraen documentación del código fuente.

#### 4.8.8 Function Annotations

(Anotaciones de función.)

Información (metadatos) completamente opcional sobre cada parámetro y sobre el valor de retorno.

Su efecto es básicamente el de almacenar esos metadatos en el atributo ***\_\_annotations\_\_*** de la función (es un diccionario).

Podemos indicar la información que queramos, el compilador no lo usa para nada, lo pueden usar aplicaciones de terceros. Normalmente sirve para indicar el tipo de los parámetros y/o del valor de retorno, a modo de *type hint*, pero en realidad se puede indicar cualquier tipo de valor.

Consistirá en dos puntos (***:***) después del nombre del parámetro y antes de su valor por defecto (si lo tiene). La del tipo de retorno se define mediante '***->***' tras la lista de parámetros. En todo caso, se debe escribir una sola expresión para cada anotación, que se evaluará al valor que deseemos darle a la misma.

```python
>>> def f(cantidad: int, producto: str = 'spam') -> str:
...     print(f.__annotations__)
...     return str(cantidad) + ' ' + producto
...
>>> f(50)
{'cantidad': <class 'int'>, 'producto': <class 'str'>, 'return': <class 'str'>}
```

En caso de añadir anotaciones, no es necesario anotar cada parámetro y/o el valor de retorno.

### 4.9 Intermezzo: Coding Style

El estilo de *Python* definido en el *PEP 8* se toma como oficial:

- Indentación de 4 espacios sin tabuladores.
- Líneas de máximo 79 caracteres.
- Uso de líneas en blanco para separar funciones y clases, y grandes bloques de código dentro de ellas.
- Cuando sea posible se escribirán los comentarios en su propias líneas.
- Uso de *docstrings*.
- Espacios alrededor de operadores y después de coma, pero no directamente entre los paréntesis: `a = f(1, 2) + g(3, 4)`.
- El convenio para nombres es: ***CamelCase*** para clases y ***snake_case*** para funciones y métodos. Uso de ***self*** como nombre del primer argumento de un método.
- Codificación de texto estándar. *UTF-8* va bien, o incluso *ASCII* puro y duro.
- No se deberían usar caracteres fuera del *ASCII* puro si alguien de otro idioma va a tener que ver el código.

El documento *PEP 8* indica también, entre otras, cómo partir una línea lógica en distintas líneas físicas. Ya sabemos que una forma es mediante la barra `\` a final de línea, pero existe también la **unión implícita** de líneas (*implicit joining*) dentro de **paréntesis, corchetes o llaves** (siempre y cuando no partamos un nombre en dos).

Sintácticamente, cuando hay unión de líneas físicas, la indentación de las líneas de continuación es irrelevante: solo importa la de la primera. Pero *PEP 8* incide en lo que se consideran buenas prácticas.

En una indentación implícita, por ejemplo dentro de la lista de parámetros en la definición de una función, habría dos opciones aceptables:

```python
# Opción 1. Alineación con el elemento inicial:
def funcion(var1, var2, var3,
            var4, var5):
    print(var1)

# Opción 2. Un número de espacios distinto al
# correspondiente con la indentación del cuerpo
# del bloque interior (hanging indent):
def funcion_con_nombre_largo(
        var1, var2, var3,
        var4, var5):
    print(var1)  # indentación del bloque interior
```

Los *hanging indents* (línea terminada en un paréntesis de apertura) pueden tener cualquier número de espacios que se desee, no es obligatorio que sean 4:

```python
foo = funcion_con_nombre_largo(
  var1, var2, var3,
  var4, var5)
```

En caso de que no sea un *hanging indent*, habría que alinear con el elemento inicial:

```python
v = foo(var1, var2,
        var3, var4)  # var3 alineado con var1
```

En cuanto al **paréntesis, llave o corchete de cierre**, puede ir al final de la última línea de elementos, o quedar en su propia línea, alineado con los elementos anteriores:

```python
matrix1 = [1, 2, 3,
           4, 5, 6
           ]

matrix2 = [1, 2, 3,
           4, 5, 6]
```

En el caso de una expresión compuesta por operadores binarios, se recomienda que el corte se produzca antes del operador, es decir, que este sea el que empieza la siguiente línea física (tras la correspondiente indentación).

## 5. DATA STRUCTURES

(Estructuras de datos.)

### 5.1 More on Lists

(Más sobre listas.)

Las listas tienen también los siguientes métodos (sea ***a*** una lista):

- `a.append(x)` - añade un elemento al final de la lista; equivale a `a[len(a):] = [x]`.
- `a.extend(L)` - añade a la lista los elementos del iterable ***L***; equivale a `a[len(a):] = L`.
- `a.insert(i, x)` - inserta el elemento ***x*** antes del elemento con índice ***i***; si ***i*** es igual a `len(a)`, equivale a `append()`. Si es 0, inserta por el principio.
- `a.remove(x)` - elimina el primer elemento cuyo valor es ***x***. Levanta excepción ***RaiseError*** si no hay ninguno.
- `a.pop([i])` - devuelve el elemento con índice ***i*** y lo elimina de la lista; si no especificamos índice ***i*** (opcional), usa el último elemento de la lista.
- `a.clear()` - elimina todos los elementos de la lista. Equivale a `del a[:]`.
- `a.index(x [,start [,end]])` - devuelve el índice del primer elemento con valor ***x***. Los parámetros ***start*** y ***end***, en numeración de *slice*, limitan la búsqueda a ciertas posiciones. El resultado es siempre relativo al principio de la lista.
- `a.count(x)` - devuelve el número de elementos iguales a ***x*** en la lista.
- `a.sort(*, key=None, reverse=False)` - ordena los elementos.
- `a.reverse()` - invierte el orden de los elementos.
- `a.copy()` - devuelve una *shallow copy* de la lista; equivale a `a[:]`.

> Todas las secuencias son iterables, pero no todos los iterables son secuencias.

#### 5.1.1 Using Lists as Stacks

(Usar listas como pilas.)

En una lista, el último elemento insertado es el primero en salir (*last-in-first-out*). Así, podemos gestionar una lista como si fuese una pila insertando con `append()` y extrayendo con `pop()` sin argumentos.

#### 5.1.2 Using Lists as Queues

(Usar listas como colas.)

Mientras que `append()` y `pop()` por el final son eficientes, extraer por el principio no lo es, ya que se tienen que desplazar una posición todos los elementos restantes. Por lo tanto, para implementar una cola (*first-in-first-out*) es mejor usar el tipo `collections.deque`, especial para colas, ya que tiene un método `popleft()`.

#### 5.1.3 List Comprehensions

(Comprensiones de listas.)

Modo de crear listas a partir de secuencias o iterables. Corchetes encerrando una expresión seguida por una cláusula `for ... in`, seguida por cero o más cláusulas `for ... in` y/o `if`. El resultado es una nueva lista resultado de evaluar la expresión según los valores que va tomando. Las cláusulas `for ... in` se encargan de ir nutriendo los valores de la expresión principal.

```python
>>> [(x, y) for x in [1, 2, 3] for y in [3, 1, 4] if x != y]
[(1, 3), (1, 4), (2, 3), (2, 1), (2, 4), (3, 1), (3, 4)]
```

En el ejemplo, obtenemos una lista de tuplas de 2 elementos.

Cuando hay más de un `for ... in`, se aplican en el orden que aparecen escritos, es decir, se anidan siendo el primero de todos el más exterior. El ejemplo anterior, pues, equivaldría a:

```python
resultado = []
for x in [1, 2, 3]:
    for y in [3, 1, 4]:
        if x != y:
            resultado.append((x, y))
```

La expresión inicial, que marca la forma general de cada elemento, puede ser a su vez una lista:

```python
>>> [[x, x ** 2, x ** 3] for x in range(10) if x==(x // 2) * 2]
[[0, 0, 0], [2, 4, 8], [4, 16, 64], [6, 36, 216], [8, 64, 512]]
```

Ejemplo: Aplanar una lista:

```python
>>> vec = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
>>> [num for elem in vec for num in elem]
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Se entendería así: para cada elemento en ***vec***, hacer: para cada número en el elemento, hacer: devolver ***num*** (expresión inicial).

#### 5.1.4 Nested List Comprehensions

(Comprensiones de listas, anidadas.)

La expresión de la *list comprehension* puede ser, a su vez, una *list comprehension*. Un modo de trasponer filas/columnas de una matriz:

```python
>>> matrix = [
...     [1, 2, 3, 4],
...     [5, 6, 7, 8],
...     [9, 10, 11, 12]]
>>> [ [row[i] for row in matrix] for i in range(4)]
[[1, 5, 9], [2, 6, 10], [3, 7, 11], [4, 8, 12]]
```

Podemos entender este ejemplo teniendo en cuenta que:

```python
>>> [row[0] for row in matrix]
[1, 5, 9]
>>> [row[1] for row in matrix]
[2, 6, 10]
>>> [row[2] for row in matrix]
[3, 7, 11]
>>> [row[3] for row in matrix]
[4, 8, 12]
```

### 5.2 The del statement

(La sentencia `del`.)

La sentencia `del` elimina un elemento de una lista (sin retornar su valor). Puede incluso eliminar un *slice* o todos los elementos de la misma:

```python
>>> a = [1, 2, 3, 4, 5, 6]
>>> del a[0]
>>> a
[2, 3, 4, 5, 6]
>>> del a[2:4]
>>> a
[2, 3, 6]
>>> del a[:]
>>> a
[]
```

Puede también eliminar le variable por completo de la tabla de nombres (`del a`).

### 5.3 Tuples and Sequences

(Tuplas y secuencias.)

A parte de los *strings* y listas, hay otros tipos de *secuencias*. Uno de ellos es la *tupla*. Este, consiste en una serie de valores separados por comas. Se pueden definir entre paréntesis, o sin ellos. Si los indicamos entre corchetes ya no forman una tupla, sino una lista.

```python
>>> t = 12345, 67.5, 'hola'
>>> t[0]
12345
>>> t
(12345, 54321, 'hola')
>>> # Pueden anidarse, como las listas:
... u = t, (1, 2, 3, 4, 5)
>>> u
((12345, 54321, 'hello!'), (1, 2, 3, 4, 5))
>>> # Son inmutables:
... t[0] = 88888
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>> # Pero pueden contener objetos mutables:
... v = ([1, 2, 3], [3, 2, 1])
>>> v
([1, 2, 3], [3, 2, 1])
```

En algunos lugares es obligatorio indicar los paréntesis, como por ejemplo en la expresión principal de una *list comprehension*, si sus elementos van a ser tuplas, o cuando forman parte de expresiones más grandes. Si deseamos definir una tupla vacía (0 elementos), deberemos escribirlos obligatoriamente. Para una tupla de 1 elemento hay que incluir una coma final, ya que aunque incluyamos los paréntesis, lo tomará como una expresión de un solo elemento.

```python
>>> empty = ()
>>> singleton = 'hello',  # obsérvese la coma final
>>> len(empty)
0
>>> len(singleton)
1
>>> singleton
('hello',)
```

Como vimos anteriormente, la sentencia `t = 12345, 67.5, 'hola'` es un caso de empaquetado de tupla (*tuple packing*), mientras que el caso contrario `x, y, z = t` es un desempaquetado de secuencia (*sequencie unpacking*). En la primera sentencia, la lista de valores separados por comas, a la derecha del operador de asignación, se empaquetan en una tupla (*tuple packing*). Cuando lo que tenemos es una lista de variables a la izquierda del operador de asignación, necesitamos tener a la derecha de dicho operador una secuencia (tupla, lista, etc.).

Se pueden hacer ambas cosas a la vez:

```python
a, b, c = 1, 2, 3
```

Esta sentencia **empaqueta** los valores 1, 2 y 3 en una tupla. Dicha tupla servirá para dar valor y **desempaquetarse** posteriormente en las variables ***a***, ***b*** y ***c***,

### 5.4 Sets

(Conjuntos.)

Los *sets* no tienen elementos duplicados. Se crean con `set()` (a la que se pasa como argumento un objeto iterable) o mediante llaves ***{}***. Se pueden hacer operaciones como unión, intersección, diferencia o diferencia simétrica.

Para crear un conjunto vacío se hace con `set()` sin argumentos, ya que `{}` crearía un diccionario vacío (veremos los diccionarios más adelante).

```python
>>> basket = {'manzana', 'naranja', 'manzana', 'pera',
...           'naranja', 'plátano'}
>>> print(basket)  # se han eliminado los duplicados
{'naranja', 'plátano', 'pera', 'manzana'}
>>> 'naranja' in basket  # test rápido de pertenencia
True
>>> 'artemisa' in basket
False
```

Admiten operaciones matemáticas de conjuntos:

```python
>>> a = set('abracadabra')  # equivale a set(['a', 'b',
                            # 'r', 'a', 'c', 'a', 'd',
                            # 'a', 'b', 'r', 'a'])
>>> b = set('alacazam')
>>> a
{'a', 'r', 'b', 'c', 'd'}
>>> a - b   # letras en a pero no en b
{'r', 'd', 'b'}
>>> a | b   # letras en a o b
{'a', 'c', 'r', 'd', 'b', 'm', 'z', 'l'}
>>> a & b   # letras en ambos conjuntos a la vez
{'a', 'c'}
>>> a ^ b   # letras en a o b pero no en ambos
{'r', 'd', 'b', 'm', 'z', 'l'}
```

También se pueden hacer *set comprehensions* (elimina los repetidos):

```python
>>> a = {x for x in 'abracadabra' if x not in 'abc'}
>>> a
{'r', 'd'}
```

### 5.5 Dictionaries

(Diccionarios.)

Es un conjunto de pares **clave:valor** (en otros lenguajes se pueden denominar *arrays* asociativos, por ejemplo). El acceso al diccionario es por la clave (*key*), y no por número de índice. Las claves deben ser de tipo inmutable y deben ser únicas (no podemos repetir una clave). Por lo tanto, podríamos usar como clave cosas como un número, un *string*, o incluso una tupla (siempre y cuando no tuviera elementos mutables).

El diccionario vacío se puede definir mediante `{}`.

Añadir un valor a través de una clave inexistente, añade el par clave:valor al diccionario. Si la clave ya existe, simplemente actualizará el valor correspondiente a esa clave. Mediante `del` sobre la clave podemos eliminar el par clave:valor correspondiente a esa clave.

Si tenemos un diccionario ***d***, con `list(d)` obtenemos una lista con las claves (con el orden en que fueron insertadas en su momento). Si lo queremos ordenado, lo haremos mediante `sorted(d)`.

Para saber si una clave existe en el diccionario se puede comprobar mediante `'clave' in d`.

```python
>>> tel = {'jack': 4098, 'sape': 4139}
>>> tel['guido'] = 4127
>>> tel
{'jack': 4098, 'sape': 4139, 'guido': 4127}
>>> tel['jack']
4098
>>> del tel['sape']
>>> tel['irv'] = 4127
>>> tel
{'jack': 4098, 'guido': 4127, 'irv': 4127}
>>> list(tel)
['jack', 'guido', 'irv']
>>> sorted(tel)
['guido', 'irv', 'jack']
>>> 'guido' in tel
True
>>> 'jack' not in tel
False
```

Se puede usar el constructor `dict()` para crear un diccionario, pasándole una secuencia que contenga a su vez secuencias de dos elementos (clave y valor):

```python
>>> dict([('sape', 4139), ('guido', 4127), ('jack', 4098)])
{'sape': 4139, 'jack': 4098, 'guido': 4127}
```

Aunque el constructor también acepta parámetros con nombre, cuyos nombres convierte en *strings* para actuar como claves:

```python
>>> dict(sape=4139, guido=4127, jack=4098)
{'sape': 4139, 'guido': 4127, 'jack': 4098}
```

Se pueden crear *dict comprehensions*:

```python
>>> {x: x**2 for x in (2, 4, 6)}
{2: 4, 4: 16, 6: 36}
```

### 5.6 Looping Techniques

(Técnicas iteración en bucle.)

El método `items()` de un *diccionario* retorna una lista de parejas *key*:*value*:

```python
>>> knights = {'gallahad': 'the pure', 'robin': 'the brave'}
>>> for k, v in knights.items():
...     print(k, v)
...
gallahad the pure
robin the brave
```

En el ejemplo, se hace *sequence unpacking* en ***k*** y ***v*** con los sucesivos valores retornados por `items()`: listas de dos elementos **[clave, valor]**.

La función `enumerate()`, a la que se pasa como argumento una secuencia, retorna un iterable cuyos elementos son listas con dos elementos: el número de índice del elemento de la secuencia pasada y su valor.

Se pueden combinar 2 secuencias con la función `zip()`, de tal modo que el elemento N del objeto retornado será una pareja formada por el elemento N de la primera secuencia y el elemento N de la segunda secuencia.

`reversed()` retorna una secuencia con los elementos invertidos de orden, mientras que `sorted()` la retorna ordenada. Ninguna de ellas altera el objeto original.

> Recordemos que es una mala idea modificar una lista mientras se está iterando sobre ella.

### 5.7 More on Conditions

(Más sobre condiciones.)

Las expresiones condicionales de sentencias como `while` pueden ser de cualquier tipo, no solo comparaciones. Los operadores `in` y `not in`, comprueban la existencia de un elemento dentro de un contenedor (una secuencia, por ejemplo). Los operadores `is` e `is not`, miran si los objetos comparados son en realidad el mismo (según ID). Los operadores de comparación tienen todos la misma prioridad, y menor prioridad que todos los operadores numéricos.

Si encadenamos comparaciones que siguen un orden lógico, como por ejemplo `a < b and b == c and c < d`, podemos escribirlas así: `a < b == c < d`.

Se pueden encadenar expresiones con `and`, `or` y `not`; `not` tiene la máxima prioridad, y `or` la mínima de los tres. Estos tienen menor prioridad que los operadores de comparación. Operadores `and` y `or` se evalúan de izquierda a derecha, y **cortocircuitan**: se evalúan las subexpresiones de izquierda a derecha, y termina la evaluación en el momento en que se puede determinar el resultado final. El resultado de una expresión lógica se puede asignar a una variable. No siempre retornarán ***True*** o ***False***: cuando se usan operandos no booleanos (***True***/***False***), el resultado es el último operando evaluado antes de terminar o cortocircuitar (`not` retorna siempre un valor booleano).

Valores como 0 o *string* vacío se evalúan a ***False***, mientras un *string* no vacío o un número no cero evalúan a ***True***

```python
>>> 5 and not 4
False
>>> 6 and 3
3
>>> 6 or 3
6
>>> 0 or not 5
False
>>> 0 or 5
5
>>> 0 and True
0
>>> '' or 'Uno' or 'Dos' or 'Tres'
'Uno'
```

No se puede utilizar el operador de asignación (como en *C*) en medio de una expresión de comparación. Pero sí se puede hacer como en *C* utilizando en su lugar el operador morsa (*walrus*) `:=`. La subexpresión retorna el valor asignado.

```python
>>> a = 3 + ( b = 5)
  File "<stdin>", line 1
    a=3+(b=5)
          ^
SyntaxError: invalid syntax

>>> a = 3 + (b := 5)
>>> a
8
```

### 5.8 Comparing Sequences and Other Types

(Comparación de secuencias y otros tipos.)

Se pueden comparar entre sí secuencias *del mismo tipo*. Si son de distinto tipo pero existen mecanismos de comparación, también. Se comparan los elementos de ambas secuencias de uno en uno, desde el primero hasta el último (recursivamente si hay subsecuencias), hasta encontrar una diferencia (o no). Si una de las dos es una subsecuencia de la otra por el principio, la que tiene menos elementos será considerada menor.

Ejemplos que se evalúan a verdadero:

```python
(1, 2, 3) < (1, 2, 4)
[1, 2, 3] < [1, 2, 4]
'ABC' < 'C' < 'Pascal' < 'Python'
(1, 2, 3, 4) < (1, 2, 4)
(1, 2) < (1, 2, -1)
(1, 2, 3) == (1.0, 2.0, 3.0)
(1, 2, ('aa', 'ab')) < (1, 2, ('abc', 'a'), 4)
```

## 6. MODULES

(Módulos.)

Un módulo se puede importar en otro módulo o en el módulo principal (módulo de nivel superior o intérprete interactivo), mediante `import <nombre_módulo>`. El módulo importado puede ser un *script Python*.

Al importar un módulo no inserta los nombres de las funciones allí definidas en la tabla de nombres actual, sino solo el nombre del módulo. Se puede ver como un objeto módulo en la tabla de nombres actual. A través de ese objeto podemos acceder a las funciones que contiene, de este modo: `modulo.foo()`. Aunque podemos insertar una función en la tabla actual tecleando algo como `fun = modulo.foo`.

Uno de los atributos del objeto módulo es la variable ***\_\_name\_\_***, que simplemente guarda el nombre del mismo. ***\_\_name\_\_*** a secas es el nombre del módulo actual, y ***modulo.\_\_name\_\_*** es el nombre del módulo importado ***modulo***.

### 6.1 More on Modules

(Más sobre módulos.)

Al importar el módulo con la sentencia `import`, se ejecutan todas las sentencias que contiene. Luego, aunque se repita la sentencia de importación no se vuelven a ejecutar: una sentencia `def` define una función, con lo que una sola vez es suficiente; lo mismo para clases etc.

El módulo se puede ejecutar también como *script* si así se le invoca. Las variables definidas en un módulo se guardan en la tabla particular del mismo, que, vista desde el interior de este es una tabla global (no hay tablas superiores visibles); los objetos del módulo, vistos desde el código que lo importa, son accesibles mediante sintaxis del tipo ***modulo.objeto***.

Un módulo (o *script*) puede importar a su vez otros módulos. Cada uno tiene su tabla global, donde coloca los nombres de los módulos que importa a su vez. A parte, cada función del módulo tendrá su propia tabla local.

```python
>>> from fibo import fib, fib2
```

Esto añade los nombres (objetos) especificados directamente (en este caso ***fib1*** y ***fib2***) en la tabla del módulo importador, sin añadir el nombre del módulo importado en ella.

```python
>>> from fibo import *
```

Esto último importa todas las definiciones del módulo ***fibo*** (menos las empezadas por '***\_***'). No se debería usar este tipo de importación, porque llena la tabla local nombres desconocidos, que podrían generar colisiones y ocultar otros nombres existentes en la tabla.

Para modificar los nombres en el momento de añadirlos a nuestra tabla, podemos usar:

```python
import <módulo> as <minombre>
```

En este caso, el nombre añadido a la tabla será ***minombre***, y contendrá el módulo original. Para modificar el nombre de los **elementos** del módulo al importarlos:

```python
from <módulo> import <foo> as <mifun1>, <fee> as <mifun2>
```

Y así tantas como queramos.

Para recargar un módulo (ya que si no solo lo importa/ejecuta una vez):

```python
import importlib
importlib.reload(nombremodulo)
```

#### 6.1.1 Executing modules as scripts

(Ejecutar módulos como *scripts*.)

Desde línea de comandos:

```
python3 m1.py <argumentos>
```

Cuando un módulo se está ejecutando en el nivel superior, el valor de ***\_\_name\_\_*** es ***'\_\_main\_\_'***. Este es el modo de saber si nuestro módulo está funcionando como un *script* invocado directamente (nivel superior), o está siendo importado para que otro módulo utilice sus funciones.

#### 6.1.2 The Module Search Path

(La ruta de búsqueda de módulos.)

Al importar un módulo (o elementos de un módulo), *Python* busca primero el nombre del mismo en los módulos incorporados (*built-in modules*); si no lo encuentra, buscará un archivo *Python* con ese nombre (y extensión ***.py***) en los lugares definidos en la variable ***sys.path***. Esta variable, que es modificable, indica por defecto los valores siguientes:

- Directorio que contiene el *script* importador, o directorio desde el que se ha llamado al intérprete interactivo.
- Variable del sistema ***PYTHONPATH*** (mismo formato que ***PATH***).
- Otros (dependiendo de la instalación de *Python* concreta).

#### 6.1.3 "Compiled" Python files

(Archivos *Python* "compilados".)

Al importar un módulo ***m1***, *Python* lo "compila" (lo codifica usando un código *platform-independent*) y guarda ese archivo compilado en el subdirectorio ***\_\_pycache\_\_*** (dentro del directorio donde se encuentra el módulo), con nombre ***m1.ver.pyc***, donde 'ver' es la versión de *Python* con que se "compiló". Así, pueden coexistir varias implementaciones/versiones de *Python* en el sistema, así como varias versiones "compiladas" de un *script*.

Cada vez que importamos un módulo, si el archivo ***.py*** es más nuevo que el ***.pyc*** (o el ***.pyc*** no existe), se "recompila" (o "compila") el *script*; de lo contrario, se lee y ejecuta el archivo compilado, que es más rápido de cargar que el archivo de texto. La velocidad de ejecución después de cargarse no se ve afectada.

Al ejecutar un módulo como *script*, no se produce tal "compilación" en el cache. Se puede importar un módulo que exista solo en forma compilada, aunque solo si no existe el archivo fuente, y el archivo compilado está en el directorio fuente. En caso de que no exista el archivo fuente en el directorio fuente, *Python* no consulta el directorio *cache*.

### 6.2 Standard Modules

(Módulos estándar.)

*Python* viene con una gran biblioteca formada por módulos estándar. Para utilizarlos, se deberán importar adecuadamente.

### 6.3 The dir() Function

(La función `dir()`.)

Esta función *built-in* muestra los nombres definidos (funciones, variables, módulos,...) en un módulo concreto. Sin parámetros, muestra los nombres de la tabla del módulo actual. Con `dir(<módulo>)` vemos los nombres definidos en el módulo específico (cuyo nombre debe estar en la tabla actual).

No muestra los nombres *built-in*. Para ello tendríamos que usar el módulo estándar ***builtins***:

```python
import builtins
dir(builtins)
```

### 6.4 Packages

(Paquetes.)

Podemos estructurar el nombre de espacio (*namespace*) de los módulos, agrupándolos jerárquicamente en paquetes (*packages*). Para acceder a ellos prefijaremos los nombres de los módulos pertinentes separados por punto. Cada submódulo tendrá su propia tabla de nombres, independientemente de los demás módulos del *package*. Así, ***m1.m2*** indica el módulo ***m2*** del *package* ***m1***.

Así, un *package* se organiza en un directorio donde se incluirán todos sus módulos (archivos ***.py***) y/o *subpackages* (subdirectorios con más módulos). El nombre del directorio es el que define el nombre del *package* (o *subpackage*).

La búsqueda del *package* se lleva a cabo igualmente como se indica en ***sys.path***. Para que al importar *Python* trate a esa carpeta como un paquete, esta debe contener un archivo ***\_\_init\_\_.py*** (o una versión compilada del mismo). Si no existe tal archivo, el directorio es una simple carpeta de archivos, nada que ver con un paquete. Es suficiente la presencia de ***\_\_init.py\_\_*** para que la carpeta sea considerada *package*, aunque el archivo esté vacío.

Si incluimos código en ***\_\_init\_\_.py***, al importar el paquete se ejecuta como código de inicialización del paquete.

Veamos una posible estructura de directorios definiendo un paquete de utilidades de sonido:

```
sound/                 (paquete de nivel superior)
    __init__.py        (inicializa el paquete de sonido)
    install.py
    unistall.py
    formats/           (subpaquete de conversión de formato)
        __init__.py    (inicializa el subpaquete)
        wavread.py
        wavwrite.py
        aiffread.py
        aiffwrite.py
    effects/           (subpaquete de efectos)
        __init__.py    (inicializa el subpaquete)
        echo.py
        surround.py
        reverse.py
    filters/           (subpaquete de filtros)
        __init__.py    (inicializa el subpaquete)
        equalizer.py
        vocoder.py
        karaoke.py
```

En este ejemplo, el paquete se llama ***sound*** (se importaría con `import sound`), y contiene dos módulos (***install.py***, ***uninstall.py***) y tres subpaquetes (***formats***, ***effects*** y ***filters***), los cuales contienen varios módulos cada uno.

Ejemplos:

```python
import sound.effects.echo    # importa el módulo 'echo.py'
```

Para llamar a una posible función ***foo()*** deberemos usar el nombre completo:

```python
sound.effects.echo.foo()
```

Podemos importar lo mismo así:

```python
from sound.effects import echo
```

Pero en este caso, aunque también importa el módulo ***echo.py***, se puede llamar a la función así:

```python
echo.foo()
```

También podemos hacer:

```python
from soud.effects.echo import foo
```

Y entonces podemos llamar así:

```python
foo()
```

Al hacer: `from <paquete> import <elemento>`, ***elemento*** puede ser un paquete, un módulo, o cualquier nombre definido en el *package* (como una función, una variable o una clase). *Python* intenta primero averiguar si es un nombre definido en el código del *package* (en ***\_\_init\_\_.py***); si no lo encuentra, asumirá que es un subpaquete (o módulo del mismo); si tampoco lo es, se producirá error. En cambio, al hacer `import item.subitem.subsubitem`, todos los nombres deben ser paquetes, excepto el último que puede ser un paquete o un módulo.

#### Aclaraciones adicionales

En los archivos ***\_\_init\_\_.py*** y en los módulos que forman parte de un paquete se puede incluir cualquier código que estimemos oportuno. Allí se puede incluir código de inicialización del paquete o módulo, importar otros elementos, definir funciones, clases, variables, etc.

A la hora de importar elementos de un *package*, debemos hacerlo siguiendo la jerarquía de nombres, desde el paquete raíz, separándolos por puntos. El directorio donde está el paquete raíz debe estar referenciado en ***sys.path***. En nuestro caso, por ejemplo:

- ***sound*** (paquete raíz)
- ***sound.foo*** (función en paquete raíz)
- ***sound.uninstall*** (módulo)
- ***sound.uninstall.foo*** (función en módulo)
- ***sound.effects*** (paquete)
- ***sound.effects.foo*** (función en paquete)
- ***sound.effects.echo*** (módulo)
- ***sound.effects.echo.foo*** (función en módulo)

Podemos importar un módulo o paquete sin utilizar la sintaxis de jerarquía de nombres, o tomando cualquier otra carpeta como raíz del paquete, por ejemplo `import echo`, o `import effects`, siempre y cuando el directorio del módulo o paquete en cuestión esté referenciado en las rutas de búsqueda estándar (***sys.path***).

##### Importar con *import*

Importaremos con `import <nombre>`, donde ***nombre*** es una secuencia jerárquica de nombres separados por punto. Dichos nombres solo pueden ser nombres de paquetes (en orden jerárquico), a excepción del último que puede ser el nombre de un paquete o de un módulo.

Al importar, se van ejecutando todos los ***\_\_init\_\_.py*** en el orden en que se van encontrando, empezando por el del paquete raíz. Si el último nombre es de un módulo, lo ejecuta, después de los ***\_\_init\_\_.py*** superiores en la jerarquía.

En el momento de ejecutar la sentencia `import`, el nombre que se añade a la tabla de nombres es únicamente el del *paquete raíz*. Luego, en el código podremos acceder, a través de ese nombre, a los demás paquetes de la ruta (y/o al módulo final, si lo hay), así como a todos los elementos (funciones, clases, variables, etc.) definidos en esta jerarquía.

Por lo tanto, cualquier referencia a un elemento importado mediante una sentencia `import`, deberá incluir la ruta de nombres completa desde el paquete raíz.

##### Importar con *from-import*

Otro modo de importar es con `from <nombre> import <elemento>`. Al igual que en el caso anterior, ***nombre*** es la ruta de nombres separados por punto. Todos ellos deben ser nombres de paquetes excepto el último, que puede ser nombre de paquete o de módulo. Si el último es el nombre de un paquete, ***elemento*** puede ser el nombre de un subpaquete, módulo, u objeto (función, clase, variable, etc.). Si el último es un nombre de módulo, ***elemento*** será forzosamente un nombre de objeto definido en el módulo en cuestión.

Igual que en el caso anterior, también se van ejecutando los ***\_\_init\_\_.py*** (y el posible módulo final) en orden, desde el raíz, hasta el último de los nombres de ***nombre***. La única diferencia es que en lugar de añadirse a la tabla de nombres el nombre del paquete raíz, se añade directamente el elemento indicado en ***elemento***, ya sea este un subpaquete (cuyo ***\_\_init\_\_.py*** se ejecuta en último lugar), un módulo (que se ejecuta en último lugar), o un objeto (definido en un paquete o módulo cuyo código se ejecuta en último lugar).

Si ***elemento***, que es el nombre que será añadido a la tabla de nombres, es un paquete o módulo, a través de dicho nombre podremos acceder a *todos* los elementos definidos en él. En cambio, si es un objeto, solo podremos acceder a este (y sin prefijo alguno).

Haciendo la importación con `from ... import`, no podremos acceder a otros elementos de la jerarquía. Si el elemento importado fuese un subpaquete, no habría ningún tipo de acceso ni al paquete padre, ni a posibles subpaquetes, ni incluso a módulos que estuvieran en el subpaquete. Los únicos elementos a los que se podría acceder serían los objetos (funciones, clases, etc.) definidos en el subpaquete, prefijándoles el nombre del mismo. Lo mismo sucedería si se tratara de un módulo.

Si quisiéramos que al importarse un paquete estuviesen accesibles otros elementos, como submódulos del paquete, o incluso otros subpaquetes, se deberían incluir esas importaciones en el código del archivo ***\_\_init\_\_.py*** del paquete.

En cuanto al elemento a importar, conviene tener en cuenta que este nunca puede ser una jerarquía de nombres, sino un nombre simple.

Por otro lado, en cuanto a la ejecución de los ***\_\_init\_\_.py*** y módulos, se debe tener en cuenta que los que se hayan ejecutado ya no se vuelven a ejecutar.

#### 6.4.1 Importing * From a Package

(Importar `*` desde un paquete.)

Si hacemos `from <paquete> import *`, no se importan submódulos ni *subpackages*, sino únicamente los elementos que estén definidos en el código del paquete (en su ***\_\_init\_\_.py***), aunque también se pueden importar otras cosas explícitamente desde allí.

Sin embargo, podemos definir fácilmente el contenido del *package* mediante la lista de *strings* ***\_\_all\_\_***. Si está definida en ***\_\_init\_\_.py***, se importará lo que la lista especifique, en el orden especificado, en el caso específico de que hagamos un `from <pack> import *`:

```python
__all__ = ['install', 'uninstall', 'formats', 'foo']
```

Podemos incluir nombres de submódulos, subpaquetes y elementos definidos en el mismo paquete (en ***\_\_init\_\_.py***). Cuando ***\_\_all\_\_*** no está incluido, pues, solo se importarán los elementos definidos en ***\_\_init\_\_.py***; sin embargo, si ***\_\_all\_\_*** está definido, dichos elementos no se cargan si no están explícitamente incluidos, uno a uno, en ***\_\_all\_\_***.

Solo se pueden incluir en ***\_\_all\_\_*** elementos que pertenezcan directamente al paquete (sus submódulos, subpaquetes y objetos), no elementos de niveles inferiores (ni superiores, claro), por lo que todos los *strings* especificados son nombres simples, sin prefijos.

#### 6.4.2 Intra-package References

(Referencias intra-paquete.)

Podemos hacer referencia a otros paquetes o módulos de la jerarquía del paquete actual en la sentencia `from ... import`. Por ejemplo, desde el paquete ***effects***, podríamos escribir:

```python
from . import echo    # '.' se refiere al mismo 'effects'
from .. import formats    # '..' es el paquete padre
from ..filters import equalizer    # subimos y bajamos
```

Un punto (***.***) es el paquete actual, dos (***..***) el superior, tres (***...***) el superior a este, etc. Tras los puntos, podemos bajar opcionalmente uno o más niveles, hasta ir a parar a un paquete concreto (o módulo). Por ejemplo, desde cualquier archivo de ***effects*** (ya sea desde uno de los módulos o desde dentro de ***\_\_init\_\_.py***):

```python
from ..filters.equalizer import foo    # sube, luego baja 2
```

Este mecanismo no funciona desde los módulos que se estén ejecutando en el nivel superior, y su nombre sea ***\_\_main\_\_***.

#### 6.4.3 Packages in Multiple Directories

(Paquetes en múltiples directorios.)

Los paquetes tienen una variable tipo lista con nombre ***\_\_path\_\_*** que especifica el directorio donde se encuentra su ***\_\_init\_\_.py*** antes de ejecutarse. Al cambiarla, se cambia el lugar donde se buscarán los submódulos y subpaquetes del paquete actual.

## 7. INPUT AND OUTPUT

(Entrada y salida.)

### 7.1 Fancier Output Formatting

(Formateo más elegante de la salida.)

Para formatear *strings* se puede hacer mediante *strings* con formato (llamadas también *f-strings*):

```python
>>> año = 2016
>>> evento = 'Referéndum'
>>> f'Resultados del {evento} de {año}.'
'Resultados del Reférendum de 2016.'
```

Pueden ser del tipo ***f'...'*** o ***F'...'***, y se puede prefijar a comillas simples, dobles o *triple-quoted strings*. Dentro de las llaves puede ir cualquier expresión válida.

Otra opción es usando el método `format()` de los objetos *string*.

Y una tercera forma es haciéndolo todo manualmente, con *slices*, concatenaciones, etc.

Las funciones *built-in* `str()` y `repr()` retornan representaciones en *string* del valor que les pasamos, de forma legible para los humanos la primera, y con forma de literal *string* comprensible al intérprete la segunda.

```python
>>> s = 'Hello, world.'
>>> str(s)
'Hello, world.'
>>> repr(s)
"'Hello, world.'"
>>> str(1/7)
'0.14285714285714285'
>>> x = 10 * 3.25
>>> y = 200 * 200
>>> s = 'El valor de x es ' + repr(x) + ', e y es ' + repr(y) + '.'
>>> print(s)
El valor de x es 32.5, e y es 40000.
>>> hello='hello, world\n'
>>> print(hello)
hello, world
>>> print(repr(hello))
'hello, world\n'
```

Muchos tipos de datos tienen la misma representación con `str()` que con `repr()`.

#### 7.1.1 Formatted String Literals

(Literales *string* formateados.)

En un *f-string*, dentro de las llaves, y tras la expresión, se puede tener más control del formato mediante dos puntos (***:***) seguido del especificador de formato. Para más detalles, véase el apartado de especificación de formato.

#### 7.1.2 The String format() Method

(El método `format()` de ***String***.)

Para una descripción detallada del método `format()` de la clase `str`, véase la sección de especificación de formato.

#### 7.1.3 Manual String Formatting

(Formato manual de *strings*.)

`print()` muestra un espacio entre los argumentos imprimidos, y un *newline* (salto de línea) al final; si especificamos el parámetro ***end***, sustituye ese *newline* por el *string* que especifiquemos.

Los *strings* disponen de los métodos `rjust()`, `ljust()` y `center()` que justifican a la derecha, izquierda o centro, a los que debemos pasar el ancho deseado (nunca truncan el *string* original). Los *strings* disponen también del método `zfill()` (*zero fill*) que rellena un número con ceros (respetando signos correctamente).

#### 7.1.4 Old string formatting

(Formateo antiguo de *strings*.)

Se pueden formatear los *strings* usando el operador `%`. Por ejemplo:

```python
>>> import math
>>> print('El valor de pi es %5.3f.' % math.pi)
El valor de pi es 3.142.
```

El segundo operando puede ser una tupla, en cuyo caso sus valores irán alimentando a los distintos elementos a formatear.

Equivalente mediante una *f-string*:

```python
>>> import math
>>> print(f'El valor de pi es {math.pi:5.3f}.')
El valor de pi es 3.142.
```

### 7.2 Reading and Writing Files

(Lectura y escritura de archivos.)

La función `open(nombre [,modo])` retorna un objeto archivo. El modo puede ser ***r*** (*readonly*, solo lectura, valor por defecto), ***'w'*** (*write*, escritura, trunca el archivo al abrirlo), ***a*** (*append*, añadir al final, no trunca el archivo) o ***r+*** (*read*/*write*, lectura y escritura, tampoco trunca el archivo). Si al modo añadimos ***b***, abre en modo binario (no texto). En modo texto, los finales de línea específicos de la plataforma de ejecución **leídos** (***\\n*** de *Unix*, ***\\r\\n*** de *Windows*) se convierten internamente a ***\\n***; esos ***\\n*** se convierten, al *escribir*, en finales de línea específicos de la plataforma.

Es buena idea usar `with` para manipular archivos, ya que lo cerrará automáticamente una vez haya terminado el trabajo, incluso si se produce una excepción:

```python
>>> with open('workfile') as f:
...     read_data = f.read()
>>> f.closed
True
```

Si lo usamos simplemente con `f = open(...)`, tendremos que cerrar manualmente cuando terminemos, mediante `f.close()`. El *garbage collector* (mecanismo de liberación de recursos no usados) lo cerraría y liberaría el objeto archivo igualmente, pero no se puede saber en qué momento sucederá eso.

`open()` admite un tercer parámetro, ***encoding***, para indicar la codificación del archivo de texto. El valor por defecto depende de la plataforma de ejecución. Se recomienda utilizar la codificación ***utf-8***.

#### 7.2.1 Methods of File Objects

(Métodos de objetos archivo.)

El método `read()`, lee hasta el final del archivo (*string* para texto o *bytes* para binario). Con `read(n)` se leen como máximo ***n*** *bytes*. Si ***n*** es negativo, no hay máximo. La lectura se inicia donde se quedó la anterior. Si se ha llegado al fin de archivo, la siguiente lectura retorna un *string* vacío.

El método `readline()` retorna la siguiente línea de texto, hasta el salto de línea ***\\n*** (se conserva al final del *string*) o el fin de archivo (*end of file*). Si ya se llegó al *EOF* en la anterior lectura, retorna un *string* vacío. Una línea en blanco es simplemente un *string* con el carácter ***\\n***.

Para leer línea a línea un archivo, se puede iterar sobre el objeto archivo:

```python
>>> f = open('archivo.txt')
>>> for linea in f:
...     print(linea)
```

El método `readlines()` retorna una lista de *strings* con todas las líneas del archivo de texto (también podemos usar `list(f)`).

El método `write(s)` solo acepta *strings* (o *bytes*) como argumento. Escribe ese *string* (o *bytes*) y retorna el número de elementos escritos (caracteres o *bytes*).

El método `tell()` retorna un entero con la posición actual dentro del archivo en *bytes* desde el principio (modo binario) o un número opaco, del que no conocemos sus características (modo texto). Para cambiar esta posición, se usa el método `seek(offset, whence=0)`, donde ***offset*** es un valor numérico que indica desplazamiento, y ***whence*** (opcional) indica la posición de partida para calcular ese desplazamiento: su valor puede ser 0 (*beginning of file*, principio del archivo, valor por defecto), 1 (*current position*, posición actual) o 2 (*end of file*, fin del archivo). Retorna la posición final.

En los archivos de texto (no binarios) el *seek* sólo se puede hacer relativo al *beginning of file*, a excepción de `seek(0,2)`, que lo deja en el *EOF*. Para los valores de ***offset*** se debe usar un valor retornado por `ftell()` con anterioridad, o 0.

#### 7.2.2 Saving structured data with json

(Guardar datos estructurados con *JSON*.)

Como solo se pueden guardar datos en archivos de texto usando *strings*, un simple valor numérico implica la conversión a y desde *string*. Para guardar estructuras de datos más complejas y jerarquizadas hay que parsear a mano, lo cual se vuelve farragoso. Para ello está el módulo ***json***, que serializa (convierte una estructura de datos en una secuencia *string*) y deserializa (convierte un *string* a una estructura de datos) en el popular formato de intercambio *JSON* (*JavaScript Object Notation*).

Dado un objeto cualquiera ***x***, se puede serializar, convirtiendo a un *string* ***s***:

```python
>>> import json
>>> s = json.dumps(x)
```

O directamente guardar el contenido en un archivo de texto (representado por un objeto archivo ***f*** ya abierto):

```python
>>> import json
>>> json.dump(x, f)
```

Para la operación contraria, es decir para obtener un objeto a partir de un *string* serializado:

```python
>>> import json
>>> x = json.loads(s)
```

O para leer directamente de un archivo ***f*** (de texto o binario):

```python
>>> import json
>>> x = json.load(f)
```

Los archivos **de texto** utilizados para serializar o deserializar datos *JSON* deben estar codificados como *UTF-8*.

También se puede usar el módulo ***pickle***, pero este usa en un formato propio de *Python*, que permite serializar/deserialezar objetos específicos de *Python*, pero no se puede utilizar para compartir datos con aplicaciones realizadas en otros lenguajes.

## 8. ERRORS AND EXCEPTIONS

(Errores y excepciones.)

### 8.1 Syntax Errors

(Errores sintácticos.)

Los errores sintácticos son reportados por el *parser*. No se trata de mal funcionamiento del programa, sino de código mal escrito.

### 8.2 Exceptions

(Excepciones.)

Las excepciones se producen cuando existe un error en la ejecución, no en el *parsing*. Existen varios tipos de excepciones incorporadas (*built-in*) en *Python*, aunque se pueden definir nuevos tipos de ellas.

### 8.3 Handling Exceptions

(Tratamiento de las excepciones.)

Se utiliza la cláusula `try` para tratar las excepciones:

```python
try:
    # sentencias
except excepcion1:
    # sentencias
except excepcion2:
    # sentencias
```

En este casos, ***excepcionN*** son las distintas clases de excepción que deseamos tratar. Esta es la secuencia de acciones:

- Primero se ejecutan las sentencias de la cláusula `try` (de `try` a `except`).
    - Si no se levanta (produce) ninguna excepción en toda la cláusula (incluyendo excepciones no tratadas provenientes de funciones a las que hayamos llamado), se ignoran las cláusulas `except` y la ejecución sigue tras el **bloque** `try` completo (incluidas todas sus cláusulas).
    - Si se levanta excepción durante la cláusula `try`, se salta el resto de esta y se empieza a buscar en las cláusulas `except`, en orden de aparición, hasta encontrar una cuyo excepción (***excepcionN***) coincida con la excepción producida.
        - Si se encuentra coincidencias, se ejecuta esa cláusula entera, para luego ignorar el resto de cláusulas `except` (suponiendo que dentro de la cláusula `except` ejecutada se produzca otra excepción, deberá ser tratada a su vez mediante un bloque `try` en su interior, o de lo contrario, el programa se interrumpirá por excepción no tratada).
        - Si no se encuentra coincidencia, pasa el tratamiento de la excepción a un posible bloque `try` exterior (caso de bloques `try` anidados, o estando en una función que ha sido llamada desde otro bloque `try`).
            - Si lo hay, se repite allí el proceso.
            - Si no lo hay, es una excepción no tratada (*unhandled exception*) y la ejecución se interrumpe.

La cláusula `except` puede tener como argumento un tipo de excepción, o varios de ellos en una tupla si queremos tratar todos esos tipos del mismo modo:

```python
except (excepcion1, excepcion2, excepcion3, excepcion4):
```

Dado que un tipo de excepción es una clase, una excepción es recogida por una cláusula `except` de ese tipo o de un tipo base (véase el apartado de herencia de clases). Por ejemplo, si ***ExBase*** es una clase excepción, y ***ExDeriv*** una clase derivada de esta, y definimos nuestras cláusulas `except` en este orden:

```python
except ExBase:
    # sentencias
except ExDeriv:
    # sentencias
```

Cada vez que se produzca una excepción del tipo ***ExBase*** o ***ExDeriv***, será recogida por la cláusula `except ExBase`, de tal modo que la cláusula `except ExDeriv` no se ejecutará nunca. Si queremos manejar diferentes excepciones con relaciones jerárquicas, debemos siempre incluirlas desde la más específica e ir ascendiendo por la jerarquía:

```python
except ExDeriv:
    # sentencias
except ExBase:
    # sentencias
```

La última cláusula `except` puede omitir el tipo de excepción (`except:`), sirviendo de comodín (resto de tipos de excepción). Se puede usar, por ejemplo, para informar de que no hemos podido manejar la excepción y la pasamos a un `try` exterior, por ejemplo, con un `print()` informativo y un `raise` para volver a levantarla para el `try` exterior.

Después de las cláusulas `except` puede haber opcionalmente una cláusula `else`. Esta se ejecuta solo si la cláusula `try` se ha ejecutado entera sin leventar ninguna excepción (si hay excepción no se ejecuta). Sería la continuación de las sentencias de la cláusula `try`, pero fuera de la protección de `try...except`. Su utilidad es precisamente la de quedar fuera de esa protección, y que al mismo tiempo no se ejecute si se ha producido (y tratado) una excepción.

Si escribimos por ejemplo `except excepcion1 as err:` podemos acceder al contenido del error mediante la variable ***err***, una instancia de la excepción, de tipo ***excepcion1***. Si la excepción ha sido levantada pasándole argumentos, podemos acceder al valor de estos mediante `err.args`. El objeto (instancia) de la excepción concreta también define la propiedad ***\_\_str\_\_***, que contiene un *string* con estos argumentos, con lo que se pueden imprimir sin pasar por ***args***:

```python
>>> try:
...     raise Exception('spam', 'eggs')
... except Exception as inst:
...     print(type(inst))    # tipo de la instancia
...     print(inst.args)    # argumentos en .args
...     print(inst)    # __str__ contiene los argumentos
...     x, y = inst.args    # unpack args
...     print('x =', x)
...     print('y =', y)
...
<class 'Exception'>
('spam', 'eggs')
('spam', 'eggs')
x = spam
y = eggs
```

### 8.4 Raising Exceptions

(Levantar excepciones.)

Para levantar una excepción de tipo ***excepcion1***:

```python
raise excepcion1
```

El argumento de `raise` es una instancia de una excepción o una clase de excepción (clase ***Exception*** o derivada de esta); si pasamos una clase, se creará la instancia usando el constructor sin argumentos. En caso de que se levante una excepción en un bloque `try`, y queramos hacer algo con ella (en una cláusula `except`), pero queramos dejarla sin tratar (*unhandled*) para que se detenga la ejecución o para pasarla a un `try` exterior, dentro de la cláusula `except` pertinente la trataremos adecuadamente y posteriormente la re-levantaremos mediante `raise` **sin argumentos**. Ejemplos:

```python
raise Exception('spam', 'eggs', 10)    # excepción con
                                       # argumentos
raise NameError    # equivale a raise NameError()
raise    # re-raise la excepción que estamos tratando
```

### 8.5 Exception Chaining

(Encadenado de excepciones.)

Es posible levantar una excepción encadenándola con otra en cuanto a una relación causa-efecto. Esto se consigue mediante `raise ... from ...` para definir que la excepción que estamos levantando está causada por otra excepción (la que estamos tratando). Veamos un ejemplo:

```python
try:
    conecta()
except ConnectionError as ex:
    raise RuntimeError('Error al abrir BD...') from ex
```

Esto producirá la terminación del programa, y el mensaje de error indicará que se ha producido una excepción ***RuntimeError***, cuyo causante ha sido la excepción ***ConnectionError***:

```
Traceback (most recent call last):
File "<stdin>", line 2, in <module>
File "<stdin>", line 2, in conecta
ConnectionError

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
File "<stdin>", line 4, in <module>
RuntimeError: Error al abrir BD...
```

Como podemos ver, el constructor de la excepción ***RuntimeError*** admite un *string* como argumento, el cual indica el mensaje de error asociado a la excepción

Si por ejemplo el código visto está dentro de un bloque `try` envolvente, esta excepción se propagará a este bloque, siendo posible, a su vez, encadenarla la excepción exterior que la capture. Y así sucesivamente.

Dado que el encadenado de excepciones es automático cuando se levanta una excepción dentro de una cláusula `except` o `finally` (pila de excepciones), es posible deshabilitar este encadenamiento mediante `raise ... from None`.

### 8.6 User-defined Exceptions

(Excepciones definidas por el usuario.)

Podemos definir tipos de excepciones, derivando la clase ***Exception***. Podemos redefinir (*override*) cosas como ***\_\_str\_\_***, o hacer que el inicializador defina atributos adicionales, etc. Los nombres de estas clases derivadas suelen terminar en ***Error***. Se utilizan para tratar posibles excepciones de naturaleza específica a nuestro código.

### 8.7 Defining Clean-up Actions

(Definir acciones de limpieza.)

Otra cláusula opcional del bloque `try` es `finally`. Este bloque se ejecuta en cuanto abandonamos el bloque `try`, tanto si es hemos terminado el `try`, o un `except`, o hemos salido con `break`, `continue` o `return`. Es decir, cuando hemos terminado, y tocaría ir saliendo del bloque `try`, se ejecutará esta cláusula obligatoriamente.

Si la excepción ha quedado *unhandled*, o se ha producido una excepción en una cláusula `else` o `except`, tocaría abortar el programa o pasar inmediatamente al `try` exterior; pero si hay cláusula `finally`, se captura momentáneamente la excepción, se ejecuta el `finally`, y después se vuelve a levantar la excepción, produciéndose entonces el *abort*, o el paso al *outer* `try`. Es bueno para liberar recursos (*clean-up actions*) que tengan que hacerse siempre, ya que **la ejecución de** `finally` **está asegurada bajo cualquier circunstancia**.

Si se produce una excepción en una sentencia `return`, y queda *unhandled*, normalmente se pasaría al bloque `try` del código que llamó a la función actual (si existe bloque). En este caso, si hay cláusula `finally`, lo que retornará la función a dicho bloque es el valor de la sentencia `return` que incluiremos dentro de la cláusula `finally`.

### 8.8 Predefined Clean-up Actions

(Acciones predefinidas de limpieza.)

Hay objetos que disponen de acciones de limpieza predefinidas, como los objetos archivo. Cuando esto es así, se pueden usar con la sentencia `with`, lo cual obliga a la ejecución de estas acciones en cualquier circunstancia (nos asegura que los recursos son liberados después de su uso):

```python
with open('myfile.txt') as f:
    for line in f:
        print(line)
```

En este caso podemos asegurar que el archivo ***f*** queda cerrado después de su uso, incluso aunque se haya producido una excepción *unhandled* y el programa se interrumpa.

## 9. CLASSES

(Clases.)

Mecanismo para definir nuevos tipos y objetos.

Haciendo una comparativa con otros lenguajes, los miembros de una clase en *Python* son normalmente públicos, y las funciones miembro son virtuales.

Una clase define un nuevo tipo, pero al mismo tiempo, una clase es un objeto en sí (objeto clase, independientemente de sus instancias).

Se pueden derivar (extender) los tipos *built-in* también. Los operadores *built-in* pueden redefinirse para las nuevas clases.

### 9.1 A Word About Names and Objects

(Una palabra sobre nombres y objetos.)

El hecho de que varios nombres (variables) en uno o más *scopes* distintos pueden referenciar al mismo objeto, es decir, el hecho de que las variables se comportan de forma análoga a los apuntadores (en lenguajes como *C*), se llama *aliasing*.

### 9.2 Python Scopes and Namespaces

(Ámbitos y nombres de espacios en *Python*.)

Un nombre de espacio (*namespace*) es un mapeo de nombres a objetos, mapeo que se produce en la tabla de nombres: nombres *built-in*, nombres globales del módulo, nombres locales en una función, atributos de un objeto, etc. A su vez, cada *namespace* puede tener su propia tabla de nombres, con otros *namespaces* dentro. A cada *namespace* le corresponde pues una tabla de nombres.

Un nombre detrás de un punto es un atributo del objeto o módulo "padre"; en ***modname.funcname*** el módulo ***modname*** tiene una función ***funcname()***. Se dice que ***funcname*** es un atributo del objeto ***modname***. Los atributos pueden ser de lectura o de lectura/escritura (estos últimos pueden hasta ser borrados con `del`).

El *namespace* de los *built-ins* se crea al iniciarse el intérprete, y permanece hasta que este se cierra. El *namespace* global de cada módulo se crea al cargar el módulo, y persiste hasta que se cierra el intérprete. Esto incluye el módulo principal (***\_\_main\_\_***). El *namespace* de una función se crea en cada llamada a esta, y se elimina tras su ejecución.

El *scope* (ámbito) es la región textual de código en la que un *namespace* es directamente accesible, es decir, es accesible mediante un *unqualified name*, o nombre sin calificar, es decir, sin prefijar el nombre del *namespace*. *Scopes* existentes (en orden de búsqueda, desde más interior a más exterior):

- *Scope* local de la función o clase, el que se busca primero (nombres locales).
- *Scope(s)* de la(s) posible(s) función(es) envolvente(s). Se buscan desde dentro hacia fuera. Sus nombres no son locales, pero tampoco globales.
- Penúltimo *enclosing scope*, con los nombres globales del módulo actual.
- *Scope* más exterior: donde están los nombres *built-in*.

Si una variable está declarada `global` en cualquier *scope*, todas las referencias y asignaciones a ella se hacen en el *scope* global del módulo. Si se declara `nonlocal`, se refiere a una variable de un *scope* exterior al actual (lo más interior posible) exceptuando el global. Si no se declara la variable, las referencias se buscan de dentro hacia fuera, pero las asignaciones (y borrados, con `del`) se hacen en el *scope* local, enmascarando posibles variables exteriores con el mismo nombre.

La definición de una clase introduce un nuevo *namespace* dentro del *scope* actual.

Para una función, su *scope* global es el módulo donde está definida, independientemente del alias que se use para invocarla.

### 9.3 A First Look at Classes

(Un primer vistazo a las clases.)

#### 9.3.1 Class Definition Syntax

(Sintaxis de definición de clases.)

Para definir una clase se usa `class`:

```python
class NombreClase:
    # sentencias
```

La definición de una clase crea un *objeto clase*, que en realidad es un tipo. Los nombres definidos dentro (funciones, variables, etc.) están en el *scope* de esta definición (pertenecen al *namespace* de este objeto clase). El nombre de la clase definida queda en el *namespace* del bloque que contiene la definición.

#### 9.3.2 Class Objects

(Objetos clase.)

Para acceder a los miembros de la clase definida, se utiliza la sintaxis ***objeto.atributo***.

```python
class MiClase:
    """Ejemplo de clase"""  # docstring de la clase
    i = 12345
    def f(self):
        return 'hola'
```

En este caso, ***MiClase.f*** es un objeto función que puede ser llamado; también podemos acceder a la variable ***MiClase.i***, que es un atributo de la clase. En este caso, el atributo ***MiClase.\_\_doc\_\_*** tiene como valor el *docstring* de la clase.

Una vez hemos definido la clase, podemos declarar objetos de este tipo (como hemos dicho, la clase es en sí un tipo). A esto se la llama instanciación. Para instanciar un objeto de clase ***MiClase***:

```python
x = MiClase()
```

Esto crea un *objeto instancia* (o simplemente *instancia*) de la clase ***MiClase***, que estará referenciado por la variable ***x***.

Si al instanciar se desean dar valores iniciales a dicha instancia, la clase debe definir un método (llamado constructor) de nombre ***\_\_init\_\_()***, que recogerá esos valores e inicializará adecuadamente la instancia creada. Véase un ejemplo:

```python
>>> class Complejo:
...     def __init__(self, real, imag):
...         self.r = real
...         self.i = imag
...
>>> x = Complejo(3.0, -4.5)
>>> x.r, x.i
(3.0, -4.5)
```

Como puede observarse, el primer parámetro en la definición de un método es simplemente una referencia a la misma instancia (no debe pasarse como argumento al instanciar).

#### 9.3.3 Instance Objects

(Objetos instancia.)

Las instancias pueden contener dos tipos de atributos: métodos y/o datos (*data attributes*). Si una clase define un *objeto función*, tal objeto será un *método* de cualquier **instancia** de esa clase.

#### 9.3.4 Method Objects

(Objetos método.)

Al llamar a un método, este recibe automáticamente en su primer parámetro una referencia a la instancia a la que pertenece dicho método. Es por ello que todas las funciones definidas en una clase deben incluir ese primer parámetro obligatoriamente.

La diferencia entre un objeto de tipo función y un objeto de tipo método, es que el objeto método tiene dos referencias: una al objeto función y otra a la instancia a través de la cual se llama a ese método. Cuando se llama a un método, internamente se inserta el objeto instancia como primer argumento, y todo ello se pasa al objeto función correspondiente.

Si ***x*** es una instancia de ***MyClass***, que define una función ***foo(self)***:

```python
x.foo()  # llamada a un método
```

Esto equivale a:

```python
MyClass.foo(x)    # llamada a una función
```

#### 9.3.5 Class and Instance Variables

Las variables **definidas** en una **clase** (o añadidas posteriormente) son compartidas por todas las **instancias**. Las variables *añadidas a la instancia* (en el constructor, o en cualquier otra parte del código, incluso fuera de la definición de la clase), quedan definidas únicamente para esa instancia.

### Aclaraciones adicionales

Considérese el siguiente ejemplo:

```python
class MiClase:
    peso = 70    # atributo de datos (de la clase)
    def __init__(self):    # constructor
        self.nombre = 'Pepe'  # atrib. de datos
                              # (de la instancia)
        peso = 80    # simple variable local de la función
```

Veamos en primer lugar, que para definir atributos de datos desde el código de un método, hay que utilizar el argumento ***self***. De lo contrario sería una simple variable local del método.

> No es obligatorio que el primer parámetro de un método tenga como nombre ***self***, pero por convenio se hace siempre así.

Como vemos, en ***MiClase*** se define un atributo de datos ***peso***, que será compartido por todas las instancias. Si ***x*** e ***y*** son instancias de ***MiClase***, podemos acceder a ***x.peso*** y ***y.peso***, además de ***MiClase.peso***. Además, al instanciar ***x*** e ***y***, se ha creado *para cada instancia* el atributo ***nombre***, al que puede accederse con ***x.nombre*** y ***y.nombre***, sin embargo no se trata de un valor compartido, pues cada instancia almacena su propio valor. Cambiando ***x.nombre*** no estamos cambiando ***y.nombre*** (no existe ***MiClase.nombre***).

Por otro lado, si cambiamos ***MiClase.peso***, sí cambiará ***x.peso*** y ***y.peso***. Sin embargo, si hacemos `x.peso = 50`, estamos creando un atributo ***peso*** para **la instancia** ***x***, que enmascara (*overrides*) el atributo ***peso*** de ***MiClase***. Así pues, esto cambiará ***peso*** únicamente para la instancia ***x***.

Si ahora cambiamos ***MiClase.peso***, cambiará para ***y.peso*** también, pero no ***x.peso*** que tiene su propia versión de este atributo.

Si tras esto hacemos `del x.peso`, el atributo ***peso*** de ***x*** desaparece, con lo que ***x.peso*** pasará a retornar el valor (compartido) de ***MiClase.peso***.

Si hacemos ahora `del MiClase.peso`, las instancias dejarán de tener atributo ***peso***, a excepción de aquellas que lo hayan definido en sí mismas (como por ejemplo mediante `x.peso = 50`).

Se pueden ir creando y borrando atributos de datos en instancias y clases sobre la marcha, en cualquier punto del código, mediante asignaciones (`x.peso = 50`, `MiClase.peso = 0`, etc.) o borrados mediante `del`. Los nombres añadidos pueden enmascarar o cambiar los de la clase, o ser completamente nuevos (`x.speed = 30`, `MiClase.speed = 0`, etc.).

Se pueden también añadir/quitar funciones a las clases. También se pueden añadir y quitar funciones **y métodos** a las instancias. Supongamos que ***foo()*** es una función cualquiera; se podrían hacer cosas como `x.fun = foo`, `MiClase.f = foo`, `del MiClase.fun`, etc. Se pueden incluso eliminar de la clase funciones incluidas en la definición de la misma. También se pueden añadir y quitar funciones y métodos a la instancia.

#### Sobre funciones y métodos

Como hemos visto, **un método es una función asociada a una instancia**.

```python
>>> class MiClase:
...     def foo(self):
...         print("¿Esto es un método o una función?")
...
>>> x = MiClase()
>>> MiClase.foo
<function MiClase.foo at 0x7f91fce727a0>
>>> x.foo
<bound method MiClase.foo of <__main__.MiClase object at 0x7f91fce69060>>
>>>
```

Como vemos en este ejemplo, ***MiClase.foo*** es una función, ya que no está asociada a ninguna instancia, mientras que ***x.foo*** es un método (asociado a la instancia ***x***). Es por ello que al llamar a la función mediante ***MiClase.foo()*** debemos pasarle un argumento, mientras que en la llamada al método ***x.foo()*** esto se hace automáticamente (se le pasa una referencia a la misma instancia ***x***).

Añadamos ahora una función a la clase:

```python
>>> MiClase.f = MiClase.foo
>>> MiClase.f
<function MiClase.foo at 0x7f91fce727a0>
>>> MiClase.f(x)
¿Esto es un método o una función?
>>> MiClase.foo(x)
¿Esto es un método o una función?
```

En este caso, ***MiClase.f*** es una referencia a ***MiClase.foo***, pero podríamos haberle dado como valor cualquier función definida en el *scope* actual. Pero en el ejemplo, tanto ***MiClase.foo*** como ***MiClase.f*** son ahora dos referencias a la **misma función**. Tanto la una como la otra precisan de un argumento a la hora de ser llamadas.

Dado que ahora la clase tiene las dos funciones (***foo*** y ***f***), podemos llamarlas a través de la instancia ***x***. Téngase en cuenta que a través de instancia estamos llamando a métodos, con lo que no se debe indicar argumento:

```python
>>> x.f()
¿Esto es un método o una función?
>>> x.foo()
¿Esto es un método o una función?
>>> x.f
<bound method MiClase.foo of <__main__.MiClase object at 0x7f91fce69060>>
>>> x.foo
<bound method MiClase.foo of <__main__.MiClase object at 0x7f91fce69060>>
```

Veamos qué pasa si añadimos **una función a la instancia**:

```python
>>> x.otraf = MiClase.foo
>>> x.otraf
<function MiClase.foo at 0x7f91fce727a0>
>>> x.otraf()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: MiClase.foo() missing 1 required positional argument: 'self'
```

Como vemos, hemos asignado la función ***MiClase.foo*** (podríamos haberlo hecho con cualquier función que estuviese en el *scope* actual) directamente a un atributo ***otraf*** de la instancia ***x***. Al hacerlo así, ese atributo no pasa a ser un método sino una función, ya que para que quede asociado a la instancia es necesario que la función pertenezca a la clase, no simplemente a la instancia.

Sin embargo, dado que ***x.foo*** sí es un método, podemos hacer:

```python
>>> x.extraf = x.foo
>>> x.extraf
<bound method MiClase.foo of <__main__.MiClase object at 0x7f91fce69060>>
>>> x.extraf()
¿Esto es un método o una función?
```

Ahora, ***x.extraf*** sí es un método, asociado a la instancia ***x***.

Por complicarlo un poco más, veamos un ejemplo que, aunque no tiene sentido, nos puede ayudar a comprender la diferencia entre funciones y métodos.

```python
>>> y = MiClase()
>>> y
<__main__.MiClase object at 0x7f91fcf74670>
>>> y.foo
<bound method MiClase.foo of <__main__.MiClase object at 0x7f91fcf74670>>
```

Vemos que ***y.foo*** es un método, asociado a la instancia ***y***.

```python
>>> x.externaf = y.foo
>>> x.externaf
<bound method MiClase.foo of <__main__.MiClase object at 0x7f91fcf74670>>
```

Ahora ***x.externaf*** es una referencia a un método asociado ¡a la instancia ***y***! Realmente esto no se utiliza, puesto que sería absurdo y una muy mala práctica.

Por otro lado, **desde dentro de un método**, se puede acceder a los miembros (variables y métodos) *de la instancia* mediante el primer parámetro, así: ***self.atributo***. Para acceder a las variables **de la clase**, lo haremos usando el nombre de la misma: ***MiClase.atributo***. Si no queremos escribir explícitamente el nombre de la clase, se podría hacer mediante `type(self).atributo` (también, aunque menos elegante, `self.__class__.atributo`). Para acceder a variables globales (módulo donde está definido el método), se hace igual que en funciones ordinarias (mediante `global`).

> A una función, que es también un objeto, se le pueden añadir atributos. Por ejemplo `foo.n=3` le añade una variable ***n*** a la función ***foo()***. Como analogía con otros lenguajes, esto sería un mecanismo equivalente a las variables estáticas (en lenguajes como *C*). El atributo mantendría su valor entre llamadas. Para acceder a esa variable desde dentro de la función, se haría mediante ***foo.n***, ya que ***n*** a secas sería una simple variable local. También podría accederse a ***foo.n*** desde fuera de la función.

### 9.4 Random Remarks

(Observaciones varias.)

Como hemos visto, si una instancia define una variable con el mismo nombre que una variable de la clase, la de la instancia se impone (*overrides*) a la de la clase.

Como hemos visto, las funciones se pueden *definir* también fuera del bloque `class`:

```python
def f1(self, x, y):
    return min(x, x + y)

class C:
    f = f1
    def g(self):
        return 'hello world'
    h = g
```

En este caso tenemos 3 métodos: ***f***, ***g*** y ***h***.

**Cualquier valor** en *Python* es un objeto, por lo que tiene una clase (también llamada **tipo**), que está almacenada en el atributo ***\_\_class\_\_*** del objeto (***objeto.\_\_class\_\_***). Equivale a llamar a la función *bult-in* `type(objeto)`.

### 9.5 Inheritance

(Herencia.)

Una clase (***derivada***) puede construirse a partir de otra (***base***). Este es el mecanismo:

```python
class Deriv(Base):
    # Definición de la clase
```

El nombre de la clase base tiene que estar en el *scope* de la definición de la derivada, naturalmente. Si la clase base estuviese en un módulo externo ***m1***:

```python
class Deriv(m1.Base):
    # Definición de la clase
```

Cuando se referencia un atributo, primero se busca en la clase; si no se encuentra, se busca en su clase base; y así recursivamente hasta el final de la jerarquía. Así, una clase derivada reemplaza (*overrides*) los atributos (*data attributes* y métodos) de la base.

Si un método, aunque sea uno definido en la clase base, llama a otro método de la instancia, se empieza a buscar ese método llamado de la forma habitual: empezando por la clase actual y subiendo.

Desde el interior de la clase derivada, una forma de llamar a un método de la clase base es hacerlo directamente como función (como haríamos desde fuera):

```python
Base.metodo(self, argumentos)
```

`isinstance(obj, clase)` retorna verdadero si ***obj*** es una instancia de la clase ***clase*** o de una clase derivada de esta.

`issubclass(<clase1>, <clase2>)` retorna verdadero si ***clase1*** es una clase derivada de ***clase2***.

#### 9.5.1 Multiple Inheritance

(Herencia múltiple.)

Es posible para una clase tener más de una clase base:

```python
class Deriv(Base1, Base2, Base3):
    # Definición de la clase
```

La resolución de nombres de atributos es del tipo *depth-first* (prioridad a la profundidad), *left-to-right* (izquierda a derecha). Así, si un atributo buscado no se encuentra en ***Deriv***, se buscará en ***Base1***, y luego recursivamente en las clases base de ***Base1***; si tampoco lo encuentra allí, se busca en ***Base2***, luego en sus bases, etc.

De hecho, el orden de búsqueda (*MRO: Method resolution order*, u orden de resolución de métodos) es algo más complejo (ordenación dinámica o *dynamic ordering*), ya que asegura que las estructuras de diamante serán resueltas sin buscar dos veces en la misma clase. Una estructura de diamante se produce cuando hay más de un camino para llegar a una clase jerarquía arriba; y si partimos de que en *Python* *todas las clases* (y por consiguiente, todos los tipos) son descendientes de la clase ***object***, tenemos como mínimo un diamante asegurado en cuanto exista una herencia múltiple.

A parte de evitar múltiples búsquedas en la misma clase, el *dynamic ordering* gestiona el problema de múltiples llamadas a `super()` (llamada al constructor de la clase base). Imaginemos que todas las clases realizan esta llamada desde su propio constructor. La clase a la que se puede llegar por más de una vía sería llamada más de una vez. El *dynamic ordering* lo evita.

Sin embargo, este *dynamic ordering* de *Python* **asegura la búsqueda de izquierda a derecha**, con lo que es posible diseñar clases extensibles y confiables con herencia múltiple (no empezará con la jerarquía de ***Base2*** hasta que no haya terminado con la jerarquía de ***Base1***, etc.).

### 9.6 Private Variables

(Variables privadas.)

No existen variables privadas en *Python*, entendiéndose como tales las variables pertenecientes a un objeto que sean accesibles únicamente desde los métodos del mismo. Por convenio, en todo caso, se suele indicar que un atributo debe ser tratado como privado haciendo que su nombre empiece por ***\_***.

Sin embargo hay un pequeño mecanismo llamado *name mangling* que intenta evitar accidentes: un miembro (*data member* o método) cuyo nombre sea del tipo ***\_\_spam*** (mínimo 2 guiones bajos al principio, y máximo 1 al final) queda reemplazado internamente, durante la ejecución, por ***\_Clase\_\_spam***, siendo ***Clase*** el nombre de la clase a la que pertenece el objeto. Si el nombre de la clase empezara por uno o más guiones bajos (***\_***), estos no formarían parte de este nombre.

Si en una clase base ***B*** definimos el atributo ***\_\_atrib***, y luego creamos una clase derivada ***D***, las referencias que se hagan a ***\_\_atrib*** desde métodos de ***B*** se sustituyen por ***\_B\_\_atrib***, mientras que las que se hagan desde métodos definidos en ***D*** se referirán a ***\_D\_\_atrib***.

Así, por ejemplo, los métodos heredados de ***B*** que hagan referencia a ***\_\_atrib***, serán referencias a ***\_B\_\_atrib***, mientras que desde código definido en ***D*** (incluidos los métodos que reemplazan los de ***B***) serán referencias a ***\_D\_\_atrib***.

En la práctica, esto sería equivalente a sustituir todas las ocurrencias de ***\_\_atrib*** por ***\_B\_\_atrib*** dentro del bloque `class` que define ***B***, y por ***\_D\_\_atrib*** dentro del bloque `class` que define ***D***.

Así, cualquier método o atributo de datos con este formato (en este caso ***\_\_atrib***) no se puede reemplazar (*override*) en la clase derivada, a no ser que en la derivada definamos explícitamente el atributo ***\_B\_\_atrib***, cosa que no sería conveniente, al hacerlo pierde todo el sentido este mecanismo.

### 9.7 Odds and Ends

(Cosas varias.)

Si queremos algo así como un *struct* de *C*, podemos crear una clase vacía, con una simple sentencia `pass` (no nos interesan datos compartidos por las instancias), e ir añadiendo (o borrando) a cada instancia los valores (atributos) que queramos, sobre la marcha.

Por otro lado, supongamos un **método** cualquiera ***m***: este tiene un atributo ***m.\_\_self\_\_*** que es una referencia al objeto (instancia) a la que pertenece el método, y otro atributo ***m.\_\_func\_\_*** que es una referencia al objeto función en sí. Con esto podemos ver que, efectivamente, un método no es más que una función asociada (*bound*) a un objeto instanciado.

### 9.8 Iterators

(Iteradores.)

Los objetos contenedores vistos hasta ahora (listas, tuplas, diccionarios, *strings*, archivos, etc.) son *iterables*, es decir, se puede iterar por sus elementos mediante `for`.

Lo que hace `for` internamente es llamar a la función *builtin* `iter()` pasándole como argumento el objeto contenedor. Esta función retorna lo que se denomina un objeto **iterador**, el cual dispone de un método `__next__()`, cuyo cometido es ir retornando el "siguiente elemento", levantando al final de todo una excepción ***StopIteration*** para indicar que ya no hay más elementos.

Se puede llamar explícitamente al método `__next__()` del objeto iterador usando la función *builtin* `next()`.

```python
>>> s = 'abc'
>>> it = iter(s)
>>> it
<iterator object at 0x00A1DB50>
>>> next(it)
'a'
>>> next(it)
'b'
>>> next(it)
'c'
>>> next(it)
Traceback (most recent call last):
  File "<stdin>", line 1, in ?
    next(it)
StopIteration
```

Viendo como funcionan los iteradores, es fácil dotar de funcionalidad de iterador a nuestra clase: solo hay que definir un método `__iter__()`, que retornará un objeto que disponga de un método `__next__()` (normalmente `__iter__()` retornará ***self*** y listos); y un método `__next__()` que vaya retornando los elementos secuencialmente al ser llamado, y tras el último levante la excepción ***StopIteration***.

Todos los iteradores son iterables, aunque no todos los iterables son iteradores.

### 9.9 Generators

(Generadores.)

Los generadores son una herramienta simple y poderosa para *crear iteradores*. Se escriben como una función normal, y usan la sentencia `yield` para ir retornando elementos:

```python
>>> def generador(n1, n2, n3):
...    yield n1 * 5
...    yield n2 * 9
...    yield n3 * 14
...
>>> for n in generador(2, 2, 2):
...    print(n)
...
10
18
28
```

En el ejemplo, ***generador*** es un **objeto función**, y ***generador(2, 2, 2)*** es un **objeto generador** (la llamada a la función retorna tal objeto), que tiene todas las características de un iterador. Un generador crea automáticamente los métodos `__iter__()` y `__next__()`, y levanta la excepción ***StopIteration*** cuando hace falta.

Lo que hace el generador cuando se itera en él es ejecutarse hasta el primer `yield`, retornando ese primer valor; al volver a ejecutarse (`next()`, siguiente iteración) para buscar el segundo valor, seguirá la ejecución donde la había dejado la primera vez (guarda el estado de ejecución completo), y acabará retornando el segundo valor (segundo `yield`); y así sucesivamente hasta el final. Tras retornar el último `yield` y volver a ejecutarse `next()`, se ejecutará todo el código tras ese último `yield` hasta el final (fin del código o sentencia `return`) y levantará la excepción ***StopIteration***.

### 9.10 Generator Expressions

(Expresiones generador.)

Las *generator expressions* son sintácticamente como las *list comprehensions* pero entre paréntesis en lugar de entre corchetes. En este caso crean y retornan un iterador. Se suele usar como argumento de una función que recoja un iterable.

```python
(i * i for i in range(10))    # objeto generador
```

## 10. BRIEF TOUR OF THE STANDARD LIBRARY

(Breve tour por la biblioteca estándar.)

En este apartado se darán unas breves pinceladas sobre la biblioteca estándar. Evidentemente, dicha biblioteca es increíblemente extensa, con lo que se tratará aquí únicamente de forma muy superficial.

### 10.1 Operating System Interface

(Interfaz con el sistema operativo.)

El módulo ***os*** proporciona funciones para interactuar con el sistema operativo.

```python
>>> import os
>>> os.getcwd()  # Retorna el directorio de trabajo actual
'C:\\Python310'
>>> os.chdir('/server/accesslogs')  # Cambia de directorio
>>> os.system('mkdir today')  # Ejecuta comando del sistema
0
```

Tras importar un módulo ***m***, las funciones `dir(m)` y `help(m)` nos proporcionan una lista completa de las funciones del módulo, o una explicación del mismo (extraída de sus *docstrings*).

Para gestión de directorios:

```python
>>> import shutil
>>> shutil.copyfile('data.db', 'archive.db')  # copia
'archive.db'
>>> shutil.move('/build/exec', 'installdir')  # mueve
'installdir'
```

### 10.2 File Wildcards

(Comodines en archivos.)

Para obtener una lista de archivos de un directorio, usando comodines (***\**** y ***?***) en los nombres:

```python
>>> import glob
>>> glob.glob('*.py')
['primes.py', 'random.py', 'quote.py']
```

### 10.3 Command Line Arguments

(Argumentos de la línea de comandos.)

Para recoger los argumentos pasados a un *script Python*, se usa la variable ***argv*** del módulo ***sys*** (***sys.argv***), que almacena una lista de *strings* con todos ellos (el primero es el nombre del *script*). Suponiendo que un *script* ***demo.py*** se haya invocado como `python3 demo.py uno dos tres` (o posiblemente como `demo.py uno dos tres` en un sistema *Unix*):

```python
import sys
print(sys.argv)
```

Producirá la salida:

```
['demo.py', 'uno', 'dos', 'tres']
```

Para una forma más sofisticada de tratar los argumentos, existe el módulo ***argparse***.

### 10.4 Error Output Redirection and Program Termination

(Redirección a salida de error y terminación de programa.)

El módulo ***sys*** tiene atributos que referencia la entrada estándar (***stdin***), la salida estándar (***stdout***) y la salida de error (***stderr***). Por ejemplo, para escribir un mensaje a la salida de error:

```python
>>> import sys
>>> sys.stderr.write("Un error")
```

Por otro lado, `sys.exit()` termina la ejecución de un *script*.

### 10.5 String Pattern Matching

(Búsqueda de patrones en *strings*.)

Para búsquedas sofisticadas con expresiones regulares, se dispone del módulo ***re***.

### 10.6 Mathematics

(Matemáticas.)

El módulo ***math*** proporciona funciones matemáticas para punto flotante.

El módulo ***random*** permite la generación de números aleatorios.

El módulo ***statistics*** tiene funcionalidades para el cálculo de propiedades estadísticas básicas.

### 10.7 Internet Access

(Acceso a internet.)

Los módulos ***urllib.request*** y ***smtplib*** proporcionan mecanismos para la obtención de datos desde una *URL* y para el envío de correo electrónico, respectivamente.

### 10.8 Dates and Times

(Fechas y horas.)

El módulo ***datetime*** proporciona mecanismos para el tratamiento y formato de fechas y/o horas.

```python
>>> from datetime import date
>>> hoy = date.today()
>>> hoy
datetime.date(2003, 12, 2)
>>> hoy.strftime("%m-%d-%y. %d %b %Y is %A, %d of %B.")
'12-02-03. 02 Dec 2003 is Tuesday, 02 of December.'
```

Si se desea hacer lo mismo en otros idiomas, hay que definir antes el *locale* deseado, mediante el módulo ***locale***:

```python
>>> import locale
>>> locale.setlocale(locale.LC_ALL, 'es_ES.UTF-8')
'es_ES.UTF-8'
>>> hoy.strftime("%d/%m/%Y. %d %b %Y es %A, %d de %B.")
'02/12/2003. 02 dic 2003 es martes, 02 de diciembre.'
```

Las fechas soportan aritmética de calendario:

```python
>>> aniversario = date(1964, 7, 31)
>>> edad = hoy - aniversario
>>> edad.days
14368
```

### 10.9 Data Compression

(Compresión de datos.)

Existen varios módulos para la compresión de datos: ***zlib***, ***gzip***, ***bz2***, ***lzma***, ***zipfile*** y ***tarfile***.

### 10.10 Performance Measurement

(Medición del rendimiento.)

El módulo ***timeit*** permite medir el tiempo que tarda en ejecutarse una sentencia o conjunto de sentencias.

### 10.11 Quality Control

(Control de calidad.)

El módulo ***doctest*** es útil para asegurar que el funcionamiento de las funciones es correcto a través de tests incluidos en las *docstrings* de las funciones que deseamos comprobar.

```python
def average(values):
    """Computes the arithmetic mean of a list of numbers.

    >>> print(average([20, 30, 70]))
    40.0
    """
    return sum(values) / len(values)

import doctest
doctest.testmod()  # validación automática de los tests
```

## 11. BRIEF TOUR OF THE STANDARD LIBRARY - PART II

(Breve tour por la biblioteca estándar, parte 2.)

Los ejemplos de esta sección son más específicos y menos utilizados. Veremos solo algunos de ellos.

### 11.4 Multi-threading

(Multi hilos.)

El *multi-threading* permite ejecutar tareas (secuencialmente independientes) de un mismo proceso en varios hilos simultáneos. Para ello, el módulo ***threading*** proporciona mecanismos de coordinación (bloqueos, eventos y semáforos).

### 11.6 Weak References

(Referencias débiles.)

El *garbage collector* de *Python* es un mecanismo que libera (de la memoria memoria) los objetos cuyo número de referencias llega a cero.

En ocasiones, sin embargo, puede ser necesario tener una referencia a un objeto que no inclremente el número de referencias en sí. Eso es precisamente una referencia débil. Estas no influyen en el conteo de referencias para el *garbage collector*, con lo que si las únicas referencias que tenemos al objeto son débiles, es posible que el objeto haya sido ya liberado.

Pueden crearse estas referencias débiles con las utilidades que proporciona el módulo ***weakref***.

### 11.8 Decimal Floating Point Arithmetic

(Aritmética de punto flotante decimal.)

El módulo ***decimal*** proporciona el tipo de datos ***Decimal***, que es un tipo numérico de punto flotante, pero en lugar de tener una representación interna en base 2 (binario, como el tipo *builtin* ***float***), lo hace en base 10 (decimal). En este caso, la precisión es absoluta para los números decimales (hasta el número de cifras significativas que configuremos, por defecto 28).

En este caso, pues, no tenemos que temer los problemas de representación que sufren los ***float***, con lo que podemos comparar números flotantes por igualdad.

Por ejemplo, en sistema binario no hay representación exacta del número decimal 0.1. Por lo tanto:

```python
>>> 0.1 * 3.0 == 0.3
False
```

Aquí, lo que hace *Python* internamente es convertir los literales de punto flotante en ***float***:

```python
>>> float('0.1') * float('3.0') == float('0.3')
False
```

Aquí podemos incorporar la aritmética de punto flotante decimal:

```python
>>> from decimal import *
>>> Decimal(0.1) * Decimal(3.0) == Decimal(0.3)
False
```

¿Por qué sigue fallando? Porque internamente *Python* sigue convirtiendo primero los literales de punto flotante en ***float***:

```python
>>> Decimal(float('0.1')) * Decimal(float('3.0')) == Decimal(float('0.3'))
False
```

Para que funcione correctamente debemos pasar directamente de *string* (o entero) a ***Decimal***, sin pasar por ***float***:

```python
>>> Decimal('0.1') * Decimal('3.0') == Decimal('0.3')
True
```

## 12. VIRTUAL ENVIRONMENTS AND PACKAGES

(Entornos virtuales y paquetes.)

Los programas hechos en *Python* utilizan con frecuencia funcionalidades proporcionadas por módulos (o bibliotecas) externos. Un ejemplo de ello son los módulos de la biblioteca estándar, pero con frecuencia utilizan otras biblioteca. Por lo tanto, será necesario que dichas bibliotecas estén instaladas en el sistema en el que se ejecuta la aplicación *Python*. Además, cada aplicación precisará de una versión concreta o rango de versiones de las bibliotecas que utilice. Es más, cada aplicación *Python* existente en el sistema puede utilizar una versión de *Python* distinta.

Así, resulta imposible que la configuración de *Python* (versión del intérprete, bibliotecas instaladas y versiones de las mismas) se adapte a los requisitos de todas las aplicaciones *Python* existentes en el sistema. Es posible que dos aplicaciones distintas tengan requisitos incompatibles.

La solución es la creación de **entornos virtuales**, mediante directorios que contengas una instalación específica de *Python*, así como una serie de paquetes o bibliotecas que conforman los requisitos de una aplicación *Python* específica. De esta forma, el entorno global del sistema, la configuración de *Python*, no debe adaptarse a una aplicación concreta: cada aplicación usará un entorno virtual distinto.

### 12.2 Creating Virtual Environments

(Creación de entornos virtuales.)

Para crear un entorno virtual solo se debe ejecutar el módulo ***venv***, perteneciente a la biblioteca estándar:

```
python3 -m venv nombre-ev
```

Aquí, ***nombre-ev*** es el nombre del directorio, relativo al directorio actual de trabajo, que se utilizará para almacenar el entorno virtual concreto.

Para crear y activar un entorno virtual en sistemas *Unix*:

```
python3 -m venv .venv
source .venv/bin/activate
```

El comando `source` evita la creación de un nuevo proceso terminal para la ejecución del comando `activate`. En este caso, se ejecuta `activate` en el mismo terminal en el que tecleamos el comando. En sistemas *Windows* se haría así:

```
python3 -m venv .venv
.venv\Scripts\activate.bat
```

Una vez activado el entorno virtual, el *prompt* del sistema cambiará para mostrar el nombre del entorno activo.

> La práctica habitual es que el directorio que almacena nuestro entorno virtual tenga por nombre ***.venv***, y sea un subdirectorio de la carpeta raíz de nuestro proyecto *Python*.

En el entorno virtual se suelen crear varios *alias* para el intérprete *Python*, de tal modo que `python`, `python3` y `python3.10` suelen ser lo mismo.

Suponiendo que deseemos crear un entorno virtual con una **versión antigua** de *Python*, siempre y cuando tal versión antigua esté instalada en nuestro sistema:

```
python3.5 -m venv .venv
```

Al activar este entorno virtual, `python` será una referencia al intérprete *Python* 3.5.

### 12.3 Managing Packages with pip

(Gestión de paquetes con ***pip***.)

El módulo ***pip*** es una utilidad de *Python* que sirve para la gestión (instalación, desinstalación, etc.) de módulos y bibliotecas para nuestro entorno *Python*. Antes de usar este módulo, deberemos instalarlo como un programa a parte, siguiendo las instrucciones de instalación en cada sistema. Una vez instalado, se puede ejecutar como módulo:

```
python -m pip install pandas
```

Esto instalará el módulo ***pandas*** **y todas sus dependencias** en el entorno virtual (si está activado) o en el sistema (si no hay entorno virtual activado).

Con frecuencia, el entorno virtual suele disponer de los comandos sinónimos `pip`, `pip3` y `pip3.10` que permiten abreviar el trabajo con paquetes:

```
pip install pandas
```

También es posible instalar una **versión específica** de una biblioteca:

```
python -m pip install requests==2.6.0
```

Esto instala la versión 2.6.0 del módulo ***requests***.

Para desinstalar un paquete específico, se utiliza el subcomando ***uninstall***:

```
python -m pip uninstall pandas
```

Para obtener información sobre un paquete instalado, se usa ***show***:

```
python -m pip show pandas
```

Para obtener una lista de todos los paquetes instalados, se usa ***list***:

```
python -m pip list
```

Recordemos que todos estos comandos actúan sobre el entorno virtual activo; si no lo hay, actúan sobre el sistema global.

El subcomando ***freeze*** produce una lista de los paquetes instalados y sus versiones, en un formato comprensible para `pip install`. Si dicha lista la guardamos en un archivo llamado (por convenio) ***requirements.txt***:

```
python -m pip freeze > requirements.txt
```

Entonces este archivo puede formar parte del proyecto, de tal modo que si se clona el proyecto en cualquier lado, utilizando este archivo se puede reconstruir el estado de los paquetes instalados, de esta forma:

```
python -m pip install -r requirements.txt
```

## 16. APPENDIX

(Apéndice.)

### 16.1.2 Executable Python Scripts

(*Scripts Python* ejecutables.)

Los *scripts Python* se pueden hacer directamente ejecutables en entornos *Unix*, añadiendo por ejemplo, como primera línea (línea *shebang*):

```python
#!/usr/bin/python3.10
```

Si no deseamos especificar la ruta del ejecutable:

```python
#!/usr/bin/env python3.10
```

Teniendo en cuenta que el comando de *Python* (en este caso, `python3.10`) debe estar efectivamente incluido en la variable *path* del sistema.

En todo caso, el archivo *script* deberá tener permisos de ejecución.
