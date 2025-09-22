Date: 2025-09-22
Course: [[programacion_concurrente]]
Tags: [[monitores]], [[Practica 3 (Concurrencia)]]

# Enunciado
Se debe simular una maratón con C corredores donde en la llegada hay UNA máquina expendedoras de agua con capacidad para 20 botellas. Además, existe un repositor encarga de reponer las botellas de la máquina. Cuando los C corredores han llegado al inicio comienza la carrera. Cuando un corredor termina la carrera se dirigen a la máquina expendedora, espera  su turno (respetando el orden de llegada), saca una botella y se retira. Si encuentra la máquina sin botellas, le avisa al repositor para que cargue nuevamente la máquina con 20 botellas; espera a que se haga la recarga; saca una botella y se retira. **Nota**: mientras se reponen las botellas se debe permitir que otros corredores se encolen.

# Respuesta
Acá he realizado dos propuestas -> Una con cola y otra sin cola:

**Con cola:**

```java
  Monitor Carrera {
        int cantidad = 0;
        cond marca, esperando;
        
        procedure llegar() {
            cantidad++;
            if (cantidad == C) signal(esperando);
            wait(marca);
        }
        
        procedure comenzar() {
            if (cantidad < C) wait(esperando);
            signal_all(marca);
        }
    }

    Monitor Expendedora {
        Cola colaEspera;
        cond asignado[C];
        cond aviso, finalizado, hayCliente;
        int idAux;
        int cantidad_botellas = 20;
        
        procedure acceder(int id) {
            push(colaEspera, id);
            
            while (cantidad_botellas == 0) {
                signal(aviso);
                wait(finalizado);
            }
            signal(hayCliente);
            wait(asignado[id]);
        }
        
        procedure siguiente() {
            if (colaEspera.isEmpty()) wait(hayCliente);
            pop(colaEspera, idAux);
            cantidad_botellas--;
            signal(asignado[idAux]);
        }
        
        procedure reponer() {
            wait(aviso);
            cantidad_botellas = 20;
            signal_all(finalizado);
        }
        
    }
    
    Process Coordinador() {
        while (true) 
            Expendedora.siguiente();
    }
    
    Process Carrera() {
        Carrera.comenzar();
    }
    
    Process Repositor() {
        Expendedora.reponer();
    }
    
    Process Corredor[id: 1..C] {
        Carrera.llegar();
        -- Corriendo
        Expendedora.acceder(id);
        -- Consumir Bebida;
    }


```

**Sin cola:**

```java
Monitor Carrera {
    int cantidad = 0;
    cond marca, esperando;
    
    procedure llegar() {
        cantidad++;
        if (cantidad == C) signal(esperando);
        wait(marca);
    }
    
    procedure comenzar() {
        if (cantidad < C) wait(esperando);
        signal_all(marca);
    }

}

Monitor Expendedora {
    cond cola, finalizado, aviso;
    bool libre = true;
    int cantidad_botellas = 20;
    bool disponible = true;
    int esperando = 0;

    procedure acceder() {
        while (cantidad_botellas == 0) {
            disponible = false;
            signal(aviso);
            wait(finalizado);
        }
        
        if (not libre) { esperando++
                         wait(cola);
        } else libre = false;
        
    }
    
    procedure salir() {
        if (esperando > 0) { esperando--;
                             signal(cola);
        } else libre = true;
        cantidad_botellas--;
    }
    
    procedure reponer() {
        if (disponible) wait(aviso);
        
        cantidad_botellas = 20 // Simulando la carga de botellas
        
        disponible = true;
        signal_all(finalizado);
    }
    
}


Process Corredor[id: 1..C] {
    Carrera.llegar();
    -- Corriendo...
    Expendedora.acceder();
    -- Consumir bebida
    
}

Process Carrera() {
    Carrera.comenzar();
}

Process Repositor() {
     Expendedora.reponer();

}
```

Las diferencias que creo que hay entre ambas respuestas es que uno prioriza el orden en el que llegan los procesos por encima del otro. El que posee cola logra hacer bien el priorizar el orden de llegada, mientras que el otro.
