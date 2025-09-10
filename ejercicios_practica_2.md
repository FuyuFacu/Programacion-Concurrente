```java
 Proccess Chico[id: 0..1]
 { while (true)
	 { -- tomar caramelo
		 P(mutex);
		 cant = cant+1;
		 V(mutex);
		 -- comer caramelo
	 }
 }
``` 
 
---
### Ejercicio 1
Existen N personas que deben se chequeadas por un detector de metales

1) Analice el problema y defina que proceso, recursos, semáforos/sincronizaciones serán necesarios/convenientes para resolverlo.
2) Implemente una solución que modele el acceso de las personas a un detector (es decir, si el detector esta libre la persona lo puede utilizar; caso contrario, espera).
3) Modifique la solución para el caso de que hayan tres detectores.
4) Modifique la solución anterior para el caso de que cada persona pueda pasar mas de una vez, siendo aleatoria la cantidad de veces.

a)
Los procesos que debería utilizar seria simplemente uno, que seria el de la persona. Este proceso persona va a ejecutarse dependiendo de N cantidad personas y va a tener un semáforo el cual va a chequear cuando s > 1 para así ocupar el puesto y restringir a los demás. Posteriormente a eso ejecuta el proceso de chequeando y entonces tras terminar esto utiliza V(s) para poder volver a incrementar la  variable y así otro proceso pueda ejecutarla.

b)
```java
int s = 1;
Process Persona[id: 1..N]
{
	{
		P(s)
		chequeando(id)
		V(s)
	}
}
```

c)
```java
int contador = 3;

Process Persona[id: 1..N]
{ 
	{   P(contador)
		chequeando(id)
		V(contador)
	}
}
```

d)
```java
int contador = 3;
Process Persona[id: 1..N]
{ while(true)
	{
		P(contador);
		chequeando(id);
		V(contador);
	}
}
```

---
### Ejercicio 2

Un sistema de control tiene 4 procesos, estos realizan chequeos en forma colaborativa.  Reciben el historial de fallos del día anterior (tamaño N). De cada fallo, se conoce su numero de identificación (ID) y su nivel de gravedad (0=bajo, 1=intermedio, 2=alto, 3=critico). Resuelva en base a las siguientes situaciones:

>a) Se debe imprimir en pantalla los ID de todos los errores críticos (no importa el orden).
   b) Se debe calcular la cantidad de fallos por nivel de gravedad, debiendo quedar los resultados en un vector global.
   c) Idem b) pero cada proceso debe ocuparse de contar los fallos de un nivel de gravedad determinado.


a) y b)
```java
Error[N] errores;
int puntero = 0;
sem mutex = 1;
int[4] resultados;

Process Control[id: 0..3] 
{ while (puntero <= N)
	{ 
		P(mutex);
		if (puntero > N) { // preguntar por esto porque no se si esta bien
			V(mutex);
			break;
		}
		
		int gravedad = errores[puntero].gravedad;
		
		if (gravedad == 3){
			println("Id's del error critico: ");
			println(errores[puntero].id);
		}
		
		resultados[errores[puntero].gravedad]++;
		puntero++;
		V(mutex);
	}
}

struct Error
{
	int id;
	int gravedad;
}
```

c)
```java
Error[N] errores;
int puntero = 0;
sem mutex = 1;
int[4] resultados;

Process Control[id: 0..3] 
{ 	P(mutex);
	int contador = 0;
	while (puntero < N)
	{ 
		Error aux = errores[puntero];
		
		if (aux.gravedad == 3) {
			println("Id's del error critico");
			println(aux.id);
		}
		V(mutex);
		
		if (gravedad == id) contador++;
		puntero++;
		P(mutex);
	}
	resultados[id] = contador;
	V(mutex)
}

struct Error
{
	int id;
	int gravedad;
}
```

---

### Ejercicio 3

```java
cola c[5];
sem disponibles = 5;
sem mutex = 1;

Process recurso[1..P]
{ while(true)
	{
		P(disponibles);
		
		P(mutex);
		resultado = c.desencolar();
		V(mutex);
		
		consumir_recurso(resultado);	
		
		P(mutex);
		c.encolar(recurso);
		V(mutex);		
		V(disponibles);
		
	}
}

```

### Ejercicio 4

```java
Var
	total: sem := 6;
	alta: sem := 4;
	baja: sem := 5;

Process Usuario-Alta [I:1..L]::
{   
	P(total);
	P(alta);
	// usa la BD
	V(total);
	V(alta);
}

Process Usuario-Baja [I:1..K]::
{
	P(total);
	P(baja);
	// usa la BD
	V(total);
	V(baja);
}

```


No es la solución mas adecuada para este caso. Ya que puede pasar que un Proceso Alta o Baja entre al semáforo de total porque hay espacio y que, después se quede suspendido en P(alta/baja) porque el limite depende de si es alta o baja. Lo que pasa es que ese espacio que utiliza ese proceso lo podría haber utilizado el otro proceso que capaz tenia la posibilidad de poder entrar ya que no había llegado al limite de su propio proceso de cuantos pueden acceder.
Osea:

La solución original no es la más eficiente. Esto es porque los procesos adquieren primero el semáforo `total` y luego el de prioridad (`alta` o `baja`). Puede pasar que, un usuario de prioridad alta pueda decrementar `total` y luego quedar bloqueado esperando a que `alta` tenga espacio. Mientras que otros usuarios de baja prioridad, que si tienen la posibilidad de  entrar según su límite, quedan bloqueados porque `total` ya fue reducido. Esto lo que hace es generar una **espera innecesaria** y reduce la eficiencia en el uso de la base de datos.

Una forma más adecuada de implementacion seria invertir el orden en que consiguen los semáforos, de manera que primero se consiga el semáforo de prioridad y luego el semáforo `total`.

### Resultado

```java
Var
total: sem := 6;   // max 6 usuarios concurrentes en total
alta: sem := 4;    // max 4 usuarios de alta
baja: sem := 5;    // max 5 usuarios de baja

Process Usuario-Alta [I:1..L]:
{
    P(alta);    // verifica que hay espacio para alta
    P(total);   // verifica que hay espacio total
    // usa la BD
    V(total);   // libera total
    V(alta);    // libera alta
}

Process Usuario-Baja [I:1..K]:
{
    P(baja);    // verifica que hay espacio para baja
    P(total);   // verifica que hay espacio total
    // usa la BD
    V(total);   // libera total
    V(baja);    // libera baja
}
```

5)

```java
typeP buf[N]
int ocupado = 0;
int libre = 0
sem lleno = 0;
sem vacio = 0;
lleno = 0;


Process Preparador
{ while (true)
    { 
        Paquete p = --generar paquete
        P(vacio) buf[libre] = p, libre += 1 mod n, V(lleno);

    }
}

Process Entregador 
{ while (true)
    { P(lleno);
      resultado = buff[ocupado], ocupado += 1 mod n, V(vacio);
      --entregar resultado;
    }
}

```

---

b)

```java
typeP buf[N]
sem mutex = 1;
int ocupado = 0;
int libre = 0
sem lleno = 0;
sem vacio = 0;
lleno = 0;


Process Preparador[0..P-1]
{ while (true)
    { 
        Paquete p = --generar paquete
        P(mutex);
        P(vacio) buf[libre] = p, libre += 1 mod n, V(lleno);
        V(mutex);
    }
}

Process Entregador 
{ while (true)
    { P(lleno);
      resultado = buff[ocupado], ocupado += 1 mod n, V(vacio);
      --entregar resultado;
    }
}

```


c)

```java
typeP buf[N]
sem mutex = 1;
int ocupado = 0;
int libre = 0
sem lleno = 0;
sem vacio = 0;
lleno = 0;


Process Preparador[0..P-1]
{ while (true)
    { 
        Paquete p = --generar paquete
        P(vacio) buf[libre] = p, libre += 1 mod n, V(lleno);
    }
}

Process Entregador 
{ while (true)
    { P(lleno);
      P(mutex);
      resultado = buff[ocupado], ocupado += 1 mod n, V(vacio);
      V(mutex);
      --entregar resultado;
    }
}

```


d)

```java
typeP buf[N]
sem mutex = 1;
int ocupado = 0;
int libre = 0
sem lleno = 0;
sem vacio = 0;
lleno = 0;


Process Preparador[0..P-1]
{ while (true)
    { 
        Paquete p = --generar paquete
        P(mutex);
        P(vacio) buf[libre] = p, libre += 1 mod n, V(lleno);
        V(mutex);
    }
}

Process Entregador 
{ while (true)
    { P(lleno);
      P(mutex);
      resultado = buff[ocupado], ocupado += 1 mod n, V(vacio);
      V(mutex);
      --entregar resultado;
    }
}

```

6)
a)

```java
sem impresora = 1;

Process Persona[0..N-1]
{ while (true)
    {
        --generar documento
        P(impresora);
        imprimir(documento);
        V(impresora);
    }
}

```

b)

```java
cola C;
sem mutex = 1, esp[N] = ([N] 0);

Process Persona[id: 0..N-1]
{
    while(true)
    {
        P(mutex);
        if (not libre) {
            push(C, id);
            V(mutex); P(esp[id]); }
        libre = false;

        imprimir(documento);
        
        if (not empty (C)) { pop(C, aux)
            V(esp[aux]);}

    }
}

```

c)

```java
esp[N] = ([N] 0);

Process Persona[id: 0..N-1]
{
    if (not id = 0){
        P[esp[id-1]];
    }

    imprimir(documento);
        
    V(esp[id]);
}

```

d)

```java
cola C;
sem mutex = 1
sem disponible = 1;
sem hayPersona = 0;
turno[N] = ([N], 0);

Process Persona[id: 0..N-1]
{
    while(true)
    {
        P(mutex);
        push(C, id);
        V(mutex);
        V(hayPersona);

        P(turno[id]);
        imprimir(documento);

        V(disponible);

    }
}

Process Coordinador
{ while (true)
    {
        P(hayPersona);
        P(disponible);
        aux = C.pop();
        V(turno[aux]);
    }
}
```

e)

```java
cola impresoras = {0,1,2,3,4}
cola C;
sem mutex = 1
sem disponibles = 5;
sem hayPersona = 0;
sem turno[N] = ([N], 0);
Impresora asignada[N] = ([N], 0);

Process Persona[id: 0..N-1]
{
    while(true)
    {
        P(mutex);
        push(C, id);
        V(mutex);

        V(hayPersona); // Necesario para que empieze a chambear el coordinador.

        P(turno[id]); // Espero a que se pueda imprimir y tenga asignada ya una impresora.
        imprimir(documento, asignada[id]); 
        push(C, asignada[id]); // Vuelvo a pushear la impresora. Ya la he utilizado.

        V(disponible);

    }
}

Process Coordinador
{ while (true)
    {
        P(hayPersona); // Aca verifico que existe una persona esperando a imprimir
        P(disponible); // Verifico que haya disponibilidad de impresoras

        id = C.pop();   // consigo el id de la persona
        impresora = impresoras.pop();   // desencolo alguna de las impresoras disponibles en la cola
        asignada[id] = impresora;   // asigno al id del array la impresora
        V(turno[id]);   //Libero a la Persona con el id pertinente
    }
}
```

7)
```java
int asignada[N] = ([N], 0);
int terminados[N] = ([N], 0);
sem listo[N] = ([N], 0);
int orden = 0;



int contador = 0;
sem comenzar = 0;
sem terminado = 0;
Cola c;



Process Alumno[id: 1..A]
{
    int numero_tarea;
    numero_tarea = elegirTarea();

    P(mutex);
    asignada[numero_tarea]++;
    contador++;
    if (contador == A)
        { for i = 1..A V(barrera)}
    V(mutex);
    P(barrera);

    --realizar tarea 

    P(mutex);
    terminado++;
    c.encolar(numero_tarea);
    V(mutex);

    P(listo[numero_tarea]);


}


Process Profesor {
    while (true)
    { 
        P(terminado);
        P(mutex);
        numero_tarea = c.desencolar();
        terminados[numero_tarea]++;
        V(mutex);

        if (asignada[numero_tarea] == terminados[numero_tarea])
        {
            P(mutex);
            orden++;
            V(mutex);

            otorgar_puntaje(orden);
            for i = 1..asignada[numero_tarea]
                V(listo[numero_tarea]);

        }
    }
}

```


8)
```java
sem barrera = 0;
int cant_id[N] = ([N], 0);
sem cantidad_piezas = T;

Process Empleado[id: 1.. E]
{   
    P(mutex);
    contador++;
    if (contador == E)
        for i: 1 to E V(barrera);
    V(mutex);
    P(barrera);

    while (true)
    {
        if (not P(cantidad_piezas)) break; // Creo que no esta bien hacerlo de esta manera...
        P(cantidad_piezas);
        --fabricar pieza;

        P(mutex);
        cant_id[id]++;
        V(mutex);
    }

    P(mutex);
        finalizado++
        if (finalizado == E) 
            V(terminado);
    V(mutex);
}



Process Dia {
    P(terminado);
    int maximo = -1;
    int id_max = 1;
    for i: 1 to E {
       if cant_id[i] > max {
            max = cant_id[i]; 
            id_max = i;
       }
    } 
    informar_mayor_productor(id_max);
}
```








