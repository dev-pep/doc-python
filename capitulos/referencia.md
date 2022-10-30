# Referencia de Python

El presente documento es un resumen de los aspectos más relevantes de la referencia oficial de *Python* 3.10.

## 2. LEXICAL ANALYSIS

(Análisis léxico.)

El archivo de código fuente se traduce a una secuencia de *tokens*. La conversión la hace el analizador sintáctico, que pasa luego los *tokens* al *parser*. Veamos cómo.

### 2.1 Line structure

(Estructura de líneas.)

Un programa *Python* está formado por **líneas lógicas**.

#### 2.1.1 Logical lines

(Líneas lógicas.)

Una línea lógica termina con el *token NEWLINE*. Corresponde a una o más líneas físicas, unidas mediante unión implícita (dentro de paréntesis, llaves o corchetes) o explícita (***\\*** al final) de líneas.

#### 2.1.2 Physical lines

(Líneas físicas.)

Secuencias de caracteres terminadas con una secuencia de fin de línea (en *Unix LF*, en *Windows CR-LF*, en antiguos *Mac CR*; cualquiera de ellas vale en cualquier plataforma). En los literales *string*, ***\\n*** representa fin de línea.

#### 2.1.3 Comments

(Comentarios.)

Empieza por una almohadilla ***#*** que no forme parte de un literal *string*; el comentario termina al final de la línea física. No puede estar en medio de una línea lógica, con lo que su presencia marca el fin de una línea lógica. Los comentarios se ignoran (no generan *tokens*).

#### 2.1.4 Encoding declarations

(Declaraciones de codificación.)

La codificación del archivo (de texto) fuente por defecto es *UTF-8*. Si el archivo está codificado de forma distinta, se debe especificar la codificación en un comentario (***#***) en la primera línea. Dicha especificación podría estar en la segunda línea, siempre y cuando la primera fuese también un comentario (por ejemplo, la línea *shebang*). La especificación de codificación debe coincidir con la expresión regular `coding[=:]\s*([-\w.]+)`.

Ejemplo (reconocido por editor *emacs*):

```
# -*- coding: utf-16 -*-
```

Otro ejemplo (reconocido por editor *Vim*):

```
# vim:fileencoding=latin-1
```

La codificación concreta indicada tiene que ser reconocida por *Python*.

#### 2.1.5 Explicit line joining

(Unión explícita de líneas.)

La unión explícita de líneas se consigue con una línea física terminada con *backslash* (***\\***, que no forma parte de un literal *string* o comentario), seguido del carácter de fin de línea. Dicha línea se junta a la siguiente línea física (desaparece el backslash y el *newline*) formando una sola línea lógica. No se puede usar para partir *tokens* (excepto literales *string*). Tampoco se puede usar en un línea con comentario.

#### 2.1.6 Implicit line joining

(Unión implícita de líneas.)

Las expresiones entre **paréntesis, corchetes o llaves** pueden escribirse en varias líneas físicas, siempre que no rompamos *tokens*. Las físicas así partidas sí pueden tener su comentario. Se aceptan líneas en blanco, y la indentación de las líneas (excepto la de la línea inicial) es irrelevante (aunque *PEP 8* da una serie de recomendaciones al respecto). Los *triple-quoted strings* también se pueden romper en líneas implícitamente, pero no pueden llevar comentarios.

#### 2.1.7 Blank lines

(Líneas en blanco.)

Una línea en blanco (espacios y/o tabuladores seguidos de un *newline*) no generan un nuevo *token NEWLINE*, sino que son ignoradas.

#### 2.1.8 Indentation

(Indentación.)

La indentación (espacios y tabuladores) marca el nivel de indentación de una **línea lógica**: si hay varias líneas físicas, es la primera de ellas la que marca el nivel de indentación. La indentación no se puede partir en líneas físicas: el nivel lo marca el número de espacios antes del primer carácter no blanco (incluido un posible *backslash* de la unión explícita de líneas). Se van generando *tokens* *INDENT* (aumento de indentación) y *DEDENT* (disminución de indentación) para marcar los cambios de indentación.

#### 2.1.9 Whitespace between tokens

(Espacios entre *tokens*.)

El espacio blanco (espacios y tabuladores) no tiene significado en sí, excepto para separar *tokens* (a excepción del espacio de indentación al principio de las líneas lógicas, y dentro de literales *string*).

### 2.2 Other tokens

(Otros *tokens*.)

A parte de *NEWLINE*, *INDENT* y *DEDENT*, existen varias categorías de *tokens*: identificadores (*identifiers*), palabras clave (*keywords*), literales (*literals*), operadores (*operators*), y delimitadores (*delimiters*).

### 2.3 Identifiers and keywords

(Identificadores y palabras clave.)

Los nombres de identificadores dentro de los caracteres *ASCII*, son como en *C* ('\_', 'a'-'z', 'A'-'Z', y, a excepción del primer carácter del identificador, '0'-'9'). Si añadimos caracteres fuera del estándar *ASCII*, hay otras reglas: véase *PEP 3131*.

La longitud de los identificadores es ilimitada. Los nombres de identificadores son sensibles a las mayúsculas.

#### 2.3.1 Keywords

(Palabras clave.)

Las palabras clave de *Python* son:

`False`, `None`, `True`, `and`, `as`, `assert`, `async`, `await`, `break`, `class`, `continue`, `def`, `del`, `elif`, `else`, `except`, `finally`, `for`, `from`, `global`, `if`, `import`, `in`, `is`, `lambda`, `nonlocal`, `not`, `or`, `pass`, `raise`, `return`, `try`, `while`, `with` y `yield`.

Se trata de palabras reservadas que no pueden usarse como identificadores.

#### 2.3.2 Soft Keywords

(Palabras clave débiles.)

Algunos identificadores solo están reservados dentro de ciertos contextos. Es el caso de `match`, `case` y `_`, que solo están reservados dentro de un bloque `match`.

#### 2.3.3 Reserved classes of identifiers

(Clases reservadas de identificadores.)

***\_\**** no se importan con `from <modulo> import *`.

***\_*** es un comodín (*wildcard*) dentro de un bloque `match`. Fuera de él, no tiene ningún significado especial.

***\_\_\*\_\_*** son nombres definidos por el sistema; no se debería usar este tipo de nombre.

***\_\_\**** son reemplazados por la forma *mangled* dentro de la definición de una clase.

### 2.4 Literals

(Literales.)

Los literales son una notación específica para denotar un valor de un tipo *builtin* concreto.

#### 2.4.1 String and Bytes literals

(Literales *string* y *bytes*.)

La codificación del *source character set* (caracteres del archivo fuente) está especificado en la codificación del archivo fuente (2.1.4). Por defecto es *UTF-8*.

Los *strings* y *bytes* puede ir entre comillas dobles o simples. Pero entonces no pueden incluir ese tipo de comilla (si no es *escaped*, precedida del caràcter ***\\***). También pueden ir delimitados por grupos de 3 comillas simples o dobles, en cuyo caso, los saltos de línea presentes en el literal serán tomados como tal.

El *backslash* (***\\***) se usa como carácter *escape*. Si queremos un *backslash* en sí, hay que incluirlo *escaped* (***\\\\***).

Un literal *bytes* tiene como prefijo ***b*** o ***B***. Solo admite caracteres *ASCII*. Para valores superiores a 127, deben ir *escaped*. El elemento básico es el *byte*, mientras que en los *strings* es el carácter.

Tanto *bytes* como *strings* pueden tener prefijo ***r*** o ***R*** (*raw string* o *raw bytes*), en cuyo caso el *backslash* se toma literalmente.

El prefijo ***u*** o ***U*** se sigue manteniendo en *strings*, pero su única utilidad es para compatibilidad con *Python* 2 (se ignora).

El prefijo ***f*** o ***F*** es para literales con formato (*formatted string literals*, se verán más adelante). Solo para *strings* (no *bytes*).

El orden de dos prefijos, en caso de coincidir, es indiferente. En el caso de *bytes*, pueden coincidir ***r*** y ***b***. En *strings*, pueden coincidir ***r*** y ***f***.

Secuencias de escape (*escape sequences*): ***\\*** + salto de línea (se ignoran ambos), ***\\\\***, ***\\'***, ***\\"***, ***\\a*** (*bell*), ***\\b*** (*backspace*, retroceso), ***\\f*** (*FF*), ***\\n*** (*LF*), ***\\r*** (*CR*), ***\\t*** (tabulador), ***\\v*** (tabulador vertical), ***\\⁠ooo*** (hasta 3 caracteres octales), ***\\xhh*** (2 caracteres hexadecimales).

En los literales *string*, las secuencias *escape* octal y hexadecimal denotan un carácter *Unicode*; en un literal *bytes*, el valor del *byte*.

En *strings* (no en *bytes*) se acepta: ***\\N{nombre}*** (carácter con ese nombre en la tabla *Unicode*), ***\\uhhhh*** (hexadecimal, 16 bits, se pueden poner *surrogate pairs*), ***\\Uhhhhhhhh*** (hexadecimal, 32 bits, cualquier carácter *Unicode* se puede especificar así).

Las *escape sequences* no reconocidas se dejan tal cual. Un *raw string* no puede terminar en un número impar de *backslashes*, aunque igualmente no los procesa. Si una línea de un *raw string* termina en ***\\*** + salto de línea, se mantendrá tal cual en el *string*.

#### 2.4.2 String literal concatenation

(Concatenación de literales *string*.)

Varios literales *string* o *bytes* consecutivos separados únicamente por espacio en blanco (incluyendo comentarios) son concatenados automáticamente, si están en la misma línea lógica (pueden estar en distintas líneas físicas). Esto es así aunque sean de distinto tipo (*raws*, *formatted*, *triple-quoted*, etc.).

Esta concatenación se realiza en tiempo de compilación; para concatenar *strings* en tiempo de ejecución (evaluación de expresiones), se utiliza el operador de concatenación ***+***.

#### 2.4.3 Formatted string literals

(Literales *string* con formato.)

Estos literales contienen campos (expresiones entre llaves) que se reemplazan en tiempo de ejecución. Si queremos incluir un signo llave en sí, se debe escribir doblado (***{{*** o ***}}***).

Tras la expresión, dentro de las llaves, se puede añadir un campo de conversión, tras un signo de exclamación (***!***), o un campo de formato, tras dos puntos (***:***). Con ***!s*** el resultado es pasado a `str()`, con ***!r*** a `repr()` y con ***!a*** a `ascii()`. Finalmente el valor obtenido es pasado al método `__format__()` del objeto resultante, junto con el especificador de formato (tras los dos puntos); si no se especifica, se pasa un *string* vacío como especificador de formato.

En cuanto a los valores incluidos en el especificador de formato, pueden ser a su vez campos entre llaves que serán remplazados en tiempo de ejecución. Estos campos anidados pueden tener sus propios especificadores de formato, que ya no pueden ser campos, sino valores concretos.

En la biblioteca se hablará del mini-lenguaje de especificadores de formato.

No se pueden incluir expresiones vacías. Una expresión lambda o una expresión asignación (`:=`) se deben encerrar entre paréntesis.

Cada una de las expresiones se evalúa de izquierda a derecha.

Los *f-strings* se pueden concatenar con cualquier tipo de *string*, pero los campos no se pueden partir entre dos literales *string* a concatenar.

Se debe tener cuidado con que las expresiones no contengan posibles comillas que entren en conflicto con las comillas delimitadoras del *string*.

Las expresiones de los campos no pueden contener *backslashes*. Si necesitamos un valor que tenga, por ejemplo, un carácter *escaped*, se deberá crear una variable temporal con ese valor y se usará en lugar del literal con el *backslash* directamente.

Los *docstrings* pueden ser de cualquier tipo de *string*, excepto *f-string*.

#### 2.4.4 Numeric literals

(Literales numéricos.)

Tipos de literal numérico: entero, punto flotante y número imaginario.

Los literales numéricos no incluyen signo: ***-5*** es el operador `-` y el literal entero ***5***.

#### 2.4.5 Integer literals

(Literales enteros.)

Los literales entero no tienen límite de tamaño. Existen cuatro tipos, según su base:

- Decimal: secuencia de números de ***0*** a ***9***. No puede empezar por ***0***, aunque el número cero puede representarse mediante uno o más ceros.
- Binario: ***0b*** (o ***0B***) seguido de uno o más dígitos binarios (***0*** y ***1***).
- Octal: ***0o*** (o ***0O***) seguido de uno o más dígitos octales (***0*** a ***7***).
- Hexadecimal: ***0x*** (o ***0X***) seguido de uno o más dígitos hexadecimales (***0*** a ***F*** o ***f***).

Para aumentar la legibilidad podemos insertar guiones bajos en los literales, entre los dígitos, y/o entre el prefijo de base y el número en sí (***0x\_00ff\_0050***). Para la evaluación del valor son ignorados.

#### 2.4.6 Floating point literals

(Literales de punto flotante.)

Se trata de un número que incluye punto decimal. En este caso, se puede omitir la parte entera o la fraccionaria, pero no ambas. Opcionalmente puede seguir una parte de exponente: ***e*** o ***E*** seguido por signo opcional y los dígitos (sin punto decimal) del mismo.

En el caso de incluir la parte del exponente, el número puede omitir el punto decimal.

Tanto la parte numérica como el exponente son decimales, y pueden empezar por ***0*** si se desea.

Se pueden incluir guiones bajos entre los dígitos, tanto en la parte entera, como en la fraccionaria, así como en el número del exponente.

#### 2.4.7 Imaginary literals

(Literales imaginarios.)

Se trata de un literal *float* o entero seguido por el sufijo ***j*** o ***J***. Un número complejo se almacena como un *float* más un imaginario: ***5+3j***.

### 2.5 Operators

(Operadores.)

`+`, `-`, `*`, `**`, `/`, `//`, `%`, `@`, `<<`, `>>`, `&`, `|`, `^`, `~`, `:=`, `<`, `>`, `<=`, `>=`, `==`, `!=`.

## 3. DATA MODEL

(Modelo de datos.)

### 3.1 Objects, values and types

(Objetos, valores y tipos.)

Los datos de *Python* se almacenan como objetos. Todo objeto tiene una *identidad* única que nunca cambia, y se puede obtener mediante `id()`. En la implementación *CPython* (la implementación de *Python* por defecto), `id()` es la dirección de memoria del objeto. El operador `is` compara la identidad de dos objetos).

Todo objeto tiene también un *tipo*, que tampoco cambia. Puede obtenerse el tipo de un objeto mediente `type()`. El tipo de un objeto marca qué valores puede tener y qué operaciones acepta. El tipo de un objeto está descrito, de hecho, mediante un objeto, de tipo ***type***.

Un objeto tiene un tipo y un **valor**. Este último puede cambiar solo si el objeto es **mutable**, aunque un contenedor inmutable puede contener objetos mutables, por ejemplo. La mutabilidad depende del tipo del objeto.

Existe *garbage collection*, de objetos no referenciados. Los recursos "externos" que tiene un objeto son liberados cuando es *collected*, pero como la *garbage collection* no está garantizada, es mejor liberar recursos explícitamente (normalmente mediante métodos llamados `close()`) si deseamos tener control sobre la liberación de los mismos. Un buen mecanismo para asegurar que no quedan recursos abiertos es mediante `try ... finally`, o con `with`.

Cuando hablamos del valor de un contenedor (cualquier objeto que tenga referencias a otros objetos), nos referimos a los valores, no las identidades de sus elementos. Cuando hablamos de la mutabilidad del contenedor, entonces sí nos referimos a las identidades de sus elementos. Si un contenedor inmutable, como una tupla, contiene elementos mutables, al cambiar el valor de alguno de esos elementos, cambia el valor de esa tupla.

Si a dos identificadores les asignamos un valor inmutable, estos pueden tener o no tener el mismo `id()`; pero si lo hacemos con valores mutables, los `id()` serán siempre distintos: si hacemos `a=1` y `b=1`, entonces `a is b` puede ser ***True*** o ***False*** (depende de la implementación), pero si hacemos `a = [1, 2, 3]` y `b = [1, 2, 3]`, entonces `a is b` es siempre ***False*** (sin embargo, si hacemos `a = b = [1, 2, 3]`, tendremos que `a is b` es ***True***).

### 3.2 The standard type hierarchy

(La jerarquía estándar de tipos.)

A continuación se describen los tipos *built in* dentro de *Python*.

- ***None*** - este tipo solo tiene un valor posible: ***None***. Indica "sin valor". Lo retornan, por ejemplo, las funciones que no retornan un valor explícitamente. Su valor booleano es falso.
- ***NotImplemented*** - al igual que pasaba con ***None***, este tipo solo acepta un valor (***NotImplemented***). Algunos métodos pueden retornarlo si la operación no está definida para los operandos facilitados, en cuyo caso el intérprete intentará ejecutar la operación reflejada. No se debe evaluar su valor booleano.
- ***Ellipsis*** - también acepta un solo valor: ***Ellipsis***, también escrito como `...`; su valor booleano es verdadero.

**Números**: son inmutables. Su valor booleano es falso cuando el número tiene valor 0 y verdadero en cualquier otro caso.

- ***int*** - enteros, positivos y negativos. No tienen límite de tamaño.
- ***bool*** - ***True*** o ***False***. Al convertirse a ***int***, ***float*** o ***complex*** obtienen el valor 0 o 1, 0.0 o 1.0, o 0j o 1+0j respectivamente. Al convertir a ***string***, su valor es ***False*** o ***True***.
- ***float*** - números de punto flotante de doble precisión.
- ***complex*** - almacenado como un par de ***float***. Atributos ***real*** e ***imag***.

**Secuencias**: conjuntos finitos indexados por enteros positivos y cero (el primer elemento). La función `len()` retorna el número de elementos que contiene la secuencia. Se accede por un índice entre corchetes (`a[i]`). Un *slice*, del tipo `a[i:j]` es una lista del mismo tipo que la original, desde el elemento con índice ***i*** (incluido) hasta el ***j*** (no incluido). Un *slice* crea una copia de la secuencia original, que a su vez se puede indexar (empezando por 0). Algunas secuencias admiten un *slice* extendido: `a[i:j:k]`, siendo ***k*** la magnitud de los incrementos del índice.

Las secuencias pueden ser mutables o inmutables.

- **Secuencias inmutables**: contienen siempre los mismos objetos (aunque estos objetos sean mutables).
    - ***Strings***: secuencias de caracteres *Unicode* (***U+0000*** a ***U+10FFFF***). No existe el tipo carácter: cualquier *string* no es más que una sucesión de *strings* de longitud 1. La función `ord()` retorna el índice *Unicode* (número entero) de un elemento de un *string*. La función `chr()` hace la operación inversa: retorna un *string* de longitud 1 a partir de un número de índice *Unicode*. Por otro lado, el método `str.encode()` pasa de *string* a *bytes*, y `bytes.decode()` hace la operación inversa.
    - **Tuplas**: expresiones separadas por comas; una sola expresión seguida de coma (tupla de un solo elemento o *singleton*), o tupla vacía, expresada como ***()***.
    - ***Bytes***: *array* de *bytes* (8 bits). Se crean con literales *byte*, o con el constructor `bytes()`. Decodificables con método `decode()`.
- **Secuencias mutables**: mediante índice o *slicing* se puede asignar valor a los elementos, o borrarlos (con `del`).
    - **Listas**: 0 o más expresiones separadas por comas, entre corchetes.
    - ***Byte arrays***: creados con el constructor `bytearray()`. Se comportan exactamente igual que un elemento `bytes`, pero son mutables.

***Sets***: **conjuntos** no ordenados (no indexables) de objetos inmutables y únicos. La forma que tiene *Python* para localizar los objetos del conjunto es mediante *hashes*, con lo que estos deben ser *hashables*. Este tipo de acceso se realiza a través del valor, no de la identidad. Es por ello que solo puede contener elementos inmutables, ya que los elementos mutables no son *hashables*. Un *set* se puede iterar; acepta `len()` y operadores de conjuntos matemáticos.

- ***Sets***: mutables. Constructor `set()`.
- ***Frozen sets***: set inmutable, constructor `frozenset()`.

***Mappings***: conjunto de objetos indexados por un conjunto de claves, que actúan como índice. El acceso a los elementos del *mapping* se realiza mediante ***a[k]***, donde ***k*** es una clave, y se puede usar para acceder o asignar valor a los elementos, o borrarlos (con ***del***). Acepta `len()`. Solo hay un tipo de *mapping*:

- **Diccionarios**: son *mappings* mutables. Las claves deben ser inmutables, ya que el acceso se hace a través de *hash*. Los elementos del diccionario conservan siempre el orden de inserción de los elementos. La notación se realiza mediante llaves (***{}***).

Tipos ***Callable*** (invocables): se pueden llamar o invocar, mediante la sintaxis de llamada a función.

- **Funciones definidas por el usuario**. Poseen algunos atributos especiales: ***\_\_doc\_\_*** (*docstring*), ***\_\_name\_\_*** (nombre de la función), ***\_\_module\_\_*** (módulo donde está definida), ***\_\_defaults\_\_*** (tupla con los valores por defecto de los parámetros), ***\_\_code\_\_*** (objeto código con el cuerpo de la función compilado), ***\_\_globals\_\_*** (diccionario con las variables globales de la función, es decir, los atributos del módulo donde fue definida; es de solo lectura), ***\_\_dict\_\_*** (diccionario donde se ven los atributos arbitrarios que hemos definido sobre el objeto función), ***\_\_annotations\_\_*** (diccionario con las anotaciones de los parámetros; las claves son los nombres de parámetro, y ***return*** si hay anotación para el valor de retorno), ***\_\_kwdefaults\_\_*** (diccionario con los valores por defecto de los argumentos *keyword-only*). En el caso de que una variable de estas no tenga elementos, no será un diccionario vacío sino ***None***. Solo se pueden añadir atributos arbitrarios (del tipo `foo.pepe = 5`) a las funciones definidas por el usuario.
- **Métodos** de una instancia: atributos especiales: ***\_\_self\_\_*** (instancia asociada), ***\_\_func\_\_*** (objeto función), ***\_\_doc\_\_*** (*docstring* de la función), ***\_\_name\_\_*** (nombre del método), ***\_\_module\_\_*** (módulo donde está definido). Los métodos pueden acceder (pero no modificar) a los atributos arbitrarios de la función a la que están vinculados.
- **Generadores** (*generator functions*), vistas en el tutorial.
- **Corrutinas** (*coroutine functions*). Se verán más adelante.
- **Generadores asíncronos** (*asynchronous generator functions*): es como un generador pero definido con `async def`. Retorna un **iterador asíncrono** que puede utilizarse en una sentencia `async for`. El método `__anext__()` de dicho iterador retorna un *awaitable* (véase más adelante) que al ser esperado se ejecutará hasta que proporcione un valor mediante `yield`. Tras la última iteración levantará una excepción ***StopAsyncIteration***.
- Funciones *built-in*: tienen los atributos ***\_\_doc\_\_***, ***\_\_name\_\_***, ***\_\_self\_\_*** (es ***None***) y ***\_\_module\_\_***.
- **Métodos** ***built-in***: como una función *built-in*, pero además tienen un objeto pasado implícitamente a la función. En este caso, el atributo ***\_\_self\_\_*** (solo lectura) retorna ese objeto.
- **Clases**: son invocables. Normalmente crean instancias de sí mismas, a no ser que definan el método `__new__()`. Inicialización es típicamente con `__init__()`.
- **Instancias de una clase**: las podemos hacer invocables si definen el método `__call__()`.

**Módulos**: el atributo ***\_\_dict\_\_*** (solo lectura) es la definición de los elementos del *namespace* del módulo, donde están las referencias a sus atributos; las referencias a un atributo de un módulo ***m***, del tipo `m.x` equivalen a `m.__dict__['x']`. El atributo ***\_\_globals\_\_*** de las funciones definidas en el módulo es una referencia a este atributo del módulo.

Otros atributos disponibles en un módulo son ***\_\_name\_\_*** (nombre), ***\_\_doc\_\_*** (*docstring* del módulo) o ***\_\_file\_\_*** (ruta completa del archivo desde el que se leyó el módulo). ***\_\_annotations\_\_*** es un diccionario con las anotaciones de variables del módulo que se han ido produciendo en la ejecución del mismo.

**Clases definidas por el usuario**: el atributo ***\_\_dict\_\_*** contiene un diccionario con otros atributos de la clase; también tiene atributos como ***\_\_name\_\_***, ***\_\_module\_\_***, ***\_\_bases\_\_*** (tupla con las clases base, en orden jerárquico), ***\_\_doc\_\_*** o ***\_\_annotations\_\_***.

**Instancias**: tienen su propio ***\_\_dict\_\_***. Si un *lookup* de atributo no encuentra el nombre en este diccionario, lo busca en el diccionario de la clase; si no, sube por la jerarquía; si aun así no lo encuentra, y la clase tiene definido el método `__getattr__()`, lo ejecuta. Asignaciones y borrados de sus atributos modifican el diccionario de la instancia, no de la clase. Si la clase tiene definidos `__setattr__()` o `__delattr__()`, los llama para añadir o borrar atributos respectivamente, sin modificar el diccionario directamente. El atributo
***\_\_class\_\_*** es el objeto clase de la instancia.

**Objetos de entrada/salida** (objetos archivo): representan archivos. Destacar ***sys.stdin***, ***sys.stdout*** y ***sys.stderr*** (*streams* de entrada, salida y error estándar).

**Tipos internos**: utilizados internamente por el intérprete, como objetos de código, *frame*, *traceback* o *slice*.

Los **métodos estáticos** retornados por el constructor `staticmethod()` y los **métodos de clase** retornados por el construntor `classmethod()` se verán en el apartado de *builtins* de la biblioteca.

### 3.3 Special method names

(Nombres de métodos especiales.)

Existen ciertos nombres de método que indican cómo se llevan a cabo ciertas operaciones sobre objetos de la clase que los define. Cuando una clase define el valor de uno de estos nombres a ***None***, la operación que representa no puede realizarse sobre un objeto de esa clase.

#### 3.3.1 Basic customization

(Personalización básica.)

Métodos especiales que puede implementar una clase.

```python
__new__(clase [,...])
```

Es un método estático (aunque en este caso no hace falta declararlo como tal). Se llama a la hora de crear una nueva instancia de ***clase*** si queremos más control sobre esta creación. Los argumentos adicionales son los que pasamos al constructor de la clase para crear la instancia. Este método retorna normalmente la nueva instancia de tipo ***clase***.

Típicamente, `__new__()` llamará a `__new__()`de la clase base:

```python
super().__new__(clase [,...])
```

Obsérvese que hay que pasarle la clase como argumento. La clase actual la recibe automáticamente, pero al llamar manualmente al método de la clase padre, se debe especificar el tipo (clase) de la instancia a crear. Este método de la clase padre retornará el objeto creado, y la clase derivada lo recibirá, pudiéndolo modificar, a su vez, antes de retornarlo.

`__new__()` retorna la instancia creada. Si, efectivamente, retorna un objeto de tipo ***clase*** (el tipo de clase que ha recibido el `__new__()` primero), entonces el método `__init__()` de esa nueva instancia es llamado, pasándole como argumentos los mismos que se le pasaron a `__new__()`. Si, en cambio, no retorna una instancia de ***clase***, no se llama a `__init__()`.

Si `__new__()` no está definido, se crea la instancia de la forma predeterminada y se llama a `__init__()` (si está definido). Siempre habrá un `__new__()`, aunque sea heredado (como mínimo de ***object***).

```python
__init__(self [,...])
```

Inicializador de instancia. Los argumentos son los que se pasan al invocar a la clase para crear la instancia. Es llamado después de la creación de la instancia (mediante `__new__()`), y antes de retornar la instancia creada al llamador. Si lo definimos y la clase base también, hay que llamar también al de la clase base (`super().__init__()`), puesto que lo sobreescribe (a no ser que no queramos que se ejecute el inicializador de la base). No debe retornar ningún valor (***None***).

```python
__del__(self)
```

Destructor (mejor llamarle finalizador). Si la instancia tiene varias referencias (apuntadores) a ella y hacemos `del x`, se decrementa el contador de referencias; el destructor sólo se ejecuta cuando el contador llega a 0. Si la definimos y la clase base también, deberíamos llamarla para asegurar el borrado de la parte base. Debido a lo precario de las circunstancias en que se llama al destructor, las excepciones que ocurren durante su ejecución son ignoradas; solo se imprime un *warning* en ***sys.stderr***.

```python
__repr__(self)
```

Se usa su valor de retorno para la función `repr()`. Debe ser una representación "oficial" del objeto mediante un *string*. Debería parecer una **expresión válida** ***Python***, es decir, código *Python* que permita recrear el objeto utilizando dicha expresión; si no es posible, debería ser una descripción útil (se usa básicamente para depuración de código). Si `__str__()` no está definida y `__repr__()` sí, se usará el valor de esta última como valor de retorno para `str()` también.

```python
__str__(self)
```

Representación "informal" en un *string*, legible para un humano, del objeto en cuestión. Es usado por las funciones `format()`, `print()` y `str()`.

```python
__bytes__(self)
```

Representación del objeto en un *string* de `bytes`. Es el valor retornado por la función `bytes()`.

```python
__format__(self, format_spec)
```

Retorna la representación del objeto en un *string*, según el formato dado en ***format_spec***. Es llamado cuando el objeto aparece en la función `format()` o en el método `format()` de un *string*.

```python
__lt__(self, other)
```

Compara el objeto (***self***) con otro objeto (***other***). Cuando hacemos `x < y`, se llamará a `x.__lt__(y)`. En teoría, debería retornar ***True*** si ***self*** es menor que ***other***, y ***False*** en caso contrario, pero de hecho puede retornar cualquier tipo de valor. En caso de que *Python* necesite un booleano y la función retorne otro tipo, convertirá ese valor a booleano mediante `bool()`. Si la operación no está implementada para esos tipos de operando, puede retornar ***NotImplemented***.

```python
__gt__(self, other)
__le__(self, other)
__ge__(self, other)
__eq__(self, other)
__ne__(self, other)
```

Estos métodos son similares a `__lt__()`, pero para los operadores `>`, `<=`, `>=`, `==` y `!=` respectivamente.

El funcionamiento por defecto de `__eq__()` es retornar ***True*** si ***is*** retorna ***True***. De lo contrario retorna ***NotImplemented***. Por otro lado, `__ne__()` retorna ***False*** si ***is*** retorna ***True***, y ***NotImplemented*** en caso contrario.

```python
__hash__(self)
```

Retorna el *hash* del objeto; este valor debe ser un entero. El valor se usa para incluir el objeto en colecciones *hashed*. Estas colecciones no están ordenadas; se accede a sus elementos a través del número *hash* calculado en esta función (que depende del valor del elemento), y no mediante un índice numérico secuencial. Es decir, el número utilizado para acceder al objeto dependerá de su número *hash*, el cual depende a su vez del valor del objeto. Por lo tanto, su número de localización depende de su valor.

En este tipo de colecciones podemos encontrar los diccionarios y *sets*. La función *bultin* `hash()`, por otro lado, trunca el valor retornado por el método `__hash__()` (típicamente a 8 *bytes* en sistemas de 64 *bits* y 4 *bytes* en sistemas de 32 *bits*), y retorna dicho valor.

Dos objetos que se comparan iguales deben tener el mismo *hash*.

Es buena idea tomar todos los componentes que compongan el valor del objeto, relevantes para la comparación de objetos, y empaquetarlos en una tupla para retornar el *hash* de esa tupla como *hash* del objeto:

```python
def __hash__(self):
    return hash((self.name, self.nick, self.color))
```

Si una clase no define `__eq__()`, no debería definir tampoco `__hash__()`.

Si una clase no define `__hash__()`, no es *hashable*, con lo que sus instancias no se pueden usar como elementos de colecciones *hashables*.

Si una clase contiene elementos mutables, no debe definir `__hash__()`: los elementos *hashables* deben ser inmutables para que no varíe nunca su número *hash*, de lo contrario su llave de localización cambiaría al cambiar su valor, lo cual produciría problemas en la memoria.

Las clases definidas por el usuario tienen métodos `__hash__()` y `__eq__()` por defecto: todos los objetos se comparan distintos excepto con ellos mismos, es decir, `x == y` es verdadero si y solo si `x is y` lo es, y `hash(x) == hash(y)`.

Una clase que sobreescribe (*overrides*) `__eq__()` y no define `__hash__()`, tendrá su `__hash__()` ímplicitamente a ***None*** y levantará un ***TypeError*** si se pasa una instancia como argumento a la función `hash()`; además será identificada como no *hashable*. Si una clase redefine `__eq__()` pero quiere retener la funcionalidad *hash* de una clase base, debe indicarlo explícitamente mediante `__hash__ = BaseClass.__hash__` para que no pierda dicha funcionalidad.

Una clase cuyo `__hash__()` no sea igual a ***None*** es identificada como *hashable*, aunque levante explícitamente un ***TypeError***. Si se quiere que una clase sea no *hashable*, se debe definir en ella `__hash__ = None`.

Los objetos *string* y *bytes* tienen valores de *hash* aleatorios, aunque se mantienen constantes a lo largo de la ejecución del proceso.

```python
__bool__(self)
```

Retorna el valor booleano del objeto. Es el valor que retornará la función *builtin* `bool()` sobre el objeto. Debe retornar ***False*** o ***True***. Si no está definido, se llama a `__len__()` que por defecto retornará ***True*** si contiene más de 0 elementos y ***False*** en caso contrario. Si tampoco está definido este método, retornará siempre ***True***.

#### 3.3.2 Customizing attribute access

(Personalización del acceso a los atributos.)

```python
__getattr__(self, nombre)
```

Se llama cuando no se ha encontrado el atributo ***nombre*** por vías normales (ni en la instancia ni en la jerarquía de clases). Debe retornar el valor del atributo especificado en el *string* ***nombre***, o indicar que tampoco se ha encontrado ese atributo, levantando ***AttributeError***.

```python
__getattribute__(self, nombre)
```

Todos los accesos a los atributos (métodos y datos) se hacen llamando a este método, obligatoriamente (toda clase lo tiene, porque lo hereda de ***object***, como mínimo). Si `__getattr__()` también está definido (puede no estarlo, porque ***object*** no lo tiene), no será llamado a no ser que `__getattribute__()` lo llame explícitamente o genere una excepción ***AttributeError***.

Si necesitamos hacer referencia a un atributo desde esta función, normalmente será a un atributo existente de la clase o instancia; si lo hacemos directamente (***self.x***) se genera un bucle recursivo infinito (el acceso volverá a llamar a `__getattribute__()` recursivamente). Para evitarlo, se debe obtener el valor del atributo accediendo a él a través del método `__gettatribute__()` de una clase base (o desde cualquier otro lado fuera de la clase), pasándole la instancia actual como parámetro, que lo único que hará será retornar el valor de ese atributo existente:

```python
Base.__getattribute__(self, nombre)
```

Si no existen clases base, se puede hacer desde la clase ***object***:

```python
object.__getattribute__(self, nombre)
```

Recordemos que aunque el atributo no esté definido en la clase base, el valor será bien retornado, porque lo extrae de ***self***, que se le pasa también por parámetro: estamos llamando a través de la clase, es decir, directamente a la función (*unbound*), no como método.

Importante: `__getattr__()` sufre el mismo problema cuando intentamos acceder a un atributo inexistente, con la diferencia de que al no disponer ***object*** de este método, deberemos definir esa función (pasándole una referencia a la instancia y el nombre del atributo deseado) en algún lado (en la clase base de nuestra clase, por ejemplo).

```python
`__setattr__(self, nombre, valor)`
```

Para establecer o cambiar valores. El comportamiento por defecto es cambiar el diccionario de la instancia. En el caso de que se desee asignar un valor a un atributo, para evitar recursión infinita debe hacerse a través del `__setattr__()` de la clase base (u ***object***, o cualquier otro lado).

```python
__delattr__(self, nombre)
```

Como `__setattr__()` pero para eliminar el atributo.

```python
__dir__(self)
```

Retorna una secuencia, normalmente con el diccionario de nombres de la instancia. Es lo que retorna la función *builtin* `dir()`. Esta función convierte la secuencia en lista y la ordena.

#### Customizing module attribute access

(Personalización del acceso a los atributos de un módulo.)

Se puede definir la función `__getattr__()` dentro de un módulo, que se ejecuta cuando no se encuentra un atributo de dicho módulo de la forma habitual. La función acepta un *string* con el nombre del atributo, y debe retornar su valor, o levantar la excepción ***AttributeError***.

También se puede definir `__dir__()` de un módulo, que sustituye (*overrides*) completamente el comportamiento de `dir()` sobre el módulo. Si implementamos la función, no acepta argumentos, y debe retornar una lista de *strings* con los nombres de la tabla de nombres del módulo.

#### Implementing Descriptors

(Implementación de descriptores.)

Un descriptor es una instancia de una clase que define por lo menos uno de los métodos siguientes: `__get__()`, `__set__()` o `__delete__()`. Un descriptor se usa dentro de otra clase propietaria (*owner*) como atributo. Para que una clase *owner* tenga un descriptor, debe definir un atributo de tipo descriptor o debe definirlo una de sus clases base. En resumen, la clase propietaria debe contener una instancia del descriptor.

```python
__get__(self, instancia, owner)
```

***self*** es el objeto (instancia) descriptor, ***instancia*** es la instancia de la clase propietaria, y ***owner*** es la clase propietaria. Si ***instance*** es ***None***, es que estamos accediendo al atributo a través de la clase (***owner***) y no a través de una instancia de la misma. El método retorna el valor del atributo solicitado, es decir, el valor de la instancia del descriptor.

Si p.e. ***Descrip*** es una clase que implementa `__get__()`, y diseñamos otra clase ***Own*** que tiene un *data attribute* ***a*** de tipo ***Descrip***, entonces ***Own.a*** invocará a dicha función.

```python
__set__(self, instancia, valor)
```

Cambia el valor del atributo (la instancia del descriptor) al que se refiere el descriptor.

```python
__delete__(self, instancia)
```

Se invoca cuando eliminamos el atributo (instancia del descriptor) de la instancia de la clase propietaria.

#### Invoking Descriptors

(Invocar descriptores.)

Normalmente, el acceso a un atributo de una instancia se hace con una consulta (*lookup*) al diccionario de dicha instancia. Si ***a*** es una instancia de la clase ***A***, con un atributo ***x***, entonces `a.x` empezará buscando en `a.__dict__['x']`; si no lo encuentra, seguirá por `type(a).__dict__['x']` (la clase), y seguirá por las clases base. Pero si el atributo es un descriptor, entonces se accede directamente al objeto descriptor:

- `x.__get__(a)` - invocación directa.
-  `a.x` - *instance binding*; se transforma automáticamente en `type(a).__dict__['x'].__get__(a, type(a))`; se hace desde `type(a)` porque este atributo está en la tabla de nombres de la clase, no de la instancia.
- `A.x` - *class binding*; pasa a `A.__dict__['x'].__get__(None, A)`.

Si se accede al atributo `a.x` y el descriptor no define `__get__()`, lo que retornará es la instancia del objeto descriptor, a no ser que exista un atributo con ese nombre en el diccionario de la instancia.

Del mismo modo, si el atributo descriptor está definido en la instancia y no en la clase, no funciona: al hacer `a.x` retorna el objeto descriptor tal cual.

Si un descriptor define `__set__()` y/o `__delete__()`, es un *data descriptor*, mientras que si no, es un *non-data descriptor*.

Los *data descriptors* suelen definir `__get__()` y `__set__()`, mientras que los *non-data descriptors* solo suelen definir `__get__()`. Los *non-data descriptors* pueden ser redefinidos (*overriden*) por la instancia, mientras que los *data descriptors* no.

#### \_\_slots\_\_

(Ranuras.)

> Los lenguajes con *garbage collection* suelen implementar el concepto de referencias débiles (*weak references*). Una variable que referencie a un objeto es una referencia fuerte (*strong reference*). Mientras exista por lo menos una *strong reference*, el objeto sigue vivo. Cuando no quedan *strong references*, el objeto puede ser recogido por el *garbage collector*, pero mientras esto no suceda, el objeto sigue accesible (presente en memoria) a través de posibles *weak references* (si las hay). Se pueden crear y gestionar *weak references* a objetos mediante el módulo ***weakref***. Cuando finalmente el objeto es recogido (*collected*), las *weak references* son eliminadas. Las *strong references* de un objeto se guardan en el diccionario ***\_\_dict\_\_***, y las *weak references* se encuentran en otro diccionario llamado ***\_\_weakref\_\_***. Tanto uno como otro son diccionarios que enlazan **cada nombre** al **objeto** que referencian.

Una instancia cualquiera tienen su propio diccionario donde almacena sus atributos (***\_\_dict\_\_***). Si tenemos **muchas** instancias con pocos atributos, se consume muchísima memoria de forma ineficiente, por la existencia de tantos diccionarios. En cambio si la clase define la variable (atributo) ***\_\_slots\_\_***, este puede contener una secuencia (cualquier iterable) de *strings* con los nombres de las variables (atributos) que podrán tener las instancias de esa clase, y en esas instancias se reservará internamente el mínimo espacio necesario para almacenar sus valores de forma extremadamente eficiente, en lugar de utilizar los diccionarios ***\_\_dict\_\_*** o ***\_\_weakref\_\_***, que simplemente no serán creados.

***\_\_slots\_\_*** nos dice qué atributos se pueden añadir a la instancia. Solo podremos añadirle esos atributos, pero no es obligatorio definirlos todos. Sin embargo, a la clase en sí se le pueden añadir todos los atributos que queramos, aunque la clase tampoco tiene diccionarios ***\_\_dict\_\_*** ni ***\_\_weakref\_\_***.

No se pueden añadir, pues, atributos no listados en *slots* a una instancia; si necesitamos hacerlo, deberemos añadir ***\_\_dict\_\_*** a la lista de *slots*.

No se pueden crear *weak references* a un objeto con *slots*. Si se necesita crearlas, hay que añadir manualmente ***\_\_weakref\_\_*** a la lista de *slots*.

Si una subclase hereda de una clase sin *slots*, sí tendrá diccionarios ***\_\_dict\_\_*** y ***\_\_weakref\_\_***, aunque en estos diccionarios no se incluirán los atributos definidos en ***\_\_slots\_\_***.

En la definición de *slots*, los nombres definidos allí crean descriptores, con lo que la clase no puede definir a su vez atributos con esos nombres, puesto que esas definiciones sobrescribirían los descriptores creados por el efecto de ***\_\_slots\_\_***.

Si una subclase hereda de una clase con *slots*, tendrá diccionario igualmente, a no ser que también defina ***\_\_slots\_\_***; en ese caso, esa nueva *slots* deberá definir solo los elementos *adicionales*, porque ya hereda los de la clase base (si repite nombres, los de la base son inalcanzables).

En herencia múltiple, como mucho una de las clases base puede definir *slots*.

Si se utiliza un iterador para definir *slots*, se crearán los descriptores correspondientes, pero la variable ***\_\_slots\_\_*** será un iterador vacío.

#### 3.3.3 Customizing class creation

(Personalización de la creación de clases.)

Se trata de una técnica compleja, que no se aplica a la mayoría de casos de uso, por lo que no se verá en profundidad.

A nivel informativo, si un **objeto instancia** se crea según se especifica en el **objeto clase** (tipo), ¿en qué se basa la creación de un objeto clase? La respuesta es en un **objeto metaclase**.

Por defecto, la metaclase utilizada para la creación de clases definidas por el usuario es ***type***. Sin embargo, es posible crear metaclases personalizadas.

#### 3.3.4 Customizing instance and subclass checks

(Personalización de las comprobaciones de instancia y subclase.)

Es posible sobreescribir (*override*) el comportamiento de `isinstance()` y `issubclass()` en una clase determinada. Para ello habrá que redefinir los siguientes métodos de la misma:

```python
__instancecheck__(self, objeto)
```

El método debe retornar ***True*** si el objeto ***object*** debe ser considerado instancia de la clase que define el método. ***False*** en caso contrario.

```python
__subclasscheck__(self, clase)
```

Debe retornar ***True*** si el objeto ***clase*** debe ser considerado subclase de la clase que define el método. ***False*** en caso contrario.

> Es importante recalcar que estos métodos **no se definen en la clase en sí, sino en la metaclase** de la clase que deseamos chequear. De este modo, ***self*** no se refiere a una instancia, sino a la clase en sí.

#### 3.3.6 Emulating callable objects

(Emular objetos invocables.)

Para que se pueda llamar a la instancia, como si fuera una función:

```python
__call__(self [,argumentos])
```

Si utilizamos la llamada sobre una instancia ***x***:

```python
x(a1, a2,...)
```

Será equivalente a:

```python
x.__call__(a1, a2,...)
```

#### 3.3.7 Emulating container types

(Emular tipos de contenedor.)

Los contenedores pueden ser secuencias (como listas o tuplas) o *mappings* (como los diccionarios), aunque puede haber otros tipos. La diferencia entre secuencias y diccionarios es que en las primeras, las claves (*keys*) son índices enteros de 0 a N-1 (N elementos) u objetos *slice*.

Los *mappings* deberían definir los métodos `keys()`, `values()`, `items()`, `get()`, `clear()`, `setdefault()`, `pop()`, `popitem()`, `copy()` y `update()`, y comportarse de forma análoga a como lo hacen en los diccionarios de *Python*.

Las **secuencias mutables** deberían definir los métodos `append()`, `count()`, `index()`, `extend()`, `insert()`, `pop()`, `remove()`, `reverse()` y `sort()`, y comportarse de forma análoga a como lo hacen en las listas de *Python*.

**Todas las secuencias** deberían definir la adición (concatenación) y multiplicación (repetición) mediante los métodos `__add__()`, `__radd__()`, `__iadd__()`, `__mul__()`, `__rmul__()` y `__imul__()`, y no definir otros operadores aritméticos.

Todos los contenedores, en general, deberían implementar `__contains__()` para *tests* de pertenencia (`in`, que en el caso de diccionarios, debería buscar en las claves), e `__iter__()` para iteración de sus elementos (en *mappings* debería iterar por todas las claves).

```python
__len__(self)
```

Se utiliza para la función *builtin* `len()`. Retorna un entero no negativo; normalmente, el número de elementos del contenedor. Un objeto cuyo `len()` es 0 y no define `__bool__()`, se considera ***False***.

```python
__length_hint__(self)
```

Estimación del tamaño esperado; se usa para preasignar el tamaño de un contenedor, de tal modo que se optimiza el funcionamiento interno, aunque nunca es obligatorio. Puede retornar ***NotImplemented***, que equivale a no definir este método.

El *slicing* se lleva a cabo utilizando únicamente los métodos `__getitem__()`, `__setitem__()` y `__delitem__()`, que veremos a continuación. Un *slice*, del tipo `a[1:2] = b` se transforma automáticamente en `a[slice(1, 2, None)] = b`. Los argumentos que faltan en la llamada a `slice()` se rellenan siempre con ***None***.

```python
__getitem__(self, key)
```

Para evaluar `self[key]`. Para secuencias, las claves son enteros o *slices*. La interpretación de los índices negativos depende totalmente de esta función. Si ***key*** es de un tipo inapropiado, se puede levantar ***TypeError***. En el caso de *mappings*, si ***key*** no existe, se acostumbra a levantar ***KeyError***. Para índices fuera de rango, se levanta ***IndexError*** (los bucles `for` lo usan para detectar el final).

```python
__setitem__(self, key, value)
```

Todo lo dicho para índices en `__getitem__()` se aplica aquí, pero para escribir en lugar de leer. Debe ser posible ese cambio de valores, así como añadir valores nuevos. En cuanto a las excepciones a levantar, son las mismas que en el caso de `__getitem__()`.

```python
__delitem__(self, key)
```

Para eliminar un elemento `self[key]`. Se aplica lo dicho en `__getitem__()`. Debe ser posible la eliminación. Mismas excepciones que `__getitem__()`.

```python
__missing__(self, key)
```

Para tratar claves inexistentes en el contenedor. Es llamado por `__getitem__()` para implementar `self[key]` cuando la clave no está en el diccionario.

```python
__iter__(self)
```

Debe retornar un iterador que itere sobre los elementos del contenedor. En *mappings*, debe iterar por las claves. Los iteradores también implementan este método para retornarse a sí mismos.

```python
__reversed__(self)
```

Retorna también un iterador, pero que itera en el orden inverso. Es lo que retorna la *built-in function* `reversed()` sobre el objeto. Si `__reversed__()` no está definido, `reversed()` lo implementa usando `__len__()` y `__getitem__()`, por lo que sólo deberíamos definir este método si va a ser más eficiente que esta implementación por defecto.

```python
__contains__(self,item)
```

Para comprobaciones de pertenencia (*membership tests*) mediante `in` y `not in`. Retorna ***True*** o ***False***. En *mappings* debería centrarse en las *keys*. Si no está definido, se hace automáticamente, iterando por los elementos, por lo que sólo deberíamos definir este método si va a ser más eficiente que no definirlo.

#### 3.3.8 Emulating numeric types

(Emular tipos numéricos.)

Es posible definir las operaciones matemáticas que se pueden realizar con los objetos.

Métodos:

`__add__(self, otro)`, `__sub__(self, otro)`, `__mul__(self, otro)`, `__matmul__(self, otro)`, `__truediv__(self, otro)`, `__floordiv__(self, otro)`, `__mod__(self, otro)`, `__divmod__(self, otro)`, `__pow__(self, otro [,modulo])`, `__lshift__(self, otro)`, `__rshift__(self, otro)`, `__and__(self, otro)`, `__xor__(self, otro)` y `__or__(self, otro)`.

Corresponden respectivamente a los operadores:

`+`, `-`, `*`, `@`, `/`, `//`, `%`, `divmod()`, `pow()` (y `**`), `<<`, `>>`, `&`, `^` y `|`.

`A ** B` equivale a `pow(A, B)`. Y `pow(A, B, C)` debería equivaler a `A ** B % C`. Aunque pueden definirse como se desee.

Se debería definir `pow()` para 3 argumentos. `divmod(A, B)` debería retornar una tupla (`A // B`, `A % B`). `@` es multiplicación de matrices.

Si la función no implementa la operación con el argumento que se le ha pasado, debería retornar ***NotImplemented***.

Si ***x*** es una instancia de una clase que tiene `__add__()` definido, entonces `x + y` llamaría a `x.__add__(y)`.

Cuando el operando de la izquierda no soporta la operación, es decir, cuando ese método no está definido o retorna ***NotImplemented***, hay dos opciones. Si el operando de la derecha es del mismo tipo, hemos terminado: la operación no está definida. Pero si es de un tipo distinto, podemos mirar si esa clase define la denominada **operación reflejada**, es decir, si define la operación con los operandos intercambiados. Estos métodos especiales del operando de la derecha son:

`__radd__(self, otro)`,`__rsub__(self, otro)`, `__rmul__(self, otro)`, `__rmatmul__(self, otro)`, `__rtruediv__(self, otro)`, `__rfloordiv__(self, otro)`, `__rmod__(self, otro)`, `__rdivmod__(self, otro)`, `__rpow__(self, otro)`, `__rlshift__(self, otro)`, `__rrshift__(self, otro)`, `__rand__(self, otro)`, `__rxor__(self, otro)` y `__ror__(self, otro)`.

Estos métodos se corresponden a los anteriores, solo en caso de operaciones reflejadas.

En este caso concreto, `pow()` **con 3 argumentos** no intentará llamar a `__rpow__()` (muy complejo implementar la operación reflejada).

En el caso específico de que el segundo operando sea de una subclase del tipo del primer operando, la versión reflejada del segundo se llamará prioritariamente sobre la versión normal del primero. Ello se hace para que las subclases puedan redefinir las operaciones de sus bases.

Si los operandos son de la misma clase y la operación normal no está definida, no se llamará a la reflejada, en caso de existir, ya que se asume que la operación no está definida.

Asignaciones aumentadas (*augmented assignments*):

`__iadd__(self, otro)`, `__isub__(self, otro)`, `__imul__(self, otro)`, `__imatmul__(self, otro)`, `__itruediv__(self, otro)`, `__ifloordiv__(self, otro)`, `__imod__(self, otro)`, `__ipow__(self, otro, [modulo])`, `__ilshift__(self, otro)`, `__irshift__(self, otro)`, `__iand__(self, otro)`, `__ixor__(self, otro)` y `__ior__(self, otro)`.

Estos métodos corresponden respectivamente a `+=`, `-=`, `*=`, `@=`, `/=`, `//=`, `%=`, `**=`, `<<=`, `>>=`, `&=`, `^=` y `|=`. Deberían modificar ***self*** directamente y luego retornar el resultado (que por lo general será ***self***, aunque no tiene por qué). Al encontrar uno de estos operadores, si no está definido el método correspondiente, *Python* lo intentará con el operador correspondiente a la asignación aumentada. Si tampoco, intentará con el reflejado.

`__neg__(self)`, `__pos__(self)`, `__abs__(self)` y `__invert__(self)` responden respectivamente a los operadores unarios `-`, `+`, `abs()` y `~`.

`__complex__(self)`, `__int__(self)`, `__float__(self)` y `__round__(self [,n])` responden respectivamente a las *built-in functions* `complex()`, `int()`, `float()` y `round()`. Deben retornar un valor del tipo adecuado.

`__round__(self [,digits])`, `__trunc__(self)`, `__floor__(self)` y `__ceil__(self)` responden a la *bult-in function* `round()`, y a las funciones del módulo ***math*** `trunc()`, `floor()` y `ceil()`. Todas ellas deberían retornar un número entero, a excepción de `round()` en caso de especificar un número de dígitos decimales superior a cero. Si `__int__()` no está definida, `int()` usará el método `__trunc__()`.

Truncar es eliminar la parte decimal, con lo que equivale a *floor* en positivos, y a *ceil* en negativos.

#### 3.3.9 With Statement Context Managers

(Gestores de contexto con sentencia `with`.)

Usados con la sentencia `with`, algunos objetos realizan algunas acciones al entrar en esa sentencia, y otras al salir. Normalmente se usa para inicializar recursos y luego liberarlos, guardar y restaurar un estado global, etc. Son dos métodos que se invocan al ejecutarse la sentencia `with`, pero también pueden ser llamados directamente. Por ejemplo, para objetos de tipo archivo:

```python
with open('/tmp/workfile', 'r') as f:
    # hacer cosas con el objeto f
```

Cuando termina la sentencia `with`, cierra el archivo automáticamente, con lo que no hay que hacer `f.close()`.

```python
__enter__(self)
```

Lo que ejecutará la sentencia `with` al principio, si la usamos con nuestra clase. Lo que retorne este método será asignado a la variable después de `as`.

```python
__exit__(self, exc_type, exc_val, traceback)
```

Es lo que se ejecutará al terminar el código del bloque `with`. Los parámetros extra reciben la causa de salida de `with`. Si no se produjo excepción, `__exit__()` será llamada con tres ***None***. Si se produjo excepción durante la ejecución del bloque `with`, se llamará con tres valores correspondientes a la excepción, su valor y el objeto de tipo *traceback* asociado (que tiene la información de dónde se produjo el error). Si deseamos que no se propague la excepción después de la cláusula `with`, `__exit__()` debería retornar un valor verdadero (p.e. ***True***), de lo contrario la excepción se recogerá tras `__exit__()`.

### 3.3.10 Customizing positional arguments in class pattern matching

(Personalizar argumentos posicionales en coincidencia de patrones de clase.)

En las cláusulas de coincidencia de patrones (`case`), por defecto, no se pueden utilizar argumentos posicionales en una clase utilizada como patrón. Por ejemplo, `case MiClase(x, y)` no está permitido por defecto. Para poder usar este tipo de patrón, la clase necesita definir un atributo con nombre ***\_\_match\_args\_\_***.

Este patrón tendrá como valor una tupla de *strings*, que definirán, las claves que se añadirán a los argumentos posicionales, de tal modo que estos se convertirán en *keyword arguments*.

Por ejemplo, si ***MiClase.\_\_match\_args\_\_*** tiene por valor ***('izquierda', 'centro', 'derecha')***, entonces `case MiClase(x, y)` equivale a `case MiClase(izquierda=x, centro=y)`. Si no hay suficientes valores en ***MiClase.\_\_match\_args\_\_*** para todos los argumentos, se levantará ***TypeError***.

#### 3.3.11 Special method lookup

(Búsqueda de los métodos especiales.)

Los métodos especiales que hemos estado describiendo deben definirse en la clase, no en el diccionario de la instancia, ya que de lo contrario no se encontrarán al hacer el *lookup*.

### 3.4 Coroutines

(Corrutinas.)

Este mecanismo se utiliza para la creación de código asíncrono. En esta sección veremos un breve resumen del documento ***PEP 492***.

En primer lugar, vamos a definir una función **corrutina** como una función en la que en algún momento de su ejecución se detiene momentáneamente en espera de que se produzca un evento. Esta parada momentánea la denominaremos una **espera**. La función que contiene esta esperas, la corrutina, se define como:

```python
async def miFuncion(parametros):
    # sentencias
```

Estas funciones pueden contener la sentencia `await`, que es la sentencia que ejecuta la espera. Una función que no se declara como `async` no puede contener sentencias `await`. Por otro lado, del mismo modo que una invocación a un generador retorna un objeto generador, una invocación a una corrutina **retorna un objeto corrutina**.

```python
async def lectura_datos(bd):
    # ...
    datos = await bd.fetch('SELECT * FROM coches')
    # ...
```

En este caso, se realiza una lectura asíncrona a una base de datos mediante la sentencia `await`. La ejecución se suspende temporalmente hasta que se reciben los datos. La expresión `await` retorna los datos recibidos.

La sentencia `await` acepta como único argumento un **objeto esperable** (***awaitable***). Un *awaitable* es un objeto que implementa el método `__await__()`, el cual debe retornar un iterador.

En cuanto al **objeto corrutina** que retorna una llamada a una función corrutina, es un objeto *awaitable*.

### *Context managers* asíncronos

Un gestor de contexto asíncrono es un gestor de contexto que puede suspender su ejecución en sus métodos de entrada y salida. En este caso, estos tendrán los métodos `__aenter__()` y `__aexit__()`, equivalentes a los métodos `__enter__()` y `__exit__()` de los *context managers*, con la diferencia que estos nuevos métodos son corrutinas, y por lo tanto definidos mediante `def async`.

Para ejecutar el gestor de contexto, se utilizará `async with` en lugar de `with`. Hay que señalar que `async with` no acepta un *context manager* regular:

```python
async with obj as v:
    # bloque de código
```

Los *context managers* asíncronos solo pueden ejecutarse dentro de una *corrutina*.

### Iteradores asíncronos

Un iterable asíncrono es capaz de ejecutar código asíncrono dentro de su método *iter* (iteración), y un iterador asíncrono puede llamar código asíncrono en su método *next* (siguiente).

Así, el iterable debe implementar un método `__aiter__()`, el cual debe retornar un iterador asíncrono. Por lo tanto el método no tiene por que ser asíncrono, pero el iterador retornado sí debe serlo. Que el iterador retornado sea asíncrono significa que contendrá un método (`async`) `__anext__()`. Dicho método `__anext__()` debe retornar un *awaitable*, y para señalar el fin de la iteración deberá levantar una excepción del tipo ***StopAsyncIteration***.

Esto sería un ejemplo de iterable cuyo método `__aiter__()` se retorna a sí mismo (como es habitual):

```python
class AsyncIterable:
    def __aiter__(self):
        return self

    async def __anext__(self):
        data = await self.fetch_data()
        if data:
            return data
        else:
            raise StopAsyncIteration

    async def fetch_data(self):
        # ...
```

Como puede verse, el método `__aiter__()` retorna una instancia de sí mismo. Esta instancia es un iterador, pues define el método asíncrono `__anext__()`. Dicho método realiza una serie de acciones, básicamente obtener datos de una fuente específica a la que se accede de forma asíncrona a través del método concreto ***fetch_data()***, que es una corrutina. A cada iteración se retornará un dato. Cuando ***fetch_data()*** no retorne dato alguno (o un valor falso), el iterador levantará ***StopAsyncIteration***.

Para ejecutar este iterable asíncrono, utilizaremos `async for` en lugar del habitual `for`. Hay que señalar que `async for` no acepta un iterable regular.

```python
async for v in iter:
    # bloque de código
```

La sentencia `async for` admite (al igual que `for`) una sentencia `else` opcional. Por otro lado solo puede usarse `async for` dentro de una función asíncrona.

## 4. EXECUTION MODEL

(Modelo de ejecución.)

### 4.2 Naming and binding

(Nombres y vínculos.)

#### 4.2.1 Binding of names

(Vinculación de nombres.)

Los nombres están vinculados (*bound*) a objetos. Si un nombre se vincula dentro de un bloque de código (módulo, función, clase, etc.), es una variable local de ese bloque, a no ser que se declare como `nonlocal` o `global`. Si se vincula a nivel de módulo, es una variable global.

#### 4.2.2 Resolution of names

(Resolución de nombres.)

El ámbito (*scope*) define la visibilidad de un objeto dentro de un bloque. Para resolver nombres se usa el *nearest enclosing scope*.

Si un nombre referenciado no se encuentra, se levanta ***NameError***. Si está en un *scope* local y no se encuentra pero está definido más adelante en el *scope*, se levanta ***UnboundLocalError*** (subclase de ***NameError***).

## 6. EXPRESSIONS

(Expresiones.)

### 6.1 Arithmetic conversions

(Conversiones aritmética.)

El comportamiento normal de los operadores aritméticos con los tipos numéricos *built-in* es convertir los operandos implícitamente, antes de realizar la operación, y en caso de ser distintos, a un tipo común: si uno de los dos es `complex`, el otro es convertido a `complex`. Si no, si uno es de punto flotante, el otro se convierte a punto flotante. En caso contrario, es que son los dos enteros.

Puede haber reglas adicionales para algunos operadores.

### 6.2 Atoms

(Átomos.)

Los átomos son los elementos más básicos de una expresión: se trata de los identificadores y literales.

#### 6.2.3 Parenthesized forms

(Formas entre paréntesis.)

Los paréntesis se pueden utilizar para agrupar expresiones. Retorna el valor al que se evalúa su contenido. Si contiene comas, retorna una tupla; si no, el valor de la expresión que engloba. Así, no son los paréntesis los que definen una tupla, sino las comas (a excepción de un par de paréntesis vacíos, que retornan una tupla vacía).

Son necesarios cuando deseamos controlar la precedencia de los operadores en una expresión compleja.

#### 6.2.4 Displays for lists, sets and dictionaries

(*Displays* para listas, conjuntos y diccionarios.)

Los *displays* son las construcciones sintácticas que permiten construir listas, conjuntos o diccionarios. Existen dos formas: simplemente especificando los elementos, separados por comas, o mediante las *comprehensions*. Siempre entre corchetes (para listas) o llaves (para conjuntos y diccionarios).

En el caso concreto de una *comprehension*, la expresión inicial es evaluada en el *enclosing scope* (bloque de código que define la *comprehension*), y es pasada como argumento al *scope* anidado que forma la *comprehension*. El resto de expresiones (en los `if` y los `for`) pertenecen a ese *scope* anidado.

#### 6.2.5 List displays

(*Displays* de lista.)

Para crear una lista, se utiliza, entre corchetes, una lista de valores separados por comas, o una comprehension.

#### 6.2.6 Set displays

(*Displays* de conjunto.)

Para crear un conjunto, se utiliza, entre llaves, una lista de valores separados por comas, o una comprehension.

#### 6.2.7 Dictionary displays

(*Displays* de diccionario.)

Para crear un diccionario, se utiliza, entre llaves, una lista de pares ***clave:valor*** separados por comas, o una comprehension.

En el caso de una *comprehension*, la expresión inicial debe estar formada por un par de expresiones separadas por dos puntos (***:***).

Si uno de los elementos es del tipo ***\*\*m*** (*dictionary unpacking*), y ***m*** es un *mapping*, entonces se insertan también los elementos de ***m*** en el diccionario .

Al añadir elementos a un diccionario, la clave se evalúa antes que el valor.

Las claves deben ser de un tipo *hashable*.

#### 6.2.8 Generator expressions

(Expresiones generador.)

Este tipo de expresiones retornan un **generador**. El formato básico consiste simplemente en una *comprehension* entre paréntesis.

No está permitido el uso de *yield* explícitamente en las expresiones que definen los elementos del generador.

Si el generador contiene cláusulas `async for` o expresiones `await`, la expresión generador retornará un nuevo objeto generador asíncrono (concretamente un iterador asíncrono).

#### 6.2.9 Yield expressions

(Expresiones `yield`.)

Un generador retorna valores mediante `yield`, o `yield from <expr>`, donde ***expr*** actúa como un subiterador del que va tomando valores para realizar los `yield`.

El método `__next__()` de los iteradores y generadores es llamado implícitamente por una cláusula `for`, o directamente con la *built-in function* `next()`.

## 6.3 Primaries

(Primarias.)

Las sentencias primarias son la base sintáctica admitida por el lenguaje. A parte de los átomos, vistos ya, existen otros tipos de expresión primaria:

```python
instancia.atributo  # referencia a atributos
objeto[3]           # indexación
lista[4:8]          # slice
foo('hola', 3)      # llamada
```

### 6.10 Comparisons

(Comparaciones.)

En *Python* es posible realizar asignaciones múltiples, del tipo `a = b = 3`, las cuales se van evaluando (y asignando) de derecha a izquierda.

Además se pueden hacer comparaciones encadenadas, del tipo `a < b < c == d`, que se encadenan con `and`. El ejemplo equivaldría a `a < b and b < c and c == d`. Las expresiones no se evalúan dos veces, con lo que tanto ***b*** como ***c*** se evaluarían únicamente la primera vez. Además, se utiliza el mecanismo de cortocircuito, con lo que si una de las subexpresiones evalúa a falso, no se evaluarán más subexpresiones, retornando la expresión final el valor falso.

#### 6.10.1 Value comparisons

(Comparaciones de valor.)

Los operadores de comparación comparan valores, no tipos ni identidades, por lo que se pueden aplicar entre operandos de tipos distintos.

Sin embargo, los operadores `==` y `!=` se basan, por defecto, en la identidad (`id()`).

Los tipos numéricos pueden compararse entre sí. Aunque los complejos no tienen comparación de orden (`>`, `<`, `<=`, `>=`).

El valor ***NaN*** (*not a number*, es decir, no numérico) se evalúa como diferente comparándose con cualquier cosa, incluso consigo mismo. Los *singletons* ***None*** y ***NotImplemented*** deberían ser comparados solo mediante `is` o `is not`.

Las secuencias *bytes* (`bytes`, `bytearray`) y *string* (`str`) se pueden comparar con secuencias de su tipo, usándose el valor numérico de sus elementos (*bytes* o puntos de codificación *Unicode*). No puede compararse una secuencia de uno de estos tipos con otra del otro tipo.

Las *secuencias* (`tuple`, `list`, `range`) pueden compararse con objetos de su mismo tipo, aunque los *ranges* no tienen comparación de orden. Las secuencias se comparan comparando elemento por elemento. Si dos secuencias son iguales hasta el final de la más corta, la más corta es la menor.

Los *mappings* (`dict`) solo se comparan iguales si tienen los mismos pares ***clave:valor***. No pueden compararse por orden.

Los *sets* (`set`, `frozenset`) se pueden comparar con su mismo tipo. Las comparaciones de orden se interpretan mediante la relación subconjunto-superconjunto.

Las clases que implementan mecanismos de comparación personalizados deberían cumplir, si es posible, algunas reglas de consistencia:

- Objetos idénticos deben ser iguales, es decir, si `x is y`, entonces `x == y`.
- Comparaciones simétricas:
    - `x == y` implica `y == x`.
    - `x != y` implica `y != x`.
    - `x >= y` implica `y <= x`.
    - `x > y` implica `y < x`.
- Comparación transitiva:
    - Si `x > y` y `y > z`, entonces `x > z`.
    - Si `x >= y` y `y > z`, entonces `x > z`.
    - Si `x > y` y `y >= z`, entonces `x > z`.
    - Si `x >= y` y `y >= z`, entonces `x >= z`.
    - Si `x < y` y `y < z`, entonces `x < z`.
    - Etc.
- La comparación inversa equivale a la negación booleana:
    - `x == y` implica `not x != y`.
    - `x >= y` implica `not x > y`.
    - `x > y` implica `not x >= y`.
- El valor *hash* debe ser consistente con la igualdad, es decir, dos objetos que se evalúan iguales deben retornar el mismo *hash*, o bien no ser *hashables*.

#### 6.10.2 Membership test operations

(Operaciones de comprobación de pertenencia.)

Para comprobaciones del tipo `in` y `not in`. Los *strings*, *bytes*, secuencias, *mappings* y *sets built-in* implementan esta comprobación (en diccionarios se comprueba si existe tal clave).

#### 6.10.3 Identity comparisons

(Comparaciones de identidad.)

`x is y` es verdadero solo si ***x*** e ***y*** son referencias al mismo objeto; `x is not y` es verdadero en caso contrario.

### 6.13 Conditional expressions

(Expresiones condicionales.)

La expresión condicional, también llamada operador ternario (*ternary operator*) es del tipo:

```python
expr1 if cond else expr2
```

Retorna ***expr1*** si ***cond*** es verdadero, o ***expr2*** en caso contrario. En este caso se evalúa primero ***cond***; si es verdadero se evalúa ***expr1***, de lo contrario ***expr2***.

### 6.16 Evaluation order

(Orden de evaluación.)

*Python* evalúa las expresiones de izquierda a derecha. En el ejemplo, el número del sufijo indica el orden:

```python
expr1, expr2, expr3, expr4
(expr1, expr2, expr3, expr4)
{expr1: expr2, expr3: expr4}
expr1 + expr2 * (expr3 - expr4)
expr1(expr2, expr3, *expr4, **expr5)
expr3, expr4 = expr1, expr2
```

### 6.17 Operator precedence

(Precedencia de operadores.)

Prioridad de los operadores en *Python*, de menor a mayor:

- `:=` (asignación)
- `lambda`
- `if` - `else`
- `or`
- `and`
- `not`
- `in`, `not in`, `is`, `is not`, `<`, `<=`, `>`, `>=`, `!=`, `==`
- `|`
- `^`
- `&`
- `<<`, `>>`
- `+`, `-`
- `*`, `@`, `/`, `//`, `%`
- `+x`, `-x`, `~x`
- `**`
- `await x`
- `x[index]`, `x[index:index]`, `x(argumentos...)`, `x.atributo`
- `(expresiones...)`, `[expresiones...]`, `{key: value...}`, `{expresiones...}`

En cuanto al operador de exponente, su prioridad es distinta en operadores unarios de uno y otro lado:

```python
>>> n = -1 ** 2    # -(1 ** 2)
>>> n
-1
>>> n = 2 ** -1    # 2 ** (-1)
>>> n
0.5
```

## 7. SIMPLE STATEMENTS

(Sentencias simples.)

### 7.1 Expression statements

(Sentencias expresión.)

Una expresión es cualquier sentencia que se evalúe a (retorne) un valor.

### 7.2 Assignment statements

(Sentencias de asignación.)

Las sentencias de asignación se utilizan para enlazar un nombre a un objeto.

### 7.3 The assert statement

(La sentencia `assert`.)

La sentencia:

```python
assert expr
```

Equivale a:

```python
if __debug__:
    if not expr:
        raise AssertionError
```

En cuanto a la versión extendida:

```python
assert expr1, expr2
```

Equivale a:

```python
if __debug__:
    if not expr1:
        raise AssertionError(expr2)
```

***\_\_debug\_\_*** es ***True*** en circunstancias normales. Será ***False*** cuando indiquemos que la compilación se haga con optimización (opción ***-O*** en línea de comandos).

### 7.4 The pass statement

(La sentencia `pass`)

Operación nula. Utilizada en bloques vacíos (clases, bucles, funciones, etc.), normalmente para indicar una futura implementación.

### 7.5 The del statement

(La sentencia `del`.)

```python
del lista
```

Elimina de la tabla de nombres local o global el nombre (o nombres) especificados en ***lista***. La tabla elegida dependerá de cómo esté declarado el nombre en cuestión (`global`, `nonlocal` o nada). Si el nombre no existe, se levantará la excepción ***NameError***.

El argumento ***lista*** es un nombre, o una lista de nombres separados por comas, que son eliminados recursivamente, de izquierda a derecha.

### 7.6 The return statement

(La sentencia `return`.)

```python
return lista
```

Retorna la expresión o lista de expresiones separadas por comas ***lista***. Si esta no se especifica, se asume ***None***. Si una función no termina su ejecución en una sentencia `return`, retornará ***None***.

Si `return` abandona una cláusula `try` que contiene `finally`, antes de retornar se ejecuta el código de bloque `finally`.

En un iterador, `return` indica que este ha terminado las iteraciones y levanta la excepción ***StopIteration***. Si retorna valor, este quedará en ***StopIteration.value***.

### 7.7 The yield statement

(La sentencia `yield`.)

La sentencia `yield` se utiliza en generadores, como se ha visto ya:

```python
yield expr
```

o:

```python
yield from expr
```

En el último caso, la expresión ***expr*** es a su vez un iterador, de donde el generador irá extrayendo los sucesivos valores para irlos retornando al modo de `yield`.

### 7.8 The raise statement

(La sentencia `raise`.)

La sentencia `raise` se utiliza para levantar excepciones.

```python
raise
```

Sin argumento vuelve a levantar la última excepción que estaba activa en el *scope* actual. Si no había ninguna, se levanta ***RuntimeError***.

```python
raise excep
```

Levanta la excepción ***excep***, que debe ser una subclase o una instancia de ***BaseException***. Si es una clase, se construirá internamente la instancia cuando sea necesario, a partir de esa clase. Para indicar argumentos al constructor:

```python
raise MiExcepcion(argumentos)
```

Al levantarse una excepción se genera un objeto *traceback* (sucesión de llamadas hasta el punto del código donde se produce dicha excepción), que se añade a la instancia de la excepción levantada, en su atributo ***\_\_traceback\_\_***. Este atributo es modificable, a través del método `with_traceback()`:

```python
raise MiExcep('Texto de error').with_traceback(objeto_traceback)
```

En este caso, hemos levantado una excepción del tipo ***MiExcep***, pasándole un texto del error al constructor, y aportando nuestro propio objeto *traceback*. Lo podemos hacer así porque el método `with_traceback()` retorna el propio objeto (instancia) excepción.

```python
raise excep1 from excep2
```

En este caso, estamos indicando que levantamos la excepción ***excep1***, que se ha producido a causa de otra excepción ***excep2***. Si la excepción (***excep1***, que es la que debemos tratar) queda sin tratar, se imprimirá en el mensaje de error una referencia a las dos excepciones, y que una de ellas es causa de la otra. Al levantarse así, internamente se adjuntará ***excep2*** (que también puede ser instancia o clase) en el atributo ***\_\_cause\_\_*** de la excepción levantada (***excep1***).

De forma similar, si durante el tratamiento de una excepción se produce otra, que no queda tratada, en el mensaje de error se informará de este hecho. Internamente se hace añadiendo a la nueva excepción una referencia a la que estábamos tratando, en el atributo ***\_\_context\_\_***.

### 7.9 The break statement

(La sentencia `break`.)

Sale del bucle `for` o `while` más exterior posible (desde el *scope* de la sentencia `break`), saltándose el posible `else` que pueda tener el bucle.

Si la sentencia implica salir de una cláusula `try` que tiene una sección `finally`, el código de dicha sección se ejecutará antes de salir del bucle.

### 7.10 The continue statement

(La sentencia `continue`.)

Produce una nueva iteración del bucle `for` o `while` más exterior posible (desde el *scope* de la sentencia `break`).

Si la sentencia implica salir de una cláusula `try` que tiene una sección `finally`, el código de esta sección se ejecutará antes de iniciar la nueva iteración.

### 7.11 The import statement

(La sentencia `import`.)

Mecanismo para importar módulos.

```python
import A as C, B
```

equivale a:

```python
import A as C
import B
```

### 7.12 The global statement

(La sentencia `global`.)

Permite declarar uno o más nombres (separados por comas) para que sean vinculados a la tabla de nombres global, dentro de un bloque de código.

Los nombres indicados no deben utilizarse en el bloque de código antes de la sentencia `global`.

### 7.13 The nonlocal statement

(La sentencia `nonlocal`.)

Dentro de un bloque de código, `nonlocal` permite declarar uno o más nombres (separados por comas) para que sean vinculados a la tabla de nombres del *scope* envolvente más cercano posible en el que ya existan.

Si no se declara una variable como `nonlocal`, los accesos de escritura a esa variable se harán en la tabla local. Si solo se accede a una variable (en un *scope* exterior) para lectura, sin que se modifique en ningún momento en el *scope* local, no es necesario declararla como no local.

## 8. COMPOUND STATEMENTS

(Sentencias compuestas.)

Varias sentencias simples se pueden agrupar en la misma línea separándolas por punto y coma (***;***), y opcionalmente terminando esa línea en punto y coma.

Una sentencia compuesta está formada por una o varias cláusulas. Una cláusula está formada por un **encabezado** y una *suite*, o **bloque** de código formado por una sentencia o conjunto de sentencias simples o compuestas.

El encabezado de una cláusula termina en dos puntos (***:***).

Una *suite* es una o más sentencias agrupadas en una de estas dos formas:

- En la misma línea del encabezado, tras los dos puntos. Si hay más de una sentencia, habrá que separarlas por punto y coma (***;***). Como siempre, opcionalmente se puede terminar la línea en punto y coma.
- Debajo del encabezado, una debajo de otra con una indentación superior al encabezado (podemos también agrupar varias sentencias simples líneas individuales del bloque).

No pueden incluirse varias sentencias compuestas en una sola línea.

Sentencias compuestas: `if`, `while`, `for`, `try`, `with`, `match`, `def` y `class`.

La mayor parte se ha visto en el tutorial. Veremoa a continuación algunos conceptos adicionales relacionados con las dos últimas.

### 8.7 Function definitions

Ya hemos visto cómo se definen las funciones. Podríamos añadir que las funciones son anidables en *Python*, es decir, una función puede definirse dentro de otra, dentro de una clase, de un bloque de código, etc.

Pero vamos a centrarnos, sobre todo, en los **decoradores** de función (***function decorators***). Un *decorator* es una función que acepta a su vez una función como argumento, y que retorna también una función. Es decir, ajusta o modifica la función que le pasamos como argumento. La sintaxis de los *decorators* es:

```python
@deco
def fun:
    pass
```

Esto equivale a:

```python
def fun:
    pass
fun = deco(fun)
```

¿Cómo se construye un *decorator*? En primer lugar, sabemos que es una función (***deco***) que acepta una función como entrada (***fun***), y retorna otra función (que llamaremos *wrapper* o envolvente), que es una modificación de la de entrada. Así, ***deco*** acepta ***fun***, la introduce dentro del *wrapper* y retorna ese *wrapper*, es decir, la función original modificada. La llamaremos envolvente porque en realidad envuelve ***fun***.

Vamos a hacer un *decorator* que duplica las acciones de una función, es decir, que la ejecuta dos veces:

```python
# Definición del decorator:
def deco_duplica(f):
    def wrapper():
        f()
        f()
    return wrapper

# Definición de una función cualquiera usando el decorator:
@deco_duplica
def fun():
    print('¡Buenos días!')

fun()
```

Resultado:

```
¡Buenos días!
¡Buenos días!
```

¿Y si queremos decorar una función ***fun*** que acepta argumentos? Para que no se produzca error y el *decorator* acepte todo tipo de listas argumentos, haremos:

```python
def deco_duplica(f):
    def wrapper(*args, **kwargs):
        f(*args, **kwargs)
        f(*args, **kwargs)
    return wrapper

@deco_duplica
def fun(nombre):
    print(f'¡Buenos días, {nombre}!')

fun('Pepe')
```

Resultado:

```
¡Buenos días, Pepe!
¡Buenos días, Pepe!
```

Esto es así, porque si nos fijamos, ***deco_duplica*** retorna el objeto función ***wrapper***, y este objeto función se asigna a ***fun***. Es decir, ***fun*** pasa a ser la función ***wrapper***. Por lo tanto, al hacer la llamada `fun('Pepe')` estamos, de hecho, invocando al objeto función `wrapper('Pepe')`.

¿Qué pasa al encadenar *decorators*?

```python
@deco1
@deco2
def foo: pass
```

Esto equivale a `foo = deco1(deco2(foo))`.

A parte de aceptar argumentos en la función que va a ser decorada, en ocasiones podemos querer darle argumentos a nuestro *decorator* en sí. Imaginemos que queremos pasarle el número de veces que queremos repetir la función original. Para definir un *decorator* con argumentos se hace así:

```python
def deco_duplica_n(n):
    def deco_duplica(f):
        def wrapper(*args, **kwargs):
            for i in range(n):
                f(*args, **kwargs)
        return wrapper
    return deco_duplica

@deco_duplica_n(4)
def fun(nombre):
    print(f'¡Buenos días, {nombre}!')

fun('Pepe')
```

Resultado:

```
¡Buenos días, Pepe!
¡Buenos días, Pepe!
¡Buenos días, Pepe!
¡Buenos días, Pepe!
```

Vamos a estudiarlo: `deco_duplica_n(4)`, de hecho, nos está retornando esta función:

```python
def deco_duplica(f):
    def wrapper(*args, **kwargs):
        for i in range(4):
            f(*args, **kwargs)
    return wrapper
```

Que, de hecho, un simple *decorator* que, en este caso, cuatriplica la acción. Esta función retornada es la que se usa como decorador de la función a decorar, es decir:

```python
fun = deco_duplica_n(4)(fun)
```

Y dado que `deco_duplica_n(4)()` retorna una función ***deco_duplica*** que cuatriplica la acción (utiliza un ***n*** con valor ***4***), esto equivale a:

```python
fun = deco_duplica(fun)  # en su versión con n=4
```

Generalizando, tenemos que, suponiendo dos decoradores que acepten argumentos:

```python
@deco1(N)
@deco2(A,B)
def foo(...): pass
```

Esto equivale a:

```python
foo = deco1(N)(deco2(A, B)(foo))
```

Para finalizar, hay que tener en cuenta que si queremos que nuestra función decorada por el *decorator* siga retornando el mismo valor que antes de pasarla por el *decorator*, es decir, si queremos que la decoración no afecte al valor de retorno de la función, entonces la función *wrapper* debe terminar con:

```python
return f(*args,**kwargs)
```

En caso contrario, se puede modificar ese valor de retorno a voluntad.

Un *decorator* puede ser cualquier tipo de expresión válida, siempre y cuando tal expresión retorne una función de tipo *decorator*. Por ejemplo, si tenemos una lista ***lst***, en los que sus elementos tienen un atributo (de tipo función adecuado) ***decfoo***, se puede hacer:

```python
@lst[0].decfoo
def fooA():
    # ...

@lst[1].decfoo
def fooB():
    # ...
```

### 8.8 Class definitions

También se pueden definir *decorators* para los métodos de una clase, que funcionarán del mismo modo.

Pero además se pueden utilizar *decorators* de clases, que se utilizan en la clase entera. En este caso, la diferencia estriba en que el *decorator* acepta como argumento una clase, que retorna modificada (decorada). Por lo demás, el mecanismo es análogo a los *decorators* de funciones.

```python
@deco
class MiClase:
    pass
```

Equivale a:

```python
MiClase = deco(MiClase)
```

Un *decorator* de un método es, en la práctica, lo mismo que vimos para funciones. En cambio, un *decorator* de clase es una función que recibe como parámetro un objeto clase, y que retorna esa clase modificada.

Veamos un ejemplo: una clase simple que solo dice 'hola', y un *decorator* que añade un método para decir adiós. Además, el *decorator* admite un parámetro que indica cuántas veces se dirá '¡Adiós!' al despedirse:

```python
def complicaN(n):
    def complica(cls):
        cls_complic = cls

        def adios(self):
            print('¡Adiós!' * n)

        cls_complic.adios = adios
        return cls_complic
    return complica

@complicaN(4)
class Simple:
    def hola(self):
        print('¡Hola!')

a = Simple()
a.hola()
a.adios()
```

Resultado:

```
¡Hola!
¡Adiós!¡Adiós!¡Adiós!¡Adiós!
```
