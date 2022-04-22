# Dynamiczna alokacja pamięci



## Alokowanie pamięci

Dotychczas wszystkie zmienne oraz tablice, deklarowane były statycznie. Taka deklaracja jest bardzo prosta i intuicyjna, a zarządzanie pamięcią obsługiwane jest przez komputer. W praktyce taka pamięć alokowana jest podczas uruchomienia programu i zwalniana po jego wyłączeniu. Rozwiązanie to pozbawia jednak programisty optymalizacji zużycia pamięci. Może zdarzyć się również, że oszacowany rozmiar danych jest niedostatecznie duży z punktu widzenia użytkownika programu. Rozwiązaniem tych problemów jest właśnie dynamiczne zarządzanie pamięcią komputera. Narzędzie to pozwala objąć kontrolę przez programistę nad używanymi zasobami, zwalniając i rezerwując pamięć w trakcie działania programu. Takie podejście nakłada jednak pewien obowiązek na programistę. Kiedy programista przejmuje kontrolę nad obsługą pamięci to musi pamiętać aby poprawie się po niej poruszać oraz w odpowiednim momencie ją zwolnić. Brak uwagi może kosztować programistę błędy krytyczne oraz wycieki pamięci.

W języku ANSI C mamy do czynienia z trzema funkcjami, służącymi do rezerwacji pamięci. Są to `calloc` `malloc` oraz `realloc`. Funkcje te zaimplementowane są w bibliotece standardowej o nazwie `stdlib.h`.

## Funkcja malloc(), free() oraz siezeof()

Funkcja `malloc` jest najpopularniejszą i najprostszą w swoim działaniu. Za argument funkcja ta przyjmuje zmienną typu `size_t` (zmienna całkowitoliczbowa), która oznacza ona liczbę bajtów jaką chcemy zaalokować na zmienną. Wartością jaką zwraca funkcja `malloc`, jest wskaźnik typu `void` na pierwszy bajt zaalokowanej pamięci. Do zwolnienia pamięci służy funkcja `free`. Za argument przyjmuje ona wskaźnik, do pierwszego bajtu zaalokowanej pamięci.

Biblioteka `stdlib.h` udostępnia również operator `sizeof`, która umożliwia nam sprawdzenia jaki rozmiar zajmuje dany typ zmiennych prostych oraz złożonych. Użycie tego rozwiązaniu w kontekście alokacji przekazuje odpowiedzialność za częściowe oddanie odpowiedzialności obliczeniowej kompilatorowi. 

*Składnia operatora sizeof*
```
sizeof unary-expression
sizeof ( type-name )
```



*Przykład (14.0) alokacja pamięci za pomocą funkcji malloc()*

```
#include <stdio.h>
#include <stdlib.h>

int main() 
{
	int* wsk; 
	/* dynamiczna alokacja zmiennej typu int */
	wsk = (int*) malloc (sizeof(int));
	
	*wsk = 6;
	printf (" %d", *wsk);
	/* zwolnienie pamięci */
	free (wsk);
	
	/* tutaj nie wiemy co się wyświetli ponieważ już nad tym obszarem pamięci nie mamy kontroli */
	printf (" %d", *wsk);
	
	return 0;
}
```

*Wynik działania program*

> 6 12386640

Analizując przykład warto zwrócić uwagę na jawną konwersję zwracanego przez `malloc` wskaźnika 	`void*` na wskaźnik typu `int*`. Każda alokacja za pomocą tej funkcji powinna być jawnie konwertowana na odpowiedni typ. Zachowanie tej zasady daje programiście czytelność kodu oraz zapobiega wystąpieniu niektórych błędów.


## Funkcja calloc()

Funkcja `calloc` jest wykonuje to samo zadanie co funkcja `malloc` i zwraca wskaźnik `void*` do zaalokowanej pamięci, jednak przyjmuje inne argumenty. Pierwszym z nich jest ilość jest zmienna całkowita oznaczająca ilość komórek, a drugim rozmiar komórki. Dodatkowo funkcja `calloc` zeruje zaalokowaną pamięć.

*Przykład (14.1) alokacja pamięci za pomocą funkcji calloc()*


```
int main() 
{
	int* wsk; 
	/* dynamiczna alokacja zmiennej typu int */
	wsk = (int*) calloc (1,sizeof(int));
	
	*wsk = 6;
	printf (" %d \n", *wsk);
	printf (" %d \n", wsk[0]);

	free (wsk);
	return 0;
}

```

*Wynik działania programu*

> 6
>
> 6

W przykładzie 14.1 wykorzystana została własność, która była przedstawiona w rozdziale z tablicami. Pamiętamy bowiem, że nazwa tablicy jest wskaźnikiem na jej pierwszy element, czyli taka dereferencja za pomocą nawiasów kwadratowych jest legalna w języku C, czyli zapis wsk[0] jest poprawny, mimo że nie mamy do czynienia z tablicą.

## Dynamiczne alokowanie tablic

W praktyce dynamicznej alokacji pamięci używamy do tworzenia tablic dynamicznych. W tym celu należy wykorzystać opisane wyżej funkcje `malloc` i `calloc`. Dodatkowo jedynie musimy zadbać o zalokowanie więcej niż jednej komórki pamięci danego typu jak było to przedstawione w przykładach 14.0 i 14.1. Przykład 14.2 obrazuje implementację dynamicznej tablicy wielowymiarowej oraz różne sposoby jej wyświetlania. Aby dobrze zrozumieć ideę dynamicznego alokowania pamięci polecam dokładnie przeanalizować poniższy kod i podczas jego analizy przypomnieć sobie poznane narzędzia. Jest to ten etap nauki języka C gdzie trzeba zacząć łączyć pozyskaną wiedzę w całość.

*Przykład 14.2 dynamiczne alokowanie pamięci tablic za pomocą `malloc` i `calloc`*

```
#include <stdio.h>
#include <stdlib.h>

void printArrayOfInt_1(int* ptr, int size)
{
	int i;
	for(i=0; i<size; i++)
	{
		printf("%d ", ptr[i]);
	}
	printf("\n");
}

void printArrayOfInt_2(int* ptr, int size)
{
	int* tmp;
	for(tmp = ptr ; tmp < &ptr[size]; tmp++)
	{
		printf("%d ", *tmp);
	}
	printf("\n");
}

void printArrayOfInt_3(int* ptr, int size)
{
	int i;
	for(i=0; i<size; i++)
	{
		printf("%d ", *(ptr+i));
	}
	printf("\n");
}

int main() 
{
	int* wskCalloc,wskMalloc; 
	/* dynamiczna alokacja tablicy typu int */
	wskCalloc = (int*) calloc (6,sizeof(int));
	wskMalloc = (int*) malloc (6*sizeof(int));
	
	printf("Calloc:\n");
	printArrayOfInt_1(wskCalloc, 6);
	printArrayOfInt_2(wskCalloc, 6);
	printArrayOfInt_3(wskCalloc, 6);
	
	printf("Malloc:\n");
	printArrayOfInt_1(wskMalloc, 6);
	printArrayOfInt_2(wskMalloc, 6);
	printArrayOfInt_3(wskMalloc, 6);


	free (wskCalloc);
	free (wskMalloc);
	return 0;
}

```

*Wynik działania programu*

>Calloc:
>
>0 0 0 0 0 0
>
>0 0 0 0 0 0
>
>0 0 0 0 0 0
>
>Malloc:
>
>11229728 0 11206992 0 1413563472 977485121
>
>11229728 0 11206992 0 1413563472 977485121
>
>11229728 0 11206992 0 1413563472 977485121

### Dynamiczne tablice wielowymiarowe

Problematycznym może wydawać się zaimplementowanie tablic wielowymiarowych, bowiem nie ma narzędzi które pozwolą nam stworzyć taką tablicę. Jednak można stworzyć pewną konstrukcję która będzie mieć własności tablicy wielowymiarowej. Konstrukcja ta będzie swego rodzaju zagnieżdżeniem dynamicznej tablicy, wewnątrz dynamicznej tablicy. Implementacja taka polega dosłownie na zaimplementowaniu dynamicznej tablicy tablic.

*Schemat (14.0) tablica wielowymiarowa stworzona dynamicznie.*

![image](https://user-images.githubusercontent.com/71324202/164782319-116a283f-4632-4a5c-b1f4-f1f8b564c33d.png)


*Przykład (14.3) dynamiczna implementacja tablicy wielowymiarowej*

```
#include <stdio.h>
#include <stdlib.h>

int main() 
{
	int** Array2Dim;
	
	/* tworzenie tablicy wskaźników */
	Array2Dim = (int**) malloc (5*sizeof(int*));
	
	/* tworzenie tablicy int-ów z każdego wskaźnika tablicy wskaźników */
	int i;
	for(i=0; i<5; i++)
	{
		Array2Dim[i] = (int*) calloc (6, sizeof(int));
	}
	
	int x,y;
	for(x=0 ; x<5 ; x++)
	{
		for(y=0 ; y<6 ; y++)
		{
			Array2Dim[x][y] = (x+1)*(y+1);
			printf("%4d ", Array2Dim[x][y]);
		}
		printf("\n");
	}
	
	for(i=0; i<5; i++)
	{
		free(Array2Dim[i]);
	}
	free(Array2Dim);
	return 0;
}

```

*Wynik działania programu*
>   1    2    3    4    5    6
>
>   2    4    6    8   10   12
>
>  3    6    9   12   15   18
>
>   4    8   12   16   20   24
>
>  5   10   15   20   25   30


Na podstawie przykładu 14.3 zagłębię się teraz we wskażniki oraz ich dereferecję występującą w powyższym kodzie. Do tego zadania skorzystamy z schematu analogicznego do schematu 14.0:

![image](https://user-images.githubusercontent.com/71324202/164782530-c3c52adb-62cb-4cf6-b0e0-616a842efb7e.png)

Teraz odpowiadamy sobie na pytanie, gdzie zaniesie nas odwołanie się do wartości `Array2Dim`.

![image](https://user-images.githubusercontent.com/71324202/164782415-5e691d1e-5a5f-47a3-a175-fbbc2b3f7816.png)

Jeżeli użyję zapisu `Array2Dim[3]` lub `*(Array2Dim+3)`  to korzystając z własności dereferrencji odwołam się do 4 elementu tablicy wskaźników.

![image](https://user-images.githubusercontent.com/71324202/164782461-8fe0104c-8087-44c5-b3b9-7f7f6d72fa38.png)

I teraz czas na kolejną dereferencję przy użyciu drugiego nawiasu kwadratowego `Array2Dim[3][2]` lub `*(*(Array2Dim+3)+2)`

![image](https://user-images.githubusercontent.com/71324202/164782487-e399bfb8-f9e0-46db-8218-3a15cfc2fd3a.png)

## Funkcja realloc()

Funkcja `realloc`, służy do ponownej alokacji pamięci. W praktyce używa się jej do zmienienia rozmiaru tablicy w trakcie działania programu. Za argumenty przyjmuje ona wskaźnik na pierwszy element zaalokowanej pamięci, oraz nowy rozmiar zaalokowanej pamięci. Zwraca podobnie jak funkcje `malloc` i `calloc` wskaźnik `void*` do miejsca pierwszego zaalokowanego elementu.

*Przykład (14.4) zmiana rozmiaru zaalokowanej tablicy*

```
#include <stdio.h>
#include <stdlib.h>

int main() 
{
	int* wsk = (int*) calloc (6,sizeof(int));
	
	int* tmp;
	for(tmp=wsk ; tmp-wsk < 6 ; tmp++)
	{
		*tmp = tmp-wsk;
	}
	
	for(tmp=wsk ; tmp-wsk < 6 ; tmp++)
	{
		printf("%d ", *tmp);
	}
	
	printf("\n");
	
	/*zmiana rozmiaru tablicy */
	wsk = (int*) realloc (wsk, 7*sizeof(int));
	
	wsk[6] = 3;
	
	for(tmp=wsk ; tmp-wsk < 7 ; tmp++)
	{
		printf("%d ", *tmp);
	}
	
	free(wsk);
	
	return 0;
}

```

*Wynik działania programu*

>0 1 2 3 4 5
>
>0 1 2 3 4 5 3



## Zadania do samodzielnego wykonania:

*WKRÓTCE*

***
[Poprzednia część](https://github.com/CyberMALab/Wlasne-funkcje.git) | [Spis treści](https://github.com/CyberMALab/Wprowadzenie-do-programowania-w-j-zyku-ANSI-C.git) | [Następna część](https://github.com/CyberMALab/Comming-Soon.git)
***
&copy; Cyberspace Modelling and Analysis Laboratory
