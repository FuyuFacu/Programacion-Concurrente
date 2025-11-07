
```java
chan pedidoCaja(int, int, boolean);
chan boletas(int, int);
chan resComprobante[1..P](text);
chan disponible[1..P]();

Process Persona[id: 1..P]
{
	int cantBoletas;
	boolean embarazada;
	text Comprobante;

	send pedidoCaja(id, cantBoletas, embarazada);
	receive disponible[id]();
	send boletas(cantBoletas, dinero);
	receive resComprobante(Comprobante);

}

Process Caja
{
	int id, cantBoletas;
	text Comprobante;

	while (true) {
		send Siguiente();
		receive siguientePersona(id);
		send disponible[id]();

		receive boletas(cantBoletas, dinero);
		Comprobante := generarComprobante(cantBoletas, dinero);
		send resComprobante[id](Comprobante);
	}
}



Process Administrador 
{
	int idP, cantB;
	boolean ocupado = false;
	colaOrdendada filaOrdenada;
	boolean embarazada;

	while (true)
	{
		if (not filaOrdenada.isEmpty()); receive Siguiente() => {
			send siguientePersona(pop(filaOrdenada, idP));
		}
		[] receive pedidoCaja(idP, cantB, embarazada) => {
			filaOrdenada.push(idP, cantB, embarazada);
		}
		fi
	}

}



```
