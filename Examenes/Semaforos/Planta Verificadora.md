
![[Tercer Anio/Concurrente/Imagenes/Imagen6.png]]


````c
sem mutex_espera = 1;
sem esperar_caja = 0;
sem pago [N] = 0;
sem ambulancia = 1;
sem auto = 1;
text comprobante [N];


process Vehiculo [id:1..N]{
	if (get.tipo()=="auto"){
		P(mutex_espera);
		push(cola(id));
		V(mutex_espera);
		V(espera_caja);
		P(pago[id]);
		-- ve comprobante en comprobante[id];
	}
	if (get.tipo()=="ambulancia"){
		P(ambulancia);
		push(cola_ambulancia(id));
		V(ambulancia);
	}
	else {
		P(auto);
		push(cola_auto(id));
		V(auto);
	}
	V(esperar_verificacion);
	P(verificacion[id]);
	-- se va
}

process Caja {
	while (true){
		P(espera_caja);
		P(mutex_espera);
		pop(cola(aux_id));
		V(mutex_espera);
		comprobante[aux_id]=generar_comprobante();
		V(pago[aux_id]);
	}

}

process Atencion
	while (true){
		P(espera_verificacion);
		if (cola_ambulancia.isnotempty()){
			P(ambulancia);
			pop(cola_ambulancia(aux_id));
			V(ambulancia)
		}
		else {
			P(auto);
			pop(cola_auto(aux_id))
			V(auto);
		}
		verificar(comprobante[aux_id]);
		V(verificacion[aux_id]);
	
	}

````