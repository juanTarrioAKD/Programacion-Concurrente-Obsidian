
1. Suponga que existe un antivirus distribuido que se compone de R procesos robots Examinadores y 1 proceso Analizador. Los procesos Examinadores están buscando continuamente posibles sitios web infectados; cada vez que encuentran uno avisan la dirección y luego continúan buscando. El proceso Analizador se encarga de hacer todas las pruebas necesarias con cada uno de los sitios encontrados por los robots para determinar si están o no infectados.
	a) Analice el problema y defina qué procesos, recursos y comunicaciones serán necesarios/convenientes para resolverlo. 
	b) Implemente una solución con PMS sin tener en cuenta el orden de los pedidos. 
	c) Modifique el inciso (b) para que el Analizador resuelva los pedidos en el orden en que se hicieron.
 

### b.

````ruby
process Examinador [id:1..R]{
	Text sitio
	while (true){
		buscar_sitio(sitio);
		Analizador!depositar_sitio(sitio);
	}
}

process Analizador {
	Text sitio;
	while (true){
		Examinador[*]?enviar_sitio(sitio);
		resolver_problema(sitio);
	}
}
````

*Explicación:* Esta solución cumple con lo que pide el inciso b (no importa el orden ya que de forma no determinística va a aceptar un pedido de algún examinador), pero tiene un problema ya que los examinadores no van a poder buscar mas sitios infectados hasta que el analizador no acepte su envió. Esto se soluciona con un Administrador, en el inciso c.

---
### c.

````ruby
process Examinador [id:1..R]{
	Text sitio
	while (true){
		buscar_sitio(sitio);
		Admin!depositar_sitio(sitio);
	}
}

process Admin {
	Cola buffer;
	Text sitio;
	do Examinador[*]?depositar_sitio(sitio) -> push(buffer,sitio);
	▢  Analizador?solicitar_sitio() -> Analizador!enviar_sitio(pop(buffer));
	od;
}

process Analizador {
	Text sitio;
	while (true){
		Admin!solicitar_sitio();
		Admin?enviar_sitio(sitio);
		resolver_problema(sitio);
	}
}
````

*Explicación*: En este caso se resuelve la espera de los examinadores y además se mantiene el orden en la cola del administrador. El admin va a encolar todos los pedidos que van haciendo los examinadores, y el analizador va a pedir el próximo sitio a solucionar, mientras este soluciona los errores los examinadores siguen encontrando sitios infectados.









