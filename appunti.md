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
I tipi di dato sono un insieme di valori sui quali sono definite delle operazioni

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

## 7. Classi in C++

Il funzionamento é simile a quello delle struct

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

#### ESEMPIO COMPLETO DI CLASSE:
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
};
```

## 8. Liste

Una lista di elementi di tipo T é 
  1. Una lista vuota
  2. Un elemento di tipo T seguito da una lista di elementi di tipo T

7 -> 9 -> 5 -> 4 -> 2 -> 1

La lista vuota viene codificata con un puntatore a nullptr

```c++
    struct TCella{
        int info;
        TCella* next;
    };

    typedef TCella* Tlista;

```

Funzioni:

```c++
Tlista crea(){
    return nullptr;
}
```
```c++
void prepend(Tlista& t, int el){
    Tlista nuova;
    nuova = new TCella;
    nuova->info = el;
    nuova->next = t;
    t = nuova;
}
```
```c++
void append(Tlista& t,int el){
    Tlista pc = t;
    if(t!=nullptr){
        while(pc->next != nullptr){
            pc = pc->next;
        }
        pc->next = new TCella;
        pc->next->next = nullptr;
        pc->next->info = el;
    }
    else{
        prepend(t,el);
    }
}
```
```c++
void append(Tlista& t,int el){
    if(t==nullptr)
        prepend(t,el);
    else
        append(t->next,el);
}
```
```c++
void distruggi(Tlista& t){
    if(t!=nullptr){
        distruggi(t->next);
        delete(t);
        t = nullptr;
    }
}
```
```c++
int somma(Tlista& t){
    if(t == nullptr){
        return 0;
    }
    else{
        return t->info + somma(t->next);
    }
    
}
```
```c++
int massimo(Tlista& t){ //assumo l != nullptr
    if(l->next == nullptr){
        return l->info
    }
    int max = massimo(l->next);
    if(max > l->info)
        return max;
    return l->info;
}
```
```c++
/*trova elemento*/
bool presente(Tlista& t,int el){
    if(t == nullptr)
        return false;
    return (t->info == el) || presente(t->next, el); 
    //invertendo funzionerebbe ma non non sarebbe efficente
}
```
```c++
Tlista presente(Tlista& t,int el){
    if(t == nullptr)
        return nullptr;
    
    if(t->info==el)
        return t;
    
    return presente(t->next,el);
}
```
```c++
Tlista presente(Tlista& t,int el){
    if(t == nullptr)
        return nullptr;
    
    Tlist pe = presente(t->next, el);
    if(pe==nullptr){
        if(t->info==)
            return t;
        else
            return nullptr;
    }else{
        return pe;
    }
}
```
```c++
void stampa(Tlista& t){
    if(t!=nullptr){
        std::cout<<t->info<<std::endl;
        stampa(t->next);
    }
}
```
```c++
void stampa_rev(Tlista& t){
    if(t!= nullptr){
        stampa_rev(t->next);
        std::cout<<t->info<<std::endl;
    }
}
```
```c++
/*  data una lista, cancellare tutti gli elementi 
    dall'ultima occorrenza dello ero alla fine
    ES: 2->5->7->0->9->7->0->4->3 elimino 0->4->3
*/
bool elimina(Tlista& t){
    if(t==nullptr)
        return false;
        
    bool resp = elimina(t->next);
    if(resp==false)
        resp = (t->info==0);
    
    delete t;
    t = nullptr;
    return resp;
}
```
```c++
void ribalta(Tlista& t){
    if(t== nullptr){
        return;
    }
    if(t->next==nullptr){
        return;
    }
    
    Tlist support = l->next;
    ribalta(support);
    t->next->next = t;
    t->next = nullptr;
    t = support;
}
```
```c++
/*  Data una lista di int, dire se é possibile fare una certa
    somma s con i numeri pescati
*/

bool esiste_somma(Tlista& t,int somma){
    if(t== nullptr)
        return (somma ==0);
    
    return esiste_somma(Tlista& t,somma) || esiste_somma(l->next,somma-t->inf);
}

```

### Dividere la firma dalla funzione nelle classi

```c++

class ListInt{
    public:
        ListInt(); //default constructor
        ~ListInt(); //destructor
        ListInt(const ListInt& s); //copy constructor
        void prepend();
        void const itPrint();
        void const print();
        int& at(unsigned int pos);
        const int& at(unsigned int pos) const;
        void merge(const ListInt& l1,const ListInt& l2);

    private:
        struct Cell {
            int info;
            Cell* next;
        };

        Cell* head;

        void destroy(Cell *pc);
        void const recPrint(Cell *pc);
        Cell* copy(Cell* source);
        Cell* const merge_rec(Cell& h1, Cell& h2);
};

ListInt::ListInt(){
    head = nullptr;
}

List::~ListInt(){
    destroy(head);
}

void ListInt::destroy(Cell* pc){
    if(pc){
        destroy(pc->next);
        delete(pc);
    }
}

void ListInt::prepend(int el){
    Cell* newone = new Cell;
    newone->info = el;
    newone->next = head;
    head = newone;
}

void ListInt::itPrint(){
    Cell pc = head;
    while(pc){
        std::cout<<pc->info<<std::endl;
        pc = pc->next;
    }
}

void ListInt::print(){
    recPrint();
}

void recPrint(Cell *pc){
    if(pc){
        std::cout<<pc->info<<std::endl;
        recPrint(pc->next);
    }
}

void MyPrint(const ListInt& l){
    std::cout<<"Here's my list: "<<std::endl;
    l.print();
}

void ListInt::ListInt(const ListInt& s);
    head = nullptr;
    copy(head, s.head);

ListInt::Cell* ListInt::copy(Cell* source){
    if(source == nullptr)
        return nullptr;
    
    Cell* dest = new Cell;
    dest->info = source->info;
    dest->next = copy(source->next);
    return dest;
}

int& ListInt::at(unsigned int pos){
    Cell* pc = head;
    while(pc!= nullptr && pos>0){
        pc = pc->next;
    }
    assert(pc!=nullptr);
    if(pc)
        return pc->info;
    return dummy;
}

const int& ListInt::at(unsigned int pos){
    Cell* pc = head;
    while(pc!= nullptr && pos>0){
        pc = pc->next;
    }
    assert(pc!=nullptr);
    if(pc)
        return pc->info;
    return dummy;
}

void ListInt::merge(const listInt& l1,const listInt& l2){
    destroy(head);
    head = merge_rec(l1,l2);

}

ListInt::Pcell ListInt::merge_rec(Pcell& h1,Pcell& h2){
    if(h1==nullptr && h2 == nullptr){
        retunr nullptr;
    }
    else{
        Pcell newone == new Cell;
        if(h1 == nullptr){
            newone->info = h2->info;
            newone->next = merge_rec(h1,h2->next);
        }
        else if(h2 == nullptr){
            newone->info = h1->info;
            newone->next = merge_rec(h1->next,h2);
        }
        else{
            if(h1->info < h2->info){
                newone->info = h1->info;
                newone->next = merge_rec(h1->next, h2);
            }
            else{
                newone->info = h2->info;
                newone->next = merge_rec(h1, h2->next);
            }
        }
        return newone;
    }
}

void ListInt::merge_and_destroy(ListInt& l1, ListInt& l2){
    //this é un puntatore all'oggetto corrente
    if(this != &l1 && this != &l2){
        destroy(head);
        head = merge_dest_resc(l1.head, l2.head);

        l1.head = nullptr;
        l2.head = nullptr;
    }
    else if(this == &l1 && this != &l2){
        ListInt supp(l1);
        merge_and_destroy(supp,l2);
    }
    else if(this != &l1 && this == &l2){
        ListInt supp(l2);
        merge_and_destroy(l1,supp);
    }
    else{
        ListInt supp1(l1);
        ListInt supp2(l2);
        merge_and_destroy(supp1,supp2);
    }
}
```

### Override operatori 

Ri-definire un operatore in c++ é possibile.
Prendiamo in esempio due liste ```l1``` e ```l2```, se di default facessimo ```l1 = l2``` ci troveremmo con due liste confluenti e non con la deep copy che ci aspettiamo.

#### Assegnamento

```c++
ListInt& operator=(const ListInt& x){
    if(this == x){
        return *head;
        /* Se faccio a=a non devo fare nulla actually*/
    }
    destroy(head);
    head = copy(x.head);
    return *this;
}
```
(Ovviamente va fatto nella classe)

#### Somma

```c++
ListInt& operator+(const ListInt& x){
    ListInt supp(*this);
    Pcell pc = supp.head;
    if(pc==nullptr){
        head = copy(x.head);
    }
    else{
        while(pc->next!=nullptr){
            pc = pc->next;
            pc->next = copy(x.head);
        }
    }

    return supp;
}
```


**Esempio classe:**
```c++
#include <iostream>

class Complex{
    public:
        Complex();
        Complex(double real);
        Complex(double real, double imag);

        ~Complex();

        bool operator==(const Complex& c) const;
        Complex operator+(const Complex& c) const;
        Complex operator*(const Complex& c) const;
        Complex operator=(const Complex& c);
        Complex operator==(const Complex& c) const;
    private:
        double x; //real part
        double y; //imaginary part
};

Complex::Complex(){
    x = 0.0;
    y = 0.0;
}

Complex::Complex(double real){
    x = real;
    y = 0.0;
}

Complex::Complex(double real, double imag){
    x = real;
    y = imag;
}

Complex::~Complex(){
}

Complex Complex::operator==(const Complex& c) const{
    return x == c.x && y == c.y;
}

Complex Complex::operator+(const Complex& c) const{
    Complex res;
    res.x = x+c.x;
    res.y = y+c.y;
    return res;
}

Complex Complex::operator*(const Complex& c) const{
    Complex res;
    res.x = this->x*c.x - this->y*c.y;
    res.y = this->x*c.y + this->y*c.x;
    return res;
}

Complex Complex::operator=(const Complex& c){
    this->x = c.x;
    this->y = c.y;
    return *this
}
```

### Metodi Friend

Dichiari un operatore amico della classe e puoi fare operazioni che accedono alla roba privata di una classe

## Header

Nome: \<nomefile>.hpp

comp.hpp
```c++

#ifndef COMPLEX
#define COMPLEX
class Complex{
    public:
        Complex();
        Complex(double real);
        Complex(double real, double imag);

        ~Complex();

        bool operator==(const Complex& c) const;
        Complex operator+(const Complex& c) const;
        Complex operator*(const Complex& c) const;
        Complex operator=(const Complex& c);
        Complex operator==(const Complex& c) const;
    private:
        double x; //real part
        double y; //imaginary part
};

#endif
```

comp.cpp
```c++
#include "comp.hpp"

Complex::Complex(){
    x = 0.0;
    y = 0.0;
}

Complex::Complex(double real){
    x = real;
    y = 0.0;
}

Complex::Complex(double real, double imag){
    x = real;
    y = imag;
}

Complex::~Complex(){
}

Complex Complex::operator==(const Complex& c) const{
    return x == c.x && y == c.y;
}

Complex Complex::operator+(const Complex& c) const{
    Complex res;
    res.x = x+c.x;
    res.y = y+c.y;
    return res;
}

Complex Complex::operator*(const Complex& c) const{
    Complex res;
    res.x = this->x*c.x - this->y*c.y;
    res.y = this->x*c.y + this->y*c.x;
    return res;
}

Complex Complex::operator=(const Complex& c){
    this->x = c.x;
    this->y = c.y;
    return *this
}
```

main.cpp
```c++
#include <iostream>
#include "comp.hpp"

int main(){
    Complex x;
    Complex y(34);
    Complex z(0,1);
}
```

Makefile
```yaml
comp: comp.o main.o
    g++ std=c++14 comp.o main.o -ocomp

comp.o: comp.cpp comp.hpp
    g++ std=c++14 -c comp.cpp -ocomp.o

main.o:
    g++ std=c++14 -c main.cpp -omain.o

clean:
    rm comp comp.o main.o
```

### PIMPL - Pointer to impementation

Fino ad ora abbiamo usato il metodo classico, ma per usare pimpl facciamo:

```c++
private:
    Impl* pimpl;

```

#### Esempio classe PIMPL

comp.cpp
```c++
#include "comp.hpp"

class Impl{
    public:
        double x;
        double y;
};

Complex::Complex(){
    pimpl = new Impl;
    pimpl->x = 0.0;
    pimpl->y = 0.0;
}

Complex::Complex(const Complex& c){
    pimpl = new Impl;
    pimpl->x = c.pimpl->x;
    pimpl->y = c.pimpl->y;
}

Complex::Complex(double real){
    pimpl = new Impl;
    pimpl->x = real;
    pimpl->y = 0.0;
}

Complex::Complex(double real, double imag){
    pimpl = new Impl;
    pimpl->x = real;
    pimpl->y = imag;
}

Complex::~Complex(){
    delete pimpl;
}

Complex Complex::operator==(const Complex& c) const{
    return pimpl->x == c.pimpl->x && pimpl->y == c.pimpl->y;
}

Complex Complex::operator+(const Complex& c) const{
    Complex res;
    res.pimpl->x = pimpl->x+c.pimpl->x;
    res.pimpl->y = pimpl->y+c.pimpl->y;
    return res;
}

Complex Complex::operator*(const Complex& c) const{
    Complex res;
    res.pimpl->x = pimpl->x*c.pimpl->x - pimpl->y*c.pimpl->y;
    res.pimpl->y = pimpl->x*c.pimpl->y + pimpl->y*c.pimpl->x;
    return res;
}

Complex Complex::operator=(const Complex& c){
    pimpl->x = c.pimpl->x;
    pimpl->y = c.pimpl->y;
    return *this;
}

void Complex::print() const{
    std::cout<<" " << pimpl->x << " + i" << pimpl->y << " ";
}
```

comp.hpp
```c++

#ifndef COMPLEX
#define COMPLEX

class Impl;

class Complex{
    public:
        Complex();
        Complex(double real);
        Complex(double real, double imag);

        ~Complex();

        bool operator==(const Complex& c) const;
        Complex operator+(const Complex& c) const;
        Complex operator*(const Complex& c) const;
        Complex operator=(const Complex& c);
        Complex operator==(const Complex& c) const;
    private:
        Impl* pimpl;
};

#endif
```

comp_float.cpp
```c++
#include "comp.hpp"

class Impl{
    public:
        float x;
        float y;
};

Complex::Complex(){
    pimpl = new Impl;
    pimpl->x = 0.0;
    pimpl->y = 0.0;
}

Complex::Complex(const Complex& c){
    pimpl = new Impl;
    pimpl->x = c.pimpl->x;
    pimpl->y = c.pimpl->y;
}

Complex::Complex(double real){
    pimpl = new Impl;
    pimpl->x = real;
    pimpl->y = 0.0;
}

Complex::Complex(double real, double imag){
    pimpl = new Impl;
    pimpl->x = real;
    pimpl->y = imag;
}

Complex::~Complex(){
    delete pimpl;
}

Complex Complex::operator==(const Complex& c) const{
    return pimpl->x == c.pimpl->x && pimpl->y == c.pimpl->y;
}

Complex Complex::operator+(const Complex& c) const{
    Complex res;
    res.pimpl->x = pimpl->x+c.pimpl->x;
    res.pimpl->y = pimpl->y+c.pimpl->y;
    return res;
}

Complex Complex::operator*(const Complex& c) const{
    Complex res;
    res.pimpl->x = pimpl->x*c.pimpl->x - pimpl->y*c.pimpl->y;
    res.pimpl->y = pimpl->x*c.pimpl->y + pimpl->y*c.pimpl->x;
    return res;
}

Complex Complex::operator=(const Complex& c){
    pimpl->x = c.pimpl->x;
    pimpl->y = c.pimpl->y;
    return *this;
}

void Complex::print() const{
    std::cout<<" " << pimpl->x << " + i" << pimpl->y << " ";
}
```

## Double Linked List

```c++
struct CellaDL{
    int info;
    CellaDL* prev;
    CellaDL* next;
};
```

la lista DL vuote sono codificate da due puntatori nullptr

inserire in testa
```c++
typedef CellaDL* ListaDL; //puntatore a cella

void prepend(ListaDL& head, ListaDL& tail,int  el){
    ListaDL pCell = new CellaDL;
    pCell->prev = nullptr;
    pCell->info = el;
    pCell->next = head;
    if(head != nullprt){
        head->prev = pCell;
    }
    else{
        tail = pCell;
    }
    head = pCell;
}
```

inserire in coda
```c++
void append(ListaDL& head, ListaDL& tail,int  el){
    ListaDL pCell = new CellaDL;
    pCell->next = nullptr;
    pCell->info = el;
    pCell->prev = tail;
    if(tail != nullprt){
        tail->next = pCell;
    }
    else{
        head = pCell;
    }
    tail = pCell;
}
```

inserire in posizione
```c++
void insert(ListaDL& head, ListaDL& tail,int pos ,int  el){
    if(pos==0){
        ListaDL pCell = new CellaDL;
        pCell->info = el;
        if(head!=nullptr){
            pCell->prev = head->prev;
            head->prev = pCell;
        }
        else{
            pcell->prev = tail;
            tail = pCell;
        }
        pcell->next = head;
        head = pCell;
    }

    if(pos>0 && head!=nullptr){
        insert(head->next,tail,pos-1,el);
    }
}
```

rimuovere in posizione
```c++

void remove(ListaDL& head, ListaDL& tail,int pos){
    //modifica doppio puntatore
    //next va a next->next
    //prev va a prev->prev
}
```

destroy di una DLlist
```c++
void destroy(ListaDL& head, ListaDL& tail){
    while(head){
        TlistaDL pc=head;
        head=head->next;
        delete pc;
    }
    head = nullptr;
    tail = nullptr;
}
```
## Liste circolari

Nullptr se vuota

punto di accesso puntato

"ultima cella" punta alla prima

usate per OS

```c++
struct Cella{
    int info;
    Cella* next;
};

typedef Cella* ListC;
```

inserimento
```c++

void add(ListC& l, int info){
    ListC pnuova = new Cell;
    pnuova->info = info;
    if(l!= nullptr){
        pnuova->next = l->next;
        l->next = pnuova;
    }
    else{
        pnuova->next = pnuova;
        l = pnuova;
    }
}
```

stampa
```c++
void print(ListC l){
    if(l){
        ListC p = l;
        do{
            std::cout<<l->info;
            l=l->next;
        }while(p != l);
    }
}
```

eliminazione
```c++
bool delete_elem(ListaC &l, int elem){
    //paletto, cerco, sposto pointer, elimino
}
```

## Aggiunta pimpl

porto dentro sia struttura dati sia metodi che dipendono dalla struttura dati

```c++

struct Impl{
    int a;
    double b;
    doumble somma(int x, int y);
};

Class x{
    public:

    ...

    private:
        Impl* pimpl;
        ...
}

double Impl::somma(int x,int y){
    ...
}

//chiamo con
Impl->somma(...)
```

## Stack

In italiano pila

Abbiamo solo la push e la pop

Push -> metti sulla pila
Pop -> prendi dalla pila

Lifo - Last In First Out

stack.hpp
```c++
#ifndef mystack
#define mystack

class stack{
    public:
        Stack();
        Stack(const Stack& s);
        ~Stack();

        void push(int el);
        int pop();
        bool empty() const;
    private:
        struct Impl;
        Impl* pimpl;
}

#endif
```

stack_vett.cpp
```c++
#include <vector>
#include "stack.hpp"

struct Stack::Impl{
    std::vector<int> v;
};

Stack::Stack(){
    pimp = new Impl;
}

Stack::Stack(const Stack& s){
    pimpl = new Impl;
    pimpl->v = s.pimpl->v;
}

Stack::~Stack(){
    delete pimpl;
}

Stack::push(int el){
    pimpl->v.push_back(el);
}

int Stack::pop(){
    int res = pimpl->v.back();
    pimpl->v.pop_back();
    return res;
}

bool Stack::empty() const{
    return pimpl->v.size()==0;
}
```

main.cpp
```c++
#include "stack.hpp"

int main(){
    s.push(20);
    s.push(40);
    s.push(12);

    while(!s.empty()){
        std::cout <<s.pop() << std::endl;
    }

    return 0;
}
```

```Makefile
stack: main.o stack.o
    g++ --std=c++14 main.o stack_vect.o -ostack

main.o: main.cpp stack.hpp
    g++ --std=c++14 -c main.cpp -omain.o

stack_vect.o: stack_vect.cpp stack.hpp
    g++ --std=c++14 -c stack_vect.cpp -ostack_vect.o
```

## Code

FiFo - First In First Out

Enqueue -> metti nella coda
Dequeue -> prendi dalla coda -> estraggo l'elemento piú vecchio

```c++

class Queue{
    public:
        Queue();
        Queue(const Queue& s);
        ~Queue();
        bool isempty();
        void enqueue(int el);
        int dequeue();
    private:
        struct Impl;
        Impl* pimpl;
};
```
Implementazione con vettori

```c++
struct Queue::Impl{
    vector v;
    int first;
    int next;
    int elem;
}

Queue::Queue(){
    pimpl = new Impl;
    pimpl->v.resize(100,0);
    pimpl->first = 0;
    pimpl->next = 0;
    pimpl->elem = 0;
}

void Queue::enqueue(int el){
    if(pimpl->elem <100){
        v.at(pimpl->next) = elem;
        pimpl->next = (pimpl->next=1)%100;
        pimpl->elem++;
    }
    else{
        //shit, devo cambiare size
    }
}

int Queue::dequeue(){
    if(pimpl->elem>0){
        int res = pimpl->v.at(pimpl->first);
        pimpl->first = (pimpl->first+1)%100;
        pimpl->elem--;
        return res;
    }
}
```

Implementazione con liste

```c++
struct Cella{
    int info;
    Cella* next;
};

Struct Queue::Impl{
    Cella* head;
    Cella* tail;
}

Queue::Queue(){
    pimpl = new Impl;
    pimpl->head = nullptr;
    pimpl->tail = nullptr;
}

void Queue::enqueue(int elem){
    Cella* nuova = new Cella;
    nuova->next = nullptr;
    nuova->indo = elem;
    if(pimpl->head == nullptr){
        pimpl->head = nuova;
    }
    else{
        pimpl->tail->next = nuova;
    }
    pimpl->tail = nuova;
}

int Queue::dequeue(){
    if(pimpl->head!=nullptr){
        int res = pimpl->head->info;
        Cella *pc = pimpl->head;
        pimpl->head = pimpl->head->next;
        delete pc;
        if(pimpl->head == nullptr){
            pimpl->tail = nullptr;
        }
        return res;
    }else{
        //gestisci errore    
    }
}
```

## Liste doppiamente concatenate con i vector


listdlvec.cpp
```c++
#include "listdl.hpp"
#include <vector>

struct List_dl::Impl{
    vector<int> v;
    int first;
    int last;
    int free;
    int capacity;
    int dummy;
};

List_dl::List_dl(){
    pimpl = new Impl;
    pimpl->capacity = 10;
    pimpl->v.resize(3*pimpl->capacity); //al max 10 elem
    pimpl->first = -1;
    pimpl->last = -1;
    pimpl->free = 0;

    for(int i=0; i<pimpl->capacity-1; i++){
        pimpl->v.at(i*3+2) = 3*(i+1);
    }
    pimpl->v.at((pimpl->capacity-1)*3+2) = -1;
}

List_dl::~List_dl(){
    delete pimpl;
}

void List_dl::prepend(int elem){
    
    if(pimpl->free != -1){ //ho blocchi liberi
        int nb;
        nb = pimpl->free;
        pimpl->free = pimpl->v.at(pimpl->free+2);

        pimpl->v.at(nb+2) = first;
        pimpl->v.at(nb+1) = elem;
        
        if(first != -1){ //lista con almeno un elemento
            pimpl->v.at(pimpl->first) = nb
        }
        else{ //caso lista vuota
            pimpl->last = nb;
        }
        pimpl->first = nb;
    }
}

int& List_dl::at(int pos){
    int pc = pimpl->first;
    while(pos>0){
        if(pc!=-1){
            pc = pimpl->v.at(pc+2) //scorre la pos nel vettore
            pos--;
        }
    }

    if(pc!=-1){
        return pimpl->v.at(pc+1);
    }
    else{
        return pimpl->dummy; //posizione non esistente
    }
}

void List_dl::pop_back(){
    if(pimpl->first!=-1){ //lista non vuota
        int tr = pimpl->last;
        pimpl->last = pimpl->v.at(pimpl->last);
        if(pimpl->last != -1){ //ho ancora almeno un elemento
            pimpl->v.at(pimpl->last + 2) = -1;
        }
        else{
            pimpl->first = -1;
        }

        pimpl->v.at(tr+2) = pimpl->free;
        pimpl->free = tr;
    }
}

```

## Ruzzle

https://en.wikipedia.org/wiki/Ruzzle

Risolutore di ruzzle, riceviamo una stringa e una tabella e rispondiamo se é possibile fare la parola o no

``` c++
vector <vector<char>> t;
```

Usiamo i vector per salvare la matrice

IO: vabb partiamo dalla matrice cosi posso tagliare dal dizionario le parole che non iniziano con lettere non presenti nella matrice

MARIN: in una matrice 4x4 hai: 2*10^13 possibilitá (tipo 20.922.789.888.000)

SEMPRE IO: ```0-0``` ...

``` c++

using std::string;
using std::vector;

bool ruzzle(const vector<vector<char>>& t, string s){
    vector<vector<bool>> used(4,vector<bool>(4,false));
    bool trovato = false;
    int n=0;c=0;

    while(!trovato && r<4){
        c=0;
        while(!trovato && c<4){
            trovato = ruzzle_ric(t,s,r,c,used);
        }
    }
    return trovato;
}

bool ruzzle_ric(const vector<vector<char>>& t, string s, int r, int c, vector<vector<bool>>& used){
    if(r<0 || c<0 || r>=4 || c>=4 || used.at(r).at(c)){
        return false;
    }
    else{
        if(t.at(r).at(c)!=s.at(0)){
            return false
        }
        else{
            used.at(r).at(c) = true;
            if(s.length()==1){
                return true;
            }
            else{
                bool trovato = false;
                int sr=r-1,sc;
                while(!trovato && sr <= r+1){
                    sc = c-1;
                    while(!trovato && sc <= c+1){
                        trovato = ruzzle_ric(t,s.substr(1),sr,sc, used);
                        sc++;
                    }
                    sr++;
                }
                if(!trovato){
                    used.at(r).at(c) = false; //backtracking
                }
                return trovato;
            }
        }
    }
}

```

### Metodo statico

Non dipende dallo stato della classe

puó accedere solo alle variabili statiche che esistono anche se la classe viene solo importata senza stanziare un oggetto

### Notazione polacca postfissa

```5 + 7``` notazione infissa

```5 7 +``` notazione polacca postfissa

```5 7 + 8``` semplice calcolo

```5 7 + 8 *```

notaz. infissa  => notaz. polacca
```5 + (7*8)``` => ```5 7 8 * +```


```(12/(5+1))*20```
```12 5 1 + / 20 *```

```c++
struct token{
    double number;
    char op;
    bool is_number;
}

double valuta(vector<token> exp){
    std::stack<token> s;
    for(const auto& t:exp){
        if(t.is_number){
            s.push(t);
        }
        else{
            Token op2 = s.top(); s.pop();
            Token op1 = s.top(); s.pop();
            Tokend res;
            res.is_number = true;
            switch(t.op){
                case '+':
                    result.number = op1.number + op2.number;
                    break;
                case '-':
                    result.number = op1.number - op2.number;
                    break;
                case '*':
                    result.number = op1.number * op2.number;
                    break;
                case '/':
                    result.number = op1.number / op2.number;
                    break;
            }
            s.push(result);
        }
    }
    return s.pop.().number
}
```