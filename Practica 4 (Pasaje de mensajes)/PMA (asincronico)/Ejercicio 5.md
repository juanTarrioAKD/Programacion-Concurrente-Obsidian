
5. Resolver la administración de 3 impresoras de una oficina. Las impresoras son usadas por N administrativos, los cuales están continuamente trabajando y cada tanto envían documentos a imprimir. Cada impresora, cuando está libre, toma un documento y lo imprime, de acuerdo con el orden de llegada. 
	a) Implemente una solución para el problema descrito. 
	b) Modifique la solución implementada para que considere la presencia de un director de oficina que también usa las impresas, el cual tiene prioridad sobre los administrativos. 
	c) Modifique la solución (a) considerando que cada administrativo imprime 10 trabajos y que todos los procesos deben terminar su ejecución.
	 d) Modifique la solución (b) considerando que tanto el director como cada administrativo imprimen 10 trabajos y que todos los procesos deben terminar su ejecución. 
	 e) Si la solución al ítem d) implica realizar Busy Waiting, modifíquela para evitarlo. Nota: ni los administrativos ni el director deben esperar a que se imprima el documento.



### a.

````ruby
chan solicitar_impresion(Int,Text);
chan recibir_impresion[N](Text);

process Administrativo [id:1..N]{
	Text documento;
	Text impresion;
	while (true){
		trabajando(documento);
		send solicitar_impresion(id,documento);
		receive recibir_impresion[id](impresion);
	}
}

process Impresora [id:1..3]{
	Int idAdministrativo;
	Text documento_aImprimir;
	Text impresion;
	while (true){
		receive solicitar_impresion(idAdministrativo,documento_aImprimir);
		imprimir_documento(documento_aImprimir,impresion);
		send recibir_impresion[idAdministrativo](impresion);
	}
}
````

*Explicación:* En este ejercicio con 1 canal único de pedidos de impresión es suficiente para solucionar el problema, los administrativos piden impresiones y las impresoras van agarrando los pedidos y van enviando las impresiones.

---
### b.

````ruby
chan solicitar_impresion(Int,Text);
chan recibir_impresion[N](Text);
chan solicitar_empleado(Int);
chan solicitar_impresionJefe(Int,Text);
chan enviar_impresion[3](Int,Text);

process Administrativo [id:1..N]{
	Text documento;
	Text impresion;
	while (true){
		trabajando(documento);
		if ("soy jefe"){
			send solicitar_impresionJefe(id,documento);
		}
		else  send solicitar_impresion(id,documento);
		receive recibir_impresion[id](impresion);
	}
}

process Coordinador{
	Text documento;
	Int idEmpleado;
	Int idImpresora;
	while(true){
		receive solicitar_empleado(idImpresora);
		if (not empty(solicitar_impresionJefe)){
			receive solicitar_imrpesionJefe(idEmpleado,documento);
		}
		else {
			receive solicitar_impresion(idEmpleado,documento);
		}
		send enviar_impresion[idImpresora](idEmpelado);
	}
}

process Impresora [id:1..3]{
	Int idAdministrativo;
	Text documento_aImprimir;
	Text impresion;
	while (true){
		send solicitar_empleado(id);
		receive enviar_impresion[id]
									(idAdministrativo,documento_aImprimir);
		imprimir_documento(documento_aImprimir,impresion);
		send recibir_impresion[idAdministrativo](impresion);
	}
}
````

*Explicación:* A diferencia del inciso "b" en este caso hay que darle prioridad a un tipo de Administrativo (jefe), por lo tanto para manejar esto es necesario tener 2 canales distintos 1 para administrativos y otro para jefes. Porque? Ya que hay que darle prioridad a los jefes, y la unico forma es teniendo un canal propio. Ademas es necesario un Coordinador, ya que no se puede consultar not empty(canal_jefe) directamente en la impresora ya que son 3 procesos y si hay 1 solo pedido del jefe a todos les responde true, 1 solo obtiene el pedido en el receive y los otros 2 se quedan trabados en recieve canal_jefe y no permitis que atiendan a administrativos.

---

### c.

````ruby
chan solicitar_impresion(Int,Text);
chan recibir_impresion[N](Text);

process Administrativo [id:1..N]{
	Text documento;
	Text impresion;
	for i:= 1 to 15{
		trabajando(documento);
		send solicitar_impresion(id,documento);
		receive recibir_impresion[id](impresion);
	}
}

process Administrador{
	Int idImpresora;
	Int idAdminsitrativo;
	Text documento;
	for i:= 1 to N*15 {
		receive solicitar_administrativo(idImpresora);
		receive solicitar_impresion(idAdministrativo,documento);
		send enviar_impresion[idImpresora](idAdministrativo,documento);
	}
	for i:= 1 to 3{
		receive solicitar_administrativo(id);
		send enviar_impresion(-999,"invalido");
	}
}

process Impresora [id:1..3]{
	Int idAdministrativo;
	Text documento_aImprimir;
	Text impresion;
	while (idAdminitrativo<>-999){
		send solicitar_administrativo(id);
		receive enviar_impresion[id]
									(idAdministrativo,documento_aImprimir);
		imprimir_documento(documento_aImprimir,impresion);
		send recibir_impresion[idAdministrativo](impresion);
	}
}
````

*Explicación:* En este caso tenemos 3 process impresora, por lo tanto no podemos saber cuantos pedidos van a recibir cada una, por lo tanto es necesario un process coordinador que reciba en un for la cantidad de pedidos totales (15 * N) y luego para finalizar a los process impresora enviarles un valor invalido para terminar el loop.

---
### d.

````ruby
chan solicitar_impresion(Int,Text);
chan recibir_impresion[N](Text);
chan solicitar_empleado(Int);
chan solicitar_impresionJefe(Int,Text);
chan enviar_impresion[3](Int,Text);

process Administrativo [id:1..N+1]{
	Text documento;
	Text impresion;
	while (true){
		trabajando(documento);
		if ("soy jefe"){
			send solicitar_impresionJefe(id,documento);
		}
		else  send solicitar_impresion(id,documento);
		receive recibir_impresion[id](impresion);
	}
}

process Coordinador{
	Text documento;
	Int idEmpleado;
	Int idImpresora;
	for i:= 1 to (N*15)+1{
		receive solicitar_empleado(idImpresora);
		if (not empty(solicitar_impresionJefe)){
			receive solicitar_imrpesionJefe(idEmpleado,documento);
		}
		else {
			receive solicitar_impresion(idEmpleado,documento);
		}
		send enviar_impresion[idImpresora](idEmpelado);
	}
	for i:= 1 to 3{
		receive solicitar_administrativo(id);
		send enviar_impresion(-999,"invalido");
	}
}

process Impresora [id:1..3]{
	Int idAdministrativo;
	Text documento_aImprimir;
	Text impresion;
	while (idAdministrativo<>-999){
		send solicitar_empleado(id);
		receive enviar_impresion[id]
									(idAdministrativo,documento_aImprimir);
		imprimir_documento(documento_aImprimir,impresion);
		send recibir_impresion[idAdministrativo](impresion);
	}
}
````

*Explicación:* Identico al inciso "d" se utilizan los for (en este caso el process coordinador ya estaba dado por la prioridad a los jefes).
