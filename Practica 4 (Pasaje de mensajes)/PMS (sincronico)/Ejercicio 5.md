5. En un estadio de fútbol hay una máquina expendedora de gaseosas que debe ser usada por E Espectadores de acuerdo con el orden de llegada. Cuando el espectador accede a la máquina en su turno usa la máquina y luego se retira para dejar al siguiente. Nota: cada Espectador una sólo una vez la máquina.


````ruby
process Espectador [id:1..E]{
	Admin!solicitar_paso(id);
	Maquina?pasar();
	tomar_bebida();
	Maquina!salir();
}

process Admin {
	Cola buffer(Int);
	Int idEspect;
	do Espectador[*]?solicitar_paso(idEspect) -> push(buffer,idEspect);
	▢  (not empty(buffer)); Maquina?solicitar_esp() ->
							Maquina!enviar_esp(pop(buffer));
	od
}

process Maquina {
	Int idEspectador;
	for i:= 1..E {
		Admin!solicitar_esp();
		Admin?enviar_esp(idEspectador);
		Espectador[idEspectador]!pasar();
		Espectador[idEspectador]?salir();
	}
}
````

