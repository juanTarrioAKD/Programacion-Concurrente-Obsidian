
Resolver el siguiente problema con ADA. En una empresa de organización de recitales cuentan con un sitio web para la venta de entradas. Existen N personas que quieren comprar una de las E entradas disponibles. Las personas se dividen en pacientes de impacientes. Cuando las personas pacientes intentan hacer la compra en el sitio web, esperan a lo sumo 5 minutos a que el servidor responda. Si pasado ese tiempo no fueran atendidas, lo vuelven a intentar. En el caso de las personas impacientes, si no son atendidas inmediatamente por el servidor, esperan 10 segundos y vuelvan a intentarlo. En todos los casos, en el sitio web les dice si pudieron venderle la entrada o no.



````ruby
procedure Recital is

	Task type Persona 
	arrPersona : array (N) of Persona;
	Task body Persona is
	begin
		if ("soy impaciente"){
			while (NOT meAtendieron){
				SELECT
					Servidor.solicitar_entrada(entrada,
								monto,resultado);
					meAtendieron=true;
				OR DELAY (5*60)
					null;
				END SELECT;
			}
		}
		else {
			while (NOT meAtendieron){
				SELECT
					Servidor.solicitar_entrada(entrada,
								monto,resultado);
					meAtendieron=true;
				ELSE
					Delay(10);
				END SELECT;
			}
		}
	end Persona;
	
	Task Servidor is
		Entry solicitar_entrada(entrada:OUT Text
								monto:IN Real
								resultado:OUT Bool);
	Task body Servidor is
	begin
		loop
			Accept solicitar_entrada(entrada:OUT Text
										monto:IN Real
										resultado:OUT Bool) do
				if (cant_entradas>0){
					obtener_entrada(entrada,monto);
					resultado=true;
				}
				else{
					resultado=false;
					entrada="invalida";
			end solicitar_entrada;
		end loop;
	end Servidor;

begin
	null;
end Recital;
````

