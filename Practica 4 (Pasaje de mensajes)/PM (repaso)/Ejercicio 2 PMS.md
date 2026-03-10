
2) Resolver el siguiente problema con PMS. En la estación de trenes hay una terminal de SUBE que debe ser usada por P personas de acuerdo con el orden de llegada. Cuando la persona accede a la terminal, la usa y luego se retira para dejar al siguiente. Nota: cada Persona usa sólo una vez la terminal.

````ruby
process Persona [id:1..P]{
	Terminal!solicitar_paso(id);
	Terminal?pasar();
	pasar();
	Terminal!termine();	
}

process Terminal {
	bool pasando=false;
	int idPersona;
	int cant=0;
	cola buffer(int);
	do Persona[*]?solicitar_paso(idPersona) ->
					if (!pasando){
						pasando=true;
						Persona[idPersona]!pasar();
					}
					else push(buffer,id);
	▢  Persona[*]?termine() ->
						if (not empty (buffer)){
							Persona[pop(buffer)]!pasar();
						}
						else pasando=false;
	od				
}
````