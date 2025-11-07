
P personas que deben pasar por la única caja de cobros para realizar el pago de sus boletas.

Personas son atendidas de acuerdo con el orden de llegada, teniendo prioridad aquellos que deben pagar menos de 5 boletas de los que pagan más

Personas ancianas tienen prioridad sobre los dos casos anteriores.

Las personas entregan sus boletas al cajero y el dinero de pago; el cajero les devuelve el vuelto y los
recibos de pago.

```java

Program CajaCobros is

TASK Persona is
	ENTRY Iden(id: IN INTEGER);
end Persona; 

TASK Caja is
	ENTRY PedidoMayor(id: IN INTEGER);
	ENTRY PedidoNormal(id: IN INTEGER);
	ENTRY PedidoBoletaMayor(id: IN INTEGER);

	ENTRY cobrar(recibo: OUT text, monto: IN DOUBLE, vuelto: OUT DOUBLE);
end Caja;

arregloPersonas = array[1..P] of Persona;

TASK BODY Persona is
int edad, cantBoletas;
double monto, vuelto;
text recibo;

begin
	monto := X; // definido por la persona
	cantBoeltas := X // lo mismo de arriba;

	if (esMayor(edad)) {
		Caja.PedidoMayor(recibo, monto, vuelto);
	} else if (cantBoletas < 5) {
		Caja.PedidoBoletaMayor(recibo, monto, vuelto);
	} else {
		Caja.PedidoNormal(recibo, monto, vuelto);
	}

end Persona;


TASK BODY Caja is
	int idP, cantBoletas;
begin
	loop

		SELECT
			accept PedidoMayor(recibo: OUT text, monto: IN DOUBLE, vuelto: OUT DOUBLE) do 
				recibo := procesarPago(cantBoletas, monto);
				vuelto := procesarVuelto(cantBoletas, monto);
			end PedidoMayor;
		OR 
			when(PedidoMayor'count' = 0) => {
				accept PedidoBoletaMayor(recibo: OUT text, monto: IN double, vuelto: OUT double) do
					recibo := procesarPago(cantBoletas, monto);
					vuelto := procesarVuelto(cantBoletas, monto);
				end PedidoBoletaMayor;
			}
		OR 
			when (PedidoMayor'count' = 0) && (PedidoBoletaMayor'count' = 0) => {
				accept PedidoNormal(recibo: OUT text, monto: IN double, vuelto: OUT double) do
					recibo := procesarPago(cantBoletas, monto);
					vuelto := procesarVuelto(cantBoletas, monto);
				end PedidoNormal;
			}
		END SELECT;
	end loop;
end Caja;

begin
	null;
end CajaCobros;
```
