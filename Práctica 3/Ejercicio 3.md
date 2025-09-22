3)
a)
```java
Monitor Fotocopiadora {
	bool libre = true;
	cond cola;
	
	Procedure acceder() {
		if (not libre) -> wait(cola);
		else libre = false;
	} 
	
	Procedure salir() {
		libre = true;
		signal(cola);
	}
}

Process Persona[id: 1..N] 
{
	Fotocopiadora.acceder();
	Fotocopiar();
	Fotocopiadora.salir();
}
```

b)
```java
Monitor Fotocopiadora {
	bool libre = true;
	cond cola;
	int esperando = 0;
	
	Procedure acceder() {
		if (not libre) -> {
			wait(cola);
			esperando++;
		}
		else libre = false;
	} 
	
	Procedure salir() {
		if (esperando > 0) {
			signal(cola);
			esperando--;
		} else {
			libre = true;
		}
	}
}

Process Persona[id: 1..N] 
{
	Fotocopiadora.acceder();
	Fotocopiar();
	Fotocopiadora.salir();
}

```

c)
```java
Monitor Fotocopiadora {
	bool libre = true;
	cond espera[N];
	int idAux, esperando = 0;
	colaOrdenada fila;
	
	Procedure acceder(int idP, int edad) {
		if (not libre) -> {
			insertar(fila, idP, edad);
			++esperando;
			wait(espera[idP]);
		}
		else libre = false;
	} 
	
	Procedure salir() {
		if (esperando > 0) {
			esperando--;
			sacar(fila, idAux);
			signal(espera[idAux]);
		} else libre = true;
	}
}

Process Persona[id: 1..N] 
{
	Fotocopiadora.acceder(id, edad);
	Fotocopiar();
	Fotocopiadora.salir();
}
```

d)
```java
Monitor Fotocopiadora {
	bool libre = true;
	cond cola;
	
	Procedure acceder() {
		if (not libre) -> wait(cola);
		else libre = false;
	} 
	
	Procedure salir() {
		libre = true;
		signal(cola);
	}
}

sem esperando[N] = ([N], 0);

Process Persona[id: 1..N] 
{
	if (id == 1) {
		Fotocopiadora.acceder();
		Fotocopiar();
		Fotocopiadora.salir();
		V(esperando[1]);
	} else {
		P(esperando[id-1]);
		Fotocopiadora.acceder();
		Fotocopiar();
		Fotocopiadora.salir();
		V(esperando[id]);
	}
}
```

e)
```java
Monitor Fotocopiadora {
	bool libre = true;
	cond cola;
	
	Procedure acceder() {
		if (not libre) wait(cola);
		libre = false;
	} 
	
	Procedure salir() {
		libre = true;
		signal(cola);
	}
}

sem libre = 0;
sem terminado = 0;

Process Persona[id: 1..N] 
{
	P(libre)
	Fotocopiar();
	V(terminado);
}

Process Empleado {
	while (true) {
		Fotocopiadora.acceder();
		V(libre);
		P(terminado);
		Fotocopiadora.salir();
	}
}
```

f)
```java
Monitor Fotocopiadora[id: 1..10] {
	bool libre = true;
	cond cola;
	int esperando = 0;
	
	Procedure acceder() {
		if (not libre) -> {
			wait(cola);
			esperando++;
		}
		else libre = false;
	} 
	
	Procedure estaLibre(int size) {
		if (not libre) -> size = esperando;
		else idM = id;
	}
	
	Procedure salir() {
		if (esperando > 0) {
			signal(cola);
			esperando--;
		} else {
			libre = true;
		}
	}
}

sem libre = 1;
sem terminado = 0;
sem espera = 0;
int asignada[] = ([N], 0);
cola disponibles;

Process Persona[id: 1..N] 
{
	V(espera);
	P(libre)
	idI = disponibles.pop();
	Fotocopiadora[idI].acceder();
	Fotocopiar();
	Fotocopiadora[idI].salir();
	
	Fotocopiar();
	V(terminado);
}

Process Empleado {
	while (true) {
		int size = 0;
		int max = 999;
		int i = 1;
		int idI, idIC = 0;
		
		P(espera);
		
		while (not finalizado) and (i <= 10) {
			Fotocopiadora[i].estaLibre(size);
			
			if (Fotocopiadora[i].libre) {
				idI = i;
				finalizado = true;
			} else {
				if (size < max) { // Esto es en caso no este libre
					idIC = size;
					max = size;
				} // Para que, en caso de que salga porque llego al final
				// Se haga push de aquel que tenga menos cola 
			}
		}
		
		if (finalizada) -> disponibles.push(asignada)
		else disponibles.push(asignadaC)
		
		V(libre);
	}
}
```
