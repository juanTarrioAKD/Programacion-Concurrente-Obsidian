
4. En una clínica existe un médico de guardia que recibe continuamente peticiones de atención de las E enfermeras que trabajan en su piso y de las P personas que llegan a la clínica ser atendidos. Cuando una persona necesita que la atiendan espera a lo sumo 5 minutos a que el médico lo haga, si pasado ese tiempo no lo hace, espera 10 minutos y vuelve a requerir la atención del médico. Si no es atendida tres veces, se enoja y se retira de la clínica. Cuando una enfermera requiere la atención del médico, si este no lo atiende inmediatamente le hace una nota y se la deja en el consultorio para que esta resuelva su pedido en el momento que pueda (el pedido puede ser que el médico le firme algún papel). Cuando la petición ha sido recibida por el médico o la nota ha sido dejada en el escritorio, continúa trabajando y haciendo más peticiones. El médico atiende los pedidos dándole prioridad a los enfermos que llegan para ser atendidos. Cuando atiende un pedido, recibe la solicitud y la procesa durante un cierto tiempo. Cuando está libre aprovecha a procesar las notas dejadas por las enfermeras.

````ruby
Procedure Ejercicio4 is

	Task Medico is
		Entry solicitar_atencionEnfermo;
		Entry solicitar_atencionEnfermera;
	end Medico;
	
	Task Nota is
		Entry dejar_nota(nota:in text);
		Entry solicitar_nota(nota:out text);
	End Nota;
	Task body Nota is
		notas: cola(text);
		loop
			SELECT
				Accept dejar_nota(nota: in text) do
					push(notas, nota);
				end dejar_nota;
			OR
				when(not empty(notas)) -> 
					Accept solicitar_nota(nota: out text) do
						pop(notas, nota);
					end solicitar_nota;
			END SELECT;
		end loop;
	end Nota;
	
	Task body Secretario is
		nota: text;
		loop
			Nota.solicitar_nota(nota)
			Medico.recibir_nota(nota)
		end loop;
	end Nota;

	Task type Paciente is
	arrPacientes: array (1..E+P);
	Task body Paciente is
	begin
		if ("soy persona"){
			SELECT
				Medico.solicitar_atencionEnfermo;
			OR DELAY (60*5)
				cant:=0;
				while (cant<3){
					Delay(10*60);
					SELECT
						Medico.solicitar_atencionEnfermo;
					ELSE
						cant++;
					END SELECT;
				}
			END SELECT;
		}
		else {
			loop
				SELECT
					Medico.solicitar_atencionEnfermera;
				ELSE
					Nota.dejar_nota(nota);
				END SELECT;
			end loop
		}
	end Paciente;
	
	Task Medico is 
		loop
			SELECT
				where (solicitar_atencionEnfermo==0) ->
					Accept solicitar_atencionEnfermera do
						resolver_pedido();
					end solicitar_atencionEnfermera;
			OR 
				Accept solicitar_atencionEnfermo do
					resolver_pedido();
				end solicitar_atencionEnfermo;
			OR 
				where (solicitar_atencionEnfermo==0) 
								and (solicitar_atencionEnfermera==0) ->
					Accept recibirNota (N IN text) do
						nota = N ;
					end solicitar_atencionEnfermo; 
					resolver_nota(nota);
			END SELECT;
		end loop;
	end Medico;

begin
 null;
end Ejercicio4
````