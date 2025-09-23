Date: 2025-09-23
Course:[[programacion_concurrente]]
Tags:[[monitores]], [[Practica 3 (Concurrencia)]]

# Enunciado
En un entrenamiento de fútbol hay 20 jugadores que forman 4 equipos (cada jugador conoce el equipo al cual pertenece llamando a la función `DarEquipo()`). Cuando un equipo está listo (han llegado los 5 jugadores que lo componen), debe enfrentarse a otro equipo que también esté listo (los dos primeros equipos en juntarse juegan en la cancha 1, y los otros dos equipos juegan en la cancha 2). Una vez que el equipo conoce la cancha en la que juega, sus jugadores se dirigen a ella. Cuando los 10 jugadores del partido llegaron a la cancha comienza el partido, juegan durante 50  minutos, y al terminar todos los jugadores del partido se retiran (no es necesario que se esperen para salir).
# Respuesta

```java
Monitor Organizador {
    int equipos[4] = {0, 0, 0, 0};
    int canchaAsignada[4] = {0, 0, 0, 0};
    cond lleno[4];
    cond esperando;
    int canchas_disponibles = 1;
    
    procedure unirse(int equipo, numero_cancha) {
        equipos[equipo]++;
        if (equipos[equipo] == 5) {
            push(colaEquipos, equipo);
            signal(esperando);
        }
        
        if (canchaAsignada[equipo] == 0) wait(lleno[equipo]);
        numero_cancha = canchaAsignada[equipo];
        
    }
    
    procedure asignar()  {
        if (colaEquipos.size() < 2) wait(esperando);
        
        repeat 2:
            int idAux = colaEquipos.pop();
            canchaAsignada[idAux] = canchas_disponibles;
            signal(lleno[idAux]);
            
        canchas_disponibles = (canchas_disponibles % 2) + 1;
        
    }
}

Monitor Partido[id: 1..2]
{
    int jugadores = 0;
    cond todos, comenzar, finalizado;
    
    procedure comenzar() {
        jugadores++;
        if (jugadores == 10) signal(todos);
        else wait(comenzar);
    }
    
    procedure jugando() {
        wait(finalizado);
    }
    
    procedure esperar() {
        if (jugadores < 10) wait(todos);
        signal_all(comenzar);
    }
    
    procedure finalizar() {
        signal_all(finalizado);
    }
}

Process Jugador[id: 1..20]
{
    int numero_cancha;
    equipo = DarEquipo();
    
    Organizador.unirse(equipo, numero_cancha);
    Partido[numero_cancha].comenzar(numero_cancha);
    Partido[numero_cancha].jugando();
}

Process Coordinador {
    repeat 2:
        Organizador.asignar();
}

Process Cancha[id: 1..2]
{
    Partido[id].esperar();
    delay(50m);
    Partido[id].finalizar();
}
```
