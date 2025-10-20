
a)
```java
Process Alumno[id: 1..N]
{
    int nota;
    text respuesta = ResolverExamen();

    Administrador!envio_examen(id, respuesta);
    Profesor?recibir_nota(nota);
}

Process Administrador {
    int cant_examenes_asignados = 0;
    int idAux;
    text respuesta;
    cola examenes;

    do (cant_examenes_asignados < N); Alumno[*]?enviar_examen(idAux, respuesta) ->  
                                                                examenes.push(idAux, respuesta);
    [] (cant_examenes_asignados < N) and not empty(examenes); Profesor?pedido() -> {
        cola.pop(idAux, examen);
        Profesor!respuesta(idAux, examen);
        cant_examenes_asignados++;
    }
    od

    Profesor?pedido();
    Profesor!respuesta(-1, "FINALIZAR");
}

Process Profesor {
    int id_alumno, nota;
    text examen;
    boolean continuar;

    while (continuar) {
        Administrador!pedido();
        Administrador?respuesta(id_alumno, examen);

        if (examen == "FINALIZAR") continuar = false
        else {
            nota = CorregirExamen(examen);
            Alumno!recibir_nota(nota);
        }
    }
}
```

b)
```java

Process Alumno[id: 1..N]
{
    int nota;
    text respuesta = ResolverExamen();
    Administrador!envio(id, respuesta);

    Profesor[*]?resolucion(nota);
}

Process Administrador {
    cola examenes;
    int id_alumno, id_prof;
    int cant_examenes_asignados = 0;
    text examen;
    
    do (cant_examenes_asignados < N); Alumno[*]?envio(id_alumno, examen) ->  {
        push (examenes, (id_alumno, examen));
    }
    [] (cant_examenes_asignados < N) and not empty(examenes); Profesor[*]?pedido(id_prof) -> {
        pop(examenes, (id_alumno, examen));
        // Enviar al profesor el id y el examen
        Profesor[id_prof]!respuesta(id_alumno, examen);
        cant_examenes_asignados++;
    }
    od

    for i := 1 to P {
        Profesor[*]?pedido(id_prof);
        Profesor[id_prof]!respuesta(-1, "FINALIZAR");
    }
}

Process Profesor[id: 1..P]
{
    boolean continuar = true;
    text examen;
    int nota, idAux;

    while (continuar) {
        Administrador!pedido(id);
        Administrador?respuesta(idAux, examen);

        if (examen == "FINALIZAR")  // significa que ya terminó
            continuar = false;
        else {
            nota = CorregirExamen(examen);
            Alumno[idAux]!resolucion(nota);
        }
    }
}
```

c)
```java
Process Coordinador {
    int cant_llegados = 0;

    while (cant_llegados < N) {
        Alumno[*]?llegada();
        cant_llegados++;
    }

    for i = 1 to N 
        Alumno[i]!comenzar();
}



Process Alumno[id: 1..N]
{

    Coordinador!llegada();
    Coordinador?comenzar();

    int nota;
    text respuesta = ResolverExamen();
    Administrador!envio(id, respuesta);

    Profesor[*]?resolucion(nota);
}

Process Administrador {
    cola examenes;
    int id_alumno, id_prof;
    int cant_examenes_asignados = 0;
    text examen;
    
    do (cant_examenes_asignados < N); Alumno[*]?envio(id_alumno, examen) ->  {
        push (examenes, (id_alumno, examen));
    }
    [] (cant_examenes_asignados < N) and not empty(examenes); Profesor[*]?pedido(id_prof) -> {
        pop(examenes, (id_alumno, examen));
        // Enviar al profesor el id y el examen
        Profesor[id_prof]!respuesta(id_alumno, examen);
        cant_examenes_asignados++;
    }
    od

    for i := 1 to P {
        Profesor[*]?pedido(id_prof);
        Profesor[id_prof]!respuesta(-1, "FINALIZAR");
    }
}

Process Profesor[id: 1..P]
{
    boolean continuar = true;
    text examen;
    int nota, idAux;

    while (continuar) {
        Administrador!pedido(id);
        Administrador?respuesta(idAux, examen);

        if (examen == "FINALIZAR")  // significa que ya terminó
            continuar = false;
        else {
            nota = CorregirExamen(examen);
            Alumno[idAux]!resolucion(nota);
        }
    }
}
```




