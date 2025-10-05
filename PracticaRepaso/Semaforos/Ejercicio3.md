Implemente una solución para el siguiente problema. Se debe simular el uso de una máquina
expendedora de gaseosas con capacidad para 100 latas por parte de U usuarios. Además, existe un
repositor encargado de reponer las latas de la máquina. Los usuarios usan la máquina según el orden
de llegada. Cuando les toca usarla, sacan una lata y luego se retiran. En el caso de que la máquina se
quede sin latas, entonces le debe avisar al repositor para que cargue nuevamente la máquina en forma
completa. Luego de la recarga, saca una botella y se retira. Nota: maximizar la concurrencia; mientras
se reponen las latas se debe permitir que otros usuarios puedan agregarse a la fila.

```java

cola botellas;
cola fila;
int cant_botellas = 100;

Process Usuario[id: 1..U] {
    Botella b;

    P(mutex);
    if (not libre) {
        c.push(id);
        V(mutex);
        P(asignado[id]);
    } else {
        libre = false;
        V(mutex);
    }

    if (cant_botellas == 0) {
        V(reponer);
        P(finalizado);
    }

    pop(botellas, b);

    P(mutex);
    if not (fila.isEmpty()) {
        int idAux = fila.pop();

        V(asignado[idAux]);
    } else libre = true
    V(mutex);

    consumirBotella(b);
}

Process Repositor() {
    while (true) {
        P(reponer);
        repeat 100:
            push(botellas, generarBotella());
        V(finalizado);
    }
}

```