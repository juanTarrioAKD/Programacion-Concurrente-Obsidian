
Resolver con PMS. En una exposición aeronáutica hay un simulador de vuelo (que debe ser usado con exclusión mutua) y un empleado encargado de administrar el uso del mismo. A su vez hay P personas que van a la exposición y solicitar usar el simulador, cada una de ellas espera a que el empleado lo deje acceder, lo usa por un rato y se retira para que el empleado deje pasar a otra persona. El empleado deja usar el simulador a las personas respetando el orden en que hicieron la solicitud. Nota: cada persona usa una sola vez el simulador.

`````ruby
process Persona [id:1..P]{
	Empleado!solicitar_simulador(id);
	Empleado?pasar_simulador();
	usar_simulador();
	Empleado!salir_simulador();
}

process Empleado {
	do Persona[*]?solicitar_simulador(idPersona) ->
						if (NOT usando){
							Persona[idPersona]!pasar_simulador();
							usando=true;
						}
						else push(buffer,idPersona);
	▢  Persona[*]?salir_simulador()  ->
						if (NOT empty(buffer)){
							Persona[pop(buffer)]!pasar_simulador();
						}
						else usando=false;
}
`````

*Explicación:* En este ejercicio no es necesario un proceso Admin ya que el empleado solamente avisa cuando pasar, mientras un cliente esta usando el juego el empleado va a estar encolando a los clientes. (En caso de tener que hacer otra cosa mientras el cliente usa el juego y que esta acción impida que el empleado siga encolando a los clientes, en ese caso si va a ser necesario un proceso Admin).