# Ejercicio 1
>Estudiar en detalle los Code Smells y los Refactoring del libro y una  con flechas los code smells y los refactoring a los que se relacionan.

### Duplicate Code
Si se ve la misma estructura de código en mas de un lugar, entonces puede haber una manera en la cual el programa pueda "unificarlos".
El problema mas simple de código duplicado es cuando tengo la misma expresión en dos métodos de la misma clase. Por lo tanto lo que se tiene que hacer es aplicar el refactoring "Extract Method" e invocar ese código en **ambos  lugares**.

Otro problema común de código duplicado es cuando aparece la misma **expresión** pero en dos subclases hermanas. Se puede eliminar dicha duplicación utilizando Extract Method y después haciendo un Pull Up Method. En caso de que el código sea similar **pero no el mismo**, entonces se separa lo similar con lo que difiere. En esos casos se recomienda utilizar un "Form Template Method"

**Form Template Method**
Esto se aplica cuando el código duplicado aparece en subclases. Lo que se hace es agarrar el flujo el cual es común en ambas clases y subirlo a un método plantilla en la superclase, y los pasos los cuales derivan se las subclases y son diferentes se definen como métodos abstractos en la superclase para así cada subclase implementara a su manera.

>Otra alternativa seria cuando yo tengo dos clases que poseen código duplicado pero no están relacionadas. Lo que se debe hacer es aplicar "Extract Class" y entonces invocar esa clase en ambas.

---
### Large Class
Cuando una clase intenta hacer varias cosas, realmente se nota porque posee varias variables de instancia y métodos largos con código repetido. Esto puede generar code smells como la duplicación de código y que sea muy complejo.
Es por eso que se utiliza "Extract Class" para poder agrupar esas variables en otras clases que las cuales tengan mas sentido y que cumplan cierto propósito dentro de ellas.  Prefijos o sufijos que son comunes en un subconjunto de variables o métodos pueden indicar que ese conjunto puede extraerse.

Si un conjunto de variables o métodos pertenecen a un "tipo especializado" de clase, se puede convertir en subclase. A veces es mas fácil el extract class cuando existe una relación jerárquica natural.

Se puede utilizar también "Extract Interface" cuando hay distintos clientes que usan la clase de una manera diferente, esto permite separar responsabilidades y reducir acoplamiento.

---
### Long Method
Desde los primeros días de programación la gente llega a la conclusion de que, a medida que el proceso es mas largo, mas complicado es entenderlo.
La idea central es que los métodos que son cortos y tienen un buen nombre hacen que los programas Orientados a Objetos puedan ser mas claros, mantenibles y duraderos en el tiempo.
El problema no es la longitud, sino la distancia **semántica** entre lo que hace el método y como lo hace.

Las ventajas de los métodos pequeños es que explican mejor la intención del código mediante nombres de métodos, facilitan la reutilización y reemplazo del código y mejoran la legibilidad del mismo, ya que se puede entender el propósito sin tener que mirar la implementacion.

**Estrategias a utilizar:**
*Extract Method:* Lo que se hace es identificar los bloques de código que deberían ir juntos y convertirlos en métodos con nombres que sean claros, los cuales expliquen bien el propósito del mismo.
*Replace Temp with Query:* Si existen muchas variables temporales que lo que hacen es que complican la extracción, entonces se reemplazan con otros métodos que las calculen.
*Introduce Parameter Object:* si la lista de los parámetros es muy larga, entonces se encapsulan en un objeto.
*Replace method with Method Object:*  Esto es cuando todo lo anteriormente mencionado falla, y el método es demasiado grande o complejo.

>Acá quiero hacer una aclaración sobre este code smell ya que menciona una heuristica muy interesante:
>Cada vez que necesites un comentario para tener que explicar el código, ten en consideración extraer un método el cual su nombre refleje la intención del mismo.

---

### Data Class
Es una clase que solo posee datos, y getters y setters para esos datos, pero no poseen un comportamiento propio. O sea no sabe hacer nada por si misma, sino que solo guarda información que otras clases utilizan y manipulan.
Cuando otras clases manipulan exageradamente los campos de esta Data Class, el código se vuelve frágil y acoplado.

**Refactoring a aplicar:**
*Encapsulate field:* Si tenes campos públicos, entonces se convierten en privados y se controla su acceso con getters/setters.

*Encapsulate Collection:* Si los campos son colecciones, hay que asegurarse de que estos estén correctamente encapsulados para que otras clases no los manipulen de manera directa.

*Remove Setting Method:* Si los campos son colecciones, hay que asegurarse de que estén correctamente encapsuladas para que otras clases no las manipulen directamente.

*Mover comportamiento a la Data Class:* Buscar métodos que usen getters/setters en otras clases y aplica **Move Method** para trasladar la lógica a la Data Class. En caso de que solo una parte lógica puede moverse, primero utiliza **Extract Method** y luego mueve ese nuevo método.

*Hide Method:* Una vez que la lógica se ha movida y la Data Class es mas "inteligente", se puede ocultar el acceso directo a getters/setters innecesarios.

---
### Feature Envy
Un método parece estar mas interesado en los datos de otras clase que en los de la propia. Un ejemplo claro seria que un método llama repetidamente a getters y setters de otro objeto para así poder calcular un valor.
El problema es que la lógica estaría "celosa" de otra clase, esto rompe el encapsulamiento y dificulta el mantenimiento: cada cambio en la otra clase con los datos puede afectar métodos de otras.

**Refactoring:**
*Move Method:* Trasladar el método a la clase que posee la mayoría de los datos que utiliza.
*Pasos Extra:* Usar Extract Method para separar la parte que es celosa y mover luego solo esa parte a la clase concreta.

---
### Long Parameter List
Un método con demasiados parámetros es difícil de leer, de mantener, y cuando quieres hacer un cambio, tienes que modificar todas las llamadas.
Existen varios Refactorings que se pueden utilizar dependiendo de la situación:

*Replace Parameter with Method:* Si un parámetro se puede obtener simplemente pidiéndoselo a un objeto que ya conoce, entonces mejor reemplazarlo por esa llamada y eliminar el parámetro.
*Preserve Whole Object:* Si estas pasando varios atributos de un mismo objeto, es preferible pasar el objeto completo directamente.
*Introduce Parameter Object:* Si ya se tiene un grupo de parámetros que viajan juntos todo el rato pero no tiene un objeto que los represente, se crea uno nuevo.

---
### Switch Statement
El problema cuando hay muchos switch o  if-else repetidos es que, al agregar un nuevo caso, se tienen que modificar todos esos lugares relacionados. Esto lo que lleva es a duplicación y termina siendo frágil el código.
La solución a este problema es usar polimorfismo para reemplazar el `switch`, identificar el método cuyo comportamiento depende del type code y entonces mover ahí la lógica.

**Refactorings:**
*Replace Type Code with Sub classes:* Seria crear una jerarquía de clases
*Replace Type Code with State/Strategy:* Aplicar composición o strategy dependiendo de si cambia el type code en tiempo de ejecución o no.

>Posterior a eso aplicar `Replace Conditional with polymorfism`

Existen excepciones. Si hay pocos casos, afectan a un solo método y no van a cambiar seguido, el polimorfismo seria overkill.

**En esos casos, se podrían utilizar:**
- Replace parameter with Explicit Methods.
- Introduce Null object (en caso de que algunos casos pregunte si es un objeto nulo).

