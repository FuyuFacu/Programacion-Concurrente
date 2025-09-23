Date: 2025-09-23
Course: [[programacion_concurrente]]
Tags:[[monitores]], [[Practica 3 (Concurrencia)]]

# Enunciado
En un examen de la secundaria hay un preceptor y una profesora que deben tomar un examen escrito a 45 alumnos. El **preceptor** se encarga de darle el enunciado del examen a los alumnos cuando los 45 han llegado (es el mismo enunciado para todos). La profesora se encarga de ir corrigiendo los exámenes de acuerdo con el orden en que los alumnos van entregando. Cada alumno, al llegar, espera a que le den el enunciado, resuelve el examen, y al terminar lo deja para que la profesora lo corrija y le envíe la nota. 
**Nota:** maximizar la concurrencia; todos los procesos deben terminar su ejecución; suponga que la profesora tiene una función `corregirExamen` que recibe un examen y devuelve un entero con la nota.
# Respuesta
```java
Monitor Pregunta {
    int alumnos = 0;
    Enunciado enunciado;
    
    procedure recibir(out Enunciado e) {
        alumnos++;
        if (alumnos == 45) signal(completado);
        wait(comenzar);
        
        e = enunciado;
    }
    
    procedure entregar(Enunciado e) {
        if (alumnos < 45) wait(completado);
        enunciado = e;
        signal_all(comenzar);
    }
}

Monitor Examen {
    colaEspera cola;
    cond espera[45];
    notas[45] = ([45], 0);
    
    procedure entregar(int id, in Enunciado e, int nota) {
        push(cola, id, e);
        signal(hayExamen);
        
        wait(finalizado[id]);
        nota = notas[id];
    }
    
    procedure recibir(out Enunciado e, out int idAux) {
        if (cola.isEmpty()) wait(hayExamen); // Uso while por que es solo un profesor, si fueran multiples utilizaria while
        pop(cola, idAux, e);
    }
    
    procedure enviar_nota(nota, idAux) {
        notas[idAux] = nota;
        signal(espera[idAux]);
    }
}

Process Preceptor {
    Encunciado e;
    Pregunta.entregar(e);
}

Process profesora {
    int corregidos = 0;
    
    while (corregidos < 45)  { // como es solo un proceso no se puede generar otra historia, si hubiera mas de un profesor   
                                //  Habria puesto esta condicion dentro del monitor
        Enunciado enunciado_alumno;
        int idAux, nota = 0;
        
        Examen.recibir(enunciado_alumno, idAux);
        corregirExamen(enunciado_alumno) => nota;
        
        Examen.enviar_nota(nota, idAux);
        corregidos++;
    }
}

Process Alumno {
    Encunciado e;
    Pregunta.recibir(e);
    
    -- Realizar Examen
    
    int nota;
    Examen.entregar(e, nota);
}
```
