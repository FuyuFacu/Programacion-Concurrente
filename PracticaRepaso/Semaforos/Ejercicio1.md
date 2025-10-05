
1) Resolver los problemas siguientes:
a) En una estación de trenes, asisten P personas que deben realizar una carga de su tarjeta SUBE
en la terminal disponible. La terminal es utilizada en forma exclusiva por cada persona de acuerdo
con el orden de llegada. Implemente una solución utilizando únicamente procesos Persona. Nota:
la función UsarTerminal() le permite cargar la SUBE en la terminal disponible.
b) Resuelva el mismo problema anterior pero ahora considerando que hay T terminales disponibles.
Las personas realizan una única fila y la carga la realizan en la primera terminal que se libera.
Recuerde que sólo debe emplear procesos Persona. Nota: la función UsarTerminal(t) le permite
cargar la SUBE en la terminal t.



1)a)
```java
cola c;
sem asignado[1..P];
bool libre = true;


Process Persona[id:1..P]
{
    P(mutex);
    if (not libre) {
        c.push(id);
        V(mutex);
        P(asignado[id]);
    } else {
        libre = false;
        V(mutex);
    }

    UsarTerminal();

    P(mutex);
    if (c.isEmpty()) libre = true;
    else {
        int idAux;
        pop(cola, idAux);
        V(asignado[idAux]);
    }
    V(mutex);
}
```

1)b)
```java
cola c;
sem asignado[1..P];
int terminales = T;
sem mutex, acceso = 1;
cola terminal;


Process Persona[id:1..P]
{
    P(mutex);
    if (terminales == 0) {
        push(c, id);
        V(mutex);
        P(asignado[id]);
    } else {
        terminales--;
        V(mutex);
    }

    int t;
    P(acceso);
    pop(terminal, t);
    V(acceso);

    usarTerminal(t);

    P(acceso);
    encolar(c, t);
    V(acceso);

    P(mutex);
    if (c.isEmpty()) {
        terminales++;
    } else {
        int idAux;
        pop(c, idAux);
        V(asignado[id]);
    }
    V(mutex);

}
```