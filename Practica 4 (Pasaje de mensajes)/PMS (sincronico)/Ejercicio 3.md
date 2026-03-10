
3. En un examen final hay N alumnos y P profesores. Cada alumno resuelve su examen, lo entrega y espera a que alguno de los profesores lo corrija y le indique la nota. Los profesores corrigen los exámenes respetando el orden en que los alumnos van entregando. 
	a) Considerando que P=1. 
	b) Considerando que P>1. 
	c) Ídem b) pero considerando que los alumnos no comienzan a realizar su examen hasta que todos hayan llegado al aula. 
	Nota: maximizar la concurrencia; no generar demora innecesaria; todos los procesos deben terminar su ejecución. 


### a.

````ruby
process Alumno [id:1..N]{
	Text examen;
	Int resultado;
	tomar_examen(examen);
	resolver_examen(examen);
	Admin!entregar_examen(id,examen);
	Profesor?entregar_nota(resultado);
}

process Admin {
	cola buffer(Int,Text);
	do Alumno[*]?entregar_examen(idAlumno,examen) -> 
									push(buffer,idAlumno,examen);
	▢  (not empty(buffer)); Profesor?solicitar_examen() ->
							Profesor!enviar_aCorregir(pop(buffer)
	od					
}

process Profesores [id:1..P]{
	Int idAlumno;
	Text examen;
	Int resultado;
	while (true){
		Admin!solicitar_examen();
		Admin?enviar_aCorregir(idAlumno,examen);
		corregir(examen,resultado);
		Alumno[idAlumno]!entregar_nota(resultado);
	}
}
````

*Explicación:* En este ejercicio es necesario un administrador para que los alumnos puedan ir dejando lo exámenes y se respete el orden de entrega. Los alumnos resuelven el examen y lo depositan en la cola del admin, el profesor solicita el próximo examen a corregir y le envía al alumno la nota.

---
### b.

````ruby
process Alumno [id:1..N]{
	Text examen;
	Int resultado;
	tomar_examen(examen);
	resolver_examen(examen);
	Admin!entregar_examen(id,examen);
	Profesor[*]?entregar_nota(resultado);
}

process Admin {
	cola buffer(Int,Text);
	do Alumno[*]?entregar_examen(idAlumno,examen) -> 
									push(buffer,idAlumno,examen);
	▢  (not empty(buffer)); Profesor[*]solicitar_examen(idProfesor) ->
							Profesor[idProfesor]enviar_aCorregir(pop(buffer)
	od					
}

process Profesores [id:1..P]{
	Int idAlumno;
	Text examen;
	Int resultado;
	while (true){
		Admin!solicitar_examen(id);
		Admin?enviar_aCorregir(idAlumno,examen);
		corregir(examen,resultado);
		Alumno[idAlumno]!entregar_nota(resultado);
	}
}
````

*Explicación:* igual al inciso "a" pero al tener mas de un profesor los alumnos esperan la nota de cualquier profesor ([ * ]) y el profesor al solicitar un examen envía su id y el admin lo envía en la posición del id ([ idProfesor ]);

---
### c.

````ruby
process Barrera {
	for i:= 1..N{
		Alumno[*]?llegue();
	}
	for i:= 1..N{
		Alumno[i]!comenzar();
	}
}

process Alumno [id:1..N]{
	Text examen;
	Int resultado;
	Barrera!llegue();
	tomar_examen(examen);
	Barrera?comenzar();
	resolver_examen(examen);
	Admin!entregar_examen(id,examen);
	Profesor[*]?entregar_nota(resultado);
}

process Admin {
	cola buffer(Int,Text);
	do (cant<=N); Alumno[*]?entregar_examen(idAlumno,examen) -> 
									push(buffer,idAlumno,examen);
									cant++;
	▢  (not empty(buffer)) or (cant<=N); 
							Profesor[*]solicitar_examen(idProfesor) ->
							Profesor[idProfesor]enviar_aCorregir(pop(buffer)
	od					
	for i:=1..P{
		Profesor[*]solicitar_examen(idProfesor) ->
		Profesor[idProfesor]enviar_aCorregir(-999,"invalido");
	}
}

process Profesores [id:1..P]{
	Int idAlumno;
	Text examen;
	Int resultado;
	Bool seguir=true;
	while (seguir){
		Admin!solicitar_examen(id);
		Admin?enviar_aCorregir(idAlumno,examen);
		if (idAlumno==-999){
			seguir=false;
		}
		else{
			corregir(examen,resultado);
			Alumno[idAlumno]!entregar_nota(resultado);
		}
	}
}
````

*Explicación:* Igual al inciso "b" pero maneja una barrera que primero espera a que todos los alumnos lleguen y luego les avisa a todos que pueden comenzar. Agrego metodo para finalizar los procesos.
