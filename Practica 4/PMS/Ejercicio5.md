
```java
Process Espectador[id: 1..E] {
    Administrador!reservar(id);

    Expendedora?enviarTurno();
    usarMaquina();
    Expendedora!finalizar();
}

Process Expendedora {
    int idP;
    repeat E {
        Administrador!pedido();
        Administrador?recibir(idP);
        Espectador[idP]!enviarTurno();
        Espectador[idP]?finalizar();
    }
}

Process Administrador {
    cola Fila;
    int idE;
    int cant_atendidos = 0;

    do (not empty(Fila)) && (cant_atendidos < E); Expendedora?pedido() -> {
        Fila.pop(idE);
        Expendedora!recibir(idE);
        cant_atendidos++;
    }
    [] (cant_atendidos < E) Espectador[*]?reservar(idE); -> {
        Fila.push(idE);
    }
    od
}
```