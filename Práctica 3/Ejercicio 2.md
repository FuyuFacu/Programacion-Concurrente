2)
### Con monitor:
```java
Monitor BaseDatos {
	cond esperando;
	int usando = 0;
	
	Procedure acceder() {
		if (usando == 5) 
			wait(cola);
		usando++;
	}
	
	Procedure salir() {
		usando--;
		signal(espera);
	}
}

Process lectores[id: 1..N]
{
	BaseDatos.acceder();
	leerBaseDeDatos();
	BaseDatos.salir();
}
```
### Sin monitor
```java
sem usando = 5;

Process lectores[id: 1..N]
{
	P(usando);
	leerBaseDeDatos();
	V(usando);
}
```
