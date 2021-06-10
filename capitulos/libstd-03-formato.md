# Biblioteca estándar de Python: formato

## Specification

La especificación de formato viene definida como base en el **PEP 3101**. Con el tiempo se han añadido otras características. Se ha seguido la estructura del mencionado *PEP* para realizar el presente resumen, pero se ha ampliado la información según la documentación de la biblioteca estándar de *Python* 3.9.

Los títulos se han mantenido en inglés, que es el idioma de la documentación que se ha utilizado como base para este resumen.

Este documento trata sobre *strings Unicode* de *Python* 3.

## String Methods

Hablaremos del método `str.format()` y de la *built-in function* `format()`. El primero acepta un número arbitrario de argumentos, mientras que la segunda acepta un objeto y un *string format* opcional.

## Format Strings

Los argumentos del método `format()` sirven para dar valor a los campos de reemplazo de la *format string*, que es, de hecho, la instancia de ***str*** que llama al método.

```python
'My name is {0}'.format('Fred')
```

produce:

```
'My name is Fred'
```

Si queremos especificar los caracteres llave, hay que indicarlos doblados:

```python
'Hola, {0}. Esto son llaves: {{ y }}'.format('Fred')
```

produce:

```
'Hola, Fred. Esto son llaves: { y }'
```

Cuando los campos de reemplazo siguen un orden estricto, del tipo ***'...{0}...{1}...{2}...{3}...'***, se pueden omitir estos, con lo que podríamos indicar un *string* del tipo ***'...{}...{}...{}...{}...'***. Esto no se puede hacer a algunos campos sí y otros no. Se hace a todos (*automatic field numbering*) o a ninguno.

## Simple and Compound Field Names

Los campos simples se componen de números enteros (base 10), que se refieren al orden de los argumentos posicionales de `format()`. También pueden ser identificadores, y entonces se refieren a sus *keyword arguments*.

Los campos compuestos admiten dos operaciones: acceso al atributo (*getattr*) y acceso al elemento (*getitem*). Por ejemplo, ***{0.atrib}*** es un campo compuesto que accede al atributo ***atrib*** del objeto en el primer argumento posicional, mientras que ***{x[3]}*** es un campo compuesto que accede al cuarto elemento del *keyword argument* ***x***. Por ejemplo, para acceder a un elemento de un diccionario:

```python
"My name is {0[name]}".format(dict(name='Fred'))
```

Como circunstancia especial, el acceso al elemento no se puede hacer con la llave entre comillas. ***{0['name']}*** sería incorrecto.

## Format Specifiers

Cada campo puede ir acompañado de especificadores de formato, tras el carácter dos puntos (***:***), del tipo ***{0:8}***. El especificador de formato puede, a su vez contener un campo de reemplazo, como ***{0:{1}}***, en cuyo caso, el segundo parámetro posicional definirá el valor del especificador. No puede haber más niveles de anidamiento en los campos: el campo ***{1}*** del ejemplo no podría contener, pues, ningún campo.

Por otro lado, observemos que en el ejemplo hay unas dobles llaves de cierre. Esto no se interpreta como *escaped*, ya que solo se interpretan así fuera de los campos de reemplazo.

## Standard Format Specifiers

Si el objeto no define sus propios especificadores de formato, se usan estos. La forma general es (los corchetes ***[]*** indican elemento opcional):

```
[[fill] align] [sign] [#] [0] [minwidth] [grouping] [.precision] [type]
```

El *flag* ***align*** puede ser:

- ***\<*** indica alineación a la izquierda (por defecto).
- ***>*** alinea a la derecha.
- ***^*** centra el texto.
- ***=*** alinea a la derecha, pero el signo queda a la izquierda (solo para números).

Estos campos solo tienen sentido cuando se define una anchura.

Cuando se incluye este *flag*, se puede, opcionalmente, indicar una carácter de relleno (***fill***).

El *flag* ***sign*** (solo números) puede ser:

- ***+*** indica que se debe incluir signo tanto en positivos como en negativos.
- ***-*** indica que solo se debe incluir signo en negativos (por defecto).
- Un espacio indica que en los positivos se utilizará un espacio en la posición del signo.

Si se incluye el carácter almohadilla (***#***), los enteros usarán la forma alternativa, es decir, las salidas binarias, octales y hexadecimales tendrán un prefijo ***0b***, ***0o*** y ***0x*** respectivamente, mientras que para *floats* aparecerá siempre el punto decimal. En ***g*** y ***G*** no se eliminan los *trailing zeros*.

El *flag* ***minwidth*** es el que define la anchura mínima, de tal modo que si es necesario se rellenará la salida (según el *flag* ***align***). Si este *flag* va precedido de ***0***, realizará el relleno con ceros, equivalente a definir ***align*** con ***=*** y ***fill*** con ***0***. Los flags ***align***/***fill*** tienen prioridad sobre este ***0***.

El *flag* ***grouping*** tiene relación con los caracteres de agrupación de miles. Si este es coma (***,***) se usará la coma para separar los miles. Si es guión bajo (***\_***) se utilizará este carácter para separar miles en salidas `float` y en salidas de entero tipo ***d***.

***precision*** indica el número de dígitos tras el punto decimal (en conversiones ***f***, ***F***, ***e*** o ***E***), o en total (conversiones tipo ***g*** o ***G***).

En objetos no numéricos, ***precision*** indica el número de caracteres a utilizar del objeto. No se puede usar en números enteros.

Finalmente, ***type*** indica cómo se van a presentar los datos. Para *strings* se puede indicar ***s***. Para enteros:

- ***b*** (binario) muestra el número en base 2 (solo enteros).
- ***o*** (octal) muestra el número en base 8 (solo enteros).
- ***d*** (decimal) muestra el número en base 10 (solo enteros).
- ***x*** (hexadecimal) muestra el número en base 16, en minúsculas (solo enteros).
- ***X*** (hexadecimal) muestra el número en base 16, en mayúsculas (solo enteros).
- ***c*** (carácter) convierte el número a un carácter *Unicode* (solo enteros).
- ***n*** es como ***d***, pero insertando los caracteres adecuados de separación según el *locale* definido.

En cuanto a los valores relacionados con *floats*:

- ***e*** indica notación con exponente. La precisión por defecto es 6.
- ***E*** como ***e*** pero en mayúscula.
- ***f*** indica notación de punto fijo. Precisión por defecto 6.
- ***F*** como ***f*** pero en mayúsculas (***NAN***, ***INF***).
- ***g*** indica lo mismo que ***f*** o que ***e***, según la magnitud del número.
- ***G*** es igual que ***g*** pero en mayúscula.
- ***n*** es igual que ***g*** pero insertando los caracteres adecuados de separación según el *locale* definido.
- ***%*** (porcentaje) multiplica el número por 100, le aplica ***f*** y añade un signo ***%*** al final.

Si ***type*** no se indica, por defecto se usa ***d*** en enteros y en *floats* ***g*** pero con un dígito tras el punto decimal como mínimo. Para *strings*, es ***s***.

Los objetos pueden definir y/o redefinir su propio formato y seguir sus propias reglas.

## Explicit Conversion Flag

Se coloca antes de los especificadores de formato, y se aplica antes que estos. Actualmente, existen dos tipos: ***!r*** aplica `repr()`, y ***!s*** aplica `str()`.

```python
>>> "{0!r:20}".format("Hello")
"'Hello'             "
```

En este ejemplo se aplica primero `repr()` al *string* ***'Hello'*** (le coloca las comillas simples alrededor). Luego se aplica el resto de especificadores de formato.

## Controlling Formatting on a Per-Type Basis

Se puede controlar cómo responderá un objeto al formateo. Cada vez que `format()` se encuentra con un objeto (uno de sus argumentos) y una serie de especificadores (el campo de reemplazo), llama al método `__format__()` del objeto, al que le pasa esa ristra de especificadores. Luego el objeto lo procesa y retorna un *string* con el formato terminado.

La *built-in function* `format()` hace lo mismo: llama a `__format__()`, y retorna a su vez lo mismo que le ha retornado este método. Acepta un argumento (el objeto a formatear), y opcionalmente una *format string*. Si no se indica la *format string* en el segundo argumento, el comportamiento por defecto es que toma ***None***, lo cual es idéntico a llamar a `str()`.

## Notas personales

Cuando hemos utilizado *automatic field numbering* en los campos, seguimos pudiendo indicar especificadores de formato y/o nombres compuestos, como ***{[3]:20}*** o ***{.atrib!r:10.3}***.

Todo lo dicho aquí es válido también para formatear los *f-strings*.
