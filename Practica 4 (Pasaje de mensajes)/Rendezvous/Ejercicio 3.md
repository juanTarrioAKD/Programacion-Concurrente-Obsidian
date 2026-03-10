
3. Se dispone de un sistema compuesto por 1 central y 2 procesos periféricos, que se comunican continuamente. Se requiere modelar su funcionamiento considerando las siguientes condiciones: 
- La central siempre comienza su ejecución tomando una señal del proceso 1; luego toma aleatoriamente señales de cualquiera de los dos indefinidamente. Al recibir una señal de proceso 2, recibe señales del mismo proceso durante 3 minutos. 
- Los procesos periféricos envían señales continuamente a la central. La señal del proceso 1 será considerada vieja (se deshecha) si en 2 minutos no fue recibida. Si la señal del proceso 2 no puede ser recibida inmediatamente, entonces espera 1 minuto y vuelve a mandarla (no se deshecha).

````ruby
Procedure Sistema is

	Task Central is
		Entry recibir_señal1;
		Entry recibir_señal2;
		Entry fin_comunicacion(seguir:in bool);
	end Central;
	
	Task Periferico1 is
	Task body Periferico1 is
	begin
		loop
			señal = generarSeñal()
			SELECT 
				Central.recibir_señal1(señal);
			OR DELAY (120)
				Null;
			END SELECT;
		end loop
	end Periferico1;
	
	Task Periferico2 is
	Task body Periferico2 is
	begin
		señal = generarSeñal()
		loop
			SELECT 
				central.recibir_señal2(señal);
				señal = generarSeñal()
			ELSE 
				Delay(60);
			END SELECT;
		end loop
	end Periferico2;
	
	Task Timer is
		Entry iniciar;
	end Timer;	
	Task body Timer is
	begin
		loop
			Accept iniciar;
			Delay(180);
			Central.fin_comunicacion;
		end loop;
	end Timer;
	
	Task body Central is
		contando: bool := false;
		Accept recibir_señal1;
		loop 
			SELECT
				when (NOT contando) ->
					Accept recibir_señal1;
			OR 
				when (fin_comunicacion´COUNT == 0) ->
					Accept recibir_señal2;
					if (NOT contando){
						contando=true;
						Timer.iniciar;
					} 
			OR
				when (contando) ->
					Accept fin_comunicacion;
					contando:=false;
			END SELECT;
		end loop;
	end Central; 
	
begin
	null;
end Sistema;
````