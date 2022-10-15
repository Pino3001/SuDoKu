# SuDoKu
Proyecto de programación 1


#include <iostream>
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include<windows.h>
#include <string.h>

const int amateur= 70;
const int facil= 60;
const int normal= 40;
const int dificil= 20;
const int muydificil= 25;
const int leonhardEuler= 17;
const int tam=9;
const int cantElem=81;
const int ayudasP=5;

/// Crea Estructura partidas, para contabilizar los resultados de las partidas de Jugador.
struct Partidas{
    int puntaje;
    char resultado[5];
};

///Crea Estructura "Jugador".
struct Jugador{
    char alias[6];
    char nombre[21];
    char apellido[21];
    int cedula;
    int edad;
    int punTotal=0;
    int partidasJugadas=0;
    Partidas game[3];
} players[10];

//FUNCIONES PARA GENERAR UN TABLERO DE SUDOKU COMPLETO.

/// Define los colores de las celdas, dependiendo si es un elemento colocado por defecto en el tablero o un elemento para que el usuario coloque.
void color_celda(char s[100],int celdaAux, int celdaUSR);

/// Llama a la funcion "SetConsoleTextAttribute" contenida en la libreria windows, para poder utilizarla en la cuncion color_celda.
void imprimir_con_color(char s[100], int c, int color);

/// Genera una matriz de 9x9 completa de ceros.
void inicia_matriz(int matrizSuD[9][9]);

/// Imprime el tablero.
void imprimir_tablero(int matrizSuD [9][9],int matrizUSR[9][9]);

/// Indica el numero del cuadrante.
int numero_cuadrante(int fila, int columna);

/// Busca posibles candidatos para las pociciones (fila, columna y cuadrante).
void crear_candidato(int matriz[9][9], int fila, int columna, bool candidatos[9]);

/// Busca si existe un candidato exlusivo para la (fila, columna y cuadrante) de la matrizSuD, de encontrarlo, retorna el valor de este.
int candidato_exclusivo(int matrizSuD[9][9]);

/// Busca en la funcion crear candidatos si existe algun candidato unico, de ser asi retorna el valor del candidato a colocar.
int candidato_unico(int matrizSuD[9][9], int pos1, int pos2);

/// Itera en la matrizSuD de forma random, buscando candidatos unicos y exclusivos, hasta completar el tablero con 81 elementos, en caso de no poder encontrar esos 81 elementos, se fuerza la salida.
void completar_tablero(int matrizSuD[9][9], int fila, int columna, int &colocar);

/// Itera la funcion completar tablero, hasta que en una de estas el tablero quede completo con 81 elementos colocados de forma random.
void sudoku_vadido(int matrizSud[9][9],int matrizUSR[9][9]);

//FUNCIONES PARA JUGAR.

/// Limpia el buffer.
void limpia_buffer();

/// "Borra" numeros de la matrizSuD (ya completa con los 81 elementos) de manera aleatoria, a modo de que el jugador pueda volver completarlo. La cantidad de numeros a borrar es elegida por el propio usuario.
void quitar_numeros_tablero( int matrizUSR[9][9],int matrizAux[9][9]);

/// Recibe datos ingresados por el usuario para interactuar con la matriz a la que se le han quitado valores. A su vez controla que el usiario haya ingresado bien dichos datos.
void leer_datos(int matrizSuD[][9], int matrizUSR[9][9], bool &sal);

/// Detecta si el juego fue terminado, devolviento un false en caso contrario.
int sudoku_resuelto(int matrizSuD[][9], int matrizUSR[][9]);

/// Copia matrices para poder comparar y generar cambios en esta sin cambiar la matrizSuD.
void copia_matrices(int matrizUSR[9][9],int matrizSuD[9][9]);

///Compara la matrizUSR con la matrizSuD(matriz completa), colocando en un espacio que contenga un 0 de la matrizUSR el valor que tiene la matrizSuD en la misma posicion.
void generar_ayuda(int matrizUSR[][9], int matrizSuD[9][9], int &cantAyu);

/// Compara la matrizUSR con la matrizSuD, si no existe una diferencia, el sudoku tiene solucion y devuelve true, en caso contrario este es irresoluble y devuelve false.
bool es_resoluble(int matrizSuD[][9], int matrizUSR[][9]);

/// Permite al usuario ingresar una posicion del tablero para quitar un elemento de este, el elemento solo puede ser retirado si no pertenece a la matrizAux, la cual contiene los elementos iniciales del tablero.
void usuario_quita_celda(int matrizUSR[9][9], int matrizAux[9][9]);

//Estructuras

/// Imprime un menu de opciones.
void menu_opciones();

/// Lee los datos ingresados por el usuario.
void leer_linea(char arr[], int tam);

/// Itera la estructura Jugadore imprimiendo sus datos, a su vez ordena estos datos de mayor a menor puntaje.
void lista_jugadores_ingresados(Jugador players[], int cantJ);

/// Recibe un alias del usuario y lo busca dentro de la estructura Jugador. Si existe, imprime los dastos el alias como tambien el historial de las ultimas partidas contenido el la estructura Partidas.
void buscador_de_datos(Jugador players[],char buscarAlia[], int cantJ);

/// Comprueba la cantidad de ingresos correctos del usiario y la dificultad que este eligio, devolviendo un entero con el puntaje.
int genera_puntaje(int matrizSuD[][9], int matrizUSR[][9], int matrizAux[9][9],int cantAyu,int quite,int gane);

/// Elimina un usuario del array players contenido en la estructura jugador.
void eliminar_usuario(Jugador players[], int &cantJ);

/// Permite modificar datos de la estructura Jugador que hayan sido ingresados por el usuario, con eexcepcion del alias.
void modificar_datos(Jugador players[10], int cantJ, char aliasJ[6]);

/// Función para cargar los datos ingresados en la estructura Jugador, tambien comprueba que el alias ingresado no este usado.
Jugador cargar_datos_jugador(Jugador players[], int cantJ);

int main()
{
    srand(time(NULL));
    int  matrizSuD[9][9], matrizUSR[9][9], matrizAux[9][9], cantJ= 0;
    bool salir=false;
    char opc, aliasJ[6];

    printf("-----BIENVENIDO!!!-----\n");
    do{
        int indx= 0, resJuego= 0;
        bool  esUSR= false;

        menu_opciones();
        opc= getchar();
        limpia_buffer();
        switch(opc){
        case 'L': case 'l':
            printf("<<Lista de jugadores ingresados.>>\n");
            lista_jugadores_ingresados(players,cantJ); //Imprime el listado de jugadores guardados.
            break;
        case 'R': case 'r':
            printf("<<Registrar usuario nuevo.>>\n");
            players[cantJ]=cargar_datos_jugador(players, cantJ);  //En el array players, lugar que diga cantJ guarda los datos del jugador.
            cantJ++;
            if(cantJ==10)
                printf("***Ya no puedes ingresar mas jugadores.***\n");
            break;
        case 'D': case 'd':
            printf("<<Datos del Jugador.>>\n");
            printf("Ingrese el alias del jugador a buscar:");
            leer_linea(aliasJ, 6);
            buscador_de_datos(players, aliasJ, cantJ);
            break;
        case 'J': case 'j':
            printf("    <<Jugar>>\n");
            printf("Ingrese su alias:");
            leer_linea(aliasJ, 6);
            for(indx=0; indx<10; indx++){
                if(strcmp(aliasJ, players[indx].alias)==0){
                    esUSR= true;
                    break;
                }
            }
            if(indx==10)
                printf("***Ese jugador no existe***\n");
            break;
        case 'M': case 'm':
            printf("<<Modificar datos usiario>>\n");
            printf("El alias del jugador a modificar:");
            leer_linea(aliasJ, 6);
            for(int i=0; i<cantJ; i++){
                if(strcmp(aliasJ, players[i].alias)==0){
                    modificar_datos(players,i,aliasJ);//Cargo los nuevos datos sin modificar el alias.
                    break;
                }
                else
                    printf("***Ese jugador no existe.***");
            }
            break;
        case 'E': case 'e':
            printf("<<Eliminar jugador.>>\n");
            eliminar_usuario(players, cantJ);
            break;
        case 'S': case 's':
            printf("\n      -----Hasta pronto!!!-----\n");
            salir= true;
            break;
        }
        if(esUSR){
            int  quite= 0, cantAyu= 0, gane= 0, puntos= 0, i= 0;

            sudoku_vadido(matrizSuD,matrizUSR);
            quitar_numeros_tablero( matrizUSR, matrizAux);
            imprimir_tablero(matrizAux,matrizUSR);
            while(resJuego==0){ //Mientras el sudoku no este resuelto se siguen pidiendo los datos y se vuelve a imprimir el tablero.
                char opciones;
                printf("\n       Opciones su nuevo sudoku.\n[H]Ayuda. [B]Borrar. [R] Rendirse. [C] Comprobar si hay solucion.\n[J]Para seguir jugando:");
                opciones= getchar();
                limpia_buffer();
                printf("\n");
                switch(opciones){
                case 'h': case 'H':
                    if(cantAyu<ayudasP){
                        generar_ayuda(matrizUSR,matrizSuD,cantAyu);
                        imprimir_tablero(matrizAux,matrizUSR);
                        resJuego=sudoku_resuelto(matrizSuD,matrizUSR);
                    }
                    else{
                        printf("***Ya has alcanzado el maximo de ayudas disponibles.***\n");
                        imprimir_tablero(matrizAux,matrizUSR);
                    }
                    break;
                case 'b': case 'B':
                    usuario_quita_celda(matrizUSR, matrizAux);
                    imprimir_tablero(matrizAux,matrizUSR);
                    quite++;

                    break;
                case 'c': case 'C':
                    if(es_resoluble(matrizSuD, matrizUSR))
                        printf("-Existe una solucion posible para este sudoku.-\n");
                    else
                        printf("-El sudoku no es resoluble.-\n");
                    break;
                case 'r': case 'R':
                    imprimir_tablero(matrizAux,matrizSuD);
                    resJuego= 3;
                    break;
                default:
                    bool sal= false;

                    do{
                        printf("Has utilizado %d ayudas de %d disponibles. | ", cantAyu, ayudasP);
                        leer_datos(matrizUSR,matrizSuD,sal);
                        printf("\nSi desea volver al menu ingrese [M]");
                        resJuego=sudoku_resuelto(matrizSuD,matrizUSR);
                        imprimir_tablero(matrizAux,matrizUSR);
                        if(resJuego==2 || resJuego==1)
                            sal= true;
                    }while(!sal);
                }
                if(players[indx].partidasJugadas>2){
                    for(i=2; i>0; i--)// Dejo el indice 0 libre para colocar la ultima partida, asi poder ser vista en los detalles. Solo muestro 3.
                        players[indx].game[i]= players[indx].game[i-1];
                }
                else{
                    for(i=players[indx].partidasJugadas; i>0; i--)
                        players[indx].game[i]= players[indx].game[i-1];
                }
                if(resJuego==2){
                    printf("-Felicidades, has completado el sudoku!!!-\n");
                    strcpy(players[indx].game[0].resultado, "Win");
                    gane= 50;
                }
                else if(resJuego==1){
                    printf("El sudoku no fue completado de manera exitosa.\n");
                    strcpy(players[indx].game[0].resultado, "Lose");
                }
                else if (resJuego==3){
                    printf("Has decidido abandonar el juego, tu puntaje es 0.\n\n");
                    strcpy(players[indx].game[0].resultado, "Lose");
                }
            }
            limpia_buffer();
            puntos=genera_puntaje(matrizSuD,matrizUSR,matrizAux,cantAyu,quite,gane);
            if(resJuego==3)
                puntos= 0;
            players[indx].punTotal+= puntos;
            players[indx].game[0].puntaje= puntos;
            players[indx].partidasJugadas++;
            printf("\nSu puntaje en esta partida es de: %d\n", puntos);
        }
    }
    while(!salir);
    return 0;
}

//FUNCIONES DE ESTRUCTURAS.
int genera_puntaje(int matrizSuD[][9], int matrizUSR[][9], int matrizAux[9][9],int cantAyu,int quite,int gane ){
    int cantIguales=0, numDificultad=0, puntos=0;

    for(int pos1=0; pos1<tam; pos1++){ //Cuento la cantidad de posiciones iguales.
        for(int pos2=0; pos2<tam; pos2++){
            if( matrizUSR[pos1][pos2] == matrizSuD[pos1][pos2])
                cantIguales++;
        }
    }
    for(int pos1=0; pos1<tam; pos1++){//Cuento cuantas celdas estaban colocadas al inicio.
        for(int pos2=0; pos2<tam; pos2++){
            if(matrizAux[pos1][pos2] != 0)
                numDificultad++;
        }
    }           //dificultad                 //Bien colocados.
    puntos =(cantElem - numDificultad)*(cantIguales - numDificultad);//El puntaje es exponencial, mayor dificultad, mayor los puntos que se van a conseguir.
    puntos=(puntos-(cantAyu*11+quite*2))+gane;//Resto por ayudas, sumo por victoria.
    return puntos;

}

void lista_jugadores_ingresados(Jugador players[10], int cantJ){ ///solo imprime los datos de los jugadores
    int posJ = 1;
    Jugador aux;

    if(cantJ == 0)
        printf("\n***No hay registro de jugadores.***\n");
    else{
        for(int i=0; i<cantJ-1; i++){//Ordeno por puntajes.
            for(int j=0; j<cantJ-i-1; j++){
                if (players[j].punTotal < players[j+1].punTotal){
                    aux = players[j];
                    players[j] = players[j+1];
                    players[j+1] = aux;
                }
            }
        }
        for(int i=0; i<cantJ; i++){//Imprimo de mayor puntaje total a menor.
            printf("|Posicion:%2d", posJ);
            printf("|Alias:%5s", players[i].alias);
            printf("|Nombre:%15s", players[i].nombre);
            printf("|Apellido:%15s", players[i].apellido);
            printf("|Edad:%3d", players[i].edad);
            printf("|Puntaje total:%6d|\n",players[i].punTotal);
            posJ++;

        }
    }
}

void buscador_de_datos(Jugador players[],char buscarAlia[],int cantJ){
    for(int j=0; j<cantJ; j++){
        if(strcmp(buscarAlia, players[j].alias)==0){ //Compara el alias dado con el guardado, si son iguales imprime los datos.
            printf("Nombre: %s\n", players[j].nombre);
            printf("Apellido: %s\n", players[j].apellido);
            printf("Edad: %d\n", players[j].edad);
            printf("Puntaje total: %d\n",players[j].punTotal);
            printf("<<Historial ultimas partidas.>>\n");
            int nroPartida = players[j].partidasJugadas;// Imprimo la ultima partida de inicio.
            if(nroPartida==0)
                    printf("No hay partidas jugadas.");
            else{
                int minimo=3;// Solo imprimo 3 partidas.
                if(nroPartida<3)//Menos de tres partidas, imprimo solo las que hay.
                    minimo=nroPartida;
                for(int i=0; i<minimo; i++){//Detalles de las ultimas 3 partidas.
                    printf("|Partida numero: %d",nroPartida);
                    printf("|El resultado fue: %s",players[j].game[i].resultado);
                    printf("|El puntaje fue de: %d\n",players[j].game[i].puntaje);
                    nroPartida--;// Resto al numero de partidas hasta completar las 3 que mostrare.
                }
            }
        }
    }
}

void eliminar_usuario(Jugador players[], int &cantJ){
    char aliasJ[5];
    int j= -1;

    printf("El alias del jugador a eliminar es:");
    leer_linea(aliasJ, 5);
    for(int i=0; i<cantJ; i++){
        if(strcmp(aliasJ, players[i].alias)==0){
            j = i;//Posicion en la que se encuentra el numero a borrar.
            break;
        }
    }
    if(j < 0)
        printf("***El jugador que desea eliminar no existe.***\n");
    else{
        for(int i=j; i<cantJ-1; i++)//Muevo la posicion fuera del array.
            players[i]=players[i+1];
    }
    cantJ--;//Un jugador menos.
}

void menu_opciones(){
    printf("\n <<Menu Principal.>>\n\n");
    printf("(L)istado de Jugadores.\n");
    printf("(R)egistrar Jugador.\n");
    printf("(J)ugar.\n");
    printf("(M)odificar datos.\n");
    printf("(E)liminar jugador.\n");
    printf("(D)atos de Jugador.\n");
    printf("(S)alir.\n");
}

void leer_linea(char arr[], int tamArray){
    int i=0;
    char letra;

    letra=getchar();
    while(letra!='\n' && i<tamArray-1){//Leo hasta un \n o hasta llegar al tamaño.
        arr[i]=letra;
        i++;
        letra= getchar();
    }
    arr[i]='\0';
    while(letra!='\n')  //Limpio el buffer.
        letra= getchar();
}

void modificar_datos(Jugador players[10], int indice, char aliasJ[6]){
    char edad[5];
    int numero;

    printf("Nombre: ");
    leer_linea(players[indice].nombre, 21);
    printf("Apellido: ");
    leer_linea(players[indice].apellido, 21);
    printf("Edad: ");
    leer_linea(edad, 5);
    numero=atoi(edad);
    players[indice].edad=numero;
    printf("\n");

}

Jugador cargar_datos_jugador(Jugador players[10], int cantJ){
    char edad[5];
    int numero;
    bool repetido= false;
    Jugador retorno;

    printf("Alias: ");
    leer_linea(retorno.alias, 6);
    do{
        repetido= false;
        for(int i=0; i<cantJ; i++){
            if(strcmp(retorno.alias, players[i].alias)==0){//Compruebo que no sea repetido.
                repetido= true;
                break;
            }
        }
        if(repetido){
            printf("***El alias ingresado ya esta en uso.***\n");
            printf("Ingrese un nuevo alias:");
            leer_linea(retorno.alias, 6);
        }
    }while(repetido);
    printf("Nombre: ");
    leer_linea(retorno.nombre, 21);
    printf("Apellido: ");
    leer_linea(retorno.apellido, 21);
    printf("Edad: ");
    leer_linea(edad, 5);
    numero=atoi(edad);
    retorno.edad=numero;
    printf("\n");

    return retorno;
}

//FUNCIONES QUE PERMITEN JUGAR.
void generar_ayuda(int matrizUSR[9][9], int matrizSuD[9][9], int &cantAyu){
    int fila, columna;
    bool salida= true;

    do{
        fila= rand()%tam;
        columna= rand()%tam;
        if(matrizUSR[fila][columna]==0){
            matrizUSR[fila][columna] = matrizSuD[fila][columna];//Coloco en el tablero del usiario una celda random del tablero completo.
            printf("Se coloco en la fila |%d|, en la colunma |%d| el numero |%d|.", fila+1, columna+1, matrizUSR[fila][columna]);
            cantAyu++;
            salida= false;
        }
    }while(salida);
}

bool es_resoluble(int matrizSuD[][9], int matrizUSR[][9]){
    for(int pos1=0; pos1<tam; pos1++){
        for(int pos2=0; pos2<tam; pos2++)
            if(matrizUSR[pos1][pos2]!=0){//Si encuentro una diferencia en el tablero del usuario con respecto al original, no es resoluble.//Existe una unica solucion.
                if (matrizUSR[pos1][pos2]!=matrizSuD[pos1][pos2])
                    return false;
            }
    }
    return true;
}

void usuario_quita_celda(int matrizUSR[9][9], int matrizAux[9][9]){
    int fila, columna;

    printf("Elija la fila y la columna de la celda a quitar.\n");
    printf("Fila:");
    scanf("%d", &fila);
    printf("Columna:");
    scanf("%d", &columna);
    limpia_buffer();
    if((columna>0 || fila>0) && (columna< 10 || fila<10)){
        fila--;
        columna--;
        if(matrizUSR[fila][columna]!=0){
            if(matrizUSR[fila][columna]!=matrizAux[fila][columna])//Si no es una celda inicial.
                matrizUSR[fila][columna] = 0;//Libero un espacio en la celda indicada.
            else
                printf("***Esa celda es una celda inicial de este sudoku!***");
        }
        else
            printf("***La celda que desea quitar no esta ocupada.***");
    }
    else
        printf("***Ingrese una posicion valida.***");
}

int sudoku_resuelto(int matrizSuD[][9], int matrizUSR[9][9]){
    int esIgual=0;

    for(int pos1=0; pos1<tam; pos1++){
        for(int pos2=0; pos2<tam; pos2++){
            if( matrizUSR[pos1][pos2]==0)//Si hay cero, entonces no termino la partida.
                return 0;
        }
    }
    for(int pos1=0; pos1<tam; pos1++){
        for(int pos2=0; pos2<tam; pos2++){
            if( matrizUSR[pos1][pos2]== matrizSuD[pos1][pos2])//Se encontro esa unica solucion.
                esIgual++;
        }
    }
    if(esIgual<81)//No hay ceros, pero no son iguales las matrices.
        return 1;
    else
        return 2;//No hay ceros y son iguales.
}

void leer_datos(int matrizUSR[][9], int matrizSuD[9][9], bool &sal){
    int fila, columna, num;

    printf("Inserte las coordenadas (fila/columna):\n");
    printf("Fila:");
    scanf("%d",&fila);
    printf("Columna:");
    scanf("%d",&columna);
    limpia_buffer();
    fila--;
    columna--;

    if(fila>=0 && columna>=0 && fila<9 && columna<9){//Compruebo los parametros ingresados por el usuario.
        if(matrizUSR[fila][columna]==0){
            printf("Valor: ");
            scanf("%d",&num);
            if(num>0 && num<10)//Compruebo si es un numero valido.
                matrizUSR[fila][columna] = num;
            else
                printf("***El numero ingresado no es un numero valido.***");
        }
        else
            printf("***Esa posicion ya esta ocupada.***");
    }
    else{
        printf("\n***La posicion sale de los parametros del tablero.***");
        sal=true;//Una posicion fuera de los parametros, lleva al menu del sudoku en curso.
    }

}

void quitar_numeros_tablero(int matrizUSR[9][9], int matrizAux[9][9]){
    int fila, columna, cantAborrar=0;
    char dif;
    inicia_matriz(matrizAux);
    printf("\nElija la dificultad con la que jugar:\n");
    printf("(A)mateur, (F)acil, (N)ormal, (D)ificil, (M)uy dificil, (L)eonhard Euler :");
    dif= getchar();
    limpia_buffer();
    switch(dif){
    case 'A':case 'a': cantAborrar=81-amateur; break;
    case 'F': case'f': cantAborrar= 81-facil; break;
    case 'N': case 'n': cantAborrar= 81-normal; break;
    case 'D': case 'd': cantAborrar= 81-dificil; break;
    case 'M':case 'm': cantAborrar=81-muydificil; break;
    case 'L':case 'l': cantAborrar=81- leonhardEuler; break;
    default: /*cantAborrar= 2*/;cantAborrar=81-amateur; printf("***El comando ingresado no es valido.*** \n");//Se activa lo deshabilitado y se testea mas facil.
    }
    while(cantAborrar>0){ // A cantElem (81) le resto los elementos de forma random.
        fila=rand()%tam;
        columna=rand()%tam;
        if(matrizUSR[fila][columna]!=0){
            matrizUSR[fila][columna] = 0;// Si la posicion en la que toco, habia un numero distinto de 0, lo cambio por un 0 para dejarlo como "vacio".
            cantAborrar--;
        }
    }
    copia_matrices(matrizUSR, matrizAux);
}

void copia_matrices(int matrizSuD[9][9],int matrizUSR[9][9]){
    int pos1, pos2;

    for(pos1=0; pos1<9; pos1++){
        for(pos2=0; pos2<9; pos2++)
            matrizUSR[pos1][pos2] = matrizSuD[pos1][pos2];
    }
}

void limpia_buffer(){
    char aux;
    aux= getchar();
    while(aux!='\n')
        aux= getchar();
}

//FUNCIONES PARA CREAR UN TABLERO COMPLETO.
void crear_candidato(int matrizSuD[9][9], int fila, int columna, bool candidatos[9]){
    int cont, cont2;
    for(cont=0; cont<tam; cont++){ //Marco a todos los numeros del 1 al 9 como posibles candidatos dentro del array.
        candidatos[cont]= true;
    }
    for(cont=0; cont<tam; cont++){
        if(matrizSuD[fila][cont]!=0)//Si la posicion(fila, cont) es distinta a 0, el array en esa posicion devuelve false.
            candidatos[matrizSuD[fila][cont]-1]= false;
    }
    for(cont=0; cont<tam; cont++){ //Itera la fila con la columna que fue llamada.
        if(matrizSuD[cont][columna]!=0)
            candidatos[matrizSuD[cont][columna]-1]= false;
    }
    for(cont2=0; cont2<tam; cont2++){
        for(cont=0; cont<tam; cont++){
            if(numero_cuadrante(cont2+1, cont+1) == numero_cuadrante(fila+1, columna+1)){ //Utiliza la funcion crear cuadrante para comparar con la fila y la columna dada, que valores se encuentran dentro del mismo. El +1 es porque la funcion numero_cuadrante va del 1 al 9 y los numeros de los cuadrantes estan del 1 al 9.
                if(matrizSuD[cont2][cont]!=0)
                    candidatos[matrizSuD[cont2][cont]-1]= false;//Si se encontro un valor dentro, coloca un false en el array para los cuadrantes.
            }
        }
    }
}

int candidato_exclusivo(int matrizSuD[9][9], int pos1, int pos2){
    bool candidatos[9], candidatos2[9];
    int cont, cont2, cont3;

    crear_candidato(matrizSuD, pos1, pos2, candidatos);// Obtiene candidatos, en pos x.
    for(cont=0; cont<tam; cont++){
        if(matrizSuD[pos1][cont]==0 && cont!= pos2){ //Si entra es un elemento de la misma fila, vacio y distinto de lo que voy a colocar.
            crear_candidato(matrizSuD, pos1, cont, candidatos2);// Busco los candidatos en dicho lugar.
            for(cont2=0; cont2<tam; cont2++){ // Todo lo que sea candidato de otro lugar de la fila se descarta como candidato exclusivo en (pos1, pos2).
                if(candidatos2[cont2])
                    candidatos[cont2] = false;
            }
        }
    }
    for(cont=0; cont<tam; cont++){
        if(matrizSuD[cont][pos2]==0 && cont!= pos1){ //Si entro es un elemento de la misma columna, vacio y distinto de lo que voy a colocar.
            crear_candidato(matrizSuD, cont, pos2, candidatos2);//Busco candidatos en dicho lugar.
            for(cont2=0; cont2<tam; cont2++){ // Todo lo que sea candidato de otro lugar de la columna se descarta como candidato unico en (pos1, pos2).
                if(candidatos2[cont2])
                    candidatos[cont2] = false;
            }
        }
    }
    for(cont2=0; cont2<tam; cont2++){
        for(cont=0; cont<tam; cont++){
            if(numero_cuadrante(cont2+1, cont+1) == numero_cuadrante(pos1+1, pos2+1) && (pos1!=cont2 || pos2!= cont)){ // Si entra en el if, estamos en el mismo cuadrante que (pos1, pos2).
                if(matrizSuD[cont2][cont]==0){
                    crear_candidato(matrizSuD, cont2, cont, candidatos2);//Busco los candidatos.
                    for(cont3=0; cont3<tam; cont3++){
                        if(candidatos2[cont3])// Todo lo que sea candidato de otro lugar del cuadrante se descarta como candidato exclusivo en (pos1, pos2).
                            candidatos[cont3] = false;
                    }
                }
            }
        }
    }
    int valorExclusivo= -1, cantCand=0;
    for(cont=0; cont<tam; cont++){ //Itero para recorrer el array de candidatos.
        if(candidatos[cont]){ //Si candidatos en la posicion cont es true, sumo uno a la variable cantCand y tomo a cont como el valor de valorExclusivo, en caso de que se cumpla mas de una vez la condicion significa que no hay candidato exclusivo.
            cantCand++;
            valorExclusivo= cont;//La posicion del array donde se encontro ese candidato, es quivalente al valor del candidato.
        }
    }
    if(cantCand>1)//No hay un candidato unico en (pos1, pos2)
        return 0;
    else
        return valorExclusivo+1;// Si solo existe 1 candidato, este es el candidato exclusivo y la funcion retorna el valor de este.
}

int candidato_unico(int matrizSuD[9][9], int fila, int columna){
    bool candidatos[9];
    int cont, valorUnico=-1, cantCand=0;;
    crear_candidato(matrizSuD, fila, columna, candidatos);

    for(cont=0; cont<tam; cont++){ //Itero para recorrer el array de candidatos.
        if(candidatos[cont]){ //Si candidatos en la posicion cont es true, sumo uno a la variable cantCand y tomo a cont como el valor de valorUnico, en caso de que se cumpla mas de una vez la condicion significa que no hay candidato unico.
            cantCand++;
            valorUnico = cont;//La posicion del array donde se encontro ese candidato, es quivalente al valor del candidato unico.
        }
    }
    if(cantCand>1)
        return 0;
    else
        return valorUnico+1;// Si solo existe 1 candidato, este es el candidato unico y la funcion retorna el valor de este.
}

void completar_tablero(int matrizSuD[9][9], int &colocado){
    int fila, columna, canti= 0, unico, cant2= 100, num, exclusivo;//cant2 es una variable de salida.
    bool esCandidato[9];
    while(canti<cantElem && cant2>0){ //Mientras no logre completar el tablero y no haya recorrido una gran cantidad de veces el while, completo aleatoriamente el tablero.
        fila= rand()%9;
        columna= rand()%9;

        if(matrizSuD[fila][columna]==0){
            bool ingreso= false;//Variable que sera necesaria en la linea 225.

            unico=candidato_unico(matrizSuD, fila, columna);
            if(unico>0 && canti>20){//Me aseguro tener una cantidad de celdas ingresadas antes de buscar un candidato unico.
                matrizSuD[fila][columna] = unico;
                canti++;
            }
            else{
                exclusivo=candidato_exclusivo(matrizSuD, fila, columna);
                if (exclusivo>0){ //Si encontro un candidato exlusivo, lo coloco en la posicion de la matriz.
                    matrizSuD[fila][columna] = exclusivo;
                    ingreso = true;
                    canti++;//Sumo un numero mas en la matriz.
                }
                else{ //Si no existia un candidato exclusivo en esa posicion random, llamo a crear_candidato.
                    crear_candidato(matrizSuD, fila, columna, esCandidato);;
                    bool salgo= false, b= false;
                    num= rand()%9;//Genero un numero random.
                    while(!salgo){
                        if(esCandidato[num]){ //Y me fijo si ese numero en el array es true.
                            if(!salgo){ //De ser asi. lo coloco, sumandole 1 para que sea un valor entre 1 y 9.
                                matrizSuD[fila][columna]=num+1;
                                ingreso = true;
                                canti++;
                                salgo = true;//Fuerzo la salida del while.
                            }
                            else
                                b = true;
                        }
                        else{
                            if(num==8){//Si se cumplen estas dos conciciones fuerzo la salida del while para que no quede en un loop.
                                if(!b)
                                    salgo = true;
                            }
                            else
                                num++;//Sumo a la variable para buscar otro numero que devuelva true en la posicion generada.
                        }
                    }
                }
                if(!ingreso && canti>25){ //Si en lo que va de la ejecucion he hecho cambios en la matriz, entro en esta condicion.
                    cant2--;//Resto a la variable que fuerza la salida.
                    for(int pos1=0; pos1<tam; pos1++){ //Itero por todas las posiciones de la matriz buscando en los lugares que ya coloque elementos.
                        for(int pos2=0; pos2<tam; pos2++)
                        {
                            if(matrizSuD[pos1][pos2]!=0){
                                exclusivo=candidato_exclusivo(matrizSuD, pos1, pos2);//Cuando encuentro un elemento colocado, que tiene un candidato exclusivo, cambio ese elemento por este candidato.
                                if (exclusivo>0 && matrizSuD[pos1][pos2] != exclusivo)
                                    matrizSuD[pos1][pos2] = exclusivo;//En esa posicion va a quedar el nuevo candidato exclusivo.//Aclaracion! el candidato quitado va a ser un candidato unico de esta posicion, por lo tanto si se vuelve a iterar toda la matriz el cambio se va a descartar, pero al solo entrar en esta condicion cuando realiza cambios en la matriz permite generar un gran aumento de posibilidades de completar la matriz, asi como permitir colocar numeros en posiciones donde antes no era posible.
                            }
                        }
                    }
                }
            }
        }
    }
    colocado= cantElem - canti;//Resto canti a la cantidad de elementos(81), cuando esta sea 0 (tablero completo) se forzara la salida de la funcion generar_sudoku_valido.
}

void sudoku_vadido(int matrizSuD[9][9],int matrizUSR[9][9]){
    int intentos= 70, cantidad;

    do{
        cantidad= 81;//Siempre inicio cantidad en 81, para asegurarme de que se este comenzando siempre desde el inicio.
        inicia_matriz(matrizSuD);
        completar_tablero(matrizSuD,cantidad);//Llamo a la funcion completar tablero para que me devuelva cantidad(cantElemen) y asi poder comprobar si el sudoku fue completado.
        intentos--;//Resto a intentos para forzar la salida en caso de no encontrar una matriz completa.
    }while(cantidad>0 && intentos>0); //Mientras cantidad no sea 0 o ya no se hayan realizado todos intentos de generar un sudoku valido se seguira iterando.

    inicia_matriz(matrizUSR);
    if(intentos<1)
        printf("***Ups! Algo salio mal, intentelo nuevamente.***");
    else
        copia_matrices(matrizSuD,matrizUSR);//Copio para luego poder quitar elementos para el usuario.
}

int numero_cuadrante(int fila, int columna){
//Da un valor del 1 al 9 a cada cuadrante del tablero segun la posicion de las filas y las columnas.
    if(fila<4){
        if(columna<4)
            return 1;
        if(columna<7)
            return 2;
        return 3;
    }
    if (fila<7){
        if(columna<4)
            return 4;
        if(columna<7)
            return 5;
        return 6;
    }
    if(columna<4)
        return 7;
    if(columna<7)
        return 8;
    return 9;
}

void imprimir_con_color(char s[1000], int c, int color){
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),color);//Funcion de la libreria windows.
    printf(s,c);
}

void color_celda(char s[100],int celdaAux, int celdaUSR){
    if(celdaAux==0)
        imprimir_con_color(s, celdaUSR, 15);//Si es celda para el usuario pinto planco.
    else
        imprimir_con_color(s, celdaAux, 4);//Si es celda original pinto rojo.
}

void imprimir_tablero(int matrizSuD [9][9],int matrizUSR[9][9]){
    int pos1, larBarr;

    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),6);//Color
    printf("\n           SuDoKu!\n");
    printf("    1 2 3   4 5 6   7 8 9\n");//Imprime las coordenadas de las filas del tablero.
    printf("  %c",201);//Esquina superior derecho.

    for(pos1=0; pos1<tam*2+5; pos1++) //Imprime el techo del tablero
        printf("%c",205);

    printf("%c\n",187);//Esquina superior izquierda.
    for(pos1=0; pos1<9; pos1++){ //Imprime las filas con sus respectivas separaciones, junto a las posiciones de la matriz y las coordenadas de las columnas.
        SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),6);//Color.
        printf("%d ", pos1+1);
        printf("%c ", 186);
        for(int con=0; con<3; con++){
            color_celda("%d",matrizSuD[pos1][con], matrizUSR[pos1][con]);//Dependiendo a que matriz pertenece la celda es el color que se imprime.
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),6);//Color.
            if(con!=2)
                printf("|");
        }
        printf(" %c ", 186);
        for(int con=3; con<6; con++){
            color_celda("%d",matrizSuD[pos1][con], matrizUSR[pos1][con]);
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),6);//Color.
            if(con!=5)
                printf("|");
        }
        printf(" %c ", 186);
        for(int con=6; con<9; con++){
            color_celda("%d",matrizSuD[pos1][con], matrizUSR[pos1][con]);
            SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),6);//Color.
            if(con!=8)
                printf("|");
        }
        printf(" %c\n", 186);
        if(pos1%3==2 && pos1!=8){ //Imprimo las separaciones inferiores del tablero.
            printf("  %c",204);
            for(larBarr=0; larBarr<tam*2+5; larBarr++) //Mientras el largo de la base (largB) es menor a la ecuacion 2n+5, se imprime barras horizontales.
                printf("%c",205);
            printf("%c\n",185);
        }
    }
    printf("  %c",200);//Esquina inferior izquierda.
    for(larBarr=0; larBarr<tam*2+5; larBarr++) //Basse del trablero.
        printf("%c",205);

    printf("%c\n",188);//Esquina inferior derecha.
    SetConsoleTextAttribute(GetStdHandle(STD_OUTPUT_HANDLE),15);//Color.
}

void inicia_matriz(int matrizSuD[9][9]){
    int pos1, pos2;
    //Inicializa la matriz completandola con 0.
    for(pos1=0; pos1<tam; pos1++){
        for(pos2=0; pos2<tam; pos2++)
            matrizSuD[pos1][pos2] = 0;
    }
}

