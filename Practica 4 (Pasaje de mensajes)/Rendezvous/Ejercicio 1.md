
1. Se requiere modelar un puente de un único sentido que soporta hasta 5 unidades de peso. El peso de los vehículos depende del tipo: cada auto pesa 1 unidad, cada camioneta pesa 2 unidades y cada camión 3 unidades. Suponga que hay una cantidad innumerable de vehículos (A autos, B camionetas y C camiones). Analice el problema y defina qué tareas, recursos y sincronizaciones serán necesarios/convenientes para resolverlo. 
	a. Realice la solución suponiendo que todos los vehículos tienen la misma prioridad. 
	b. Modifique la solución para que tengan mayor prioridad los camiones que el resto de los vehículos.

### a.

```ruby
Procedure Puente is
	
	task acceso is
		entry accesoAuto;
		entry accesoCamioneta;
		entry accesoCamion;
		entry salir(peso: in int);
	
	task type auto;
	vecAutos: array (1..A) of auto; 
	task body auto is
	begin
		acceso.accesoAuto();
		acceso.salir(1);
	end auto;
	
	task type camioneta;
	vecCamionetas: array (1..B) of camionetas;
	task body camioneta is
	begin
		acceso.accesoCamioneta(2);
		acceso.salir();
	end camioneta;
	
	task type camion;
	vecCamiones: array (1..C) of camiones;
	task body camion is
	begin
		acceso.accesoCamion();
		acceso.salir(3);
	end camion;
	
	task body acceso is
		peso:int:=0;
	begin
		loop
			SELECT
				when (peso<5) ->
					accept accesoAuto do 
						peso++;
					end accesoAuto;
			OR	
				when (peso<4) ->
					accept accesoCamioneta do
						peso:=peso+2;
					end accesoCamioneta;
			OR
				when (peso<3) ->
					accept accesoCamion do
						peso:=peso+3;
					end accesoCamion;
			OR
				accept salir(p:in int) do
					peso:=peso-p;
				end salir;
			END SELECT;
		end loop;
	end acceso;
				
BEGIN
	null;
END Puente;
````

*Obs:* En este ejercicio no pido prioridad a ningun vehiculo, no se puede realizar con 1 solo tipo vehiculo y mandando el valor del peso ya que no se puede controlar que no supere el peso. No se puede preguntar en la condicion por el parametro del accept, por lo tanto para que funcione habria que preguntar siempre en la condicion que el peso < 3, pero esta solucion te impide que pasen autos cuando si podrian ingresar, en caso que el peso sea 4 no cumple la condicion y un auto si puede entrar.

---
### b.

````ruby
Procedure Puente is
	
	task acceso is
		entry accesoAuto;
		entry accesoCamioneta;
		entry accesoCamion;
		entry salir(peso: in int);
	
	task type auto;
	vecAutos: array (1..A) of auto; 
	task body auto is
	begin
		acceso.accesoAuto();
		acceso.salir();
	end auto;
	
	task type camioneta;
	vecCamionetas: array (1..B) of camionetas;
	task body camioneta is
	begin
		acceso.accesoCamioneta();
		acceso.salir();
	end camioneta;
	
	task type camion;
	vecCamiones: array (1..C) of camiones;
	task body camion is
	begin
		acceso.accesoCamion();
		acceso.salir();
	end camion;
	
	task body acceso is
		peso:int:=0;
	begin
		loop
			SELECT
				when (accesoCamion´count=0)and(peso<5) ->
					accept accesoAuto do 
						peso++;
					end accesoAuto;
			OR	
				when (accesoCamion´count=0)and(peso<4) ->
					accept accesoCamioneta do
						peso:=peso+2;
					end accesoCamioneta;
			OR
				when (peso<3) ->
					accept accesoCamion do
						peso:=peso+3;
					end accesoCamion;
			OR
				accept salir(p:in int) do
					peso:=peso-p;
				end salir;
			END SELECT;
		end loop;
	end acceso;
				
BEGIN
	null;
END Puente;
````

*Obs*: En este caso se consulta por la cantidad de peticiones que se estan haciendo a accesoCamion, por lo tanto si hay alguna peticion a accesoCamion la condicion va a dar false, dejando en true solo al accept accesoCamion.

