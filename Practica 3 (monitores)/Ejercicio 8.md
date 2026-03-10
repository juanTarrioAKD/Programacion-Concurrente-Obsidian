
8. 
	 En un entrenamiento de fútbol hay 20 jugadores que forman 4 equipos (cada jugador conoce el equipo al cual pertenece llamando a la función DarEquipo()). Cuando un equipo está listo (han llegado los 5 jugadores que lo componen), debe enfrentarse a otro equipo que también esté listo (los dos primeros equipos en juntarse juegan en la cancha 1, y los otros dos equipos juegan en la cancha 2). Una vez que el equipo conoce la cancha en la que juega, sus jugadores se dirigen a ella. Cuando los 10 jugadores del partido llegaron a la cancha comienza el partido, juegan durante 50 minutos, y al terminar todos los jugadores del partido se retiran (no es necesario que se esperen para salir).

````c

Process jugador [id: 1 .. 20]{
	int equipo;
	int numero_cancha;
	DarEquipo(equipo);
	SalaEspera.esperar_equipo(equipo, numero_cancha)
	Cancha[numero_cancha].jugar_partido
}

Monitor SalaEspera {
	int cantidad_esperando [4] = ([4],0);
	int cancha_a_jugar [4];
	int cancha_disponible = 1;
	int equipos_disponibles = 4;
	cond esperando_partido [4];
	
	procedure esperar_equipo(in int equipo, out int numero_cancha){
		cantidad_esperando[equipo]++;
		if (cantidad_esperando[equipo]=5){
			signalall(esperando_partido[equipo]);
			cancha_a_jugar[equipo]=cancha_disponible;
			equipo_disponible--;
			if (equipos_disponibles=2){
				cancha_disponible++;
			}
		}
		else {
			wait(esperando_partido[equipo]);
		}
		numero_cancha=cancha_a_jugar[equipo];
	} 
}

Monitor Cancha [id:1..2]{
	cond esperando;
	int cant_jugadores=0;
	
	process jugar_partido (){
		cant_jugadores++;
		if (cant_jugadores=10){
			sleep(50 minutos)
			signalall(esperando)
		}
		else wait(esperando);
	}

}

````
