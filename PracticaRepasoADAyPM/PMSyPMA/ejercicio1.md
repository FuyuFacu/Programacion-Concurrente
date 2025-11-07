
a)
```java

chan impresiones[1..5](text);
chan pedidoDocumento(int);
chan pedidoImprimir(text);

Process Empleado[id: 1..100]
{
	text Documento;
	send pedidoImprimir(Documento);
}

Process Impresora[id: 1..5]
{
	text Documento;
	while (true) {
		send pedidoImpresora(id);
		receive impresiones[id](Documento);
		imprimirDocumento(Documento);
	}
}

Process Coordinador 
{
	int idI;
	text Documento;
	while (true) {
		receive pedidoImprimir(Documento);
		receive pedidoImpresora(idI);
		send impresiones[idI](Documento);
	}
}
```

b)

```java

Process Empleado[id: 1..100]
{
	text Documento;
	Coordinador!peticionImpresion(Documento);
}

Process Impresora[id: 1..5]
{
	text Documento;
	while (true) {
		Coordinador!peticion(id);
		Coordinador?recibirDocumento(Documento);
		imprimirDocumento(Documento); // simular proceso de impresion;
	}
}

Process Coordinador 
{
	int idI;
	text Documento;
	cola Fila;


	while (true) {
		if (not empty(Fila)); Impresora[*]?peticion(id) => {
			Documento := Fila.pop();
			Impresora[idI]!recibirDocumento(Documento);
		}
		[] Empleado[*]?peticionImpresion(Documento) => {
			Fila.push(Documento);
		}
	}

}
```
