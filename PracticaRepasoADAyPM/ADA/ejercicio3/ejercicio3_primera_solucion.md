Oficina central debe calcular cuantas veces fue vendido cada uno de los articulos del catalogo.
La empresa se compone de 100 sucursales, cada una de ellas maneja su propia base de datos.

Oficina central cuenta con una herramienta -> Ante la consulta para un articulo, herramienta envia el identificador del articulo a las sucursales.

Cuando termina de procesar un articulo comeinza con el siguiente (la herramienta posee una funcion generarArticulo() que retorna el siguiente ID a consultar)

Existe una funcion ObtenerVentas(ID) que retorna cant de veces que fue vendido el articulo con ID en la base de la sucursal que se llama.

```java

Program Empresa is

TASK TYPE Sucursal is
	ENTRY pedidoVentasProducto(ID: IN INTEGER, totalVentas: OUT integer);
end Sucursal;

TASK OficinaCentral;

arregloSucursales = array[1..100] of Sucursal;

TASK BODY Sucursal is
begin
	loop
		accept pedidoVentasProducto(ID: IN INTEGER, totalVentas: OUT integer) do
			totalVentas := ObtenerVentas(ID);
		end pedidoVentasProducto;
	end loop
end Sucursal;

TASK BODY OficinaCentral is
int cant, idP, total;
begin
	loop
		total := 0;
		idP := generarArticulo;

		for i:= 1 to 100 do begin
			arregloSucursales(i).pedidoVentasProducto(idP, cant);
			total := total + cant;
		end;

		imprimir(total);
	end loop;
end OficinaCentral;


begin
	null;
end Empresa;

```

Comentarios: Lo hice bastante rapido, pero creo que este método no maximiza la concurrencia, por lo tanto voy a plantear otra solución la cual creo que es mas concurrente