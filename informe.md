
# Laboratorio 4: Censurando un sistemas de archivos

## Parte 1: La censura
Para la censura cramos un archivo censored.h donde declaramos el arreglo dado por la catedra y modificamos la funcion fat_fuse_read. Aqui implementamos un while, en el cual mientras no se hayan leido todo los bytes del archivo, comparamos la palabra almacenada en el buffer y la comparamos con cada una de las palabras del array words, utilzando la funciones get_token_len y compare_token.
Cuando se encuentra un match censuramos la palabra con las "X". Luego de esto tenemos un if en el cual si la palabra no estaba en el buffer aumentamos en 1 la variable del while y apuntamos el buffer una posicion adelante. Caso contrario la variable del while la movemos (y apuntamos en el caso de buffer) el equivalente de los bytes del tamaño de la palabra censurada. 
Este ciclo continua hasta que se hayan leido todos los bytes del archivo deseado.

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