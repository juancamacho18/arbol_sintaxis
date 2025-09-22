EXPLICACIÓN:
Este programa de collab genera un árbol sintáctico que se forma de una cadena en base a reglas gramaticales que se le propicien y la cadena que se ingrese, en el dado caso de que esta cumpla las reglas, de lo contrario mostrara que no es valida.
Las reglas gramaticales están definidas de la siguiente manera
E->T E'
E'->opsuma T E'
E'->ε
T->F T'
T'->opmul F T'
T'->ε
F->num
F->id
F->pari E pard

Estas reglas son de gramática simple, se distribuyen en expresión, termino, factor, que a su vez estos se pueden derivar en si mismos; o incluir un operador aritmético, paréntesis izquierdo, paréntesis derecho, id o un numero dependiendo. (Esta definido de forma no recursiva a la izquierda para que el programa lo pueda detectar sin entrar en bucle infinito)

En el archivo main, que es el principal, es donde se desarrolla todo el proceso, a este se importan paquetes de matplot y networkx para la construcción y visualización del árbol.

lo primero que hace es establecer la gramática a tener en cuenta ; después se ingresa alguna cadena a analizar por la consola, esta cadena es dividida en partes que se guarden por medio de tokens validos; Luego se llama la función parser usando esos tokens y la gramática establecida, esta función además establece los nodos que se usan para representar gráficamente el árbol sintáctico que se construirá de esa cadena, partiendo del primer nodo siempre siendo el E, la raíz de la expresión.

Luego ya empieza a realizar el parser para los no terminales y ahí va comparando entre que símbolos hay por token y la relación con las reglas gramaticales, de esa manera se va definiendo los nodos padres e hijos que se tienen, hasta llegar a los terminales que vendría a ser los datos ingresados de la cadena; construyendo de esa manera los nodos y relacionándolos para guardarse en variables de raíz y su contenido. Importante a aclarar que si en algún punto el parser detecta un token o una parte de la expresión que no coincide con las reglas, el proceso se detendrá y retornara None, imprimiendo un mensaje en consola diciendo que la cadena no es valida.

Finalmente se hace la construcción del árbol y se establece de que manera se vera gráficamente, si se logra llegar a este proceso, el programa mostrar el árbol sintáctico de la cadena según la gramática.

COMO SE EJECUTA:
Descargas el archivo main.ipynb, y con una cuenta de google utilizas collab y cargas el archivo ejecutandolo todo



 
