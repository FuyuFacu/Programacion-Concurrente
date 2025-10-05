Resolver el siguiente problema. En una elección estudiantil, se utiliza una máquina para voto
electrónico. Existen N Personas que votan y una Autoridad de Mesa que les da acceso a la máquina
de acuerdo con el orden de llegada, aunque ancianos y embarazadas tienen prioridad sobre el resto.
La máquina de voto sólo puede ser usada por una persona a la vez. Nota: la función Votar() permite
usar la máquina.

```java

Process Persona[id: 1..N]
{
    int edad;
    Maquina.acceder(id, edad);
    Votar();
    Maquina.salir();
}

Process Autoridad() {
    Maquina.sig();
}


Monitor Maquina() {
    colaOrdendada fila; 
    int cant_atendidos = 0;
    cond asignado[N];
    cond finalizado;

    procedure acceder(int id, int edad) {
        insertar(fila, id, edad);
        signal(hayCliente);
        wait(asignado[id]);
    }

    procedure salir() {
        signal(finalizado);
    }

    procedure sig() {
        while (cant_atendidos < N) {
            if (fila.isEmpty()) wait(hayCliente);
            int idAux;
            pop(fila, idAux);
            signal(asignado[idAux]);
            wait(finalizado); // Esto es para cuando yo le haya dado el permiso para utilizar la maquina tenga que recibir que ya termino de usarla.
        }
    }

}


```