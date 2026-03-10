
Resolver el siguiente problema con ADA. Existen N personas que van a realizar un pago en la caja de un banco. Las personas son atendidas de acuerdo al orden de llegada, aunque aquellos que sean jubilados tienen prioridad sobre los que no lo sean. Todas las personas esperan a lo sumo 15 minutos para ser atendidas. Si pasado ese tiempo no fueron atendidas, le dejan la nota de reclamo al responsable de Informes y se retiran.


````ruby
Process Banco is

	Task Caja is
		Entry atencion_jubilado(monto: in double,cobro: out double);
		Entry atencion_normal(monto: in double, cobro: out double);
	end Caja;
	
	Task Responsable is
		Entry enviar_reclamo(reclamo:in text);
	end Responsable;
	Task body Responsable is
	begin
		reclamo:text;
		reclamos: cola(text);
		loop
			Accept enviar_reclamo(reclamo:in text) do
				push(reclamos(reclamo));
			end enviar_reclamo;
		end loop;
	end Responsable;

	
	Task type Persona is
	arrPersona: array (1..N) of Persona;
	end Persona
	Task body Persona is
		monto:int;
		reclamo: text;
	begin
		obtener_monto(monto);
		if ("soy jubilado"){
			SELECT
				Caja.atencion_jubilado(monto,cobro);
			OR DELAY 900
				generar_reclamo(reclamo);
				Responsable.enviar_reclamo(reclamo);
			END SELECT
		}
		else {
			SELECT
				Caja.atencion_normal(monto,cobro);
			OR DELAY 900
				generar_reclamo(reclamo);
				Responsable.enviar_reclamo(reclamo);
			END SELECT
		}
	end Persona
	
	Task body Caja is
	
	begin
		loop 
			SELECT 
				when (atencion_jubilado´COUNT == 0) ->
					Accept atencion_normal(monto:in double,
											cobro:out double) do
						obtener_dinero(cobro,monto);
					end atencion_normal;
			OR
				Accept atencion_jubilado(monto:in double,
											cobro:out double) do
					obtener_dinero(cobro,monto);
				end atencion_jubilado;
			END SELECT
		end loop
	end Caja;

Begin
	null;
End Banco;		
````