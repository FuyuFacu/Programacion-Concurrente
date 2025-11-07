
```java 
Process Persona[id: 1..P]
{
	while (true) 
	{
		Terminal!pedidoTerminal(id);
		Terminal?disponible();
		usarTerminal();
		Terminal!finalizar();
	}

}

Process Terminal 
{
	int idP;
	boolean terminalLibre := true;

	do (terminalLibre); Persona[*]?pedidoTerminal(idP) => {
		terminalLibre := false;
		Persona[idP]!disponible();
	}
	[] (not terminalLibre); Persona[idP]?finalizar() => terminalLibre := true;
	od
}
``` 