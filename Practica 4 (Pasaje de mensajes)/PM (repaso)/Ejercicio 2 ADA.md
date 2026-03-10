
2) Resolver el siguiente problema. En un negocio de cobros digitales hay P personas que deben pasar por la única caja de cobros para realizar el pago de sus boletas. Las personas son atendidas de acuerdo con el orden de llegada, teniendo prioridad aquellos que deben pagar menos de 5 boletas de los que pagan más. Adicionalmente, las personas ancianas tienen prioridad sobre los dos casos anteriores. Las personas entregan sus boletas al cajero y el dinero de pago; el cajero les devuelve el vuelto y los recibos de pago.

````ruby
procedure Negocio is

	Task type Persona is
	arrPersona : array (P) of Persona;
	Task body Persona is
		cantBoletas: int;
		monto: real;
		vuelto: real;
		recibo: text;
	begin
		obtener_cantidad(cantBoletas);
		if ("soy anciano"){
			Caja.enviarBoletas_anciano(monto,vuelto,recibo);
		}
		else{
			if (cantBoletas<5){
				Caja.enviarBoletas_menos(monto,vuelto,recibo);
			}
			else
				Caja.enviarBoletas_mas(monto,vuelto,recibo);
		}
	end Persona;
	
	Task Caja is
		Entry enviarBoletas_anciano (monto: IN real
									 vuelto: OUT real
									 recibo: OUT text);
		Entry enviarBoletas_menos (monto: IN real
								   vuelto: OUT real
								   recibo: OUT text);
		Entry enviarBoletas_mas (monto: IN real
								 vuelto: OUT real
								 recibo: OUT text);
	Task body Caja is
	begin
		loop 
			SELECT 
				Accept enviarBoletas_anciano(monto: IN real
											 vuelto: OUT real
											 recibo: OUT text) do
						cobrar_boletas(monto,vuelto);
						generar_recibo(recibo);
				end enviarBoletas_anciano;
			OR
				when (enviarBoletas_anciano´COUNT==0) ->
					Accept enviarBoletas_menos(monto: IN real
											   vuelto: OUT real
											   recibo: OUT text) do
						cobrar_boletas(monto,vuelto);
						generar_recibo(recibo);
					end enviarBoletas_menos;
			OR 
				when (enviarBoletas_anciano´COUNT==0) and
					 (enviarBoletas_menos´COUNT==0) ->
					Accept enviarBoletas_mas(monto: IN real
											 vuelto: OUT real
											 recibo: OUT text) do
						cobrar_boletas(monto,vuelto);
						generar_recibo(recibo);
					end enviarBoletas_mas;
			END SELECT;
		end loop;
	end Caja;

begin
	null;
end Negocio;
````