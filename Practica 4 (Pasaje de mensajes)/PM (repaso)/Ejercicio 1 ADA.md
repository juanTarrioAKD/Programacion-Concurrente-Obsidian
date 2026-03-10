1) Resolver el siguiente problema. La página web del Banco Central exhibe las diferentes cotizaciones del dólar oficial de 20 bancos del país, tanto para la compra como para la venta. Existe una tarea programada que se ocupa de actualizar la página en forma periódica y para ello consulta la cotización de cada uno de los 20 bancos. Cada banco dispone de una API, cuya única función es procesar las solicitudes de aplicaciones externas. La tarea programada consulta de a una API por vez, esperando a lo sumo 5 segundos por su respuesta. Si pasado ese tiempo no respondió, entonces se mostrará vacía la información de ese banco.


````ruby
procedure Banco is

	Task type Banco is
	arrBanco : array (20) of Banco;
	Task body Banco is
		loop
			Accept consultar_banco(valor:OUT double) do
				obtener_cotizacionAPI(valor);
			end consultar_banco;
		end loop;
	end;

	Task TareaProgramada;
	Task body TareaProgramada is
		valor:double;
		loop
			for i:=1..20{
				SELECT
					Banco(i).consultar_banco(valor);
					actualizar_cotizacion(valor);
				OR DELAY 5
					actualizar_vacio();
				END SELECT;
			}
		end loop;
	end;

begin
	null;
end Banco;
````
