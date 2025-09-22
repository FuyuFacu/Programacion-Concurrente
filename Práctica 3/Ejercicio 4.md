```java
Monitor Puente {
	int peso_actual = 0;
	int aux = 0;
	cond espera[N];
	cola fila;
	cola esperando;
	
	Procedure pasarPuente(int idV, peso) {
		push(fila, (idV, peso))
		
		if (peso_actual == 50000) {
			wait(espera[idV])
		} 
		peso_actual += peso;
			 
	}
	
	Procedure salirPuente(int peso) {
		peso_actual -= peso;
		if (not empty(fila)) siguiente = pop(fila);
		
		if (not empty(esperando)) push(esperando, siguiente)
		else if (siguiente.peso + peso_actual <= 50000) 
			signal(espera[siguiente.id])
	}
}

Vehiculo[id: 1..N] {
	int peso;
	
	Puente.pasarPuente(id, peso);
	pasar()
	Puente.salirPuente(peso);
}
```
