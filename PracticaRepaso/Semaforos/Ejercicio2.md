Implemente una solución para el siguiente problema. Un sistema debe validar un conjunto de 10000
transacciones que se encuentran disponibles en una estructura de datos. Para ello, el sistema dispone
de 7 workers, los cuales trabajan colaborativamente validando de a 1 transacción por vez cada uno.
Cada validación puede tomar un tiempo diferente y para realizarla los workers disponen de la
función Validar(t), la cual retorna como resultado un número entero entre 0 al 9. Al finalizar el
procesamiento, el último worker en terminar debe informar la cantidad de transacciones por cada
resultado de la función de validación. Nota: maximizar la concurrencia. 

```java
cola transacciones; // supongamos aca que hay 1000 transacciones, y que la estructura de datos es un tipo Transaccion

int resultados[0..9] = ([N], 0);
sem acceso, mutex, consulta = 1;
int terminados = 0;

Process Trabajador[id: 1..7]
{
    P(mutex);
    while not (transacciones.isEmpty())
    {
        Transaccion t;
        pop(transacciones, t);

        V(mutex);
        int numero = Validar(t);

        P(acceso);
        resultados[numero]++; //hacer una array local y despues al final sumar todo en el global asi te ahorras un semaforo y maximisas concurrencia y dividir el array T veces asi cada trabajador no se choque mientras recorre el array de transacciones.
        V(acceso);

        P(mutex);
    }
    V(mutex);

    P(consulta);
    terminados++;
    if (terminados == 7) {
        for (i = 0..9) System.out.println(resultados[i]);
    }
    V(consulta);

}

```