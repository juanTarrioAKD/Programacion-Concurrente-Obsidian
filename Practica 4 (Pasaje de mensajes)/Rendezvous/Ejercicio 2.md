
2. Se quiere modelar el funcionamiento de un banco, al cual llegan clientes que deben realizar un pago y retirar un comprobante. Existe un único empleado en el banco, el cual atiende de acuerdo con el orden de llegada. 
	 a) Implemente una solución donde los clientes llegan y se retiran sólo después de haber sido atendidos. 
	 b) Implemente una solución donde los clientes se retiran si esperan más de 10 minutos para realizar el pago. 
	 c) Implemente una solución donde los clientes se retiran si no son atendidos inmediatamente. 
	 d) Implemente una solución donde los clientes esperan a lo sumo 10 minutos para ser atendidos. Si pasado ese lapso no fueron atendidos, entonces solicitan atención una vez más y se retiran si no son atendidos inmediatamente.

### a.

````ruby
Process Banco is

	Task Empleado is
		Entry pedido (pago:in int; comprobante:out text);
	end Empleado;
	
	Task type Cliente;
	vecCliente: array (1..C) of Cliente;
	Task body Cliente is
		pago:int;
		comprobante:text;
	begin
		consultarMonto_aPagar(pago);
		Empleado.pedido(pago,comprobante);
	end Cliente;
	
	Task body Empleado is
		loop
			Accept pedido(p:in int; comprobante:out text) do
				cobrar(p);
				generar(comprobante);
			end pedido;
		end loop
	end Empleado;
	
	begin
		null
	end Banco;
````

---
### b.

````ruby
Process Banco is

	Task Empleado is
		Entry pedido (pago:in int; comprobante:out text);
	end Empleado;
	
	Task type Cliente;
	vecCliente: array (1..C) of Cliente;
	Task body Cliente is
		pago:int;
		comprobante:text;
	begin
		consultarMonto_aPagar(pago);
		SELECT
			Empleado.pedido(pago,comprobante);
		OR DELAY 600.0
			null;
		END SELECT;
	end Cliente;
	
	Task body Empleado is
		loop
			Accept pedido(p:in int; comprobante:out text) do
				cobrar(p);
				generar(comprobante);
			end pedido;
		end loop
	end Empleado;
	
	begin
		null
	end Banco;
````

*Obs:* Para controlar los tiempos se utiliza el SELECT OR DELAY "tiempo", esta condicion espera el tiempo ingresado en el delay, si pasa el tiempo y aun no se ha atendido el pedido se elimina la solicitud (no aparece mas en la lista de pedidos), y se ejecuta el codigo del or delay, en este caso no hace nada "null".

---
### c.

`````ruby
Process Banco is

	Task Empleado is
		Entry pedido (pago:in int; comprobante:out text);
	end Empleado;
	
	Task type Cliente;
	vecCliente: array (1..C) of Cliente;
	Task body Cliente is
		pago:int;
		comprobante:text;
	begin
		consultarMonto_aPagar(pago);
		SELECT
			Empleado.pedido(pago,comprobante);
		ELSE
			null;
		END SELECT;
	end Cliente;
	
	Task body Empleado is
		loop
			Accept pedido(p:in int; comprobante:out text) do
				cobrar(p);
				generar(comprobante);
			end pedido;
		end loop
	end Empleado;
	
	begin
		null
	end Banco;

`````

*Obs:* Igual al caso anterior pero en este caso en vez de utilizar el OR DELAY se utiliza el ELSE, a diferencia del OR DELAY el else es instantaneo, si no lo antienden cuando hace el pedido no espera nada, se ejecuta el codigo del ELSE en este caso "null".

---
### d.

````ruby
Process Banco is

	Task Empleado is
		Entry pedido (pago:in int; comprobante:out text);
	end Empleado;
	
	Task type Cliente;
	vecCliente: array (1..C) of Cliente;
	Task body Cliente is
		pago:int;
		comprobante:text;
	begin
		consultarMonto_aPagar(pago);
		SELECT
			Empleado.pedido(pago,comprobante);
		OR DELAY 600.0
			SELECT
				Empleado.pedido(pago,comprobante);
			ELSE
				null;
			END SELECT;
		END SELECT;
	end Cliente;
	
	Task body Empleado is
		loop
			Accept pedido(p:in int; comprobante:out text) do
				cobrar(p);
				generar(comprobante);
			end pedido;
		end loop
	end Empleado;
	
	begin
		null
	end Banco;

````