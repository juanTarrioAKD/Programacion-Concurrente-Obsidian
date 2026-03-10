
5. En un sistema para acreditar carreras universitarias, hay UN Servidor que atiende pedidos de U Usuarios de a uno a la vez y de acuerdo con el orden en que se hacen los pedidos. Cada usuario trabaja en el documento a presentar, y luego lo envía al servidor; espera la respuesta de este que le indica si está todo bien o hay algún error. Mientras haya algún error, vuelve a trabajar con el documento y a enviarlo al servidor. Cuando el servidor le responde que está todo bien, el usuario se retira. Cuando un usuario envía un pedido espera a lo sumo 2 minutos a que sea recibido por el servidor, pasado ese tiempo espera un minuto y vuelve a intentarlo (usando el mismo documento).

````ruby
procedure Carreras is

	Task type Usuario is
	arrUsuario : array (U) of Usuario;
	Task body Usuario is
		documento:text;
		respuesta:text;
	begin
		loop
			obtener_documento(documento);"genero nuevo doc si ya fue valido"
			trabajar_documento(documento);
			respuesta="hay error";
			while (respuesta=="hay error"){
				SELECT
					Servidor.enviar_documento(documento,respuesta);
					if (respuesta=="hay error"){
						trabajar_documento(documento);"trabajo en el mismo"
					}
				OR DELAY 60*2
					Delay(60);
			}
		end loop;
	end Usuario;

	Task Servidor is
		Entry enviar_documento(documento:IN text
							   respuesta:OUT text);
	end Servidor;
	Task body Servidor is
	begin
		loop
			Accept enviar_documento(documento:IN text
									respuesta:OUT text) do
					obtener_resultado(correcto);
					if(!correcto){
						respuesta="hay error";
					}
					else respuesta="documento correcto";
			end enviar_documento;
	end Servidor;

begin
	null;
end Carreras;
````