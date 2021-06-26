# Crear extensiones en *C*

El presente documento es un resumen de algunos capítulos del documento *Extending and Embedding Python*, del que se han resumido solo los aspectos básicos para crear un módulo *Python* en lenguaje compilado *C*. Los títulos se han dejado tal cual, sin traducir del inglés. Se ha ampliado el resumen con algunos información del documento *The Python/C API*. Ambos documentos pertenecen a la documentación oficial de *Python*.

Lo explicado aquí solo sirve para la implementación oficial (*CPython*). Para un proyecto más portable a otras implementaciones, en lugar de crear extensiones se puede utilizar la biblioteca ***ctypes*** o equivalente.

Nos centraremos en el aspecto más habitual: la creación de extensiones (módulos) de *Python* utilizando el lenguaje *C*. Los módulos creados con las técnicas que explicaremos aquí pueden definir nuevas funciones y nuevos tipos de objetos (incluyendo sus propios métodos), pueden llamar a funciones de la biblioteca estándar de *C*, y hacer *system calls*. Y lo más importante, las funciones del módulo serán mucho más rápidas que si las desarrollamos en *Python*.

La otra opción es incrustar (*embed*) *Python* dentro de otra aplicación. Por ejemplo, crear una aplicación con *C++* que tenga incrustado el *runtime CPython*, con lo que podrá hacer uso de las funciones de *Python*. Sin embargo, ignoraremos esta parte y nos centraremos en la creación de módulos.

## 2. CREATING EXTENSIONS WITHOUT THIRD PARTY TOOLS

### 2.1 Extending Python with C or C++

En el archivo fuente en *C* de una extensión (módulo) para *Python* se debe incluir:

```c
#include <Python.h>
```

Para que tal archivo de cabecera esté disponible en nuestro sistema, es posible que haya que instalar algún paquete **de desarrollo** de *Python*. En una distribución *Linux* basada en *Debian* podríamos tener que hacer algo así:

```
sudo apt install python3.9-dev
```

En ese caso, una probable ubicación para ese archivo sería ***/usr/include/python3.9/Python.h***.

Así, podríamos incluirlo directamente como:

```c
#include <python3.9/Python.h>
```

Lo cual funcionaría, pero presentaría un probable problema de portabilidad. Lo mejor es especificar el subdirectorio preciso en el comando de compilación, y así evitar escribir tal subdirectorio (dependiente de la plataforma) en el código fuente.

#### 2.1.1 A Simple Example

Vamos a crear un módulo ***spam*** que contendrá una función `system()` que llamará a la función `system()` de ***stdlib.h***, que recibe un *string* con un comando para el intérprete de comandos del sistema, y retorna un entero.

El **uso** de nuestro módulo debería ser algo así:

```
>>> import spam
>>> spam.system("ls -l")
```

En nuestro módulo, la primera línea debería ser:

```c
#include <Python.h>
```

Se debe incluir esa cabecera **antes** de incluir los *headers* estándar, ya que en algunos sistemas, las definiciones de ***Python.h*** pueden afectar a algunas de esas cabeceras estándar.

Al incluir ***Python.h*** ya quedan incluidos ***stdio.h***, ***string.h***, ***errno.h*** y ***stdlib.h***.

Como se irá viendo, todos los nombres accesibles que proporciona ***Python.h*** empiezan por ***Py*** o ***PY***.

Ahora vamos a incluir la función que será invocada cuando desde *Python* entremos `spam.system("ls -l")`.

```c
static PyObject * spam_system(PyObject *self, PyObject *args)
{
    const char *command;
    int sts;

    if (!PyArg_ParseTuple(args, "s", &command))
        return NULL;
    sts = system(command);
    return PyLong_FromLong(sts);
}
```

En primer lugar, `PyObject*` es el tipo (*C*) base para representar un objeto *Python*. Es el tipo que retorna la función invocada (lógico, ya que es el valor que recogerá *Python*). **Todos** los objetos de *Python* son, de hecho, objetos reservados dinámicamente en memoria. De ahí a usar siempre un apuntador para utilizarlos.

Por otro lado, la función `spam_system()`, que será invocada por *Python* (ya veremos cómo), recibe siempre dos argumentos: el primero (***self*** por convenio) es un apuntador al módulo llamador (si la función correspondiera, por ejemplo, a un método, sería un apuntador a la instancia a la que pertenece).

El segundo argumento (***args*** por convenio) corresponde a un apuntador a un objeto tupla de *Python* con los argumentos de la llamada. Dichos argumentos son objetos *Python*, con lo que deberemos convertirlos a tipos *C*.

Y eso es lo que hace exactamente la función `PyArg_ParseTuple()`:

```c
int PyArg_ParseTuple(PyObject *args, const char *format, ...);
```

Convierte los argumentos de una tupla en objetos de tipos de *C*. Usa una *format string* que especifica los tipos a los que se deben convertir los objetos. En el ejemplo, un solo elemento *string* (***"s"***). Los objetos serán almacenados en las variables cuyas direcciones se pasen a continuación (en este caso, ***command***).

La función retorna un valor no cero si se han convertido con éxito todos los elementos de la tupla, y cero en caso contrario.

Como vemos, si la función ha fallado, retornamos ***NULL***, lo cual es una forma de indicar a *Python* que ha habido un error.

#### 2.1.2 Intermezzo: Errors and Exceptions

Por convenio, si una llamada desde *Python* a nuestra función falla, debe levantar un estado de excepción y retornar un valor de error (normalmente ***NULL***). El estado de error se guarda en una variable global del intérprete. Si el valor de esa variable es ***NULL***, no hay estado de error. Una segunda variable global guarda el valor asociado a la posible excepción. Una tercera variable guarda la pila de *traceback* (solo si la excepción se ha producido en el código *Python*).

Para establecer un estado de error, la función más común de esta *API* es `PyErr_SetString()`, a la que se debe pasar un objeto excepción (por ejemplo ***PyExc_ZeroDivisionError***, existen objetos predefinidos para todos los tipos de excepción) y un *string C*, que se convierte a un tipo *Python* que pasa a ser el valor asociado a la excepción.

Otra función útil es `PyErr_SetFromErrno()`, que solo recibe un objeto excepción y construye el valor asociado según el valor de la variable global ***errno*** (***errno.h***).

La función más general es `PyErr_SetObject()` cuyos dos argumentos son el objeto excepción y el objeto con el valor asociado.

No es necesario incrementar el conteo de referencias (se verá más adelante) a ninguno de los objetos pasados a estas funciones.

Para saber si existe una excepción levantada, `PyErr_Occurred()` retorna el objeto excepción actual (o ***NULL*** si no hay).

Cuando una función llama a otra, que a su vez llama a otra, y esta última encuentra un error, debería, a parte de retornar un valor de indicación de error (por ejemplo ***NULL*** si retorna apuntador, o ***-1*** si retorna enteros) levantar excepción. La función llamante debería, a su vez, retornar indicación de error, pero **NO** levantar excepción; las restantes funciones llamadoras también deberían hacer esto, hasta llegar al intérprete de *Python*. Este es el comportamiento que se debe usar generalmente. Si una función llamante debe obligatoriamente levantar una excepción, se perderá la información de la primera excepción levantada.

Si no deseamos pasar una excepción levantada a *Python* y queremos gestionarla desde *C*, se puede limpiar el estado de excepción con `PyErr_Clear()`.

Si una función que usa `malloc()` o `realloc()` (***stdlib.h***) recibe un error de estas funciones, debería levantar excepción con `PyErr_NoMemory()` y retornar indicación de error. Las funciones de esta *API* que construyen objetos (como p.e. `PyLong_FromLong()`) tienen exactamente este comportamiento.

> Indicar que si bien lo habitual en caso de enteros es retornar -1 en caso de error, y positivo o 0 en caso de éxito, la familia de funciones `PyArg_ParseTuple()` no sigue este esquema.

Si retornamos indicador de error desde una función, todos los objetos creados en esta dejan de ser válidos, con lo que debemos recordar decrementar el conteo de referencias adecuadamente.

Se pueden crear excepciones a medida:

```c
PyObject* PyErr_NewException(const char *name, PyObject *base, PyObject *dict);
```

Esto retorna una referencia a una nueva clase de excepción. Se la pasa un nombre, que debe terner la forma ***modulo.clase***. En nuestro caso, podría ser ***spam.error***, por ejemplo. El atributo ***\_\_module\_\_*** de la nueva clase se establece a lo que está a la izquierda del último punto (***spam***), y su nombre a lo que está a su derecha (***error***).

Si el segundo argumento es ***NULL***, la clase derivará de `Exception`, aunque podemos indicar aquí la clase base. En cuanto a ***dict*** suele ser ***NULL***, o indicar un diccionario de variables y métodos para la clase.

La nueva clase retornada se debe almacenar en una variable global de almacenamiento estático. Así, aunque un código externo elimine la clase del módulo (de su diccionario), nosotros seguiremos teniendo acceso a ella a través de esta variable.

Las variables globales cuyo valor deseemos mantener entre llamadas al módulo, se declararán como `static`, al igual que las funciones que definamos (a excepción de una función, que veremos ahora). Esto es así para asegurarnos que los objetos y funciones no tienen *external linkage*, ya que si fuese así podrían entrar en conflicto con otros elementos de otros módulos. En cambio, con *internal linkage*, *Python* no tendrá acceso a estos elementos globales del módulo.

La única excepción es la función de inicialización del módulo, que sí debe ser accesible desde *Python*, como veremos más adelante.

Por otro lado, a veces puede interesarnos que algún elemento del módulo sea accesible a otros módulos. Para ello, habrá que declarar nuestro elemento (normalmente una función) sin `static`, proporcionar un *header file* con las declaraciones adecuadas, y, sobre todo, enlazar (*link*) nuestro módulo estáticamente recompilando el intérprete. Cuando los módulos están enlazados dinámicamente a través de librerías compartidas, el acceso puede ser más complejo. Pero esta es la práctica habitual. La visibilidad de nombres entre módulos enlazados dinámicamente depende del sistema operativo, por lo que si deseamos ofrecer nombres a otros módulos, deberemos asegurarnos de que son visibles. Para más información, el apartado 2.1.12 da una explicación al respecto.

#### 2.1.3 Back to the Example

Ahora ya sabemos que si `PyArg_ParseTuple()` ha fallado, ha levantado la excepción adecuada, y ha retornado ***NULL***. De lo contrario, tenemos en ***command*** un apuntador a un *string*. No deberíamos modificar su contenido; por eso está declarado como `const char *command`.

Ahora ya podemos invocar a `system()` de ***stdlib.h*** y pasarle ese *string*. Recogemos el valor devuelto por esta función en ***sts***, y retornamos ese entero, previa conversión a un entero de *Python* con `PyLong_FromLong()`, que retorna, como de costumbre, un objeto *Python* (`PyObject*`).

Si queremos retornar el objeto *Python* ***None*** mediante ***Py_None*** (no debemos retornar ***NULL***, ya que eso sería retornar error). Debería hacerse así:

```c
Py_INCREF(Py_None);
return Py_None;
```

Incrementamos el conteo de referencias a ***None*** también (2.1.10).

#### 2.1.4 The Module’s Method Table and Initialization Function

Para que nuestra función sea llamada al realizar la invocación desde *Python*, tenemos que registrarla en la denominada "tabla de métodos" (*method table*) del módulo:

```c
static PyMethodDef SpamMethods[] = {
    {"system", spam_system, METH_VARARGS, "Execute a shell command."},
    /* ... */
    {NULL, NULL, 0, NULL}
};
```

El identificador de la tabla puede ser cualquiera, pero debe ser de tipo `PyMethodDef`.

Para cada función, proporcionamos 4 datos: nombre de la función en el módulo (*string*), identificador de la función, *calling convention*, y una descripción de la función (*docstring*).

El último elemento debe estar a ***NULL*** para marcar el fin de la tabla.

En cuanto a la *calling convention*, lo habitual es ***METH_VARARGS***, que indica que los argumentos de *Python* se pasarán en una tupla que se podrá procesar correctamente mediante `PyArg_ParseTuple()`.

También es posible indicar ***METH_VARARGS | METH_KEYWORDS***. En este caso, la función pasa también *keyword arguments*. En ese caso, nuestra función recibiría desde *Python* un tercer argumento con un `PyObject` que contiene un diccionario de *keywords*.

Si la función no recibe argumentos, se indicará ***METH_NOARGS***.

En ese caso, hay que recoger el valor de los argumentos mediante:

```c
int PyArg_ParseTupleAndKeywords(PyObject *args, PyObject *kw, const char *format, char *keywords[], ...);
```

Aquí, proporcionamos, a parte de ***args*** (argumentos posicionales), el diccionario de argumentos ***kw***, que es el que ha recibido nuestra función. Por otro lado, indicamos el formato de cada uno en ***format***, y la secuencia (orden) de parseo de argumentos en el *array* de *strings* ***keywords***, que es un *array NULL-terminated* con los nombres de los *keyword arguments* en el orden deseado (los nombres vacíos indican parámetro *positional only*). El valor de retorno es como el de `PyArg_ParseTuple()`.

El resto de *calling conventions* disponibles están *deprecated*.

El siguiente paso es inicializar el módulo, que se hará, lógicamente a través de la **función de inicialización** de dicho **módulo**. Para ello necesitaremos proporcionar a esta función toda la información sobre dicho módulo, información que incluiremos en el `struct` de definición del módulo, de tipo `struct PyModuleDef`.

Los campos más importantes de esta estructura son (en orden de definición):

- ***m_base***: contiene siempre el valor ***PyModuleDef_HEAD_INIT*** (de tipo `PyModuleDef_Base`).
- ***m_name***: nombre del módulo (*string*).
- ***m_doc***: *docstring* del módulo (*string*).
- ***m_size***: si se le da un valor (entero) mayor que 0, el estado del módulo se guardará en una zona de memoria propia, en lugar de en las variables globales estáticas. Esto es útil en caso de sub-intérpretes que carguen el módulo, en cuyo caso cada instancia tendrá su propio estado. Sin embargo, le daremos valor ***-1***, indicando que el módulo no soporta sub-intérpretes, y tiene estado global (tipo `Py_ssize_t`, un tipo de entero).
- ***m_methods***: un apuntador a la tabla de métodos (`PyMethodDef*`). Puede retornar ***NULL*** si el método no define funciones.

En cuanto al tipo `Py_ssize_t`: el compilador *C* dispone del tipo `size_t` (***stddef.h*** y otros), que es un tipo *unsigned*, de mínimo 16 *bits*, capaz de albergar **cualquier índice** a memoria. Entonces, en el presente *API*, `Py_ssize_t` es un tipo de entero basado en `size_t`, aunque es *signed*. Para acceder a memoria es mejor utilizar un tipo de datos como `size_t` que cualquier otro tipo de entero, pero en el caso de *Python*, que admite índices negativos, había que usar un tipo con signo. De ahí al uso de `Py_ssize_t`.

```c
static struct PyModuleDef spammodule = {
    PyModuleDef_HEAD_INIT,
    "spam",
    spam_doc,
    -1,
    SpamMethods
};
```

Ahora ya podemos escribir la función de inicialización. Se deberá llamar ***PyInit_\<nombre>***, donde ***nombre*** es el nombre exacto del módulo. En nuestro caso, sería ***PyInit_spam***. La función retorna un valor de tipo `PyMODINIT_FUNC`, y **es el único elemento global que no se declarará como** `static`.

```c
PyMODINIT_FUNC PyInit_spam(void)
{
    return PyModule_Create(&spammodule);
}
```

`PyMODINIT_FUNC` en realidad define un tipo de retorno `PyObject*` con las declaraciones de *linkage* necesarias para que la función sea accesible desde *Python* (por eso no debemos declararla `static`). La función retorna lo retornado por `PyModule_Create()`: el objeto módulo que ha creado. Al retornar el módulo, este queda insertado en ***sys.modules***, listo para su uso.

La función de inicialización es invocada al hacer el `import` desde *Python*.

#### 2.1.5 Compilation and Linkage

Para compilar y enlazar el módulo creado para que sea importable a *Python* tenemos dos opciones: la primera es compilar el código fuente y *linkarlo* con el intérprete. Esto implica volver a compilar el intérprete desde su código fuente, incluyendo nuestros módulos. Ignoraremos esto y nos centraremos en la otra aproximación: la creación de nuestros módulos como librerías compartidas. Se verá más adelante (2.4 y 2.5).

#### 2.1.6 Calling Python Functions from C

A veces nos interesará que nuestra función *C* haga llamadas a funciones *Python*. Es frecuente tener que llamar a una función *callback*. Se podría pensar, como ejemplo, en una función de ordenación hecha en un módulo en *C* que recibe como argumento una función de comparación *Python*. La función de ordenación *C* tendrá que invocar a esa función *Python* de comparación para poder ordenar.

En este caso, para leer desde *C* la referencia a la función que nos llega desde *Python*, usaremos `PyArg_ParseTuple()` como de costumbre, pero en lugar de especificar ***s*** para el formato (*string*), usaremos ***O*** (objeto, sin especificar). Una vez tengamos ese objeto, podemos utilizarlo para hacer esa llamada a la función *Python*:

```c
PyObject_CallObject(my_callback, tupla_args);
```

Tanto ***my_callback*** como ***tupla_args*** son del tipo `PyObject*`, y representan respectivamente la función *callback* y los argumentos (una tupla) a esta (si es ***NULL*** no se pasan argumentos). Retorna un **nuevo** objeto *Python*.

Para pasar *keyword arguments*:

```c
PyObject_CallObject(my_callback, tupla_args, dict_args);
```

Es como la anterior, pero en este caso, además, ***dict_args*** es un diccionario con los *keyword arguments*. Tanto ***dict_args*** como ***tupla_args*** pueden ser ***NULL***. Más tarde veremos cómo construir tipos *Python* (listas, tuplas, diccionarios) con la función `Py_BuildValue()`.

#### 2.1.7 Extracting Parameters in Extension Functions

```c
int PyArg_ParseTuple(PyObject *args, const char *format, ...);
```

La *format string* definirá el tipo de datos esperado de los argumentos de la tupla. Siempre hay que pasar la dirección de la variable que almacenará el valor del argumento en cuestión. Si es un apuntador, la dirección del apuntador.

Cuando extraemos un argumento que precisa de un *buffer*, como un *string*, la función asigna al apuntador correspondiente una dirección de memoria a un *buffer* ya existente, gestionado por el objeto *Python* del que extraemos la información. Dicho *buffer* tiene el mismo tiempo de vida que el objeto, con lo que no necesitamos liberarlo de memoria (con las excepciones de los datos tipo ***es***, ***es#***, ***et*** y ***et#***).

En ocasiones, los datos se leen y rellenan una estructura `Py_buffer`, con un *buffer* en el miembro ***buf***. En ese caso, el *buffer* queda bloqueado para escritura (sobre posibles objetos mutables). Para liberarlo una vez se ha terminado de modificar, se debe llamar a `PyBuffer_Release()` para que otro posible hilo pueda escribir sobre él.

La *format string* está compuesta por unidades de formato, que describen objetos *Python*. Una *format unit* suele ser un solo carácter o una serie de unidades de formato entre paréntesis. Cada unidad proporcionará el valor de un apuntador *C* al tipo adecuado.

Algunas unidades de formato retornan la longitud del argumento (***s#***, ***y#***,...). Tal longitud es un entero, del tipo `int`, aunque es mejor que retorne un tipo `Py_ssize_t`. Para que esto sea así, hay que definir la macro `PY_SSIZE_T_CLEAN` **antes** de incluir ***Python.h***:

```c
#define PY_SSIZE_T_CLEAN
#include <Python.h>
//...
```

Veamos tipos habituales de *format units*:

- ***s*** convierte un *string Unicode* (no *bytes*) de *Python* a tipo `const char*`, que apuntará a un *buffer null-terminated*, codificado con *UTF-8*. El *string Python* no puede contener *codepoints* nulos.
- ***s\**** como ***s***, pero en lugar de establecer la dirección del apuntador *C*, rellena una estructura `Py_buffer` (le pasaremos la dirección de la misma). El *buffer* está en el miembrio ***buf***. En este caso, el *string C* puede contener *bytes* nulos.
- ***s#*** es como ***s\**** pero no acepta objetos mutables. Retorna dos valores: el *string* y la longitud (`int` o `Py_ssize_t`).
- ***z*** es como ***s***, pero el *string Python* puede ser ***None***, en cuyo caso el apuntador *C* será ***NULL***.
- ***z\**** es como ***s\****, pero el *string Python* puede ser ***None***, en cuyo caso el miembro ***buf*** de la estructura será ***NULL***.
- ***z#*** es como ***s#***, pero el *string Python* puede ser ***None***, en cuyo caso el apuntador *C* será ***NULL***.
- ***y*** es como ***s***, pero no acepta *strings Unicode* (solo *bytes*, que no puede contener *bytes* nulos).
- ***y\**** es como ***s\****, pero no acepta *strings Unicode* (solo *bytes*, que sí puede contener *bytes* nulos). **Es la forma recomendada de obtener datos binarios**.
- ***y#*** es como ***s#***, pero no acepta *strings Unicode* (solo *bytes*, que sí puede contener *bytes* nulos).
- ***S*** extrae un objeto *bytes* directamente, sin convertir. El tipo *C* será `PyObject*` o `PyBytesObject*`.
- ***Y*** extrae un objeto *bytearray* directamente, sin convertir. El tipo *C* será `PyObject*` o `PyByteArrayObject*`.
- ***U*** extrae un *string Unicode* directamente, sin convertir. El tipo *C* será `PyObject*`.
- ***w\**** es como ***y\****, pero permite lectura y escritura en el *buffer*. Una vez se ha terminado de modificar el *buffer* se debe desbloquear para escritura con `PyBuffer_Release()`.
- ***es*** variación de ***s*** que permite establecer la codificación del *string*. Necesita dos argumentos: el primero se utiliza como entrada, no salida: es un `const char*` que apunta al nombre de la codificación deseada (debe ser conocida por *Python*); si es ***NULL***, se entenderá *UTF-8*. El segundo es `char**`, es decir, un apuntador a un *buffer* de elementos `char` (se puede ver como un apuntador a un *array* de caracteres). El apuntador que referenciamos parará a apuntar a un *buffer* con el texto solicitado. Para ello, la función reservará la memoria necesaria para el *buffer* (que no es gestionado por el objeto *Python*), con lo que cuando hayamos terminado de trabajar con él deberemos invocar `PyMem_Free()` para liberarlo.
- ***et*** es como ***es***, pero acepta también objetos *bytes* (o *bytearray*), en cuyo caso, el resultado se pasará sin codificar (se asumirá que la codificación del *bytes* o *bytearray* ya es la deseada).
- ***es#*** es como ***es***, pero por un lado permite datos de entrada con caracteres nulos, y por otro lado requiere de un tercer argumento (`int` o `Py_ssize_t`), en el que colocará la longitud del *buffer*. En este caso hay dos modos de funcionamiento:
    - si el apuntador al *buffer* es nulo inicialmente, funcionará como de costumbre;
    - en caso contrario, usará el valor del *buffer* para colocar allí el texto, sin reservar memoria, ya que asumirá que ya hay una cantidad de memoria reservada, igual al valor inicial del tercer argumento.
- ***et#*** es como ***es#***, pero permite objetos *bytes* o *bytearray* (que pasarán sin codificar a la salida).
- Números. En las conversiones a *unsigned* no hay comprobación de *overflow*.
    - ***b*** entero no negativo a `unsigned char`.
    - ***B*** entero a `unsigned char`.
    - ***h*** entero a `short int`.
    - ***H*** entero a `unsigned short int`.
    - ***i*** entero a `int`.
    - ***I*** entero a `unsigned int`.
    - ***l*** entero a `long int`.
    - ***k*** entero a `unsigned long int`.
    - ***L*** entero a `long long int`.
    - ***K*** entero a `unsigned long long int`.
    - ***n*** entero a `Py_ssize_t`.
    - ***c*** objeto *bytes* o *bytearray* de longitud 1 a `char`.
    - ***C*** objeto *string* de longitud 1 a `int`.
    - ***f*** número de punto flotante a `float`.
    - ***d*** número de punto flotante a `double`.
    - ***D*** número complejo a estructura `Py_complex`.
- ***O*** es el objeto tal cual, sin conversión (tipo `PyObject*`).
- ***O!*** es como ***O***, pero en este caso precisa dos argumentos: el primero es de entrada, y es un apuntador a un objeto tipo de *Python*; el segundo es el acostumbrado apuntador a un objeto, que debe ser del tipo especificado en el primer argumento.
- ***p*** convierte un `bool` de *Python* a `int`.

Si en la *format string* se indican paréntesis, dentro de los cuales hay (opcionalmente) *format units*, definimos una secuencia *Python* con los elementos indicados. Pueden anidarse.

Existen otros caracteres (no permitidos dentro de paréntesis):
- ***|*** indica que los argumentos a continuación son opcionales. Si no existen, la función no hace nada con el argumento *C* correspondiente.
- ***$*** indica que el resto de los argumentos son *keyword only* (solo en `PyArg_ParseTupleAndKeywords()`).
- ***:*** indica que la lista de *format units* ha terminado; tras ***:*** se indica el nombre de función que aparecerá en los posibles mensajes de error (valor asociado de la excepción que se levante).
- ***;*** similar a ***:***, pero indica el mensaje de error en lugar del mensaje de error por defecto. ***:*** y ***;*** se excluyen mutuamente.

Los objetos pasados a la función llamadora son *borrowed references* (2.1.10), por lo que no debemos decrementar el conteo de referencias tras usarlos.

En los elementos ***es***, ***et***, etc., hay que asegurarse de pasar el tipo correcto para el *buffer*. En este caso, ***buffer*** un apuntador a `char*`, es decir, un `char**`. Hay que tener cuidado: se debe pasar **la dirección de memoria de ese** `char**`, es decir, un apuntador a `char**`, o sea un `char***`.

Estas funciones retornan verdadero si todo funciona bien, o falso en caso contrario.

#### 2.1.8 Keyword Parameters for Extension Functions

```c
int PyArg_ParseTupleAndKeywords(PyObject *args, PyObject *kw, const char *format, char *keywords[], ...);
```

No se pueden parsear tuplas anidadas cuando usamos *keyword arguments*.

#### 2.1.9 Building Arbitrary Values

```c
PyObject *Py_BuildValue(const char *format, ...);
```

Realiza la acción inversa a las funciones de parseo de tuplas que hemos visto. Utiliza una *format string* similar, pero los argumentos no son apuntadores sino simples valores; y no son de salida, sino de entrada. La función retorna un objeto `PyObject*` que se puede retornar a *Python* perfectamente.

El objeto retornado es una tupla siempre y cuando el número de *format units* sea superior a uno. Si es uno, retorna ese simple valor. Si es cero, retorna ***None***. Si queremos forzar a que retorne una tupla con 0 o 1 elemento, hay que indicar un par de paréntesis en la *format string*.

```c
Py_BuildValue("");                   // None
Py_BuildValue("i", 123);             // 123
Py_BuildValue("iii", 123, 456, 789); // (123, 456, 789)
Py_BuildValue("s", "hello");         // 'hello'
Py_BuildValue("y", "hello");         // b'hello'
Py_BuildValue("ss", "hello", "world");  // ('hello', 'world')
Py_BuildValue("s#", "hello", 4);     // 'hell'
Py_BuildValue("y#", "hello", 4);     // b'hell'
Py_BuildValue("()");                 // ()
Py_BuildValue("(i)", 123);           // (123,)
Py_BuildValue("(ii)", 123, 456);     // (123, 456)
Py_BuildValue("(i,i)", 123, 456);    // (123, 456)
Py_BuildValue("[i,i]", 123, 456);    // [123, 456]
Py_BuildValue("{s:i,s:i}",
      "abc", 123, "def", 456);       // {'abc': 123, 'def': 456}
Py_BuildValue("((ii)(ii)) (ii)",
      1, 2, 3, 4, 5, 6);             // (((1, 2), (3, 4)), (5, 6))
```

#### 2.1.10 Reference Counts

En nuestras funciones *C* debemos preocuparnos de emparejar correctamente nuestros `free()` con nuestros `malloc()` (***stdlib.h***). Pero cuando trabajamos con objetos *Python*, de tipo `PyObject*` la gestión de memoria la realiza el *garbage collector* de *Python*. Para gestionar la memoria reservada para estos objetos, debemos trabajar con dicho *GC*, estableciendo el conteo de referencias a los objetos *Python* correctamente, evitando así *memory leaks* (memoria reservada que no se libera) y uso de memoria liberada.

> Todos los objetos *Python* (de tipo `PyObject*`) tienen un tipo *Python* asociado (objeto de tipo `PyTypeObject`) y una *reference count*. Los objetos `PyObject*` son almacenados en memoria dinámica. Sin embargo los objetos tipo (`PyTypeObject`) no se liberan (suelen ser estáticos).

Al mantener correctamente el conteo de referencias a un objeto *Python*, este es liberado (*deallocated*) cuando el conteo llega a cero. En ese caso, se ejecuta la función de liberación (*deallocator*) del objeto, que es un apuntador a función que reside en la estructura del tipo de objeto. Si el objeto en sí contenía otros objetos, el conteo de referencias a estos objetos que contenía es a su vez decrementado, y si es necesario son liberados. Y así recursivamente.

Para incrementar el conteo de referencias a un objeto *Python* se usa la macro `Py_INCREF()`, a la que se pasa como argumento un apuntador a dicho objeto. Igualmente, para decrementar dicho conteo, se usa `Py_DECREF()` al que se pasa el también el objeto como argumento. Si al decrementar el conteo este llega a cero, se libera la memoria reservada al objeto. Por lo tanto, la liberación de un objeto *Python* solo se produce tras una llamada a `Py_DECREF()`.

Para saber cuándo invocar estas macros, hay que conocer el concepto de **propiedad** (*ownership*): ninguna función es propietaria de un **objeto**, pero sí puede serlo de una **referencia** a un objeto concreto. **Que una función o código posea la propiedad de una referencia significa únicamente una cosa: que es responsable de decrementar el conteo de tal referencia cuando deje de usarla**.

Un error típico que suele suceder con frecuencia es el siguiente: extraer un objeto de una lista, es decir, crear una referencia a dicho objeto mediante una asignación a un apuntador `PyObject*`, olvidándose de incrementar su conteo. Al no haberlo hecho, no adquirimos la responsabilidad de llamar a `Py_DECREF()` cuando no necesitemos más la referencia. Por lo tanto, no nos hemos dado cuenta de que **no somos propietarios de dicha referencia**. Así, si por ejemplo una parte de nuestro código quita ese objeto de la lista, se decrementa su conteo y posiblemente es liberado. Entonces tendríamos una referencia a un elemento liberado.

No se deberían extraer, pues, objetos de una secuencia usando asignaciones que crean referencias manualmente, pues es muy fácil olvidarse de incrementar el conteo. La solución es extraer siempre estos elementos utilizando funciones genéricas del *API*, como las que empiezan por `PyObject_`, `PyNumber_`, `PySequence_` o `PyMapping_`, que incrementan automáticamente el conteo de la referencia obtenida para que el llamador lo decremente cuando no la use más, es decir, proporcionan al código llamante **la propiedad de dicha referencia**.

Cuando una función le pasa la *ownership* de una referencia a su llamador, se dice que el llamador recibe **una nueva referencia**. Por lo tanto, recibir una nueva referencia implica que solo debemos preocuparnos por decrementar el conteo cuando haga falta, y mientras no lo hagamos, el objeto seguirá debidamente *allocated*.

La propiedad de una referencia puede pasarse no solo a través de retornar un objeto, sino también a través del paso del objeto como argumento (aunque no es muy frecuente). Esto se hace cuando la función llamada roba la propiedad de esa referencia al objeto (*stealing the reference*), es decir, la función llamada se convierte en la responsable de llamar a `Py_DECREF()` en el momento que no necesite más la referencia. Si pasamos un objeto a una función así, debemos dejar de considerarnos propietarios de tal referencia, por lo que ya no será necesario decrementar su conteo nunca (ya no tenemos ninguna responsabilidad al respecto).

También se podría pasar una referencia a un objeto como argumento a otra función, sin transferir la propiedad de dicha referencia. En ese caso, seguimos manteniendo la responsabilidad de decrementar el conteo tras el retorno de dicha función. En este caso, la función llamada puede utilizar la referencia al objeto, sin robar (*steal*) la propiedad de la misma. Se dice en este caso que la función llamada toma prestada (*borrows*) la referencia. Puede utilizar el objeto pero no tiene la propiedad de la referencia, con lo que no decrementará nunca el conteo. Sin embargo, para evitar que se libere el objeto mientras usa la referencia, la función llamada debe tener la seguridad de que el objeto no será liberado de memoria durante su ejecución. Para ello no debe trabajar con tal objeto más allá del momento en que el módulo llamador acabe decrementando la referencia (y posiblemente liberando el objeto).

En todo caso, mientras se está ejecutando la función llamada, el llamador no ejecuta nada, con lo que la función puede disponer del objeto tranquilamente hasta que retorne. Pero si la función llamada quiere conservar la referencia más tiempo, por ejemplo hasta la siguiente llamada, si no está seguro de que el llamador no decrementará el conteo, tendrá que convertirse en propietaria de la referencia invocando `Py_INCREF()` y almacenando el objeto en una variable global o estática (no en una variable local, que haría perder la referencia al salir del *scope* local). Esto no afecta al llamador, que sigue siendo responsable de la antigua referencia, y no de esta nueva.

Un caso importante de *borrow* de referencias es una llamada desde *Python* a una función *C* de un módulo, ya que está garantizado que los argumentos que se pasen a dicha función, es decir, la tupla de argumentos, estará referenciada en el código *Python* llamador durante toda la ejecución de la función *C*. Así, no es necesario incrementar el conteo (adquirir la propiedad de la referencia) para asegurarnos de que la tupla no será liberada. Y por supuesto, tampoco hay que decrementar el conteo de la tupla recibida en la función *C*, ya que la función no posee la propiedad de la referencia a la tupla. Otro caso sería si la función deseara guardar alguno de los argumentos recibidos (o todos) en el estado global del módulo, en cuyo caso sí debería convertirse en propietaria de tales objetos.

Cuando pasamos un objeto *Python* como argumento a una función de este *API*, normalmente esta toma prestada dicha referencia; son pocas las funciones que roban la propiedad de la misma (*reference stealing*). Básicamente lo hacen `PyList_SetItem()` y `PyTuple_SetItem()`, que se apropian de la referencia del objeto a añadir (no de la tupla o lista donde se añaden). En este caso no podríamos pasarle por parámetro una referencia que hubiésemos tomado prestada; necesitamos hacernos propietarios de la referencia incrementando el conteo para llamar a la función (que nos robaría tal referencia, y dejaríamos de ser propietarios inmediatamente).

La forma más habitual de crear objetos *Python* es mediante `Py_BuildValue()`. Para establecer elementos en una secuencia, se suele usar `PyObject_SetItem()`, a la que podemos pasar *borrowed references* (porque no roba referencias). Si le pasamos una referencia de nuestra propiedad, seguirá siendo de nuestra propiedad tras la llamada.

Una función que retorna un objeto *Python* es muy frecuente que transfiera también la propiedad de la referencia al llamador. Esto debe ser así en muchas ocasiones, ya que supongamos que el objeto es creado durante la ejecución de la función llamada; retornar una *borrowed reference* implicaría que el código llamante no es responsable de decrementar el conteo de un objeto cuya única referencia es la que ha recibido dicho código llamante. Por lo tanto, este objeto nunca será liberado por nadie. Todas las funciones que **crean y retornan** un objeto *Python* transfieren también la propiedad de la referencia al código llamante.

Sin embargo un retorno sí puede ser una *borrowed reference* (que toma prestada el llamador) en otras circunstancias, concretamente, una función puede retornar una referencia sin transferir su propiedad cuando tras la ejecución de dicha función existen todavía otras referencias a ese objeto en otras partes del código, es decir el conteo es superior a cero y alguien más tiene la propiedad de una referencia al objeto. La función está retornando un objeto que no ha creado ella, sino que existía ya previamente. La función no era propietaria de la referencia retornada (es por ello que retorna una *borrowed reference*):

```c
long int sum_list(PyObject *list)  // recibimos una lista de enteros que tomamos prestada
{
    Py_ssize_t i, n;
    long int total = 0, value;
    PyObject *item;
    n = PyList_Size(list);

    for (i = 0; i < n; i++)
    {
        item = PyList_GetItem(list, i);  // recibimos referencia borrowed en 'item'
        value = PyLong_AsLong(item);
        total += value;
    }
    return total;
}
```

Como vemos en el ejemplo, la variable ***item***, de tipo `PyObject*` recibe, a través de `PyList_GetItem()`, una referencia *borrowed* del elemento especificado. Es *borrowed* porque la función no es propietaria de la referencia cuando la retorna. Si en lugar de esta función hubiésemos usado `PySequence_GetItem()`, sí habríamos obtenido la propiedad de ***item*** cada vez, teniendo que llamar a `Py_DECREF()` tras cada uso de la referencia. El hecho de que la función llamada transfiera al llamador la propiedad de la referencia retornada o no, depende de la implementación de la función concreta.

Las funciones que extraen objetos de otros objetos suelen retornar dicho objeto al mismo tiempo que la *ownership* de la referencia. Sin embargo, hay excepciones, como hemos visto. Estas funciones retornan referencias *borrowed*: `PyTuple_GetItem()`, `PyList_GetItem()`, `PyDict_GetItem()` y `PyDict_GetItemString()`.

Cada función debe documentar qué hace con las referencias a objetos *Python* que recibe por parámetro: si roba o si toma prestada la propiedad. También debe documentar, en caso de retornar un objeto *Python*, si transfiere o no la propiedad al llamador. El llamador debe adaptarse al funcionamiento de la función llamada.

Una función *C* que retorne un objeto a *Python* debe transferir obligatoriamente la propiedad de la referencia, es decir, debe ser propietaria de la referencia que retorna.

Las macros `Py_INCREF()` y `Py_DECREF()` no comprueban si el apuntador pasado es ***NULL*** antes de ejecutarse. Sin embargo, las variantes `Py_XINCREF()` y `Py_XDECREF()` sí lo hacen. En caso de recibir ***NULL*** por parámetro no hacen nada, en lugar de levantar error.

### 2.4 Building C and C++ Extensions

Una extención *C* es un archivo de biblioteca compartida que exporta una función de inicialización. En *Unix* será un archivo ***.so***, y en *Windows* será un archivo ***.pyd***, que básicamente es lo mismo que un archivo ***.dll*** pero con una extensión propia de *Python* para evitar conflictos con otras bibliotecas *DLL*.

Tal archivo debe pues tener el nombre correcto del módulo la extensión adecuada al sistema operativo. Para que sea localizable, debe residir en una de las carpetas de ***PYTHONPATH***.

Este apartado explica cómo construir nuestro módulo sin utilizar directamente un compilador, sino a través de un *script* de creación de un archivo de biblioteca compartida usando el módulo *Python* ***distutils***.

Para generar el módulo usando el compilador *GCC* se podrían usar estos comandos (en sistemas *Unix*):

```
gcc -DNDEBUG -g -O3 -Wall -fPIC
        -I/usr/local/include/python3.9 -c demo.c -o build/demo.o`

gcc -shared build/demo.o -o build/demo.so
```

La primera orden genera el código objeto en ***demo.o***:

- `-DNDEBUG` define el nombre de macro ***NDEBUG***, que según el estándar *C*, desactiva las *assertions* (***assert.h***).
- `-g` compila con símbolos para depuración de código.
- `-O3` activa optimizaciones.
- `-Wall` activa todos los avisos (*warnings*) del compilador.
- `-fPIC` genera código relocalizable (*position-independent code*), necesario en bibliotecas compartidas.
- `-I/usr/local/include/python3.9` incluye el directorio ***/usr/local/include/python3.9*** en la lista de directorios donde buscar archivos de cabecera ***.h***.
- `-c` impide que se cree archivo ejecutable: no se realiza enlazado, se detiene tras la creación del archivo de código objeto.
- `demo.c` es el archivo fuente.
- `-o build/demo.o` define el nombre del archivo de salida. En este caso, ***build/demo.o***.

En cuanto a la segunda orden, crea la biblioteca compartida ***demo.so***:

- `-shared` indica que se debe crear una biblioteca compartida.
- `build/demo.o` es el archivo de entrada.
- `-o build/demo.so` es el archivo de salida (***build/demo.so***).

## Anexo: resumen de funciones útiles

Esta información puede encontrarse en el documento *The Python/C API*.

### *Reference Counts*

```c
void Py_INCREF(PyObject *o);
void Py_DECREF(PyObject *o);
void Py_XINCREF(PyObject *o);
void Py_XDECREF(PyObject *o);
```

Incrementan y decrementan el conteo de referencias, como hemos visto.

```c
void Py_CLEAR(PyObject *o);
```

Establece en 0 el conteo de referencias al objeto ***o***. Si el apuntador es ***NULL***, la función no tiene efecto.

### Excepciones

Existe un indicador global de error por cada hilo, que consiste en 3 apuntadores: el tipo de excepción, el valor de la excepción y el objeto *traceback*.

Al encontrar un error, una función debe levantar una excepción. Entonces optará por tratarla y continuar normalmente (limpiando el estado de error), o descartar todas las referencias que posea (y liberar otros recursos abiertos), y retornar indicador de error: ***NULL*** (si retorna apuntador) o ***-1*** (si retorna entero). Como excepción, las funciones que empiezan por `PyArg_` retornan ***0*** en caso de error (***1*** si tienen éxito).

Si opta por retornar error, la función llamante no levantará excepción (de eso se encarga la función que encuentra el error), y decidirá si tratar y limpiar la excepción, o pasar la ejecución a su llamador (liberando recursos y retornando error).

Este *API* proporciona unas variables globales de tipo `PyObject*` con el tipo de cada una de las excepciones posibles, cuyo nombre es el nombre del objeto excpeción *Python* con el prefijo ***PyExc\_*** (***PyExc_ZeroDivisionError***, ***PyExc_ValueError***, ***PyExc_TypeError***, etc.).

```c
void PyErr_Clear();
```

Limpia el estado de error.

```c
void PyErr_SetString(PyObject *type, const char *message);
```

Levanta una excepción. El primer argumento es el objeto excepción pertinente (no hay que incrementar el conteo de referencias), y el segundo es un mensaje de error en un *string* codificado con *UTF-8*, que será el valor asociado a la excepción.

```c
void PyErr_SetObject(PyObject *type, PyObject *value);
```

Es como `PyErr_SetString()`, pero indicando un objeto *Python* como valor de la excepción.

```c
PyObject* PyErr_Occurred();
```

Retorna una *borrowed reference* del tipo de excepción que esté levantada. Si no hay, retorna ***NULL***. Dado que el estado de error se almacena por cada hilo, si el código es *multithread*, el llamador a la función debe tener el *GIL* (*Global Interpreter Lock*).

> No se debe comparar el valor retornado con un tipo de excepción concreto, ya que uno podría ser una instancia y el otro la clase, o uno podría ser una subclase del otro y la comparación retornaría falso. Para comprobación se debe usar `PyErr_ExceptionMatches()`.

```c
int PyErr_GivenExceptionMatches(PyObject *given, PyObject *exc);
```

Retorna verdadero si la excepción ***given*** es del tipo en ***exc***. Si ***exc*** es un objeto clase, retornará verdadero si ***given*** es una subclase de ***exc***, o una instancia de esta o de una subclase de la misma. Si ***exc*** es una tupla, se comprobarán todos los tipos de excepción que contenga (recursivamente si hay anidación) buscando una coincidencia.

```c
int PyErr_ExceptionMatches(PyObject *exc);
```

Equivalente a `PyErr_GivenExceptionMatches(PyErr_Occurred(), exc)`. **Solo debe llamarse si hay una excepción levantada**.

```c
PyObject* PyErr_NewException(const char *name, PyObject *base, PyObject *dict);
```

Retorna una nueva referencia a un tipo nuevo de excepción, como vimos anteriormente. ***base*** y ***dict*** suelen ser ***NULL***.

### *Parsing arguments and building values*

```c
int PyArg_ParseTuple(PyObject *args, const char *format, ...);
int PyArg_ParseTupleAndKeywords(PyObject *args, PyObject *kw, const char *format, char *keywords[], ...);
```

Parsean los argumentos recibidos en una tupla, como se ha visto ya. Si alguno de los argumentos extraídos es un objeto *Python*, será una *borrowed reference* (no habrá que decrementar conteo).

```c
int PyArg_UnpackTuple(PyObject *args, const char *name, Py_ssize_t min, Py_ssize_t max, ...);
```

Esta forma de parsear la tupla de entrada no precisa de *format string*. Aquí la tupla ***args*** debe tener un mínimo de ***min*** elementos y un máximo de ***max*** (ambos números pueden ser iguales). Se deben proporcionar argumentos adicionales, apuntadores a `PyObject*`, que irán llenándose con los elementos de la tupla, sin conversiones. Los argumentos opcionales que no estén presentes en la tupla no modificarán el valor del correspondiente argumento (deberá ser inicializado anteriormente). El parámetro ***name*** es el nombre de función que se usará en un posible mensaje de error. Una función que parsee los argumentos de este modo debe declararse con ***METH_VARARGS*** en la tabla de funciones. Los objetos recibidos son *borrowed references*. La función retorna 1 si tiene éxito y 0 en caso contrario.

### *Building values*
