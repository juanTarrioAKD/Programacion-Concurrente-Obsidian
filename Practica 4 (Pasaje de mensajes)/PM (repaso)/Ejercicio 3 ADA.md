
3) Resolver el siguiente problema. La oficina central de una empresa de venta de indumentaria debe calcular cuántas veces fue vendido cada uno de los artículos de su catálogo. La empresa se compone de 100 sucursales y cada una de ellas maneja su propia base de datos de ventas. La oficina central cuenta con una herramienta que funciona de la siguiente manera: ante la consulta realizada para un artículo determinado, la herramienta envía el identificador del artículo a las sucursales, para que cada una calcule cuántas veces fue vendido en ella. Al final del procesamiento, la herramienta debe conocer cuántas veces fue vendido en total, considerando todas las sucursales. Cuando ha terminado de procesar un artículo comienza con el siguiente (suponga que la herramienta tiene una función generarArtículo() que retorna el siguiente ID a consultar). Nota: maximizar la concurrencia. Existe una función ObtenerVentas(ID) que retorna la cantidad de veces que fue vendido el artículo con identificador ID en la base de la sucursal que la llama.

````ruby
procedure Oficina is

	Task Consulta;
	Task body Consulta is
	begin
		loop
			generar_articulo(idArticulo);
			OficinaCentral.consultar_articulo(idArticulo,cant);
		end loop;
	end Consulta;

	Task type Sucursal is
		Entry consultar_dataBase(idaArticulo: IN int
								 cant: OUT int);
	arrSucursales : array (100) of Sucural;
	Task body Sucursal is
	begin
		loop
			Accept consultar_dataBase(idArticulo: IN int
									  cant:OUT int) do
				consulta_dataBase(idArticulo,cant);
			end consultar_dataBase;
		end loop;
	end Sucursal;
	
	Task OficinaCentral is
		Entry consultar_articulo (idArticulo: IN int);
	end OficinaCentral;
	Task body OficinaCentral is
	begin
		loop
			cantTotal:=0;
			Accept consultar_articulo(idArticulo: IN int
									  cantTotal:OUT int) do
				for i:= 1..100{
					Sucursal(i).consultar_dataBase(idArticulo,cant);
					cantTotal:=cantTotal+cant;
				}
			end consultar_articulo;
		end loop;
	end OficinaCentral;

begin
	null;
end Oficina;
````