
6. En una playa hay 5 equipos de 4 personas cada uno (en total son 20 personas donde cada una conoce previamente a que equipo pertenece). Cuando las personas van llegando esperan con los de su equipo hasta que el mismo esté completo (hayan llegado los 4 integrantes), a partir de ese momento el equipo comienza a jugar. El juego consiste en que cada integrante del grupo junta 15 monedas de a una en una playa (las monedas pueden ser de 1, 2 o 5 pesos) y se suman los montos de las 60 monedas conseguidas en el grupo. Al finalizar cada persona debe conocer el grupo que más dinero junto. Nota: maximizar la concurrencia. Suponga que para simular la búsqueda de una moneda por parte de una persona existe una función Moneda() que retorna el valor de la moneda encontrada.

````ruby
procedure Plata is

	Task type Barrera is
		Entry llegue ();
	arrBarrera : array (4) of Barrera;
	Task body Barrera is
	begin
		for i:= 1..4 {
			Accept llegue();
		}
		for i:= 1..4 {
			obtener_posicionDePersonal(posicion);
			Persona(posicion).jugar();
		}
	end Barrera;

	Task type Persona is
		Entry jugar();
	arrPersona : array (20) of Persona;
	Task body Persona is
		equipo:int;
		moneda:int;
		ganador:int;
	begin
		obtener_equipo(equipo);
		Barrera(equipo).llegue();
		Accept jugar();
		for i:= 1..15{
			Partido.tomar_moneda(equipo);
			tomar_moneda(moneda);
			Partido.contar_moneda(equipo,moneda);
		}
		Partido.obtener_resultado(ganador);
	end Persona;

	Task Partido is
		Entry tomar_moneda(equipo:IN int);
		Entry contar_moneda(equipo:IN int
							moneda:IN int);
		Entry obtener_resultado();
	Task body Partido is;
		contador:array (4) of int;"inicializado en 0"
		resultado:array (4) of int;"inicializado en 0"
		buscando:int:=0;
	begin
		while (termino<4) and (buscando==0){
			SELECT 
				Accept tomar_moneda(equipo:IN int) do
					buscando:=buscando+1;
				end tomar_moneda;
				contador[equipo]:=contador[equipo]++;
				if (contador[equipo]==(15*4)){
					termino++;
				}
			OR 
				Accept contar_moneda(equipo:IN int
									 moneda:IN int) do
					resultado[equipo]:=resultado[equipo]+moneda;
					buscando:=buscando-1;
				end contar_moneda;
			END SELECT;
		}
		for i:=1..20 {
			Accept obtener_resultado(ganador:OUT int) do
				obtener_max(ganador,resultado);
			end obtener_resultado;
		}
	end Partido;

begin
	null;
end Playa;
````