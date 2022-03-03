# Programmazione e laboratorio 1

## 0. Introduzione

Questi appunti non forniscono in alcun modo un metodo di studio, seguono il corso delle lezioni e non sono corretti e/o integrati in un secondo momento.

Il codice scritto negli esempi non é stato mai compilato, quindi potrebbe contenere errori di sintassi

Il prof Marin (che tiene il corso) **NON** é in nessun modo affiliato alla repo da cui viene questo file e di conseguenza **NON** é responsabile di eventuali errori presenti in esso
~~(molto probabilmente non é nemmeno a conoscenza di questi appunti)~~

### Licenza (CC BY-SA 4.0) 
![Attribution](https://creativecommons.org/images/deed/by.png)  ![ShareAlike](https://creativecommons.org/images/deed/sa.png)

Gli appunti sono rilasciati sotto licenza CC BY-SA 4.0
Ció rende possibile la redistribuzione del seguente materiale, anche con modifiche, gli unici due punti importanti di questa licenza sono:

- Devi dare il giusto credito all'autore, fornire un link alla licenza originale e indicare se sono state apportate modifiche.
- In caso di modifiche o utilizzo (anche indiretto) del materiale, devi distribuire a tua volta lo stesso sotto la medesima licenza. 

La copia integrale della seguente puó essere trovata [qui](https://creativecommons.org/licenses/by-sa/4.0/legalcode).

### Tipi
I tipi di dato sonod un insieme di valori sui quali sono definite delle operazioni

### Namespace

Le namespace permettono di dividere variabili e funzioni in vari "spazi", ció rende impossibile la sovrapposizione di firme (ad esempio fra il nostro codice e quello importato da una libreria)

Di default per usare una funzione o un oggetto di un altro namespace usiamo:

```c++
namespace::funzione();
```

ad esempio: 
```std::Vector() ``` _[...]_

Se invece vogliamo usare un certo namespace come default basterá aggiungere nel codice:

```using namespace <nomeNS>```c++

e quindi, questo ```std::Vector() ``` _[...]_ diventerá ```Vector() ``` _[...]_


### Reference

Una reference in C++ é un altro modo per riferirsi ad una variabile

```c++
int a; //dichiaro un intero
int &b = a; //ho dichiarato un reference
```

D'ora in poi scrivere ```a=4``` o ```b=4``` sará uguale

Le funzioni possono avere parametri per referenza, tuttavia **NON** é possibile passare espressioni ma solo left_values.
Data quindi una funzione ```swap``` con la seguente firma:

```c++
void swap(int &a,int &b){/*code*/}
```

effettuare una chiamata del tipo:
```c++
int x = 60;
int y = 350;
swap(x,y+70);
```
é **ILLEGALE**

Peculiaritá:

```c++
swap(x,y) = 80;
```

é totalmente legale _(approfondiremo fra poco)_

##1. Valore per reference:

```c++
int min1(int a,int b){
    if(a<b){
        return a;
    }
    return b;
}

int& min2(int &a,int &b){
    if(a<b){
        return a;
    }
    return b;
}

int x=7;
int y=8;
int z;

z = min1(x,y);
z=0;
z=min1(3,y);

int x=7;
int y=8;
min2(x,y) = 0; //é LEGALE come fosse una variabile
min2(3,y+2) = 0; //é ILLEGALE, per fare il passaggio per reference servono dei left value
```

## 2. Vettori
In C++ possiamo fare tutto del C, anche usare gli array in modo brutto e statico.

```c++
int v[30];
```

Questo metodo é deprecato e serve anche una variabile con la quale conto l'effettiva dimensione

```c++
int d;
```
##### IN C++ SIA I VETTORI CHE LE STRINGHE SONO STATE RIFATTE

Perché erano una m...

### Inizializzare un vettore:
```c++
#include <vectors>

std::vector<int> v;
```

potrei anche fare:
```c++
using std::vector;
```
per importare automaticamente vector dal suo namespace

Un altro costruttore per vector é:
```c++
std::vector<double> v2(50/*size, puó essere generato runtime*/,3.14/*valore di tutti gli elementi*/) 
```

Un terzo modo:
```c++
std::vector<int> v3(v); //costruzione per copia
```

###Assegnare valori

Possiamo farlo C-style:
```c++
int h=2;
v[h] = 4; //v in posizione h = 4
```
Peró **NON** controlla se andiamo out of index
infatti per essere piú sicuri conviene usare:
```c++
v.at(h) = 4; //stessa identica cosa
```
Perché con questo metodo il campo ```at``` verifica che siamo dentro al vettore

<br/>

Con i vector possiamo evitare la variabile con la dimensione dell'array:
```c++
for(int i=0;i<v.size;i++){
    //code
}
```
```size``` = dimensione array
```capacity``` = dimensione max prima di un riallocamento
la ```capacity``` la useremo poco dato che fa tutto il linguaggio

<br/>

Per aggiungere un elemento in coda:
```v.pushback(elem) //aggiunge elem in coda al vettore```c++

Per avere l'ultimo valore:
```v.back()```c++

### Altre cose fighe della classe vector

Il C++ fa automaticamente copie element by element negli assegnamenti fra vettori:
```c++
vector<int> v1 = {7,4,2};
vector<int> v2;

v2 = v1;
```
e rimangono indipendenti, ci permette anche di fare confronti element by element:
```c++
v2 = v1;
v2==v1 //TRUE;
```

Ovviamente i vettori si possono passare per copia o per reference e quindi

```c++
//copia
void stampa(std::vector<int> v){ /*cose*/}

//reference
void stampa(std::vector<int> &v){ /*cose*/}
```

Se voglio promettere al chiamante che non modifico il vettore uso una reference immodificabile:
```c++
void stampa(const std::vector<int> &v){ /*cose*/}
```
### Bubble Sort

1. Introduciamo una soglia, fino alla soglia é ordinata e tiene gli elementi piú piccoli
2. sopra la soglia prendo il piú piccolo e lo metto poco sopra la soglia
3. alzo la soglia di 1
4. ripeto fino a fine vettore

A livello pratico, confronto sempre due elementi e li swappo se non sono in ordine

```c++
void bubble(std::vector<int> &v){
    for(int soglia=0;soglia<v.size();soglia++){
        for(int i=v.size()-1;i>soglia;i--){
            if(v.at(i)<v.at(i-1)){
                swap(v.at(i),v.at(i-1));
            }
        }
    }
}
```
### Max di array

Nel seguente codice ```max``` é sempre > o = di tutti gli elementi nell'array

```c++
int massimo(const std::vector<int> &v){
    int max;
    for(int i=1;i<v.size();i++){
        if(v.at(i)>max){
            max = v.at(i);
        }
    }

    return max;
}
```

### Elemento in array

**NON** usare ```break;```,```continue;``` o ```return;```c++
e manco l'and con la flag...

```c++
bool contiene(const vector<int> &v,int el){
    bool trovato=False;
    for(int i=0;i<v.size(),i++){
        if(v.at(i)==el)
            trovato=true;
    }
    return trovato;
}
```
in sostanza ti attacchi e usi un while...

```c++
int i=0;
bool contiene(const vector<int> &v,int el){
    while(i<v.size()&&!trovato){
        if(v.at(i)==el)
            trovato=true;
        i++;
    }
    return trovato;
}
```

### Proprietá universali

Regola: proprietá su l'insieme vuoto ritorna true

ESEMPIO: tutti i numeri sono positivi:
```c++
bool universale(const std::vector<int> &v){
    bool resp=true;
    for(int i=0; i<v.size();i++){
        if(v.at(i) <= 0){
            resp=false;
        }
    }
    return resp;
}
```

## 3. Stringhe

Da importare con un include

```#include <string>```c++

Si inizializzano cosí:

```std::string l="Hello world"```c++

Valgono tutte le proprietá dei vettori e quindi si possono confrontare automaticamente

## 4. Puntatori ed iteratori

### Puntatori in c e c++

```c++
type a; //variabile
type* b = &a; //indirizzo di memoria di a
```
In c++ l'asterisco per convenzione lo mettiamo vicino il tipo

Per assegnare valori ad un puntatore:

```c++
double x,y;
double* px=&x;

*px = 3.14;

px = &y
*px = 7.2;

std::cout<<x; //3.14
std::cout<<y; //7.2
```

Scorrere array tramite aritmetica puntatori:
```c++
for(pv = vet;pv<vet+40;pv++){
    std::cout<<*pv<<std::endl; 
}
```

### Iteratori
In c++ abbiamo le classi container, per visitare gli elementi delle classi usiamo gli iteratori

Peculiaritá:
Gli iteratori funzionano indipendentemente dalla posizione dove sono salvati gli elementi

```c++
using namespace std::vector;
vector<int> v{1,3,5,7,-4,0,3}

vector<int>::iterator it;

for(it=v.begin();it!=v.end();it++){
    std::cout<<*it<<std::endl;
}
```
Somma elementi con iterator
```c++
using namespace std::vector;
vector<int> v{1,3,5,7,-4,0,3}

vector<int>::iterator it;

int somma=0;

for(it=v.begin();it!=v.end();it++){
    somma+=*it;
}
```

Dentro il ciclo for non si puó modificare (come dimensioni) il vector altrimenti gli iteratori vengono invalidati

### KeyWord AUTO

Dichiarazione normale:
```c++
tipo identificatore;
```

se abbiamo una dichiarazione con assegnamento possiamo usare 
```c++
auto var = cose
```

e sará il compilatore a mettere il tipo giusto

#### Estrarre un numero casuale:

in un intervallo [a,b]

```c++
#include <cstdlib>

x = random()%(b-a+1)+a;
```

## ESERCIZIO:

Mazzo di 40 carte estrarne una casuale senza estrarre due volte la stessa

```c++
#include <cstdlib>

//vettore di interi per vedere se carta é stata giá estratta
//estraggo numero y in posizione x
//prendo l'ultima carta e la metto in posizione x

int estrai(vector<int> & mazzo){
    int pos=random()%mazzo.size();
    int carta = mazzo.at(pos);
    mazzo.at(pos) = mazzo.back();
    mazzo.pop_back();
    return carta;
}

void inizializza(vector<int> &mazzo){
    mazzo.clear();
    for(int i=0;i<40;i++){
        mazzo.push_back(i);
    }
}

```

## 5. For Each

```c++
vector <int> v;
for(int i : v){
    std::cout<<e;
}
```

## 6. Struct in C++

```c++
#include <iostream>

struct prima{
    int a;
    double b;
};

int main(){
    prima v;
    v.a = 20;
    v.b = 34.5;
    std::cout << "val: " << v.b << std::endl

    return 0;
}
```

Convenzione: le struct iniziano con la lettera maiuscola
non abbiamo bisogno di scrivere sempre struct

### Shallow copy

```c++
prima v2

v2 = v1;
```

Questa operazione copia bit a bit di v1 su v2

```c++
v2.b[3] = v2.b[3]+1;
```

Legale!
Modifica v2 e **NON** v1

```c++
struct Prima{
    int a;
    double b[10];
    int * pc;
};

int main(){
    int buffer{20};
    Prima v1,v2

    v1.pc &buffer;

    v2=v1;

    *(v2.pc) = 24;
    *(v2.pc) = 22;
}
```

SPOILER: facendo copia bit a bit il puntatore delle due struct é uguale quindi fare v2.pc é come fare v1.pc

In questo esempio buffer == v1.pc == v2.pc

```c++
Prima* pstruct;
pstruct = new Prima;

```

```new``` operatore del linguaggio, é safe dal narrowing "WoW!"
Crea un oggetto in memoria dinamica

```delete``` operatore del linguaggio
Distrugge un oggetto in memoria dinamica

```c++
Prima* pstruct;
pstruct = new Prima;

pstruct->a = 20; // == (*pstruct).a
pstruct->b = 4.20;

delete pstruct;
```

### Costruttore

```c++
struct Prima{
    Prima(){
        std::cout<<"Hey, sono il costruttore!";
        a=15;
        b=4.20;    
    }
    int a;
    double b;
};
```
COSTRUTTORE: Funzione con stesso nome della struct e senza tipo di ritorno

NO PARAMETRI: significa costruttore di default

### Distruttore

```c++
struct Prima{
    ~Prima(){
        std::cout<<"Hey, sono il distruttore!";  
    }
    int a;
    double b;
};
```

il distruttore viene chiamato se faccio delete o finisce scope

Se faccio una new in una graffa interna devo fare una delete

### Copy constructor 

Quando passo per copia posso personalizzare il costruttore

```c++
Prima z = x //Prima z{x}   costruttore per copia
Prima* pz = new Prima(z) //costruttore per copia
```
##### Definizione copy constructor

```c++
struct Prima{
    Prima(const Prima& copia){
        a = copia.a; //il mio a (dell'istanza che creo) é uguale a copia.a (lo struct che avevo giá);
        b = copia.b;
    }
}
```

Esempio:

```c++
struct Vettore{
    int capacity;
    int size;
    int *vet;

    Vettore(){
        capacity = 100;
        size=0;
        vet = new int [100];
    }

    ~Vettore(){
        delete[] vet;
    }

    Vettore(const Vettore& copy){
        capacity = copy.capacity;
        size = copy.size;
        vet = new int[capacity];

        for(int i=0; i<size;i++){
            vet[i] = copy.vet[i];
        }
    }
}

```

ERRORI COMUNI:

```c++
struct Vettore{
    int capacity;
    int size;
    int *vet;

    Vettore(){
        capacity = 100;
        size=0;
        vet = new int [100];
    }

    ~Vettore(){
        delete[] vet;
    }
}

void stampa(Vettore x){
    //cose per la stampa;
}

int main(){
    Vettore a;
    a.size=2;
    a.vet[0] = 23;
    a.vet[0] = 33;

    stampa(a);
    a.vet[1]=0;
}

```

Quando finisce la stampa il vettore copiato x viene distrutto e siccome noi "puntiamo" ad un vettore liberiamo il vettore che é lo stesso di a, quindi a si trova senza vettore

### Metodi della struct

```c++
struct Vettore{
    int capacity;
    int size;
    int *vet;

    Vettore(){
        capacity = 100;
        size=0;
        vet = new int [100];
    }

    ~Vettore(){
        delete[] vet;
    }

    void resize(int newSize){
        if(newsize < capacity){
            size = newsize;
        }
        else{
            int* pc = new int[newsize];
            for(int i=0;i<size;i++){
                pc[i] = vet[i];
            }

            delete[] vet;
            vet = pc;
            capacity = newsize;
        }
    }

    /**
     * tramite const nella firma dichiariamo che
     * il metodo non modifica lo stato dell'oggetto
     */
    void stampa() const {
        for(int i=0;i<size; i++)
            std::cout<<vet[i]<<std::endl;
    }
};

```

#### Overloading

Lo stesso metodo puó avere piú versioni con piú parametri

```c++
struct Vettore{
    int capacity;
    int size;
    int *vet;

    Vettore(){
        capacity = 100;
        size=0;
        vet = new int [100];
    }

    Vettore(int dim, int val){
        std::cout<<"Fill constructor"<<std::endl;
        capacity = 2*dim;
        vet = new int[capacity];
        size=dim;
        for(int i=0;i<dim;i++)
            vet[i] = val;
    }
};

```
Come possiamo notare abbiamo il default constructor (quello senza parametri) e poi il secondo costruttore in Overloading che accetta i parametri ```(int dim, int val)```c++

**Il distruttore non puó essere in overloading**

### 7. Visibilitá in c++

Esistono 3 tipi di visibilitá
- Private
- Protected
- public

**NOTA:** la struct diventa una class da ora in poi

```c++
class Vettore{
    /** 
     *  queste variabili ora non possono essere lette/scritte
     *  fuori dalla classe.
     *  Questa operazione é detta incapsulamento
     */
    private:
        int capacity;
        int size;
        int *vet;
    
    /**
     * questi metodi invece sono pubblici
     */
    public:
    Vettore(){
        //default constructor ...
    }
}

```

##### ESEMPIO COMPLETO DI CLASSE:
```c++
class PascalString{
    private:
        char* str;
    public:
        PascalString(){
            //default constructor
            str = new char[256];
            str[0] = 0; //stringa vuota
        }
        
        const PascalString(const PascalString& c){
            //copyconstructor
            str = new char[256];
            for(int i=0;i<=c.str[0];i++){
                str[i]=c.str[i];
            }
        }

        PascalString(const char* cstr){
            str = new char[256];
            int i=0;
            while(cstr[i]!='\0' && i<255){
                str[i+1] = cstr[i++];
            }

            str[0] = i;
        }

        ~PascalString(){
            delete[] str;
        }

        void concatena(const PascalString& s){
            somma = s.str[0] + str[0];
            if(somma > 255){
                somma = 255;
            }

            int i=1;
            while(i<= str[0] && i+str[0]<=255){
                str[str[0]+(i)] = s.str[i++];
            }
            str[0] = str[0]+i-1;
        }
}
```
