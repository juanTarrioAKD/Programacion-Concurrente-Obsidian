
Resolver el siguiente problema con ADA. Se quiere modelar un puente colgante de un solo sentido, el cual resiste hasta 100 personas que quieren cruzar y que solo lo podrán hacer si no superan la cantidad máxima. Las personas cruzan el puente de acuerdo al orden de llegada, aunque los jubilados tienen prioridad sobre los no jubilados.



````ruby
procedure Puente is

	Task type Persona is
	arrPersona : array (N) of Persona;
	Task body Persona is
	begin
		if ("soy jubilado"){
			Puente.solicitar_pasoJubilado();
			pasando_puente();
			Puente.pase();
		}
		else {
			Puente.solicitar_pasoNormal();
			pasando_puente();
			Puente.pase();
		}
	end Persona;
	
	Task Puente is
		Entry solicitar_pasoJubilado();
		Entry solicitar_pasoNormal();
	Task body Puente is
		int cant:=0;
	begin
		loop 
			SELECT
				WHEN (cant<100) ->
					Accept solicitar_pasoJubilado();
					cant_pasando++;
			OR
				WHEN (solicitar_pasoJubilado´COUNT==0)
				AND (cant_pasando<100) ->
					Accept solicitar_pasoNormal;
					cant_pasando++;
			OR 
				Accept salir();
				cant_pasando--;
			END SELECT;
		end loop;
	end Puente;

begin 
	null;
end Puente;
````