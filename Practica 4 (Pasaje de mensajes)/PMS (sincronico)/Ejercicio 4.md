4. En una exposición aeronáutica hay un simulador de vuelo (que debe ser usado con exclusión mutua) y un empleado encargado de administrar su uso. Hay P personas que esperan a que el empleado lo deje acceder al simulador, lo usa por un rato y se retira. 
a) Implemente una solución donde el empleado sólo se ocupa de garantizar la exclusión mutua (sin importar el orden). 
b) Modifique la solución anterior para que el empleado los deje acceder según el orden de su identificador (hasta que la persona i no lo haya usado, la persona i+1 debe esperar). 
c) Modifique la solución a) para que el empleado considere el orden de llegada para dar acceso al simulador. 
Nota: cada persona usa sólo una vez el simulador.


### a.

````ruby
process Persona [id:1..P]{
	Empleado!solicitar_paso(id);
	Empleado?pasar();
	utilizar_simulador();
	Empleado!salir();
}

process Empeleado {
	for i:= 1..P{
		Persona[*]?solicitar_paso(idPersona);
		Persona[idPersona]!pasar();
		Persona[idPersona]?salir();
	}
}
````

---
### b.

````ruby
process Persona [id:1..P]{
	Empleado!solicitar_paso();
	Empleado?pasar();
	utilizar_simulador();
	Empleado!salir();
}

process Empeleado {
	for i:= 1..P{
		Persona[i]?solicitar_paso();
		Persona[i]!pasar();
		Persona[i]?salir();
	}
}
````

---
### c.

````ruby
process Persona [id:1..P]{
	Admin!solicitar_paso(id);
	Empleado?pasar();
	utilizar_simulador();
	Empleado!salir();
}

process Admin {
	Cola buffer(Int);
	do Persona[*]?solicitar_paso(idPersona) -> push(buffer,idPersona);
	▢  (not empty(buffer)); Empelado?solicitar_persona() ->
							Empleado!enviar_persona(pop(buffer));
	od
}

process Empeleado {
	for i:= 1..P{
		Admin!solicitar_persona();
		Admin?enviar_persona(idPersona);
		Persona[idPersona]!pasar();
		Persona[idPersona]?salir();
	}
}
````
