
4. 
	 Suponga que existe una BD que puede ser accedida por 6 usuarios como máximo al mismo tiempo. Además, los usuarios se clasifican como usuarios de prioridad alta y usuarios de prioridad baja. Por último, la BD tiene la siguiente restricción: • no puede haber más de 4 usuarios con prioridad alta al mismo tiempo usando la BD. • no puede haber más de 5 usuarios con prioridad baja al mismo tiempo usando la BD. Indique si la solución presentada es la más adecuada. Justifique la respuesta.

````c

	 sem altaPrioridad = 4
	 sem bajaPrioridad = 5
	 sem mutex = 6
	 
	 
	 Process usuarioAltaPrioridad [id: 0..C-1]{
		P(altaPriodad)
		P(mutex)
		-- acceso a la base de datos
		V(mutex)
		P(altaPrioridad) 
	 }

	 Process usuarioBajaPrioridad [id: 0..C-1]{
		 P(bajaPrioridad)
		 P(mutex)
		 -- acceso a la base de datos
		 V(mutex)
		 V(bajaPrioridad)
	 }

