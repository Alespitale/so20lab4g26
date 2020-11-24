
# Laboratorio 4: Censurando un sistemas de archivos

## Parte 1: La censura

Para la censura cramos un archivo censored.h donde declaramos el arreglo dado por la catedra y modificamos la funcion fat_fuse_read. 
Declaramos un arreglo letters con el abecedario en minuscula y mayuscula en letters.h. 

### En fat_util.c

Re implementamos la funcion get_token_len para que tenga en cuenta las letras en vez de las puntuaciones, espacios, etc.
Creamos una funcion compare_char, que compara cada letra que apunta el bufer con cada letra del array letters. Y sumamos uno al tamaño de la palabra mientras se esten leyendo letras.
 
### En fat_fuse_read

Luego implementamos un while, el cual no termina hasta que no se hayan leido todo los bytes del archivo.
Implementamos un bool equal para comparar que el tam_token sea igual a algun len de las palabras del array words (esto lo hicimos con un for recorriendo el array words y comparando el tam_token con cada palabra).
Luego con un if usamos compare_token para comparar la palabra almacenada en el buffer con cada una de las palabras del array words, y un and con el bool equal para asegurarnos de que la palabra tiene al menos un tamaño igual a alguna de las del array.
Cuando se encuentra un match censuramos la palabra con las "X...X". Luego de esto tenemos un if en el cual si la palabra no estaba en el buffer aumentamos en 1 la variable del while y apuntamos el buffer una posicion adelante. Caso contrario la variable del while la movemos y, en el caso del bufer, apuntamos, el equivalente de los bytes del tamaño de la palabra censurada. 

## Parte 2:

Para esta parte nos ayudamos con las funciones de fat_table.c, lo que hicimos fue que mientras nos queden bytes y nuestro cluster sea el EOC, buscar en la fat table un cluster libre.
Una vez encontrado, hacemos que nuestro cluster actual setee en su entrada del directorio el siguiente cluster libre, el cual pasa a ser el nuevo EOC.
Y luego con la funcion get_next_cluster apuntamos al nuevo cluster creado.


## Parte 3:

### 3.1)

Luego de ejecutar el fat_fuse en foreground, vimos que la funcion que nunca deja de ejecutarse es fat_fuse_read_children, la cual queda esperando.
En esta funcion llamamos a fat_fuse_mknod para crear el archivo fs.log. Usamos un if para comprobar si fs.log ya estaba creado.
 Por otro lado al debuguear con el flag -d, encontramos cual es la funcion que actua como el "ls" de unix. Esta funcion es fat_fuse_readdir, aca agregamos un if dentro del while que compara si el nombre del archivo que se esta viendo es fs.log. Si lo es, simplemente aumentamos en uno el puntero para que de esta manera la funcion "saltee" el archivo. 
 Asi el ls de unix no detectaria el archivo fs.log previamente creado. 

### 3.2)

Lo que hicimos para completar la funcion propuesta por la catedra, fue primero obtener el volumen donde se monto, luego buscar el archivo en el arbol, y tambien el padre de este para poder usar la funcion fat_file_pwrite. 
Finalmente usamos esta misma para registrar los accesos a lectura o escritura en el archivo fs.log, para esto llamamos a la funcion log_activity en la funcion fat_fuse_read y fat_fuse_write.

### 3.3)

