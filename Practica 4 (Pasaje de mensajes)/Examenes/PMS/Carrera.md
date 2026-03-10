
Resolver con PMS. En una carrera hay C corredores y 3 coordinadores. Al llegar los corredores deben dirigirse a los coordinadores para que cualquiera de ellos le de el numero de chaleco con el que van a correr y luego se va. Los coordinadores atienden a los corredores de acuerdo al orden de llegada (cuando un coordinador esta libre atiende al primer corredor que esta esperando). Nota: maximizar la concurrencia.

````ruby
process Corredor [id:1..C]{
	Text chaleco;
	Admin!llegue(id);
	Coordinador[*]?enviar_chaleco(chaleco);
}

process Admin {
	Cola buffer(Int);
	Int idCoordinador;
	Int idCorredor;
	do Corredor[*]?llegue(idCorredor) -> push(buffer,idCorredor);
	▢  (not empty(buffer)); 
				Coordinador[*]?solicitar_corredor(idCoordinador) ->
				Coordinador[idCoordinador]!enviar_corredor(push(buffer));
	od
}

process Coordinador [id:1..3]{
	Int idCorredor;
	Text chaleco;
	while (true){
		Admin!solicitar_corredor(id);
		Admin?enviar_corredor(idCorredor);
		agarrar_chaleco(chaleco);
		Corredor[idCorredor]!enviar_chaleco(chaleco);
	}

}
````

*Explicación:* En este ejercicio es necesario el proceso Admin, ya que hay 3 coordinadores y los Corredores no sabrían a que coordinador llamar (no existe [ * ] en el envió, solo en la recepción, podes recibir de cualquiera pero no enviar a cualquiera). El Admin va a ir encolando a los corredores a medida que llegan y los coordinadores van a ir solicitando a los corredores, generan el chaleco y se lo envían. 