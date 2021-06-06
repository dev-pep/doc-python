Python

Biblioteca 1:

Built-ins

Resumen

Actualizado: 5/12/2020

Pep Ribal

<pepribal@gmail.com>

*Licencia **Creative Commons Atribución**
*[*C*](https://creativecommons.org/licenses/by/4.0/)[*C-BY*](https://creativecommons.org/licenses/by/4.0/)[*
4.0*](https://creativecommons.org/licenses/by/4.0/)

El presente documento es un resumen de una parte específica de la
biblioteca estándar de *Python* 3.9. Los títulos se han mantenido en
inglés, que es el idioma del documento que se ha utilizado como base
para este resumen.

[]{#anchor}2. BUILT-IN FUNCTIONS
================================

Para los nombres de los argumentos, \'n\' será un número, \'i\' un
entero, \'it\' un iterable, \'ob\' un objeto, \'met\' un método, \'map\'
un *mapping* (diccionario), \'fun\' una función, \'c\' un carácter
(*string* de tamaño 1). Si hay más de uno del mismo tipo, usaremos
sufijos numéricos (\'n1\', \'n2\',...).

abs(n)

Valor absoluto de un entero o punto flotante. En un complejo, devuelve
la magnitud. Para cualquier otro tipo, retorna el valor del método
***x.\_\_abs\_\_()***, si está definido.

all(it)

Retorna ***True*** si todos los elementos de \'it\' se evalúan a
***True***, o si el iterable está vacío.

any(it)

Retorna ***True*** si algún elemento de \'it\' se evalúa a ***True***.
Si el iterable está vacío, retorna ***False***.

ascii(ob)

Retorna una representación imprimible del objeto \'ob\'. Como
***repr()***, pero utilizando *escapes* para los caracteres no-ASCII.
Usa escapes del tipo ***\\x***, ***\\u*** y ***\\U***.

bin(n)

Retorna un *string* con el número \'n\' en binario, precedido por
\'0b\'. Si \'n\' no es ***int***, debe implementar
***\_\_index\_\_()***, que devuelva un entero.

class bool(\[ob\])

Retorna el *truth value* del objeto (***True*** o ***False***). Si no se
pasa argumento, devuelve ***False***.

breakpoint(\*args, \*\*kwargs)

Representa un *breakpoint* y pasa el control al *debugger*, pasándole
los parámetros en \'args\' y \'kwargs\'.

class bytearray(\[source \[,encoding \[,errors\]\]\])

Crea un objeto de tipo *bytearray* (secuencia mutable de *bytes*).

Si \'source\' es un *string*, le debemos dar el \'encoding\' deseado, y
si es así, le podemos definir también \'errors\'. El *encoding* debe ser
un codec válido (\'ascii\', \'utf-8\', etc.), mientras que \'errors\'
debe ser un *string* que indica qué hacer en caso de encontrar un error
en los datos de entrada (ver explicación de ***open()***).

Si \'source\' es un entero, definirá la longitud del *bytearray*, que
será inicializado con caracteres nulos.

Si es un iterable, debe contener enteros de 0 a 255.

Sin argumentos, crea un *bytearray* de longitud 0.

class bytes(\[source \[,encoding \[,errors\]\]\])

Como ***bytearray****()***, pero para crear un objeto *bytes*
(inmutable). En este caso los objetos *bytes* también se pueden crear
con literales.

callable(ob)

Retorna ***True*** si el objeto es llamable, o ***False*** de lo
contrario. Las clases son llamables, y las instancias lo son si tienen
método ***\_\_call\_\_()***.

chr(i)

Devuelve un *string* de longitud 1 con el carácter cuyo *code point*
Unicode es \'i\'. El argumento puede tener valor desde 0 hasta 0x10FFFF.

classmethod(met)\
\@classmethod

Supongamos un objeto ***x*** de clase ***MiClase***, con un método
***met****(self)***. Un método normal tiene acceso a los atributos de la
instancia a través de \'self\'. A parte, puede, si lo desea, cambiar
atributos de la clase mediante cosas como ***type(self).atrib***, o con
el nombre explícito de la clase ***MiClase.atrib***, etc. Para llamar al
método, lo haremos mediante ***x.met()***, no podemos hacer una llamada
sin referirnos a una instancia concreta: ***MiClase.met()*** daría error
(aunque podríamos hacer ***MiClase.met(x)***, que de hecho equivale a
***x.met()***).

Pero quizá nos podría interesar un método relacionado con la clase que
no estuviese ligado a ninguna instancia. Eso es un método clase (*class
method*). Para eso tenemos ***classmethod()***. Podemos definir un
*class method* así:

class MiClase:

    def metclas(cls)

        \# código del método

    metclas = classmethod(metclas)    \# conversión a class method

Como eso es un poco feo sintácticamente, la función ***classmethod()***
permite la sintaxis de *decorator*, mucho más agradable a la vista:

class MiClase:

    \@classmethod

    def metclas(cls)

        \# código del método

Ahora, a este método *Python* le pasa automáticamente *como primer
argumento*, en lugar de una referencia al objeto instancia, una
referencia al objeto clase (***MiClase***), que se recoge, por convenio,
en un argumento que llamaremos \'cls\'. Se pueden definir otros
argumentos a continuación.

Así, este método no tiene ningún acceso a ninguna instancia de la clase,
pero sí puede cambiar los atributos de la clase, que ha recibido en
\'cls\'.

Este tipo de métodos se pueden llamar a través de una instancia
(***x.metclas()***) o a través de la misma clase
(***MiClase.metclas()***), no hay diferencia alguna, el acceso a la
instancia es siempre inexistente.

compile()

Compila un *string* con código, y retorna un objeto ejecutable. No es de
mucho interés por el momento.

class complex(\[real \[,imag\]\])

Retorna un objeto complejo, inicializado a los valores real e imaginario
especificados. También le podemos pasar un *string* u objeto (1 solo
argumento) para su conversión a complejo. Si es *string*, debe ser del
tipo \'3+5j\', sin espacios.

Si se omiten los argumentos, el constructor creará el ***0j***. Si se
omite la parte imaginaria, esta será ***0j***.

Al convertir un objeto \'x\' a complejo, se utilizará
***x.\_\_complex\_\_()***. Si no está definido, ***x.\_\_float\_\_()***
y si tampoco, ***x.\_\_index\_\_()***.

delattr(ob, name)

Elimina el atributo con nombre \'name\' (*string*) del objeto \'ob\' (si
el objeto lo permite). ***delattr(x,\'atrib\')*** equivale a ***del
****x****.a****trib***.

class dict(\*\*kwargs)\
class dict(map \[,\*\*kwargs\])\
class dict(it \[,\*\*kwargs\])

Constructor de un diccionario. Si se pasa sin argumentos, crea un
diccionario vacío. Si le pasamos un iterable, este debe contener
elementos que a su vez contengan 2 elementos cada uno: el primero para
la clave (debe ser inmutable) y el segundo para el valor.

Los elementos creados a partir de *keyword arguments* se crean con la
clave igual a un *string* con el nombre del argumento.

dir(\[ob\])

Sin argumentos devuelve la lista de nombres del *scope* actual. Con un
objeto como parámetro, retorna la lista de sus atributos.

Si el objeto implementa ***\_\_dir\_\_()***, se usará lo que retorne
esta. De lo contrario se intentará construir esa lista a partir del
atributo ***\_\_dict\_\_***, lo cual puede no ser muy preciso si el
objeto implementa ***\_\_getattr\_\_()***.

divmod(n1,n2)

Devuelve una tupla con el cociente y el resto de la división de los dos
números (no complejos).

enumerate(it, start=0)

Retorna un iterable de tipo ***enumerate***, cuyos elementos son tuplas
de dos elementos: el primero, el número de orden (empezando por el valor
de \'start\'), y el segundo, el valor de cada uno de los elementos del
iterable \'it\'.

eval(expression \[, globals \[, locals \]\])

Evalúa un *string* con una *expresión*, o un objeto de código (compilado
con ***compile()***). Se le puede dar un diccionario con las variables
globales que tendrá esa expresión. También se le puede dar un
diccionario de variables locales. Si se omiten, se usará el diccionario
de globales y locales del *scope* desde el que se llama a ***eval()***.
En ningún caso tendrá esta función acceso a las variables de los
*nesting scopes* que no sean globales (las *non-local*).

Si se indica únicamente \'globals\', entonces \'locals\' será igual a
\'globals\'.

Si en \'globals\' no se hace referencia a los *builtins*, se añadirá una
entrada automáticamente, para que siempre exista referencia a estos.

Retorna el resultado de la expresión.

exec(code \[, globals \[, locals \]\])

Similar a ***eval()***, pero en este caso para ejecutar una serie de
instrucciones especificadas en un *string*, o en un objeto de código
(compilado con ***compile()***).

Retorna ***None***.

filter(fun,it)

Retorna un iterable con los elementos de \'it\' tales que ***fun(it)***
es ***True***. Si \'fun\' es ***None***, se asume la función identidad,
es decir, el iterador resultante estará formado por los elementos de
\'it\' que evalúen a ***True***.

class float(\[ob\])

Construye (y retorna) un número en punto flotante a partir del objeto
que se le pasa, que normalmente será un número. También puede ser un
*string* con una representación correcta de un número en punto flotante.
También puede ser \'nan\', \'inf\' o \'infinity\' (*case insensitive*),
este último con signo opcional. El número puede ir rodeado de espacio en
blanco. El número del *string* puede contener guiones bajos para agrupar
dígitos.

Si el objeto no define ***\_\_float\_\_()***, *falls back to*
***\_\_index\_\_()***.

Si no se le da argumento, retorna 0.0.

format(value \[,format\_spec\])

Retorna una representación en *string* del valor indicado, formateado
según la *format string* \'format\_spec\'. Normalmente debería seguir la
especificación de formato estándar (ver *format specification*), aunque
el objeto puede definir su propia especificación. ***format(a,form)***
se traduce a ***type(a).\_\_format\_\_(form)***, *bypassing* el método
de la instancia, si lo hubiere.

Si no se indica el *string* de formato, se asumirá un *string* vacío,
que tiene el mismo efecto que llamar a ***str()***.

Si en la búsqueda del método se llega hasta ***object*** y el *string*
de formato no es un *string* vacío, se levanta excepción
***TypeError***.

class frozenset(\[it\])

Construye y devuelve un *frozenset*. Si se le pasa un iterable, lo hará
con sus elementos. Si no, será un conjunto vacío. Inmutable.

getattr(ob, name \[,default\])

Retorna el atributo con nombre \'name\' (*string*) del objeto \'ob\'.
***getattr(x,\'atri\')*** equivale a ***x.atri***. Si no existe tal
atributo, se levanta ***AttributeError***, a no ser que le indiquemos
\'default\', en cuyo caso simplemente retornará ese valor. Contraparte
de ***setattr()***.

globals()

Retorna el diccionario global de símbolos. Corresponde al módulo actual.
En una función o método, el módulo donde está definida/o, no el módulo
que llama.

hasattr(ob,name)

Retorna ***True*** si el objeto \'ob\' tiene un atributo llamado como el
*string* \'name\'. Simplemente comprueba si hay excepción llamando a
***getattr(ob,name)***.

hash(ob)

Retorna el *hash* del objeto. Si este tiene un método personalizado
***\_\_hash\_\_()***, la función ***hash()*** trunca el valor recibido
al número de bits de la arquitectura.

help(\[ob\])

Para uso interactivo. Invoca el sistema de ayuda.

hex(i)

Retorna una representación hexadecimal (*string*) en minúsculas,
precedida por \'0x\', del entero indicado. Si no es un ***int***, debe
implementar ***\_\_index\_\_()***.

id(ob)

Retorna la identidad única del objeto (en la implementación estándar es
la dirección de memoria, pero podría ser cualquier otro número único).

input(\[prompt\])

Si especificamos un *prompt*, lo imprime en la salida estándar. La
función lee un valor de la entrada estándar, y lo retorna como *string*,
sin el *newline* final. Si se lee *EOF* (cuando la entrada proviene de
un archivo, por ejemplo), se levanta ***EOFException***.

class int(\[x \[,base=10\]\])

Construye y retorna un entero (***int***) a partir del objeto \'x\', que
normalmente será un número o *string*. Si no se especifica, retornará 0.

Si el objeto define ***\_\_int\_\_()***, lo usara esta función para
construir el entero. Si no está definida, usará ***\_\_index\_\_()***;
si tampoco, usará ***\_\_trunc\_\_()***. Si tampoco, se levanta
***TypeError***.

Si especificamos la base, el número a convertir a entero debe ser un
*string*, *bytes* o *bytearray*. El valor indicado puede tener signo
opcionalmente, y puede estar rodeado de espacio en blanco.

La base por defecto es 10, pero podemos indicar una base desde 2 hasta
36, mientras que los dígitos \'a\' a \'z\' (o \'A\' a \'Z\') tienen
valor de 10 a 35. Las bases 2, 8 y 16 puede llevar, opcionalmente,
prefijo \'0b\', \'0o\' y \'0x\' (o su versión en mayúscula)
respectivamente. También podemos indicar base 0, que interpretará el
número del *string* con el mismo formato que tiene un literal entero en
el código.

Si en \'base\' damos un objeto no ***int***, debe definir
obligatoriamente ***\_\_index\_\_()***, que se usará para determinar la
base.

El número del *string* puede contener guiones bajos para agrupar
dígitos.

isinstance(ob, class)

Retorna ***True*** si el objeto \'ob\' es una instancia de \'class\', o
de una subclase de \'class\'. Si \'class\' es una tupla de tipos (puede
contener otras tuplas de tipos dentro), retorna ***True*** si \'ob\' es
instancia de alguno de los tipos de la tupla (buscando recursivamente).

En caso contrario, retorna ***False***.

Si \'class\' no es un tipo o tupla de tipos, se levanta ***TypeError***.

issubclass(class1, class2)

Retorna ***True*** si la clase \'class1\' es una subclase de \'class2\'.
Una clase se considera subclase de sí misma. Si \'class2\' es una tupla
de tipos (puede contener otras tuplas de tipos dentro), retorna
***True*** si \'class1\' es subclase de alguno de los tipos de la tupla
(buscando recursivamente).

En caso contrario, retorna ***False***.

Si \'class1\' o \'class2\' no son tipos (o tupla de tipos en caso de
\'class2\'), se levanta ***TypeError***.

iter(ob \[,sentinel\])

Si no especificamos \'sentinel\', la función retorna un iterador a
partir de \'ob\', el cual debe definir ***\_\_iter\_\_()***, o en su
defecto, ***\_\_getitem\_\_()*** con argumentos enteros empezando desde
el 0. Si no es así, la ***función*** levanta ***TypeError***.

Sin embargo, si proporcionamos \'sentinel\', \'ob\' debe ser *callable*.
El iterador creado llamará a \'ob\' sin argumentos cada vez que se
invoque su ***\_\_next\_\_()***. Se terminará la iteración
(***StopIteration***) cuando el valor retornado sea igual a
\'sentinel\'.

len(ob)

Retorna el número de elementos del objeto.

class list(\[it\])

Construye y retorna una lista (mutable), a partir de un iterable. En
caso de no dar argumentos, crea una lista vacía.

locals()

Retorna el diccionario de nombres local.

map(fun, it1 \[,it2,\...\])

Retorna un iterador que aplica la función \'fun\' a cada elemento del
iterable \'it\', y *yields* cada resultado (valor de retorno de
\'fun\').

Esto suponiendo que la función tome 1 solo argumento. Si toma 2, habrá
que pasarle dos iterables a ***map()***, y se van pasando los argumentos
por pares (primero los dos primeros, luego los dos segundos, etc.). El
iterador termina cuando el iterable con menos elementos se ha procesado
por entero.

Para 3 argumentos a \'fun\' se necesitan 3 iterables, y así
sucesivamente.

max(it, \* \[,key\] \[,default\])\
max(arg1, arg2 \[,arg3,\...\], \* \[,key\])

Retorna el valor máximo entre una serie de números.

La primera versión es mediante un solo argumento posicional: un iterable
\'it\', del que se retornará el máximo de sus elementos. Si definimos el
*keyword argument* \'default\', ese será el valor retornado en caso de
que el iterable esté vacío.

En la segunda versión, de dos o más argumentos posicionales, se
retornará el valor del más alto de ellos.

En ambas versiones podemos especificar el *keyword argument* \'key\', al
que le pasaremos una función de un solo argumento, que servirá como
función de ordenación de los valores. Si \'key\' es ***None***, se
evaluarán los objetos por su valor numérico simplemente.

class memoryview(ob)

Crea un objeto memory view a partir del objeto pasado.

min(it, \* \[,key\] \[,default\])\
min(arg1, arg2 \[,arg3,\...\], \* \[,key\])

Igual que ***max()***, pero para el menor de los valores.

next(it \[,default\])

Retorna el siguiente valor del iterador \'it\'. Si se ha agotado este,
se levantará ***StopIteration***, a no ser que indiquemos \'default\',
en cuyo caso, simplemente retornará ese valor.

class object()

Construye y retorna un objeto de tipo object, que es la clase base de
todos los objetos de *Python*. No acepta argumentos. Estos objetos no
tienen ***\_\_dict\_\_***, con lo que no se le pueden añadir atributos.

oct(n)

Retorna un *string* con una representación octal del número \'n\',
precedida por \'0o\'. El argumento debe ser entero, de lo contario debe
implementar ***\_\_index\_\_()***.

open(file, mode='r', buffering=-1, encoding=None,\
     errors=None, newline=None, closefd=True, opener=None)

Abre el archivo y devuelve un objeto archivo. Si no puede abrirlo, se
levanta ***OSError***.

\'file\' puede ser una ruta (absoluta o relativa al directorio, *string*
u objeto *path*) o un descriptor de archivo ya existente. En este último
caso, ese archivo será cerrado cuando cerremos el *file object*, a no
ser que \'closefd\' sea ***False***.

\'mode\' indica el modo de apertura:

-   ***r*** (por defecto) - solo lectura. Debe existir.
-   ***w*** - escritura. Si existe, lo trunca. No se puede leer.
-   ***x*** - creación exclusivamente. Lo crea si no existe. Si ya
    existe, falla. No se puede leer.
-   ***a*** - *append*: solo se puede añadir al final. Lo crea si no
    existe. No se puede leer.

Para lectura+escritura, se añade ***+***. En este caso, ***r+*** no
trunca el archivo, el cual debe existir, mientras que ***w+*** lo trunca
si existe (lo crea si no). Con ***a+***, aunque nos desplacemos para
leer, solo se escribirá al final. En el caso de ***x+***, también falla
si el archivo existe previamente.

En cuanto al modo texto/binario por defecto es texto, con lo que no es
necesario incluir ***t***. Si lo queremos binario, sí hay que añadir
***b*** (en cualquier punto del *string* de modo). Cuando el archivo es
binario, trabajaremos con *bytes*, mientras que cuando es de texto lo
haremos con *strings*. El funcionamiento es independiente del
tratamiento que la plataforma le de a los archivos de texto.

En cuanto a \'buffering\', si le pasamos 0 desactiva el *buffering* (no
permitido en archivos de texto), 1 para *buffering* de líneas (solo
archivos de texto), un número \>1 es el tamaño en *bytes* del *buffer*.
Si no se le pasa este argumento, *Python* decide la política de
*buffering*.

\'encoding\' es el códec utilizado para la codificación del texto, y
solo funciona en archivos de texto. El valor por defecto depende de la
plataforma. En cuanto a la codificación de texto, podemos indicarle a
Python cómo proceder con los errores encontrados a la hora de codificar
(argumento \'errors\'):

-   \'strict\' levanta ***ValueError*** si hay un error (por defecto).
-   \'ignore\' ignora errores (pérdida de datos).
-   \'replace\' remplaza el error con \'?\'.
-   \'surrogateescape\' representa los *bytes* incorrectos como una
    secuencia que indica un *code point* Unicode, desde \'U+DC80\' a
    \'U+DCFF\'. Es útil cuando tratamos un archivo del que no conocemos
    el encoding.
-   \'xmlcharrefreplace\' representa los caracteres no soportados
    mediante una referencia XML del tipo \'&\#nnn;\'.
-   \'backslashreplace\' remplaza los datos incorrectos con secuencias
    de escape tipo \'\\xhh\'.
-   \'namereplace\' remplaza con secuencias de escape del tipo
    \'\\N{\...}\'.

\'newline\' controla el funcionamiento de los caracteres *newline* (solo
archivos de texto):

-   None - reconoce cualquier tipo de *newline*, independientemente de
    la plataforma. Se traduce internamente a \'\\n\' al leer. Al
    escribir, se escribirá según el *newline* de la plataforma.
-   \'\' - También reconoce cualquier tipo de *newline*, pero al leer o
    escribir no se traduce, con lo que lo guarda en memoria tal como
    está en el archivo.
-   \'\\n\', \'\\r\' o \'\\r\\n\' - solo reconoce le tipo específico de
    *newline*, y no lo traduce al leer o escribir. Además, cualquier
    carácter \'\\n\' que hubiera que escribir, se traduce al *newline*
    de la plataforma.

\'opener\' es un *callable* que podemos especificar para que se encargue
de abrir el archivo. Obviamos los detalles.

ord(c)

Inversa de ***chr()***. Dado un carácter, devuelve el *code point
Unicode*.

pow(base, exp \[, mod\])

Retorna \'base\' elevado a \'exp\'. Si también indicamos \'mod\' le
aplica además el módulo indicado (más eficiente que
***pow(****base,exp)%mod***). Con dos argumentos equivale a
***base\*\*exp***.

Argumentos deben ser numéricos. Si son enteros, el resultado es entero,
a no ser que \'exp\' sea negativo, en cuyo caso será de punto flotante.
Si \'base\' y \'exp\' son enteros, \'mod\' también debe serlo (y
distinto de 0).

print(\*objects,sep=' ',end='\\n',file=sys.stdout,flush=False)

Muestra en el *stream* de texto definido en \'file\' (por defecto la
salida estándar) los objetos indicados, separados por lo que indica
\'sep\' (por defecto, un espacio). Al final añade lo indicado en \'end\'
(por defecto, *newline*). Si \'sep\' o \'end\' son ***None***, usarán el
valor por defecto.

Los objetos se convierten a *string* como haría ***str()***. Si no se
especifican objetos, solo se imprimirá \'end\'.

Si \'file\' es ***None***, tomará el valor por defecto. No puede ser un
archivo en modo binario.

Si el *stream* es *buffered*, se realiza un *flush* si \'flush\' es
***True***.

class property(fget=None,fset=None,fdel=None,doc=None)

Crea un objeto propiedad. Una propiedad representa un atributo, y puede
contener todas las funciones necesarias para *get*, *set* y *delete*,
así como su *docstring*. Al inicializar la propiedad se le dan estos
elementos como argumentos, respectivamente en \'fget\', \'fset\',
\'fdel\' y \'doc\'.

class C:

    def \_\_init\_\_(self):

        self.\_x = None

    def getx(self):

        return self.\_x

    def setx(self, value):

        self.\_x = value

    def delx(self):

        del self.\_x

    x = property(getx, setx, delx, \"I\'m the \'x\' property.\")

En este caso, controlamos cómo vamos a acceder y manipular el atributo
\'x\'. Si no definimos el docstring del atributo propiedad, utilizará el
docstring del getter (si lo hay). De esta forma es muy sencillo hacer
atributos de solo lectura usando un decorador:

class Parrot:

    def \_\_init\_\_(self):

        self.\_voltage = 100000

    \@property

    def voltage(self):

        \"\"\"Get the current voltage.\"\"\"

        return self.\_voltage

En este caso, se aplica ***voltage = property(voltage)***, es decir,
\'voltage\' deja de ser un método *callable*, y pasa a ser una
propiedad, que tiene un método ***fget()***.

Adicionalmente, una propiedad posee unos métodos ***getter()***,
***setter()*** y ***deleter()*** que se pueden usar como decoradores
para añadir los métodos ***fget()***, ***fset()*** y ***fdel()*** a la
propiedad, respectivamente:

class C:

    def \_\_init\_\_(self):

        self.\_x = None

    \@property

    def x(self):

        \"\"\"I\'m the \'x\' property.\"\"\"

        return self.\_x

    \@x.setter

    def x(self, value):

        self.\_x = value

    \@x.deleter

    def x(self):

        del self.\_x

En este caso, tras crearse la propiedad de solo lectura (\'x\'), se
realiza la llamada ***x = x.setter(x)***, es decir, al miembro \'x\' se
le asigna lo que retorne ***x.setter()***, al que se le pasa la función
que estamos definiendo; lo que hace ***setter()*** es añadir a la
propiedad \'x\' el método ***fset()*** que será igual a esta nueva
definición que le pasamos, con lo cual la propiedad deja de ser de solo
lectura. Luego añadimos ***fdel()*** a la propiedad, llamando a
***x.deleter()***. Si lo hacemos así, se les debe dar a las funciones el
mismo nombre que la propiedad.

class range(stop)\
class range(start,stop\[,step\])

Construye y retorna un objeto *range* (secuencia inmutable).

repr(ob)

Retorna un *string* con una representación imprimible del objeto. La
idea es que ese *string* sea una expresión válida que, por ejemplo,
pasada a ***eval()*** produzca un objeto del mismo valor. En su defecto,
el tipo del objeto entre llaves ***\<\>*** junto con otra información
del mismo, que puede incluir su nombre y dirección de memoria.

Un objeto puede controlar lo que devolverá esta función sobre él
definiendo ***\_\_repr\_\_()***.

reversed(secuencia)

Retorna un iterador que va entregando los valores de \'secuencia\' en
sentido inverso. La secuencia debe implementar ***\_\_reversed\_\_()***,
o en su defecto soportar el protocolo de secuencia (método
***\_\_len\_\_()*** y ***\_\_getitem\_\_()*** con argumento entero
iniciando en 0).

round(n\[,ndigits\])

Retorna un redondeo de \'n\' a \'ndigits\' dígitos decimales. Si no
damos \'ndigits\' (o es ***None***), redondeará al entero más cercano y
el tipo retornado será entero. En caso contrario, el tipo será el mismo
que el de \'n\'.

En caso de duda, se redondea al número par más cercano:

\>\>\> round(0.5) == round (-0.5) == 0

True

\>\>\> round(1.5) == round (2.5) == 2

True

\>\>\> round(0.50001)

1

En los demás casos, el 5 se redondea hacia arriba:

\>\>\> round(0.65, 1)

0.7

\>\>\> round(-0.65, 1)

-0.7

En general, el valor retornado es el que define el método
***\_\_round\_\_()*** del objeto a redondear.

Algunos números en punto flotante pueden producir resultados extraños.
Por ejemplo:

\>\>\> round(2.675, 2)

2.67

Esto se debe a que la mayoría de fracciones decimales no pueden
representarse *exactamente* como *float*, por sus limitaciones.

class set(\[it\])

Construye y retorna un *set*. Si se le da un iterable, lo hará con los
elementos de este.

setattr(ob,name,value)

Contraparte de ***getattr()***. Da el valor \'value\' al atributo del
objeto \'ob\' cuyo nombre está especificado por el *string* \'name\' (si
el objeto lo permite). ***setattr(x,\'atrib\',val)*** equivale a
***x.atrib=val***.

class slice(stop)\
class slice(start,stop\[,step\])

Crea y retorna un objeto *slice*, que especifica un conjunto de índices.
Su sintaxis es como la de ***range()***, pero un objeto *slice* no es
iterable.

sorted(it,\*,key=None,reverse=False)

Retorna una lista con los elementos del iterable \'it\' ordenados según
la función especificada en \'key\' (que debe tomar un argumento y
retornar un valor). Si no se especifica función, se ordenará en función
del valor de cada elemento. En caso de que \'reverse\' sea ***True***,
el orden será inverso.

La función garantiza que la posición relativa de dos elementos que se
comparan igual no será cambiada respecto al orden original.

staticmethod(met)\
\@staticmethod

Análogo a ***classmethod()***, aunque en esta ocasión se crea un método
estático. También acepta sintaxis de *decorator*:

class MiClase:

    \@staticmethod

    def metstat()

        \# código del método

En este caso, *Python* no le pasa ningún argumento, solamente los que le
definamos nosotros. Esta función no tiene absolutamente nada que ver con
la clase ni con ninguna instancia de la misma. Es simplemente una manera
de encapsularla en un *namespace* de una clase o instancia en su *scope*
correspondiente. Se puede llamar, al igual que un método clase, a través
de una instancia (***x.metstat()***) o de la clase
(***MiClase.metstat()***), sin que haya ninguna diferencia hacerlo de
una forma o de otra.

class str(object=\'\')\
class str(object=b\'\',encoding=\'utf-8\',errors=\'strict\')

Construye y retorna un *string*.

sum(it,/,start=0)

Suma el valor \'start\' más el valor de los elementos del iterable
\'it\', y retorna esa suma.

super(\[type\[,object-or-type\]\])

Retorna un objeto de tipo *super*, que es un objeto *proxy* a través del
cual podemos acceder a métodos (y atributos de datos). Este objeto
contiene información sobre una instancia concreta (a la que está
*bound*), y una clase que forma parte de la jerarquía de esa instancia.
Lo interesante es que esa clase no tiene por qué ser la clase inmediata
de la instancia, sino que puede estar más arriba en la jerarquía (tan
arriba como queramos).

De ese modo, es posible acceder a la versión de un método *overriden*
que esté hacia arriba en la jerarquía. No importa lo lejos que esté:
podemos acceder a cualquier versión jerarquía arriba.

El objeto *super* entonces se puede utilizar para invocar un método
(***super().metodo***) o acceder a un atributo (***super().x***). Cuando
este objeto *super* tiene información de una clase y una instancia,
estas llamadas se realizan como método, a través de instancia. Pero en
algunas ocasiones este objeto no tiene información de instancia, solo de
clase, con lo que la llamada se realiza como si se hiciese a través de
la clase.

El argumento \'object-or-type\' es el que marca el *method resolution
order* (*MRO*) que se va a utilizar para la invocación (recordemos que
iba hacia arriba, y en caso de herencia múltiple, de izquierda a
derecha). Si este argumento es una clase, el orden de búsqueda queda
definido por el atributo ***\_\_mro\_\_*** de dicha clase. Si es una
instancia, por el ***\_\_mro\_\_*** de la clase a la que pertenece. En
la lista que contiene ***\_\_mro\_\_*** debe aparecer el tipo definido
por el primer argumento \'type\', ya que *la búsqueda se va a iniciar en
la clase inmediatamente siguiente a \'type\' en ese MRO*. Por lo tanto,
*\'object-or-type\' debe ser una instancia o subclase de \'type\'*.

El primer uso de esta función, y el más *straightforward*, es
***super()***, sin argumentos. En este caso, \'object-or-type\' es la
instancia a través de la cual estamos llamando a ***super()***, y
\'type\' es la clase donde está definido el método que está haciendo esa
llamada a ***super()***, que será una clase en algún punto de la
jerarquía de la instancia. Así se iniciará la búsqueda en la siguiente
clase a esa, en el orden *MRO*.

Esto significa que cuando no hay herencia múltiple en la jerarquía, es
una simple referencia a la clase base inmediata, un mecanismo típico en
otros lenguajes. Se utiliza para referenciar la clase base sin tener que
escribir su nombre. Así, si cambiamos el nombre de esa clase base, no
tendremos que ir cambiando sus referencias en el código una a una. Un
caso típico es la llamada a ***super().\_\_init\_\_()*** desde el mismo
***\_\_init\_\_()*** de la derivada. En este caso, al contener el objeto
*super* información sobre el objeto instancia (instancia actual), la
llamada se hace como método.

Sin embargo, en el caso de herencia múltiple, ***super()*** sin
argumentos nos enviará a una clase hermana, si llamamos desde un nivel
con varias clases base.

Si el segundo argumento es una subclase del primero en lugar de una
instancia, la llamada a un método o atributo se hará a través de clase,
no a través de instancia, por lo que será una llamada a función, no a
método. Lo mismo sucede si indicamos el primer argumento pero no el
segundo. La llamada sin argumentos solo puede hacerse desde dentro de
una clase. En cambio, con argumentos, se puede utilizar desde cualquier
lado. Este tipo de llamada puede ser útil para invocar un *static
method*, pudiendo elegir la versión que queramos del método, dentro de
la jerarquía.

class tuple(\[it\])

Construye y retorna una tupla. Si no se le especifica parámetro, resulta
una tupla vacía. Si se especifica un iterador, se toman los datos del
mismo para construir la tupla.

class type(ob)\
class type(name,bases,dict)

Pasándole un objeto, retorna el tipo del mismo, que es en sí un objeto
tipo, y suele ser el devuelto por ***ob.\_\_class\_\_***.

A veces es mejor ***isinstance()*** para comprobar si un objeto es
instancia de un tipo concreto, ya que esta función tiene en cuenta las
subclases.

Con tres argumentos, crea un objeto tipo. Es una forma dinámica de crear
una clase. De este modo, se puede diseñar dicha clase en *run time*.
\'name\' es el nombre de la clase (***\_\_name\_\_***), \'bases\' debe
ser una tupla, con las clases base de la clase (***\_\_bases\_\_***), y
\'dict\' es un diccionario con los atributos (***\_\_dict\_\_***).

Este código:

\>\>\> class X:

\...     a = 1

Equivale a:

\>\>\> X = type(\'X\', (object,), dict(a=1))

vars(\[ob\])

Retorna el atributo ***\_\_dict\_\_*** del objeto \'ob\'. Sin argumento
es como ***locals()***.

zip(\*it)

Crea un iterador. Cada n-ésimo elemento de dicho iterador es una tupla
compuesta por el n-ésimo elemento del cada uno de los iterables que se
le han dado como argumento, en el orden en que se especifican.

El iterador termina cuando el iterable más corto se ha usado por
completo.

Con un solo argumento retorna un iterador con tuplas de 1 elemento cada
una.

Sin argumentos retorna un iterador vacío.

\_\_import\_\_(name,globals=None,locals=None,fromlist=(),level=0)

Es la función que utiliza ***import***. No es aconsejable cambiarla.

[]{#anchor-1}3. BUILT-IN CONSTANTS
==================================

***True*** y ***False*** (***bool***).

***None*** (***NoneType***).

***NotImplemented*** (***NotImplementedType***).

***Ellipsis*** (o ***\...***). Usado mayoritariamente junto con con
sintaxis extendida de *slicing* para crear tipos de contenedores
personalizados.

***\_\_debug\_\_*** es ***True*** si el intérprete no se ha iniciado con
la opción de línea de comandos de optimización -O.

[]{#anchor-2}3.1 Constants added by the site module
---------------------------------------------------

Este módulo es útil en el modo interactivo del intérprete, y no se
debería usar en programas.

quit(code=None)\
exit(code=None)

Al imprimir estos objetos muestran \'Use quit() or Ctrl-D (i.e. EOF) to
exit\'. Al llamarlos levantan ***SystemExit*** con el código
especificado.

copyright\
credits

Al imprimirlos o llamarlos dan información de copyright y de créditos
respectivamente.

license

Al ser impreso muestra \'Type license() to see the full license text\' y
al ser llamado muestra el texto íntegro de la licencia.

[]{#anchor-3}5. BUILT-IN EXCEPTIONS
===================================

BaseException

SystemExit

KeyboardInterrupt

GeneratorExit

Exception

StopIteration

StopAsyncIteration

ArithmeticError

    FloatingPointError

    OverflowError

    ZeroDivisionError

AssertionError

AttributeError

BufferError

EOFError

ImportError

    ModuleNotFoundError

LookupError

    IndexError

    KeyError

MemoryError

NameError

    UnboundLocalError

OSError

    BlockingIOError

    ChildProcessError

    ConnectionError

        BrokenPipeError

        ConnectionAbortedError

        ConnectionRefusedError

        ConnectionResetError

    FileExistsError

    FileNotFoundError

    InterruptedError

    IsADirectoryError

    NotADirectoryError

    PermissionError

    ProcessLookupError

    TimeoutError

ReferenceError

RuntimeError

    NotImplementedError

    RecursionError

SyntaxError

    IndentationError

        TabError

SystemError

TypeError

ValueError

    UnicodeError

        UnicodeDecodeError

        UnicodeEncodeError

        UnicodeTranslateError

Warning

    DeprecationWarning

    PendingDeprecationWarning

    RuntimeWarning

    SyntaxWarning

    UserWarning

    FutureWarning

    ImportWarning

    UnicodeWarning

    BytesWarning

    ResourceWarning
