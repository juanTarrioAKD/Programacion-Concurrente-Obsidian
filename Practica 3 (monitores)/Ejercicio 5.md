
5. 
	 En un corralón de materiales se deben atender a N clientes de acuerdo con el orden de llegada. Cuando un cliente es llamado para ser atendido, entrega una lista con los productos que comprará, y espera a que alguno de los empleados le entregue el comprobante de la compra realizada. 
	 a) Resuelva considerando que el corralón tiene un único empleado. 
	 b) Resuelva considerando que el corralón tiene E empleados (E > 1). Los empleados no deben terminar su ejecución. 
	 c) Modifique la solución (b) considerando que los empleados deben terminar su ejecución cuando se hayan atendido todos los clientes.


	 process cliente [id: 1..N]{
		list lista;
		comprobante comprobante;
		cargar_lista(lista);
		corralon.dejar_lista(id,lista,comprobante);
	 }

	 process empleado (){
		comprobante comprobante;
		int id_cliente;
		for i := 1 to N-1{
			corralon.tomar_lista(id_cliente,lista);
			cargar_compra(lista,comprobante);
			corralon.entregar_comprobante(id_cliente,comprobante);
		}
	 }

	Monitor corralon {
	 cond espera[id];
	 cond empleado;
	 cola cola;
	 bool esperando = false;
	 comprobante comprobanteAEntregar;
	 int aux_id;
	 list aux_lista;
	 
	procedure dejar_lista (id,in list lista,out comprobante comprobante){
		if (esperando){
			signal(empleado);
			esperando = false;
		}
		push(cola,id,lista);
		wait(espera[id]);
		comprobante=compranteAEntregar;
		signal(empleado);
	}

	procedure tomar_lista (out int id_cliente, out list lista){
		if (cola.isempty()){
			esperando=true;
			wait(empleado);
		}
		pop(cola,aux_id,aux_lista);
		id_cliente=aux_id;
		lista=aux_lista;
		}
	}

	procedure entregar_comprobante (in int id, in comprobante comprobante){
		 signal(espera[id]);
		 comprobanteAEntregar=comprobante;
		 wait(empleado);
		}
	}