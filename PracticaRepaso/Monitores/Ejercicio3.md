Resolver el siguiente problema. En una montaña hay 30 escaladores que en una parte de la subida
deben utilizar un único paso de a uno a la vez y de acuerdo con el orden de llegada al mismo. Nota:
sólo se pueden utilizar procesos que representen a los escaladores; cada escalador usa sólo una vez
el paso.

```java

Process Escalador[id: 1..30] {
    Camino.entrar();
    pasarCamino();
    Camino.salir();
}


Monitor Camino() {
    int esperando = 0;
    cola c;
    cond asignado[30];

    procedure entrar(int id) {
        if (not libre) {
            push(c, id);
            esperando++;
            wait(asignado[id]);
        } else libre = false;
    }

    procedure salir() {
        if (esperando > 0) {
            int idAux;
            pop(c, idAux);
            esperando--;
            signal(asignado[idAux]);
        } else libre = true;
    }
}
```