
1) En una oficina existen 100 empleados que envían documentos para imprimir en 5 impresoras compartidas. Los pedidos de impresión son procesados por orden de llegada y se asignan a la primera impresora que se encuentre libre: 
	a) Implemente un programa que permita resolver el problema anterior usando PMA. 
	b) Resuelva el mismo problema anterior pero ahora usando PMS.


### a.

````ruby
int E=100;
process Empleado [id:1..E]{
	text documento;
	int idImpresora;
	while (true){
		generar_pedido(documento);
		send imprimir(id,documento);
		recieve obtener_impresion[id](impresion);
	}
}

int I=5;
process Impresora [id:1..I]{
	int idEmpleado;
	text impresion;
	while (true){
		recieve imprimir(idEmpleado,documento);
		generar_impresion(impresion);
		send obtener_impresion[idEmpleado](impresion);
	}
}
````

---
### b.

````ruby
int E=100;
process Empleado [id:1..E]{
	int idImpresora;
	text documento;
	text impresion;
	while(true){
		generar_documento(documento);
		Buffer!solicitar_impresora(id,documento);
		Impresora[*]?obtener_impresion(impresion);
	}
}

process Buffer {
	cola buffer(id);
	int idEmpleado;
	int idImpresora;
	text documento;
	do Empleado[*]?solicitar_impresion(idEmpleado,documento) ->
					push(buffer, idEmpleado, documento);
	▢  not empty buffer; Impresora[*]?libre(idImpresora) ->
					Impresora[idImpresora]!dar_empleado(pop(buffer));
	od
}

int I=5;
process Impresora [id:1..I]{
	int idEmpleado;
	text documento;
	text impresion;
	while (true){
		Buffer!libre(id);
		Buffer?dar_empleado(idEmpleado,documento);
		generar_impresion(documento,impresion)
		Empleado[idEmpleado]!obtener_impresion(impresion);
	}
}
````