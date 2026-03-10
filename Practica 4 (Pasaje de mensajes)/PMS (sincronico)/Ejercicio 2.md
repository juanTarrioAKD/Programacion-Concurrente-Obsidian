2. En un laboratorio de genética veterinaria hay 3 empleados. El primero de ellos continuamente prepara las muestras de ADN; cada vez que termina, se la envía al segundo empleado y vuelve a su trabajo. El segundo empleado toma cada muestra de ADN preparada, arma el set de análisis que se deben realizar con ella y espera el resultado para archivarlo. Por último, el tercer empleado se encarga de realizar el análisis y devolverle el resultado al segundo empleado.


````ruby
process Empleado1 {
	Text adn
	while (true){
		preparar_muestra(adn);
		Admin!enviar_muestra(adn);
	}
}

process Admin {
	Cola buffer;
	Text adn;
	do Empleado1?enviar_muestra(adn)  ->  push(buffer,adn);
	▢  (not empty(buffer)); Empleado2?solicitar_adn()  ->
							Empleado2!enviar_adn(pop(buffer));
	od;
}

process Empelado2 {
	Text adn;
	Text set;
	Text resultado;
	while (true){
		Admin!solicitar_adn();
		Admin?enviar_adn(adn);
		armar_set(adn,set);
		Empleado3!enviar_set(set);
		Empelado3?enviar_resultado(resultado);
		archivar(resultado);
	}
}

process Empleado3 {
	Text set;
	Text resultado;
	while(true){
		Emplaedo2?enviar_set(set);
		examinar_set(set,resultado);
		Empleado2!enviar_resultado(resultado);
	}
}
````

*Explicación:* En este ejercicio se plantean 3 empleados que se van comunicando entre si. El primer empleado tiene que ir generando cadenas de adn (Problema: el Empleado2 recibe y tiene que esperar la respuesta del Empleado3 impidiendo que el empleado1 siga generando cadenas de adn. Por lo tanto es necesario un Admin que encole a las cadenas y haciendo dependiente a los empleados). El empleado 2 y 3 no necesitan admin ya que el empleado 2 tiene que esperar el resultado del empleado 3 para archivarlo.

