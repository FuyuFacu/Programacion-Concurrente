Oficina central debe calcular cuantas veces fue vendido cada uno de los articulos del catalogo.
La empresa se compone de 100 sucursales, cada una de ellas maneja su propia base de datos.

Oficina central cuenta con una herramienta -> Ante la consulta para un articulo, herramienta envia el identificador del articulo a las sucursales.

Cuando termina de procesar un articulo comeinza con el siguiente (la herramienta posee una funcion generarArticulo() que retorna el siguiente ID a consultar)

Existe una funcion ObtenerVentas(ID) que retorna cant de veces que fue vendido el articulo con ID en la base de la sucursal que se llama.

```java

Program Empresa is

TASK TYPE Sucursal;

TASK OficinaCentral is
	ENTRY pedidoIdProducto(idP: OUT INTEGER);
	ENTRY recibirResultadoProducto(cant: IN INTEGER);
end OficinaCentral;

arregloSucursales = array[1..100] of Sucursal;

TASK BODY Sucursal is
int idP, cant;
begin
	loop
		OficinaCentral.pedidoIdProducto(idP);
		cant := ObtenerVentas(idP);
		OficinaCentral.recibirResultadoProducto(cant);
	end loop
end Sucursal;

TASK BODY OficinaCentral is
int cant, idP, total;
begin
	loop
		total := 0;
		idProducto := generarArticulo();

		// De esta manera no priorizo el orden. Entonces si hay algun proceso mas lento que otro pues sera el utlimo en que reciba su peticion de id.
		repeat 100
			accept pedidoIdProducto(idP: OUT INTEGER) do
				idP := idProducto;
			end pedidoIdProducto;
		end;

		// Despues acepto 100 veces el resultado del producto.
		repeat 100
			accept recibirResultadoProducto(cant: IN INTEGER) do
				total := total + cant;
			end recibirResultadoProducto;
		end;

		// Por ultimo simulo la impresion del total.
		imprimir(total);
	end loop;
end OficinaCentral;


begin
	null;
end Empresa;

```
