
11. 
	 En un vacunatorio hay un empleado de salud para vacunar a 50 personas. El empleado de salud atiende a las personas de acuerdo con el orden de llegada y de a 5 personas a la vez. Es decir, que cuando está libre debe esperar a que haya al menos 5 personas esperando, luego vacuna a las 5 primeras personas, y al terminar las deja ir para esperar por otras 5. Cuando ha atendido a las 50 personas el empleado de salud se retira. Nota: todos los procesos deben terminar su ejecución; suponga que el empleado tienen una función VacunarPersona() que simula que el empleado está vacunando a UNA persona.


````c

	 process persona [id: 0..49]{
		 P(mutex)
		 c.push(id);
		 V(llegue)
		 V(mutex)
		 P(listo[id])
		 V(mefui)
	 }

	 process empleado {
		 int personas = 0;
		 int id;
		 for i:= 0 .. 49{
			P(llegue)
			personas++
			if ((personas mod 5)=0){
				for j := 0 .. 4{
					P(mutex)
					c.pop(id)
					V(mutex)
					vacunar(id);
					V(listo[id])
					P(mefui)
				}
			}
		 }
	 }
	 