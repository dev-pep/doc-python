Python

Referencia

Resumen

Actualizado: 5/12/2020

Pep Ribal

<pepribal@gmail.com>

*Licencia **Creative Commons Atribución**
*[*C*](https://creativecommons.org/licenses/by/4.0/)[*C-BY*](https://creativecommons.org/licenses/by/4.0/)[*
4.0*](https://creativecommons.org/licenses/by/4.0/)

El presente documento es un resumen de los aspectos más relevantes de la
referencia oficial de *Python* 3.9. Los títulos se han mantenido en
inglés, que es el idioma del documento que se ha utilizado como base
para este resumen.

[]{#anchor}2. LEXICAL ANALYSIS
==============================

El archivo de código se pasa a una lista de *tokens*. La conversión la
hace el analizador sintáctico, que pasa luego los *tokens* al *parser*.
Veamos cómo.

[]{#anchor-1}2.1 Line structure
-------------------------------

Un programa *Python* consta de líneas lógicas.

### []{#anchor-2}2.1.1 Logical lines

Una línea lógica termina con el *token* *NEWLINE*. Corresponde a 1 o +
líneas físicas.

### []{#anchor-3}2.1.2 Physical lines

Secuencias de caracteres terminadas con una secuencia de fin de línea
(en *Unix LF*, en *Windows CR-LF*, en antiguos *Mac CR*; cualquiera de
ellas vale en cualquier plataforma). En los literales *string*, **\\n**
representa fin de línea.

### []{#anchor-4}2.1.3 Comments

Empieza por un \'\#\' (que no forme parte de un literal *string*), y
termina al final de la línea física. No puede estar en medio de una
línea lógica, con lo que su presencia marca el fin de una línea lógica.
Los comentarios se ignoran (no generan *tokens*).

### []{#anchor-5}2.1.4 Encoding declarations

Codificación del archivo fuente por defecto es *UTF-8*. Si es otra,
especificar comentario (**\#**) en 1ª o 2ª línea, que contenga
coincidencia con la expresión regular:
***coding\[=:\]\\s\*(\[-\\w.\]+)***

Ejemplo, reconocido por *emacs*:

\# -\*- coding: utf-16 -\*-

Reconocido por *Vim*:

\# vim:fileencoding=latin-1

La codificación concreta indicada tiene que ser reconocida por *Python*.

### []{#anchor-6}2.1.5 Explicit line joining

Línea física terminada con *backslash* (***\\***), que no forma parte de
un literal *string* o comentario, seguido de *newline*, se junta a la
siguiente (desaparece el backslash y el *newline*). No se puede usar
para partir *tokens* (excepto literales *string*). No se puede usar en
un línea con comentario.

### []{#anchor-7}2.1.6 Implicit line joining

Las expresiones entre paréntesis, corchetes o llaves pueden escribirse
en varias líneas, siempre que no rompamos *tokens*. Las líneas físicas
así partidas sí pueden tener su comentario. Se aceptan líneas en blanco,
y la indentación de las líneas (excepto la primera) es irrelevante. Los
*triple-quoted* *strings* también se pueden romper en líneas
implícitamente, pero no pueden llevar comentarios.

### []{#anchor-8}2.1.7 Blank lines

Una línea en blanco (espacios, tabs seguidos de *newline*) en el código
fuente no generan un nuevo *token* *NEWLINE*. Se ignora.

### []{#anchor-9}2.1.8 Indentation

La indentación (espacios y tabs) marca el nivel de indentación de una
*línea lógica*: si hay varias líneas físicas, es la primera de ellas la
que marca el nivel de indentación. La indentación no se puede partir en
líneas físicas: el nivel lo marca el número de espacios antes del primer
carácter no blanco (incluido un posible *backslash* de *explicit line
joining*). Se van generando *tokens* *INDENT* y *DEDENT* para marcar los
cambios de indentación.

### []{#anchor-10}2.1.9 Whitespace between tokens

El espacio blanco (espacios, tabs) no tiene significado en sí, excepto
para separar tokens; a excepción del espacio de indentación al principio
de las líneas lógicas, y dentro de literales *string*.

[]{#anchor-11}2.2 Other tokens
------------------------------

A parte de *NEWLINE*, *INDENT* y *DEDENT*, existen varias categorías de
tokens: *identifiers*, *keywords*, *literals*, *operators*, and
*delimiters*.

[]{#anchor-12}2.3 Identifiers and keywords
------------------------------------------

Los nombres de identificadores dentro de los caracteres *ASCII*, son
como en *C* (\'\_\', \'a\'-\'z\', \'A\'-\'Z\', y, a excepción del primer
carácter, \'0\'-\'9\'). Si añadimos caracteres fuera del estándar
*ASCII*, hay otras reglas: ver *PEP 3131*.

La longitud de los identificadores es ilimitada. *Case sensitive*.

### []{#anchor-13}2.3.1 Keywords

**False**, **None**, **True**, **and**, **as**, **assert**, **async**,
**await**, **break**, **class**, **continue**, **def**, **del**,
**elif**, **else**, **except**, **finally**, **for**, **from**,
**global**, **if**, **import**, **in**, **is**, **lambda**,
**nonlocal**, **not**, **or**, **pass**, **raise**, **return**, **try**,
**while**, **with**, **yield**.

### []{#anchor-14}2.3.2 Reserved classes of identifiers

**\_\*** no se importan en **from \<modulo\> import \***.

**\_\_\*\_\_** son *system-defined* names; no usar este tipo de nombre.

**\_\_\*** son reemplazados por la forma *mangled* dentro de la
definición de una clase.

[]{#anchor-15}2.4 Literals
--------------------------

### []{#anchor-16}2.4.1 String and Bytes literals

La codificación del *source character set* (caracteres del archivo
fuente) es el especificado en la codificación del archivo fuente
(2.1.4).

Los *strings* y *bytes* puede ir entre comillas dobles o simples. Pero
entonces no pueden incluir ese tipo de comilla (si no es *escaped*).

También entre grupos de 3 comillas simples o dobles (se pueden incluir
*unescaped newlines* y ambos tipos de comillas).

El *backslash* se usa como carácter escape. Si queremos un *backslash*
en sí, hay que incluirlo *escaped* (**\\\\**).

Un literal *bytes* tiene como prefijo **b** o **B**. Solo admite
caracteres *ASCII*. Para valores superiores a 127, *escaped*.

Tanto *bytes* como *strings* pueden tener prefijo **r** o **R** (*raw
string*), en cuyo caso el *backslash* se toma literalmente.

El prefijo **u** o **U** se sigue manteniendo en *strings*, pero es
*legacy*, para compatibilidad con *Python* 2 (lo ignoraremos).

El prefijo **f** o **F** es para *formatted string literals* (lo veremos
más adelante). Solo para *strings* (no *bytes*).

El orden (y *case*) de dos prefijos, en caso de coincidir, es
indiferente. En el caso de *bytes*, pueden coincidir **r** y **b**. En
*strings*, pueden coincidir **r** y **f**.

*Escape* sequences: **\\newline** (se ignoran los dos caracteres),
**\\\\**, **\\\'**, **\\\"**, **\\a** (*bell*), **\\b** (*backspace*),
**\\f** (*FF*), **\\n** (*LF*), **\\r** (*CR*), **\\t** (*TAB*), **\\v**
(*TAB* vertical), **\\⁠ooo** (hasta 3 caracteres octales), **\\xhh** (2
caracteres hexa).

En los literales *string*, las secuencias *escape* octal y hexa denotan
un carácter Unicode; en un literal *bytes*, el valor del *byte*.

En *strings* (no en *bytes*) se acepta: **\\N{nombre}** (caracter con
ese nombre en la base de datos Unicode), **\\uhhhh** (hexa, 16 bits, se
pueden poner *surrogate pairs*), **\\Uhhhhhhhh** (hexa, 32 bits,
cualquier carácter Unicode se puede especificar así).

Las *escape sequences* no reconocidas, se dejan tal cual. Un *raw
string* no puede terminar en un número impar de backslashes (aunque
igualmente no los procesa). Si una línea de un *raw string* termina en
**\\newline**, se mantendrá tal cual en el *string*.

### []{#anchor-17}2.4.2 String literal concatenation

Varios literales *string* o *bytes* consecutivos separados únicamente
por *white space* (incluyendo comments) son concatenados
automáticamente, si están en la misma línea lógica (pueden estar en
distintas líneas físicas). Esto es así aunque sean de distinto tipo
(*raws*, *formatted*, *triple-quoted*, etc.).

Ello se realiza en *compile time*; para concatenar *strings* en *run
time* (evaluación de expresiones), utilizar el operador de concatenación
\'+\'.

### []{#anchor-18}2.4.3 Formatted string literals

Contienen campos (expresiones entre llaves) que se reemplazan en *run
time*. Si queremos incluir un signo llave en sí, se debe escribir
doblado (**{{** o **}}**).

Tras la expresión, dentro de las llaves, se puede añadir un campo de
conversión, tras un signo de exclamación (**!**), o un campo de formato,
tras dos puntos (**:**). Con **!s** el resultado es pasado a **str()**,
con **!r** a **repr()** y con **!a** a **ascii()**. Finalmente el valor
obtenido es pasado al método **\_\_format\_\_()** del objeto resultante,
junto con el especificador de formato (tras los dos puntos); si no se
especifica, se pasa un *string* vacío como especificador de formato.

En cuanto a los valores incluidos en el especificador de formato, pueden
ser a su vez campos entre llaves que serán remplazado en *run time*.
Estos *nested fields* pueden tener sus propios especificadores de
formato, que ya no pueden ser campos, sino valores concretos.

En la biblioteca se hablará del mini-lenguaje de especificadores de
formato.

No se pueden incluir expresiones vacías. Una expresión lambda o una
expresión asignación (**:=**) se deben encerrar entre paréntesis.

Cada una de las expresiones se evalúa de izquierda a derecha.

Los *f-strings* se pueden concatenar con cualquier tipo de *string*,
pero los campos no se pueden partir entre dos literales *string*.

Se debe tener cuidado que las expresiones no contengan posibles comillas
que entren en conflicto con las comillas delimitadoras del *string*.

Las expresiones de los campos no pueden contener *backslashes*. Si
necesitamos un valor que tenga, por ejemplo, un carácter *escaped*,
crear una variable temporal con ese valor y usarla en lugar del literal
con el *backslash* directamente.

Los *docstrings* pueden ser de cualquier tipo de *string*, excepto
*f-string*.

### []{#anchor-19}2.4.4 Numeric literals

Tipos de literal numérico: entero, punto flotante e imaginario.

Los literales numéricos no incluyen signo: **-5** es el operador \'-\' y
el literal entero \'5\'.

### []{#anchor-20}2.4.5 Integer literals

No hay límite de tamaño.

-   Decimal: secuencia de números de **0** a **9**. No puede empezar por
    **0**. El cero puede representarse mediante uno o más ceros.
-   Binario: **0b** (o **0B**) seguido de uno o más dígitos binarios
    (**0** o **1**).
-   Octal: **0o** (o **0O**) seguido de uno o más dígitos octales (**0**
    a **7**).
-   Hexadecimal: **0x** (o **0X**) seguido de uno o más dígitos
    hexadecimales (**0** a **F** o **f**).

Para aumentar legibilidad podemos incluir guiones bajos en los
literales, entre los dígitos, y/o entre el prefijo de base y el número
en sí (0x\_00ff\_0050). Para la evaluación del número son ignorados.

### []{#anchor-21}2.4.6 Floating point literals

Un número con punto decimal. En este caso, se puede omitir la parte
entera o la fraccionaria, pero no ambas. Opcionalmente puede seguir la
parte del exponente: **e** o **E**, seguido por signo opcional y los
dígitos (sin punto decimal) del mismo.

En el caso de que hayamos incluido la parte del exponente, el número
inicial puede ir sin punto decimal.

Tanto el número como el exponente son decimales, y pueden empezar por
**0** si quieren.

Se pueden incluir guiones bajos entre los números, tanto en la parte
entera, como en la fraccionaria, como en el número del exponente.

### []{#anchor-22}2.4.7 Imaginary literals

Literal **float**, o solo la parte entera (sin punto), seguido por **j**
o ***J***. Un número complejo se almacena como un **float** + un
*imaginary*: (**5+3j**).

[]{#anchor-23}2.5 Operators
---------------------------

\+ - \* \*\* / // % @ \<\< \>\> & \| \^ \~ := \< \> \<= \>= == !=

[]{#anchor-24}2.6 Delimiters
----------------------------

No tiene ninguna utilidad práctica.

[]{#anchor-25}3. DATA MODEL
===========================

[]{#anchor-26}3.1 Objects, values and types
-------------------------------------------

Los datos de *Python* se almacenan como objetos. Todo objeto tiene una
*identidad* única que nunca cambia (***id()***; ***is*** compara la
identidad de dos objetos), un *tipo*, que tampoco cambia (***type()***;
marca qué valores puede tener y qué operaciones pueden hacerse en el
objeto; un tipo, es de hecho un objeto también, de tipo ***type***), y
un *valor* (que puede cambiar si el objeto es mutable, aunque un
contenedor inmutable puede contener objetos mutables). La mutabilidad
depende del tipo del objeto. En la implementación *CPython*, ***id()***
es la dirección de memoria del objeto.

Existe *garbage collection*, de objetos no referenciables. Los recursos
\"externos\" que tiene un objeto son liberados cuando es *collected*,
pero como la *garbage collection* no está garantizada, mejor liberar
recursos explícitamente (normalmente métodos llamados ***close()***). Un
buen mecanismo para asegurar que no quedan recursos abiertos es mediante
***try***\...***finally***, o con ***with***.

Cuando hablamos del valor de un contenedor (cualquier objeto que tenga
referencias a otros objetos), nos referimos a los valores, no las
identidades de sus elementos. Cuando hablamos de la mutabilidad del
contenedor, entonces sí nos referimos a las identidades de sus
elementos. Si un contenedor inmutable, como una tupla, contiene
elementos mutables, al cambiar el valor de alguno de esos elementos,
cambia el valor de esa tupla.

Si a dos identificadores les asignamos un valor inmutable, estos pueden
tener o no tener el mismo ***id()***; pero si lo hacemos con valores
mutables, los ***id()*** serán siempre distintos: si hacemos ***a=1*** y
***b=1***, entonces ***a is b*** puede ser ***True*** o ***False***
(depende de la implementación), pero si hacemos ***a=\[1,2,3\]*** y
***b=\[1,2,3\]***, entonces ***a is b*** es siempre ***False*** (sin
embargo, si hacemos ***a=b=\[1,2,3\]***, tendremos que ***a is b*** es
***True***).

[]{#anchor-27}3.2 The standard type hierarchy
---------------------------------------------

Los tipos inmutables pueden ser hashables, los mutables no.

**Valores especiales:**

-   **None** - sin valor; p.e., lo retornan las funciones que no
    retornan nada explícitamente; *truth value is false*.
-   **NotImplemented** - algunos métodos pueden retornarlo si la
    operación no está definida para los operandos facilitados; en cuanto
    a su *truth value*, no se debe evaluar en entornos booleanos.
-   **Ellipsis** - también se escribe **\...**; *truth value is true*.

Cada uno de ellos es de un tipo especial que solo puede tomar ese valor.

**Números**: son inmutables.

-   **int** - enteros, positivos y negativos. No tienen límite.

```{=html}
<!-- -->
```
-   -   **bool** - **True** o **False**. Al convertirse a número, se
        convierten, respectivamente, a 0 y 1 (o 0.0 y 1.0). A *string*,
        \'True\' y \'False\'.

-   **float** - números de punto flotante de doble precisión.

-   **complex** - almacenado como un par de **float**\'s. Atributos
    **real** e **imag**.

**Secuencias**: conjuntos finitos indexados por enteros positivos y cero
(el primero). **len()** devuelve el número de elementos. Selección con
**a\[i\]**. *Slice* **a\[i:j\]** es una lista del mismo tipo, con los
elementos i\<=j\<k. Un *slice* crea una copia del mismo tipo, que a su
vez se puede indexar empezando por 0. Algunas secuencias admiten
extended slice: **a\[i:j:k\]**, siendo \'k\' el *«step»*.

-   **Secuencias inmutables**: contienen simpre los mismos objetos
    (aunque sean objetos mutables).

    -   **Strings**: secuencias de caracteres Unicode (U+0000 a
        U+10FFFF). No existe el tipo carácter: el *string* no es más que
        una sucesión de *strings* de longitud 1. **ord()** convierte un
        elemento de un *string* a entero (índice Unicode). **chr()**
        hace la operación inversa hacia un *string* de longitud 1.
        **str.encode()** pasa de *string* a *bytes*, y
        **bytes.decode()** hace la operación inversa.
    -   **Tuplas**: *comma-separated* expressions; una sola expresión
        seguida de coma (*singleton*), o tupla vacía mediante ***()***.
    -   **Bytes**: array de *bytes* (8 bits). Se crean con literales
        *byte*, o con la *built-in function* ***bytes()***.
        Decodificables con método ***decode()***.

-   **Secuencias mutables**: mediante índice o *slicing* se puede
    asignar valor a los elementos, o borrarlos (con ***del***).

    -   **Listas**: *comma-separated expressions* entre corchetes.
    -   **Byte arrays**: creados con el constructor ***bytearray()***.
        Se comportan exactamente igual que un elemento ***bytes***, pero
        es mutable.

**Set types**: conjuntos no ordenados (no indexables) de objetos
inmutables y únicos. Se pueden iterar; disponen de **len()**. Aceptan
operadores de conjuntos matemáticos.

-   **Sets**: mutables. Constructor **set()**.
-   **Frozen sets**: set inmutable, constructor **frozenset()**.

**Mappings**: conjunto de objetos indexados por un conjunto de índices
del tipo **a\[k\]**, donde \'k\' es una clave, y se puede usar para
acceder o asignar valor a los elementos, o borrarlos (con **del**).
Dispone de **len()**.

-   **Diccionarios**: son mutables. Las *keys* deben ser inmutables, y
    no pueden contener elementos mutables (para que los elementos tengan
    un *hash* constante durante todo su tiempo de vida). Los elementos
    del diccionario conservan siempre el orden de inserción de los
    elementos. La notación se realiza mediante llaves **{}**.

**Callable types**: se puede realizar la llamada a función con ellos.

-   **User-defined functions**: algunos atributos especiales:
    **\_\_doc\_\_** (*docstring*), **\_\_name\_\_**, **\_\_module\_\_**
    (donde fue definida), **\_\_defaults\_\_** (tupla con los valores
    por defecto de los parámetros), **\_\_code\_\_** (objeto código con
    el cuerpo de la función compilado), **\_\_globals\_\_** (diccionario
    con las variables globales de la función, es decir, los atributos
    del módulo donde fue definida; *read-only*), **\_\_dict\_\_**
    (diccionario donde se ven los atributos arbitrarios que hemos
    definido al objeto función), **\_\_annotations\_\_** (diccionario
    con las anotaciones de los parámetros; las claves son los nombres de
    parámetro, y \'return\' si hay anotación para el valor de retorno),
    **\_\_kwdefaults\_\_** (diccionario con los *default values* de los
    *keyword arguments*). En el caso de que una variable de estas no
    tenga elementos, no será un diccionario sino ***None***. Solo se
    pueden añadir atributos arbitrarios (del tipo ***foo.pepe=5***) a
    las funciones definidas por el usuario.
-   **Instance methods**: atributos especiales: ***\_\_self\_\_***
    (instancia), ***\_\_func\_\_*** (objeto función), ***\_\_doc\_\_***
    (*docstring* de la función), ***\_\_name\_\_***,
    ***\_\_module\_\_***. Los métodos pueden acceder (pero no modificar)
    a los atributos arbitrarios de la función a la que están enlazados.
-   Generator functions
-   Coroutine functions
-   Asynchronous generator functions
-   **Built-in functions** - tienen ***\_\_doc\_\_***,
    ***\_\_name\_\_***, ***\_\_self\_\_*** (es ***None***) y
    ***\_\_module\_\_***.
-   **Built-in methods**: como una *built-in function*, pero además
    tienen un objeto pasado implícitamente a la función. En este caso,
    el atributo ***\_\_self\_\_*** (*read-only*) devuelve ese objeto.
-   **Clases**: son llamables. Normalmente crean instancias de sí
    mismas, a no ser que definan el método ***\_\_new\_\_()***.
    Inicialización es típicamente con ***\_\_init\_\_()***.
-   **Instancias de una clase**: las podemos hacer llamables si definen
    el método ***\_\_call\_\_()***.

**Módulos**: el atributo ***\_\_dict\_\_*** (*read-only*) es la
definición de los elementos del *namespace* del módulo, donde están las
referencias a sus atributos; las referencias a un atributo de un módulo
\'m\' del tipo ***m****.x*** equivalen a
***m.****\_\_dict\_\_\[****\'****x****\'****\]***. El atributo
***\_\_globals\_\_*** de las funciones definidas en el módulo es una
referencia a este atributo.

Otros atributos son ***\_\_name\_\_***, ***\_\_doc\_\_*** (*docstring*
del módulo) o ***\_\_file\_\_*** (*path* del archivo desde el que se
leyó el módulo). ***\_\_annotations\_\_*** es un diccionario con las
anotaciones de variables del módulo que se han ido produciendo en la
ejecución del mismo.

**Custom classes**: el *namespace* está en ***\_\_dict\_\_*** (atributos
de la clase); otros atributos pueden ser ***\_\_name\_\_***,
***\_\_module\_\_***, ***\_\_bases\_\_*** (tupla con las base clases, en
orden), ***\_\_doc\_\_*** o ***\_\_annotations\_\_***.

**Class instances**: tienen su propio ***\_\_dict\_\_***. Si un *lookup*
no encuentra el nombre aquí, lo busca en el diccionario de la clase; si
no, sube por la jerarquía; si aun y así no lo encuentra, y la clase
tiene definido el método ***\_\_getattr\_\_()***, lo llama. Asignaciones
y borrados de atributos modifican el diccionario de la instancia, no de
la clase. Si la clase tiene definidos ***\_\_setattr\_\_()*** o
***\_\_delattr\_\_()***, los llama para añadir o borrar atributos
respectivamente, sin modificar el diccionario directamente. El atributo
***\_\_class\_\_*** es la clase de la instancia.

**I/O objects** (**file objects**): representan archivos. Destacar
***sys.stdin***, ***sys.stdout*** y ***sys.stderr*** (input, output y
error streams).

**Tipos internos**: utilizados internamente por el intérprete. **Code
objects**, **frame objects**, **traceback objects** y **slice objects**.

Slice objects

**Static method objects**: constructor ***staticmethod()***.

**Class method objects**: constructor ***classmethod()***.

[]{#anchor-28}3.3 Special method names
--------------------------------------

Métodos con funcionalidades especiales.

### []{#anchor-29}3.3.1 Basic customization

Métodos que puede implementar una clase.

\_\_new\_\_(clase\[, args\])

Es un *static method* (aunque en este caso no hace falta declararlo como
tal). Se llama a la hora de crear una nueva instancia de \'clase\' si
queremos más control sobre esta creación. Los argumentos opcionales son
los que pasamos al constructor al llamar a la clase para crear la
instancia. Este método devuelve normalmente la nueva instancia (de clase
\'clase\').

Típicamente, **\_\_new\_\_()** llamará al **\_\_new\_\_()** de la clase
base, por ajemplo así:

super().\_\_new\_\_(clase\[,args\])

o

Base.\_\_new\_\_(clase\[,args\])

Obsérvese que hay que pasarle la clase. Nosotros la recibimos
automáticamente, pero al llamar manualmente al de la clase padre,
debemos especificar el tipo (clase) de la instancia a crear. Este método
de la clase padre retornará el objeto creado, y la clase derivada lo
recibe, pudiéndolo modificar antes de retornarlo a su vez.

**\_\_new\_\_()** retorna la instancia creada. Si, efectivamente,
retorna un objeto de tipo \'clase\' (el tipo de clase que ha recibido el
**\_\_new\_\_()** primero), entonces el método **\_\_init\_\_()** de esa
nueva instancia es llamado, pasándole como argumentos los mismos que se
le pasaron a **\_\_new\_\_()**. Si, en cambio, no devuelve una instancia
de \'clase\', no se llama a **\_\_init\_\_()**.

Si **\_\_new\_\_()** no está definido, se crea la instancia de forma
automática y se llama a **\_\_init\_\_()** (si está definido). Siempre
habrá un **\_\_new\_\_()**, aunque sea heredado (como mínimo de
**object**).

\_\_init\_\_(self\[, args\])

Inicializador. Los argumentos son los que pasamos al constructor al
llamar a la clase para crear la instancia. Es llamado después de la
creación de la instancia (con **\_\_new\_\_()**), y antes de volver de
la *constructor expression* del *caller*. Si lo definimos y la clase
base también, hay que llamar también al de la clase base
(**Base.\_\_init\_\_(self\[, args\]**), puesto que lo *overrides* (a no
ser que no queramos que se ejecute el inicializador de la base). No debe
retornar ningún valor (**None**).

\_\_del\_\_(self)

Destructor (mejor llamarle finalizador). Si la instancia tiene varias
referencias (apuntadores) a ella y hacemos **del x**, se decrementa el
*count* de referencias; el destructor sólo se ejecuta cuando el *count
reaches* 0. Si la definimos y la clase base también, deberíamos llamarla
para asegurar el borrado de la parte base. Debido a lo precario de las
circunstancias en que se llama al destructor, las excepciones que
ocurren durante su ejecución son ignoradas; solo se imprime un *warning*
en ***sys.stderr***.

\_\_repr\_\_(self)

Se usa su valor de retorno para la función **repr()**. Debe ser una
representación «oficial» del objeto mediante un *string*. Debería
parecer una *expresión válida *que permita recrear el objeto utilizando
esa expresión; si no es posible, debería ser una descripción útil (se
usa básicamente para *debugging*). Si **\_\_str\_\_()** no está definida
y **\_\_repr\_\_()** sí, se usará el valor de esta última como valor de
retorno para **str()** también.

\_\_str\_\_(self)

Representación «informal» (*nicely printable*) del objeto en un
*string*, usada por **format()**, **print()** y **str()**.

\_\_bytes\_\_(self)

Representación del objeto en un string de tipo **bytes**. Llamado por
**bytes()**.

\_\_format\_\_(self,format\_spec)

Retorna la representación, en un *string*, del objeto, según el formato
dado por \'*format\_spec*\'. Es llamado cuando el objeto aparece en la
función **format()** o en el método **format()** de un *string*.

\_\_lt\_\_(self,other)

Compara el objeto (*self*) con otro objeto (*other*). Cuando hacemos
**x\<y**, se llamará a **x.\_\_lt\_\_(y)**. En teoría, debería devolver
**True** si *self* es menor que *other*, y **False** en caso contrario,
pero de hecho puede devolver cualquier tipo de valor. En caso de que
Python necesite un booleano y la función devuelva otro tipo, convertirá
ese valor a booleano con **bool()**. Si la operación no está
implementada para esos tipos de operando, puede devolver
**NotImplemented**.

**\_\_gt\_\_(self,other)** - idem para **\>**.

**\_\_le\_\_(self,other)** - idem para **\<=**.

**\_\_ge\_\_(self,other)** - idem para **\>=**.

**\_\_eq\_\_(self,other)** - idem para **==**.

**\_\_ne\_\_(self,other)** - idem para **!=**. Por defecto delega en
***\_eq\_()*** e invierte el resultado.

\_\_hash\_\_(self)

Retorna el *hash* del objeto; debe ser un entero. El valor se usa para
incluir el objeto en *hashed collections* (no ordenadas: diccionarios y
*sets*), o como valor de retorno de la función **hash()**. Esta trunca
el valor devuelto por **\_\_hash\_\_()** (típicamente a 8 bytes en
sistemas de 64 bits y 4 bytes en sistemas de 32 bits).

Dos objetos que se comparan iguales deben tener el mismo *hash*.

Es buena idea coger todos los componentes del objeto que intervengan en
las comparaciones de objetos, empaquetarlos en un una tupla y devolver
el *hash* de esa tupla como *hash* del objeto:

def \_\_hash\_\_(self):

    return hash((self.name, self.nick, self.color))

Si una clase no define **\_\_eq\_\_()**, no debería definir tampoco
**\_\_hash\_\_()**.

Si una clase no define **\_\_hash\_\_()**, no es *hashable*, con lo que
sus instancias no se pueden usar como elementos de colecciones
*hashables*.

Si una clase define elementos mutables, no debe definir
**\_\_hash\_\_()** (los elementos *hashables* deben ser inmutables para
que no varíe nunca su *hash*).

Las clases definidas por el usuario tienen métodos **\_\_hash\_\_()** y
**\_\_eq\_\_()** por defecto: todos los objetos se comparan distintos
excepto con ellos mismos, es decir, **x==y** si y solo si **x is y** y
**hash(x)==hash(y)**.

Una clase que *overrides* **\_\_eq\_\_()** y no define
**\_\_hash\_\_()**, tendrá su **\_\_hash\_\_()** ímplicitamente a
**None** y levantará un **TypeError** si se pasa una instancia a la
función **hash()**; además será identificada como no *hashable*.

Una clase cuyo **\_\_hash\_\_()** no sea igual a **None** es
identificada como *hashable*, aunque levante explícitamente un
**TypeError**. Si se quiere que una clase sea no *hashable*, se debe
definir en ella **\_\_hash\_\_=None**.

Si una clase quiere retener la funcionalidad *hash* de una clase base,
debe indicarlo explícitamente mediante
***\_\_hash\_\_=BaseClass.\_\_hash\_\_***.

Los objetos *string* y *bytes* tienen valores de *hash* aleatorios.

\_\_bool\_\_(self)

Retorna lo que devuelve la builtin **bool()** sobre el objeto. Debe
retornar **False** o **True**. Si no está definido, se llama a
**\_\_len\_\_()** que devolverá **True** si contiene más de 0 elementos.
Si tampoco está definida, será siempre **True**.

### []{#anchor-30}3.3.2 Customizing attribute access

[]{#anchor-31}\_\_getattr\_\_(self,name)

Se llama cuando no se ha encontrado el atributo \'name\' por vías
normales (ni en la instancia ni en la jerarquía de clases). Debe
devolver el valor del atributo especificado en el *string* \'name\', o
indicar que tampoco se ha encontrado ese atributo, levantando
**AttributeError**.

\_\_getattribute\_\_(self,name)

Todos los accesos a los atributos (métodos y datos) se hacen llamando a
este método, obligatoriamente (toda clase lo tiene, porque lo hereda de
**object**, como mínimo). Si **\_\_getattr\_\_()** también está definido
(puede no estarlo, porque **object** no lo tiene), no será llamado a no
ser que **\_\_getattribute\_\_()** lo llame explícitamente o genere una
excepción **AttributeError**.

Si necesitamos hacer referencia a un atributo desde esta función,
normalmente será a un atributo existente de la clase o instancia; si lo
hacemos directamente (***self.x***) se genera un bucle infinito (el
acceso volverá a llamar a **\_\_getattribute\_\_()**, y así
recursivamente). Para evitarlo, se debe obtener el valor del atributo
accediendo a él a través del **\_\_gettatribute\_\_()** de una clase
base (o desde cualquier otra clase u otro lado), pasándole la instancia
actual como parámetro, que lo único que hará será devolver el valor de
ese atributo existente:

Base.\_\_getattribute\_\_(self, nombre)

Si no tiene clases base, se puede hacer desde la clase **object**:

object.\_\_getattribute\_\_(self, nombre)

Recordemos que aunque el atributo no esté definido en la clase base, el
valor será bien devuelto, porque lo extrae de \'self\', que se le pasa
también por parámetro: estamos llamando a través de la clase, es decir,
directamente a la función (*unbound*), no como método.

Importante: **\_\_getattr\_\_()** sufre el mismo problema cuando
intentamos acceder a un atributo inexistente, con la diferencia de que
al no disponer **object** de este método, deberemos definir esa función
(pasándole una referencia a la instancia y el nombre del atributo
deseado) en algún lado (en la clase base de nuestra clase, por ejemplo).

[]{#anchor-32}\_\_setattr\_\_(self,name,valor)

Para establecer o cambiar valores. El comportamiento por defecto es
cambiar el diccionario de la instancia. En el caso de que quiera asignar
un valor a un atributo, para evitar recursión infinita debe hacerlo a
través del **\_\_setattr\_\_()** de la clase base (u **object**, o
cualquier otro lado).

\_\_delattr\_\_(self,name)

Como **\_\_setattr\_\_()** pero para eliminación.

\_\_dir\_\_(self)

Retorna una secuencia, normalmente con el diccionario de nombres de la
instancia. Es lo que retorna **dir()**. Esta función convierte la
secuencia en lista y la ordena.

#### Customizing module attribute access

Se puede definir la función **\_\_getattr\_\_()** dentro de un módulo,
que se ejecuta cuando no se encuentra un atributo del módulo de la forma
habitual. La función acepta un *string* con el nombre del atributo, y
debe devolver su valor, o levantar **AttributeError**.

También se puede definir **\_\_dir\_\_()** de un módulo, que *overrides*
completamente el comportamiento de **dir()** sobre el módulo. Si
implementamos la función, no acepta argumentos, y debe devolver una
lista de *strings* con los nombres de la tabla de nombres del módulo.

#### Implementing Descriptors

Un descriptor es una instancia de una clase que define por lo menos uno
de los métodos siguientes: **\_\_get\_\_()**, **\_\_set\_\_()** o
**\_\_delete\_\_()**. Un descriptor se usa dentro de otra clase
(*owner*) como atributo. Para que una clase *owner* tenga un descriptor,
debe definir un atributo de tipo descriptor o debe definirlo una de sus
clases base. En resumen, la clase propietaria debe tener una instancia
del descriptor.

\_\_get\_\_(self, instance, owner)

\'self\' es el objeto (instancia) descriptor, \'instance\' es la
instancia de la clase *owner*, y \'owner\' es la clase *owner*. Si
\'instance\' es **None**, es que estamos accediendo al atributo a través
de la clase (\'owner\') y no a través de una instancia de la misma. El
método devuelve el valor del atributo solicitado, es decir, el valor de
la instancia del descriptor.

Si p.e. \'Descrip\' es una clase que implementa **\_\_get\_\_()**, y
diseñamos otra clase \'Own\' que tiene un data attribute \'a\' de tipo
\'Descrip\', entonces **Own.a** invocará a dicha función.

\_\_set\_\_(self, instance, valor)

Cambia el valor del atributo (la instancia del descriptor) al que se
refiere el descriptor.

\_\_delete\_\_(self, instance)

Se invoca cuando eliminamos el atributo (instancia del descriptor) de la
instancia de la clase propietaria.

#### Invoking Descriptors

Normalmente, el acceso a un atributo de una instancia se hace con un
lookup al diccionario de la instancia. Si \'a\' es una instancia de la
clase \'A\', con un atributo \'x\', entonces **a.x** empezará buscando
en **a.\_\_dict\_\_\[\'x\'\]**, si no seguirá por
**type(a).\_\_dict\_\_\[\'x\'\]** (la clase), y seguirá por las clases
base. Pero si el atributo es un descriptor, entonces se accede
directamente al objeto descriptor:

-   **x.\_\_get\_\_(a)** - invocación directa
-   **a.x** - *instance binding*; se transforma automáticamente en
    **type(a).\_\_dict\_\_\['x'\].\_\_get\_\_(a, type(a))**; se hace
    desde **type(a)** porque este atributo está en la tabla de nombres
    de la clase, no de la instancia.
-   **A.x** - *class binding*; pasa a
    **A.\_\_dict\_\_\['x'\].\_\_get\_\_(None, A)**.

Si se accede al atributo \'a.x\' y el descriptor no define
**\_\_get\_\_()**, lo que devolverá es la instancia del objeto
descriptor, a no ser que exista un atributo con ese nombre en el
diccionario de la instancia.

Del mismo modo, si el atributo descriptor está definido en la instancia
y no en la clase, no funciona: al hacer **a.x** devuelve el objeto
descriptor tal cual.

Si un descriptor define **\_\_set\_\_()** y/o **\_\_delete\_\_()**, es
un *data descriptor*, mientras que si no, es un *non-data descriptor*.
Los *data descriptors* suelen definir **\_\_get\_\_()** y
**\_\_set\_\_()**, mientras que los *non-data descriptors* solo suelen
definir **\_\_get\_\_()**. Los *non-data descriptors* pueden ser
*overriden* por la instancia, mientras que los *data descriptors* no.

#### \_\_slots\_\_

Antes, una pequeña explicación: los lenguajes con *garbage collection*,
suelen implementar el concepto de *weak references*. Una variable que
referencie a un objeto es una *strong reference*. Mientras exista por lo
menos una *strong reference*, el objeto sigue vivo. Cuando no quedan
*strong references*, el objeto puede ser *garbage collected*, pero
mientras tanto sigue accesible (vivo) a través de posibles *weak
references* (si las hemos creado). Se pueden crear y gestionar *weak
references* a objetos mediante el módulo **weakref**. Cuando ha sido
*collected*, las *weak references* ya están *cleared*. Las *strong
references* de un objeto se guardan en el diccionario **\_\_dict\_\_**,
y las *weak references* en otro llamado **\_\_weakref\_\_**. Son
diccionarios que enlazan cada nombre al objeto que referencian.

Así, las instancias tienen su diccionario donde se guardan sus atributos
(**\_\_dict\_\_**). Si tenemos *muchas* instancias con pocos atributos,
esto consume muchísima memoria de forma ineficiente, con tanto
diccionario. En cambio si la clase define la variable (atributo)
**\_\_slots\_\_**, este puede contener una secuencia (cualquier
iterable) de *strings* con los nombres de las variables (atributos) que
podrán tener las instancias de esa clase, y en esas instancias se
reservará internamente el mínimo espacio necesario para almacenar sus
valores de forma extremadamente eficiente, en lugar de utilizar los
diccionarios **\_\_dict\_\_** o **\_\_weakref\_\_**, que simplemente no
serán creados.

**\_\_slots\_\_** nos dice qué atributos se pueden añadir a la
instancia. Solo podremos añadirle esos atributos, pero no es obligatorio
definirlos todos. Sin embargo, a la clase en sí se le pueden añadir
todos los atributos que queramos, aunque la clase tampoco tiene
diccionarios **\_\_dict\_\_** ni **\_\_weakref\_\_**.

No se pueden añadir, pues, atributos no listados en *slots* a una
instancia; si necesitamos hacerlo, deberemos añadir **\_\_dict\_\_** a
la lista de *slots*.

No se pueden crear *weak references* a un objeto con slots. Si se
necesita crearlas, hay que añadir manualmente **\_\_weakref\_\_** a la
lista de slots.

Si una subclase hereda de una clase sin *slots*, sí tendrá diccionarios
**\_\_dict\_\_** y **\_\_weakref\_\_**, aunque en estos diccionarios no
se incluirán los atributos definidos en **\_\_slots\_\_**.

En la definición de *slots*, los nombres definidos allí crean
descriptores, con lo que la clase no puede definir a su vez atributos
con esos nombres, puesto que esas definiciones sobrescribirían los
descriptores creados por el efecto de **\_\_slots\_\_**.

Si una subclase hereda de una clase con *slots*, tendrá diccionario
igualmente, a no ser que también defina **\_\_slots\_\_**; en ese caso,
esa nueva *slots* deberá definir solo los elementos *adicionales*,
porque ya hereda los de la clase base (si repite nombres, los de la base
son inalcanzables).

En herencia múltiple, como mucho una de las clases base puede definir
*slots*.

Si se utiliza un iterador para definir *slots*, se crearán los
descriptores correspondientes, pero la variable **\_\_slots\_\_** será
un iterador vacío.

### []{#anchor-33}3.3.3 Customizing class creation

Demasiado complejo para la probabilidad de tener que usarlo alguna vez.
Solo un comentario: si un objeto instancia es creado según se especifica
en el objeto clase, ¿en qué se basa para crear un objeto clase? Pues a
su vez se especifica mediante un objeto metaclase. Por defecto, la
metaclase de las clases definidas por el usuario es **type**. Podemos
crear nuestras propias metaclases, pero es demasiado complejo, y casi
nunca necesario.

### []{#anchor-34}3.3.4 Customizing instance and subclass checks

Métodos usados para override el comportamiento de **isinstance()** y
**issubclass()**:

**\_\_instancecheck\_\_(self, object)** debe retornar **True** si
\'object\' debe ser considerado instancia de la clase que define el
método. **False** si no.

**\_\_subclasscheck\_\_(self, class)** debe retornar **True** si
\'class\' debe ser considerada subclase de la clase que define el
método. **False** si no.

Hay que decir que estos métodos no se definen en la clase en sí, sino en
la metaclase de la clase que deseamos chequear.

### []{#anchor-35}3.3.5 Emulating generic types

Improbablemente útil.

### []{#anchor-36}3.3.6 Emulating callable objects

Para que se pueda llamar a la instancia, como si fuera una función:

**\_\_call\_\_(self\[,argumentos\])** - si llamamos una instancia \'x\'
del tipo: **x(a1,a2,\...)**, será equivalente a
**x.\_\_call\_\_(a1,a2,\...)**.

### []{#anchor-37}**3.3.7 Emulating container types**

Los contenedores pueden ser secuencias (como listas o tuplas) o
*mappings* (como los diccionarios), aunque puede haber otros tipos. La
diferencia entre secuencias y diccionarios es que en las primeras, las
claves (*keys*) son índices enteros de 0 a N-1 (N elementos) u objetos
slice.

Los *mappings* deberían definir los métodos **keys()**, **values()**,
**items()**, **get()**, **clear()**, **setdefault()**, **pop()**,
**popitem()**, **copy()** y **update()**, y comportarse de forma análoga
a como lo hacen en los diccionarios de *Python*.

Las *secuencias mutables* deberían definir los métodos **append()**,
**count()**, **index()**, **extend()**, **insert()**, **pop()**,
**remove()**, **reverse()** y **sort()**, y que se comporten de forma
análoga a como lo hacen en las listas de *Python*.

*Todas las secuencias* deberían definir la adición (concatenación) y
multiplicación (repetición) mediante los métodos **\_\_add\_\_()**,
**\_\_radd\_\_()**, **\_\_iadd\_\_()**, **\_\_mul\_\_()**,
**\_\_rmul\_\_()** y **\_\_imul\_\_()**, y no definir otros operadores
aritméticos.

Todos los contenedores, en general, deberían implementar
**\_\_contains\_\_()** para *membership tests* (**in**, que en el caso
de diccionarios, debería buscar en las claves), e **\_\_iter\_\_()**
para iteración de sus elementos (en *mappings* debería iterar por todas
las claves).

\_\_len\_\_(self)

Para la función *builtin* ***len()***. Devuelve un entero no negativo;
normalmente, el número de elementos del contenedor. Un objeto cuyo
***len()*** es 0 y no define ***\_\_bool\_\_()***, se considera
***False***.

\_\_length\_hint\_\_(self)

Estimación del *expected size*; se usa para *presize* *containers*, para
optimizar el funcionamiento interno, y no es obligatorio. Puede devolver
***NotImplemented***, que es como si no estuviese definido.

Nota: el *slicing* utiliza únicamente los siguientes 3 métodos.
**a\[1:2\]=b** se transforma automáticamente en
**a\[slice(1,2,None)\]=b**. Los argumentos que faltan en la llamada se
rellenan siempre con **None**.

\_\_getitem\_\_(self,key)

Para evaluar **self\[key\]**. Para secuencias, las claves son enteros o
*slices*. La interpretación de los índices negativos depende totalmente
de esta función. Si \'key\' es de un tipo inapropiado, se puede levantar
**TypeError**. En el caso de mappings, si \'key\' no existe, raise
**KeyError**. Para índices fuera de rango, se levanta **IndexError**
(los bucles **for** lo usan para detectar el final).

\_\_missing\_\_(self,key)

Es llamado por **\_\_getitem\_\_()** para implementar **self\[key\]**
cuando la clave no está en el diccionario.

\_\_setitem\_\_(self,key,value)

Todo lo dicho para índices en **\_\_getitem\_\_()** se aplica aquí, pero
para escribir en lugar de leer. Debe ser posible ese cambio de valores,
así como añadir valores nuevos. Mismas excepciones que
**\_\_getitem\_\_()**.

\_\_delitem\_\_(self,key)

Para borrar **self\[key\]**. Se aplica lo dicho en
**\_\_getitem\_\_()**. Debe ser posible la eliminación. Mismas
excepciones que **\_\_getitem\_\_()**.

\_\_missing\_\_(self,key)

Para tratar claves inexistentes en el contenedor.

\_\_iter\_\_(self)

Debe devolver un iterador que itere sobre los elementos del contenedor.
En *mappings*, debe iterar por las claves. Los iteradores también
implementan este método para retornarse a sí mismos.

\_\_reversed\_\_(self)

Devuelve también un iterador, pero que itera en el orden inverso. Es lo
que devuelve la *built-in function* **reversed()** sobre el objeto. Si
**\_\_reversed\_\_()** no está definido, **reversed()** lo implementa
usando **\_\_len\_\_()** y **\_\_getitem\_\_()**, por lo que sólo
deberíamos definir este método si va a ser más eficiente que esta
implementación por defecto.

\_\_contains\_\_(self,item)

Para membership tests (**in**, **not in**). Devuelve **True** o
**False**. En *mappings* debería centrarse en las *keys*. Si no está
definido, se hace automáticamente, iterando por los elementos, por lo
que sólo deberíamos definir este método si va a ser más eficiente.

### []{#anchor-38}3.3.8 Emulating numeric types

Habría que definir solo las operaciones que pueden realizarse sobre el
objeto.

Métodos:

**\_\_add\_\_(self,otro)**,** \_\_sub\_\_(self,otro)**,**
\_\_mul\_\_(self,otro)**, **\_\_matmul\_\_(self,otro)**,**
\_\_truediv\_\_(self,otro)**,** \_\_floordiv\_\_(self,otro)**,**
\_\_mod\_\_(self,otro)**,** \_\_divmod\_\_(self,otro)**,**
\_\_pow\_\_(self,otro,\[modulo\])**,** \_\_lshift\_\_(self,otro)**,**
\_\_rshift\_\_(self,otro)**,** \_\_and\_\_(self,otro)**,**
\_\_xor\_\_(self,otro)** y** \_\_or\_\_(self,otro)**.

Corresponden respectivamente a los operadores:

**+**, **-**, **\***, ***@***, **/**, **//**, **%**, **divmod()**,
**pow()** (y **\*\***), **\<\<**, **\>\>**, **&**, **\^** y** \|**.

**A\*\*B** equivale a **pow(A,B)**. Y **pow(A,B,C)** debería equivaler a
**A\*\*B%C**. Aunque nosotros podemos definirlo como queramos para
nuestras clases.

Se debería definir **pow()** para 3 argumentos. **divmod(A,B)** debería
devolver una tupla (*A//B*, *A%B*). ***@*** es multiplicación de
matrices.

Si la función no implementa la operación con el argumento que se le ha
pasado, debería devolver **NotImplemented** (o simplemente no
definirla).

Si \'x\' es una instancia de una clase que tiene **\_\_add\_\_()**
definido, entonces **x+y** llamaría a **x.\_\_add\_\_(y)**.

Cuando el operando de la izquierda no soporta la operación, es decir,
cuando ese método no está definido o retorna **NotImplemented**, hay dos
opciones. Si el operando de la derecha es del mismo tipo, hemos
terminado: la operación no está definida. Pero si es de un tipo
distinto, podemos mirar si esa clase define la operación en esos casos,
es decir, si define lo que se llama la operación reflejada, con los
operandos intercambiados. Estos métodos especiales del operando de la
derecha son:

**\_\_radd\_\_(self,otro)**,** \_\_rsub\_\_(self,otro)**,**
\_\_rmul\_\_(self,otro)**,** \_\_rmatmul\_\_(self,otro)**,
**\_\_rtruediv\_\_(self,otro)**,** \_\_rfloordiv\_\_(self,otro)**,**
\_\_rmod\_\_(self,otro)**,** \_\_rdivmod\_\_(self,otro)**,**
\_\_rpow\_\_(self,otro)**,** \_\_rlshift\_\_(self,otro)**,**
\_\_rrshift\_\_(self,otro)**,** \_\_rand\_\_(self,otro)**,**
\_\_rxor\_\_(self,otro)** y** \_\_ror\_\_(self,otro)**.

Estos métodos se corresponden a los anteriores, solo en caso de
operaciones reflejadas.

En este caso concreto, **pow()** con 3 argumentos no intentará llamar a
**\_\_rpow\_\_()** (muy complejo implementar la operación reflejada).

En el caso específico de que el segundo operando sea de una subclase del
tipo del primer operando, la versión reflejada del segundo se llamará
prioritariamente sobre la versión normal del primero. Ello se hace para
que las subclases puedan *override* las operaciones de sus bases.

Si los operandos son de la misma clase y la operación normal no está
definida, no se llamará a la reflejada, en caso de existir, ya que se
asume que la operación no está definida.

Augmented assignments:

**\_\_iadd\_\_(self,otro)**,** \_\_isub\_\_(self,otro)**,**
\_\_imul\_\_(self,otro)**,** \_\_imatmul\_\_(self,otro)**,
**\_\_itruediv\_\_(self,otro)**,** \_\_ifloordiv\_\_(self,otro)**,**
\_\_imod\_\_(self,otro)**,** \_\_ipow\_\_(self,otro,\[modulo\])**,**
\_\_ilshift\_\_(self,otro)**,** \_\_irshift\_\_(self,otro)**,**
\_\_iand\_\_(self,otro)**,** \_\_ixor\_\_(self,otro)** y**
\_\_ior\_\_(self,otro)**.

Estos métodos corresponden respectivamente a **+=**, **-=**, **\*=**,
***@=***, **/=**, **//=**, **%=**, **\*\*=**, **\<\<=**, **\>\>=**,
**&=**, **\^=** y** \|=**. Deberían modificar \'self\' directamente y
luego devolver el resultado (que por lo general será \'self\', aunque no
tiene por qué). Al encontrar uno de estos operadores, si no está
definido el método correspondiente, *Python* lo intentará con la forma
normal. Si tampoco, intentará con el reflejado.

[]{#anchor-39}**\_\_neg\_\_(self)**, **\_\_pos\_\_(self)**,
**\_\_abs\_\_(self)** y **\_\_invert\_\_(self)** responden
respectivamente a los operadores unarios **-**, **+**, **abs()** y
**\~**.

[]{#anchor-40}**\_\_complex\_\_(self)**, **\_\_int\_\_(self)**,
**\_\_float\_\_(self)** y **\_\_round\_\_(self\[,n\])** responden
respectivamente a las *built-in functions* **complex()**, **int()**,
**float()** y **round()**. Deben retornar un valor del tipo adecuado.

**\_\_round\_\_(self\[,digits\])**, **\_\_trunc\_\_(self)**,
**\_\_floor\_\_(self)** y **\_\_ceil\_\_(self)** responden a la *bult-in
function* **round()**, y a las funciones del módulo \'math\'
**trunc()**, **floor()** y **ceil()**. Todas ellas deberían retornar un
número entero, a excepción de **round()** en caso de especificar un
número de dígitos decimales superior a cero. Si **\_\_int\_\_()** no
está definida, **int()** *falls back to* **\_\_trunc\_\_()**.

Truncar es eliminar la parte decimal, con lo que equivale a *floor* en
positivos, y a *ceil* en negativos.

### []{#anchor-41}3.3.9 With Statement Context Managers

Usados con la sentencia **with**, algunos objetos realizan algunas
acciones al entrar en esa sentencia, y otras al salir. Normalmente se
usa para inicializar recursos y luego liberarlos, guardar y restaurar un
estado global, etc. Son dos métodos que se invocan al ejecutarse la
sentencia **with**, pero también pueden ser llamados directamente. Por
ejemplo, para files:

with open('/tmp/workfile', 'r') as f:

    \# hacer cosas con el objeto f

Cuando termina la sentencia **with,** cierra el archivo automáticamente,
con lo que no hay que hacer **f.close()**.

\_\_enter\_\_(self)

Lo que ejecutará la sentencia **with**, si la usamos con nuestra clase.
Lo que retorne este método lo asignará **with** a la variable después de
**as**.

\_\_exit\_\_(self,exc\_type,exc\_val,traceback)

Lo que se ejecutará al terminar el código del **with**. Los parámetros
extra reciben la causa de salida de **with**. Si no se produjo
excepción, **\_\_exit\_\_()** será llamada con tres **None**. Si se
produjo excepción durante la ejecución del bloque **with**, se llamará
con tres valores correspondientes a la excepción, su valor y el objeto
de tipo *traceback* (que tiene la información de dónde se produjo el
error). Si deseamos que no se popague la excepción después de la
cláusula **with**, **\_\_exit\_\_()** debería devolver un valor
verdadero (p.e. **True**), de lo contrario la excepción se recogerá tras
**\_\_exit\_\_()**.

### []{#anchor-42}**3.3.10 Special method lookup**

Los métodos especiales que hemos estado describiendo deben definirse en
el diccionario de la clase, no de la instancia, ya que de lo contrario
no se encontrarán al hacer el *lookup*.

[]{#anchor-43}3.4 Coroutines
----------------------------

Para tareas asíncronas. Muy relacionado con el módulo **asyncio**. No es
*threading* ni *multiprocessing*: es multitasking en un mismo hilo,
aprovechando las esperas de las tareas durante, sobre todo, operaciones
I/O.

Lo saltamos de momento.

[]{#anchor-44}4. EXECUTION MODEL
================================

[]{#anchor-45}4.2 Naming and binding
------------------------------------

### []{#anchor-46}4.2.1 Binding of names

Los nombres están vinculados (*bound*) a objetos. Si un nombre se
vincula dentro de un bloque de código (módulo, función, clase, etc.), es
una variable local de ese bloque, a no ser que se declare como
**nonlocal** o **global**. Si se vincula a nivel de módulo, es una
variable global.

### []{#anchor-47}4.2.2 Resolution of names

El ámbito (*scope*) define la visibilidad de un objeto dentro de un
bloque. Para resolver nombres se usa el *nearest enclosing scope*.

Si un nombre referenciado no se encuentra, se levanta **NameError**. Si
está en un *scope* local y no se encuentra pero está definido más
adelante en el *scope*, se levanta **UnboundLocalError** (subclase de
**NameError**).

[]{#anchor-48}6. EXPRESSIONS
============================

[]{#anchor-49}6.1 Arithmetic conversions
----------------------------------------

El comportamiento normal de los operadores aritméticos con los tipos
*built-in* es convertir a un tipo común los operandos cuando estos son
de tipo distinto: si uno de los dos es **complex**, el otro es
convertido a **complex**. Si no, si uno es de punto flotante, el otro se
convierte a punto flotante. Si no, es que son los dos enteros.

Puede haber reglas adicionales para algunos operadores.

[]{#anchor-50}6.2 Atoms
-----------------------

Los elementos más básicos de una expresión. Identificadores y literales.

### []{#anchor-51}6.2.3 Parenthesized forms

Para agrupar expresiones. Retorna lo que retorna la expresión que
contiene. Si contiene comas, retorna una tupla; si no, el valor de la
expresión que engloba. Así, los paréntesis no definen una tupla, sino
las comas, a excepción de un par de paréntesis vacíos, que retornan una
tupla vacía.

### []{#anchor-52}6.2.4 Displays for lists, sets and dictionaries

En las *comprehensions*, la expresión inicial es evaluada en el
*enclosing scope* (bloque de código que define la *comprehension*), y es
pasada como argumento al *scope* anidado que forma la *comprehension*.
El resto de expresiones (en **if**\'s y **for**\'s) pertenecen a ese
*scope* anidado.

### []{#anchor-53}6.2.5 List displays

**\[lista de elementos\]** o **\[comprehension\]**.

### []{#anchor-54}6.2.6 Set displays

**{lista de elementos}** o **{comprehension}**.

### []{#anchor-55}6.2.7 Dictionary displays

**{lista de elementos key:value}** o **{dict comprehension}**.

Si uno de los elementos es del tipo **\*\*m** (*dictionary unpacking*),
y \'m\' es un *mapping*, entonces se insertan también los elementos de
\'m\' en el diccionario .

En el caso de una *comprehension*, la expresión inicial debe estar
formada por un par de expresiones separadas por dos puntos (**:**).

Al añadir elementos a un diccionario, la clave se evalúa antes que el
valor.

Las claves deben ser de un tipo *hashable*.

### []{#anchor-56}6.2.8 Generator expressions

(comprehension)

No está permitido usar *yield* en el *nested scope*.

### []{#anchor-57}6.2.9 Yield expressions

Un generador devuelve valores mediante **yield**, o **yield from
\<expr\>**, donde \'expr\' actúa como un subiterador del que va tomando
valores para realizar los **yield**.

El método **\_\_next\_\_()** de los iteradores y generadores es llamado
implícitamente por una cláusula **for**, o directamente con la *built-in
function* **next()**.

[]{#anchor-58}6.10 Comparisons
------------------------------

En Python, a parte de asignaciones múltiples (**a=b=3**), se pueden
hacer comparaciones encadenadas (**a\<b\<c==d**). Se encadenan con
**and**, y no se evalúan las expresiones dos veces, a parte de que
también cortocircuitan.

Los operadores de comparación comparan valores, no tipos ni identidades,
por lo que se pueden aplicar entre operandos de tipos distintos.

Sin embargo, los operadores **==** y **!=** se basan, por defecto, en la
identidad (**id()**).

Los tipos numéricos pueden compararse entre sí. Aunque los complejos no
tienen comparación de orden (**\>**, **\<**, **\<=**, **\>=**).

El valor **NaN** es diferente a todo, incluso a sí mismo. Los singletons
**None** y **NotImplemented** deberían ser comparados solo mediante
**is** e **is not**.

Las secuencias *bytes* (**bytes**, **bytearray**) y *string* (**str**)
se pueden comparar con secuencias de su tipo, usándose el valor numérico
de sus elementos (*bytes* o *Unicode code points*). No puede compararse
una secuencia de uno de estos tipos con otra del otro tipo.

Las *secuencias* (**tuple**, **list**, **range**) pueden compararse con
objetos de su mismo tipo, aunque los *ranges* no tienen comparación de
orden. Las secuencias se comparan comparando elemento por elemento. Si
dos secuencias son iguales hasta el final de la más corta, la más corta
es la menor.

Los *mappings* (**dict**) solo se comparan iguales si tienen los mismos
pares clave:valor. No pueden compararse por orden.

Los *sets* (**set**, **frozenset**) se pueden comparar con su mismo
tipo. Las comparaciones de orden se interpretan mediante la relación
subconjunto-superconjunto.

### []{#anchor-59}6.10.2 Membership test operations

Para comprobaciones del tipo **obj in seq**. Los *strings*, *bytes*,
secuencias, *mappings* y *sets* *built-in* implementan esta comprobación
(en diccionarios se comprueba si existe tal clave).

### []{#anchor-60}6.10.3 Identity comparisons

**x is y** solo si \'x\' e \'y\' son el mismo objeto; **x is not y** en
caso contrario.

[]{#anchor-61}6.13 Conditional expressions
------------------------------------------

También llamado *ternary operator*. Es del tipo:

\<expr1\> if \<cond\> else \<expr2\>

Retorna \'expr1\' si \'cond\' es verdadero, o \'expr2\' en caso
contrario.

[]{#anchor-62}6.16 Evaluation order
-----------------------------------

*Python* evalúa las expresiones de izquierda a derecha. En el ejemplo,
el número indica el orden:

expr1, expr2, expr3, expr4

(expr1, expr2, expr3, expr4)

{expr1: expr2, expr3: expr4}

expr1 + expr2 \* (expr3 - expr4)

expr1(expr2, expr3, \*expr4, \*\*expr5)

expr3, expr4 = expr1, expr2

[]{#anchor-63}6.17 Operator precedence
--------------------------------------

Prioridad de los operadores en *Python*, de menor a mayor:

+----------------------------------+----------------------------------+
| :=                               | Assignment expression            |
+----------------------------------+----------------------------------+
| lambda                           | Lambda expression                |
+----------------------------------+----------------------------------+
| if - else                        | Conditional expression           |
+----------------------------------+----------------------------------+
| or                               | Boolean OR                       |
+----------------------------------+----------------------------------+
| and                              | Boolean AND                      |
+----------------------------------+----------------------------------+
| not                              | Boolean NOT                      |
+----------------------------------+----------------------------------+
| in, not in, is, is not,          | Comparisons, including           |
|                                  |                                  |
| \<, \<=, \>, \>=, !=, ==         | membership tests and identity    |
|                                  | tests                            |
+----------------------------------+----------------------------------+
| \|                               | Bitwise OR                       |
+----------------------------------+----------------------------------+
| \^                               | Bitwise XOR                      |
+----------------------------------+----------------------------------+
| &                                | Bitwise AND                      |
+----------------------------------+----------------------------------+
| \<\<, \>\>                       | Shifts                           |
+----------------------------------+----------------------------------+
| +, -                             | Addition and subtraction         |
+----------------------------------+----------------------------------+
| \*, @, /, //, %                  | Multiplication, matrix           |
|                                  | multiplication,                  |
|                                  |                                  |
|                                  | division, floor division,        |
|                                  | remainder                        |
+----------------------------------+----------------------------------+
| +x, -x, \~x                      | Positive, negative, bitwise NOT  |
+----------------------------------+----------------------------------+
| \*\*                             | Exponentiation                   |
+----------------------------------+----------------------------------+
| await x                          | Await expression                 |
+----------------------------------+----------------------------------+
| x\[index\], x\[index:index\],    | Subscription, slicing, call,     |
|                                  | attribute reference              |
| x(arguments\...), x.attribute    |                                  |
+----------------------------------+----------------------------------+
| (expressions\...),               | Binding or parenthesized         |
| \[expressions\...\],             | expression, list display,        |
|                                  | dictionary display, set display  |
| {key: value\...},                |                                  |
| {expressions...}                 |                                  |
+----------------------------------+----------------------------------+

En cuanto al operador de exponente (su prioridad es distinta en
operadores unarios de uno y otro lado):

\>\>\> n=-1\*\*2

\>\>\> n

-1

\>\>\> n=2\*\*-1

\>\>\> n

0.5

[]{#anchor-64}7. SIMPLE STATEMENTS
==================================

[]{#anchor-65}7.3 The assert statement
--------------------------------------

La sentencia:

assert \<expr\>

equivale a:

if \_\_debug\_\_:

    if not \<expr\>: raise AssertionError

En cuanto a la versión extendida:

assert \<expr1\>, \<expr2\>

equivale a:

if \_\_debug\_\_:

    if not \<expr1\>: raise AssertionError(\<expr2\>)

**\_\_debug\_\_** es **True** en circunstancias normales. Será **False**
cuando indiquemos que la compilación se haga con optimización (*command
line option* **-O**).

[]{#anchor-66}7.4 The pass statement
------------------------------------

Operación nula.

[]{#anchor-67}7.5 The del statement
-----------------------------------

del \<lista de objetos\>

Elimina el nombre (o nombres) especificados en la lista, de la tabla de
nombres local o global, dependiendo de si están declarados como
**global**, **nonlocal** o nada. Si el nombre está *unbound*, se levanta
**NameError**. Los elimina recursivamente, de izquierda a derecha.

[]{#anchor-68}7.6 The return statement
--------------------------------------

return \<lista de expresiones\>

Retorna una expresión o lista de expresiones, o **None** si no se
especifica. Si una función no termina en **return** también retorna
**None**.

Si **return** abandona una cláusula **try** que contiene **finally**,
antes de retornar se ejecuta el código de **finally**.

En un iterador, indica que este ha terminado las iteraciones y levanta
**StopIteration**. Si devuelve valor, este quedará en
**StopIteration.value**.

[]{#anchor-69}7.7 The yield statement
-------------------------------------

En un generador:

yield \<expr\>

o:

yield from \<expr\>

donde la expresión es un sub-iterador, de donde el generador irá
extrayendo los sucesivos valores para irlos *yielding*.

[]{#anchor-70}7.8 The raise statement
-------------------------------------

raise

*re-raises* la última excepción que estaba activa en el *scope* actual.
Si no había ninguna, se levanta **RuntimeError**.

raise \<excep\>

levanta la excepción \'excep\', que debe ser una subclase o una
instancia de **BaseException**. Si es una clase, se construirá
internamente, cuando sea necesario, la instancia a partir de esa clase,
sin argumentos.

Al levantarse una excepción se genera un objeto *traceback* (sucesión de
llamadas hasta el punto del código donde se produce la excepción), que
se añade a la instancia de la excepción levantada, en su atributo
**\_\_traceback\_\_**. Este atributo es *writable*, a través del método
**with\_traceback()**:

raise \<excep\>(\'Texto de error\').with\_traceback(\<obj\_traceback\>)

En este caso, hemos levantado una excepción del tipo \'excep\',
pasándole el texto del error al constructor, y aportando nuestro propio
objeto *traceback*. Lo podemos hacer así porque el método
**with\_traceback()** retorna el objeto (instancia) excepción.

raise \<excep1\> from \<excep2\>

En este caso, estamos indicando que levantamos la excepción de tipo
\'excep1\', que se ha producido a causa de otra excepción \'excep2\'. Si
la excepción (\'excep1\', que es la que debemos tratar) queda sin
tratar, se imprimirá en el mensaje de error las dos excepciones, y que
una de ellas es causa de la otra. Al levantarse así, internamente se
adjuntará \'excep2\' (que también puede ser instancia o clase) en el
atributo **\_\_cause\_\_** de la excepción levantada (\'excep1\').

De forma similar, si durante el tratamiento de una excepción se produce
otra, que no queda tratada, en el mensaje de error se informará de este
hecho. Internamente se hace añadiendo a la nueva excepción una
referencia a la que estábamos tratando, en el atributo
**\_\_context\_\_**.

[]{#anchor-71}7.9 The break statement
-------------------------------------

Sale del *nearest enclosing* **for** o **while**, saltándose el posible
**else** que pueda tener el bucle.

Si la sentencia implica salir de una cláusula **try** que tiene una
sección **finally**, el código de esta sección se ejecutará antes de
salir del bucle.

[]{#anchor-72}7.10 The continue statement
-----------------------------------------

Produce una nueva iteración del *nearest enclosing* **for** o **while**.

Si la sentencia implica salir de una cláusula **try** que tiene una
sección **finally**, el código de esta sección se ejecutará antes de
iniciar la nueva iteración.

[]{#anchor-73}7.11 The import statement
---------------------------------------

Para importar módulos.

import A as C, B

equivale a:

import A as C

import B

[]{#anchor-74}7.12 The global statement
---------------------------------------

Para *bind* un nombre al *scope* global.

[]{#anchor-75}7.13 The nonlocal statement
-----------------------------------------

Para *bind* un nombre al *scope* exterior más cercano en el que aparece.

[]{#anchor-76}8. COMPOUND STATEMENTS
====================================

Las sentencias simples se pueden agrupar en la misma línea separándolas
por punto y coma (**;**), y opcionalmente terminando la línea en punto y
coma.

Una sentencia compuesta está formada por una o varias cláusulas. Una
cláusula está formada por un encabezado y una *suite* (una sentencia o
conjunto de sentencias simples o compuestas).

El encabezado de una cláusula termina en dos puntos (**:**).

Una *suite* es una o más sentencias agrupadas en una de estas dos
formas:

-   En la misma línea del encabezado. Si hay más de una, habrá que
    separarlas por punto y coma (**;**). Como siempre, opcionalmente se
    puede terminar la línea en punto y coma.
-   Debajo del encabezado, una debajo de otra con una indentación
    superior al encabezado (podemos también agrupar varias sentencias
    simples en una o más de las líneas indentadas).

Las sentencias compuestas no pueden incluirse en una línea con
sentencias separadas por punto y coma.

Sentencias compuestas: **if**, **while**, **for**, **try**, **with**,
**def**, **class**.

[]{#anchor-77}8.6 Function definitions
--------------------------------------

Aquí hablaremos, sobre todo, de function *decorators*. Un *decorator* es
una función que acepta una función como argumento, y que retorna a su
vez una función. Es decir, «tunea» la función que le pasamos. La
sintaxis de *decorators* es:

\@deco

def fun:

    pass

Esto equivale a:

def fun:

    pass

fun = deco(fun)

¿Cómo se construye un *decorator*? En primer lugar, sabemos que es una
función (\'deco\') que acepta una función como entrada (\'fun\'), y
devuelve otra función (que llamaremos \'wrapper\',), que es una
modificación de la de entrada. Así, \'deco\' acepta \'fun\', la mete
dentro de \'wrapper\' y devuelve \'wrapper\'. La llamaremos \'wrapper\',
porque envuelve \'fun\'.

Vamos a hacer un *decorator* que duplica las acciones de una función:

[]{#anchor-78}def deco\_duplica(f):

    def wrapper\_duplica():

        f()

        f()

    return wrapper\_duplica

\@deco\_duplica

def fun():

    print(\'¡Buenos días!\')

fun()

Resultado:

¡Buenos días!

¡Buenos días!

¿Y si queremos decorar una función \'fun\' que tiene parámetros? Para
que no nos dé error y el *decorator* acepte todo tipo de listas de
parámetros, haremos:

[]{#anchor-79}[]{#anchor-80}def deco\_duplica(f):

    def wrapper\_duplica(\*args, \*\*kwargs):

        f(\*args, \*\*kwargs)

        f(\*args, \*\*kwargs)

    return wrapper\_duplica

\@deco\_duplica

def fun(nombre):

    print(f\'¡Buenos días, {nombre}!\')

fun(\'Pepe\')

Resultado:

¡Buenos días, Pepe!

¡Buenos días, Pepe!

Esto es así, porque si nos fijamos, \'deco\_duplica\' nos retorna y
asigna a \'fun\' el objeto función \'wrapper\_duplica\'. Es decir, al
hacer la llamada **fun(\'Pepe\')** estamos, de hecho, llamando a
**wrapper\_duplica(\'Pepe\')**.

¿Qué pasa al encadenar *decorators*?

\@deco1

\@deco2

def foo: pass

Esto equivale a **foo=deco1(deco2(foo))**.

A veces podemos querer darle argumentos a nuestro decorator. Imaginemos
que queremos pasarle el número de veces que queremos repetir la función
original. Para definir un *decorator* con argumentos se hace así:

def deco\_duplica\_n(n):

    def deco\_duplica(f):

        def wrapper\_duplica(\*args, \*\*kwargs):

            for i in range(n):

                f(\*args, \*\*kwargs)

        return wrapper\_duplica

    return deco\_duplica

\@deco\_duplica\_n(4)

def fun(nombre):

    print(f\'¡Buenos días, {nombre}!\')

fun(\'Pepe\')

Resultado:

¡Buenos días, Pepe!

¡Buenos días, Pepe!

¡Buenos días, Pepe!

¡Buenos días, Pepe!

Vamos a estudiarlo: **deco\_duplica\_n(4)**, de hecho, nos está
retornando esta función:

def deco\_duplica(f):

    def wrapper\_duplica(\*args, \*\*kwargs):

        for i in range(4):

            f(\*args, \*\*kwargs)

    return wrapper\_duplica

Que es algo similar a lo que teníamos con la versión que duplicaba. Esta
función retornada es la que se usa como decorador, es decir,
**fun=deco\_duplica\_n(4)(fun)**, que es lo mismo que
**fun=deco\_duplica(fun)**, en su versión con \'n\'=4.

Generalizando, tenemos que, por ejemplo:

\@deco1(N)

\@deco2(A,B)

def foo: pass

Esto equivale a **foo=deco1(N)(deco2(A,B)(foo))**.

Para finalizar, hay que tener en cuenta que si queremos que nuestra
función «tuneada» por el *decorator* siga retornando el mismo valor que
antes de pasarla por el *decorator*, la función *wrapper* debe incluir:

return f(\*args,\*\*kwargs)

Aunque también puede modificar ese valor de retorno.

Un *decorator* puede ser cualquier tipo de expresión válida. Por
ejemplo, si tenemos una lista \'lst\', en los que sus elementos tienen
un atributo (de tipo función) \'decfoo\', se puede hacer:

\@lst\[0\].decfoo

def fooA():

    \...

\@lst\[1\].decfoo

def fooB():

    \...

[]{#anchor-81}8.7 Class definitions
-----------------------------------

Comentar simplemente que también se pueden definir *decorators* para los
métodos de una clase.

Pero además se pueden utilizar *decorators* de clases, que se utilizan
en la clase entera. En este caso, la diferencia estriba en que el
*decorator* acepta como argumento una clase, que retorna «tuneada». Por
lo demás, el mecanismo es similar a los *decorators* de funciones.

\@deco

class MiClase:

    pass

Equivale a:

MiClase = deco(MiClase)

Un *decorator* de un método es, en la práctica, lo mismo que vimos para
funciones. En cambio, un *decorator* de clase es una función que recibe
como parámetro un objeto clase, y que retorna esa clase modificada.

Veamos un ejemplo: una clase simple que solo dice hola. Un decorator que
añade un método para decir adiós. Además, el decorator admite un
parámetro que indica cuántas veces se dirá \'¡Adiós!\' al despedirse:

def complicaN(n):

    def complica(cls):

        cls\_complic = cls

        def adios(self):

            print(\'¡Adiós!\' \* n)

        cls\_complic.adios = adios

        return cls\_complic

    return complica

\@complicaN(4)

class Simple:

    def hola(self):

        print(\'¡Hola!\')

a = Simple()

a.hola()

a.adios()

Resultado:

¡Hola!

¡Adiós!¡Adiós!¡Adiós!¡Adiós!
