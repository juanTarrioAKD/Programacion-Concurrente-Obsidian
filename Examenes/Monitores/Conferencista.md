
![[Imagen10.png]]


````c
process Conferenciasta [id:1..20]{
	Sala.esperar();
	-- presenta 10 minutos
	Sala.terminar();

}

process Coordinador {
	Sala.abrir_puerta();
	-- presentar 30 minutos
	Sala.terminar();
}

Monitor Sala {
	int cant=0;
	cond espera_coordinador;
	cond espera_conferencista;
	
	cond retirarse;

	procedure esperar (){
		cant++
		if (cant==20){
			signal(espera_coordinador);
		}
		wait(espera_conferencista)
	}
	
	procedure abrir_puerta(){
		if(cant<20){
			wait(espera_coordinador);
		}
	}
	
	procedure terminar (){
		terminaron++;
		if (terminaron==21){
			signallall(retirarse);
		}
		else {
			signal(espera_conferencista);
			wait(retirarse);
		}
	}
	
}


`````