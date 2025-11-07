Banco Central exhibe las diferentes cotizaciones del dólar oficial de 20 bancos del país
tanto para la compra como para la venta.

Existe una tarea programada que se ocupa de actualizar la página en forma periódica, para ello consulta la cotización de cada uno de los 20 bancos. 

Cada banco dispone de una API, cuya única función es procesar las solicitudes de aplicaciones externas.
La tarea programada consulta de a una API por vez, esperando
a lo sumo 5 segundos por su respuesta. Si pasado ese tiempo no respondió, entonces se mostrará
vacía la información de ese banco.
```java
Program BancoCentral is

TASK TYPE Banco is
	ENTRY requestInfo(r: IN OUT text);
end Banco;

TASK Tarea is
end Tarea

arregloBancos = array[1..20] of Banco;

TASK BODY Banco is
begin
	loop
		accept requestInfo(r: IN OUT text) do
			r := procesarInformacion();
		end requestInfo;
	end loop
end Banco

TASK BODY Tarea is
	text Resultado;
begin
	loop
		for i:= 1 to 20 {
			SELECT 
				arregloBancos(i).requestInfo(Resultado);
				imprimir(Resultado);
			OR delay(5s);
				imprimir("Vacio");
			END SELECT;
		}
	end loop
end Tarea;

begin
	null;
end BancoCentral;

```
