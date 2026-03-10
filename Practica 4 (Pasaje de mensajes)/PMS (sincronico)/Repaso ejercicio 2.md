 En un laboratorio de genética veterinaria hay 3 empleados. El primero de ellos continuamente prepara las muestras de ADN; cada vez que termina, se la envía al segundo empleado y vuelve a su trabajo. El segundo empleado toma cada muestra de ADN preparada, arma el set de análisis que se deben realizar con ella y espera el resultado para archivarlo. Por último, el tercer empleado se encarga de realizar el análisis y devolverle el resultado al segundo empleado.

````c
process empleado1{
	text cadena_adn;
	while (true){
		preparar_cadena(cadena_adn);
		Administrador!enviar_muestra(muestra);
	}
}

process Administrador{
	cola buffer;
	text cadena;
	do empleado1[*]?enviar_muestra(muestra) -> push(buffer(muestra));
	▢ not empty(buffer); empleado2?pedido() -> 
						     empleado2!enviar_pedido(pop(buffer));
}

process empleado2{
	text muestra;
	text set;
	text resultado;
	while(true){
		Administrador!pedido();
		Administrador?enviar_pedido(muestra);
		armar_set(muestra,set);
		empleado3!enviar_set(set);
		empleado3?enviar_resultado(resultado);
		archivar(resultado);
	}
}

process empleado3 {
	text set;
	text resutlado;
	while (true){
		armpleado2?enviar_set(set);
		realizar_analizis(resultado);
		empleado2!enviar_set(resultado);
	}
}
````

