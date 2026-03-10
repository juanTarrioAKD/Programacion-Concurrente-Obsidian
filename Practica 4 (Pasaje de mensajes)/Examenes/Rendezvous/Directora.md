
Resolver con ADA el siguiente problema. En una empresa están buscando nuevos empleados, para esto la Directora de Recursos Humanos tomara un examen escrito a 20 personas. Cuando una persona llega le da sus datos personales a la Directora para que esta lo registre en el sistema (de acuerdo al orden de llegada). Cuando han llegado (y se han registrado) las 20 personas, la directora les entrega a todos el examen que deben resolver. Cuando una persona termina de resolver su examen se lo entrega a la Directora y espera a que esta le de la calificación, Nota: suponga que existen las funcionalidades Resolver_Examen (llamada por las personas para simular la resolución del examen), Corregir_Examen (llamada por la directora para simular la corrección de un examen) y Registrar_Persona (llamada por la directora para simular que registra los datos de una persona en el sistema).

````ruby
procedure Directora is

	Task type Persona is
		Entry repartir_examen(examen:IN Text);
	end Persona;
	arrPersona : array (20) of Persona;
	Task body Persona is
		Text datos;
		Text examen;
		Int nota;
	begin
		obtener_datos(datos);
		Directora.registrarse(datos);
		Accept repartir_examen(examen:IN Text) do
			examen=examen;
		end repartir_examen;
		resolver_examen(examen);
		Directora.entregar_examen(examen,nota);
	end Persona
	
	Task Directora is
		Entry registrar(datos:IN Text);
		Entry entregar_examen(examen:IN Text,nota:OUT Int);
	Task body Directora is
	begin
		for i:=1..20{
			Accept registrarse(datos:IN Text) do
				registrar_persona(datos);
			end registrarse;
		}
		for i:=1..20{
			obtener_examen(examen);
			arrPersona(i).entregar_examen(examen);
		}
		for i:=1..20{
			Accept entregar_examen(examen:IN Text, nota:OUT Int) do
				corregir_examen(examen,nota);
			end entregar_examen;
		}
	end Directora;

begin
 null;
end Directora;
````
