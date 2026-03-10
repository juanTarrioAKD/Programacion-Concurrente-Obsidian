
![[Tercer Anio/Concurrente/Imagenes/Imagen2.png]]


````c

sem barrera [C] = ([C], 0);
sem mutex = 1;
sem solicitar_receta = 0;
sem recibir_receta = 0;
sem termine = 0;
cola cola;
int esperando = 0;
text recetas [0..C-1];


int esperando = 0;


process concursante [id: 0..C-1]{
	//modelado de barrera
	P(mutex);
	esperando++;
	if (esperando==C){ // si soy el ultimo despierto a todos
		for i := 0 to C-1 {
			V(barrera);
		}
	}
	V(mutex);
	P(barrera);
	//modelo concurso
	V(solicitar_receta); // aviso que quiero receta
	P(mutex);
	push(colaReceta(id));
	V(mutex);
	P(recibir_receta); // espero la receta
	cocinando_receta(recetas[id]); // cocino la receta que se me asigno
	P(mutex); // me encolo porque termine
	push(cola(id));
	V(mutex); // libero
	V(termine); // aviso al jurado que termine
}


process jurado {
	int id_aux;
	int notas [0..C-1];
	for i := 0 to C-1 {
		P(solicitar_receta);
		pop(cola(id_aux));
		asignar_receta(recetas[id_aux]);
		V(recibir_receta);
	}
	for i := 0 to C-1;
		P(termine);
		P(mutex);
		pop(cola(id_aux));
		V(mutex);
		notas[id_aux]=calcular_resultado();
}




````
