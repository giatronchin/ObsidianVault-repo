# Scala
#scala #bigdata
Scala è basato su Java, e proprio come Java è 'statically typed' ma i programmatori devono fornire le informazioni riguardanti il tipo solo in poche occasioni perché Scala può dedurre
successivamente le informazioni riguardanti il tipo

E' un linguaggio interpretato e compilato proprio come Java ma possiede si a caratteristiche dei linguaggi dinamici, sia dei linguaggi statici

Type Inference, deduzione del tipo
```scala
val sum = 1 + 2 + 3
val nums = List(1, 2, 3)   
```

Definizione esplicita del tipo
```scala
val sum: Int = 1 + 2 + 3
val nums: List[Int] = List(1, 2, 3)
//Map è una collezione iterabile che consiste in coppie di chiavi e valori
val map: Map[String, List[Int]] 
```

Definizione di un'applicazione in Scala
```scala
def main(args: Array[String])

//al contrario del vecchio classico 

public static void main(String[]args)
```

Eseguire uno script in Scala da linea di comando
```bash
scala nomeFile.scala
```

Eseguire un'applicazione da linea di comando
```bash
scalac nomeApplicazione.scala
```

4 tipi differenti di 'identificatori'

- alfanumerico (myVar, myMethod, myClass)
- operatori (+, =>, :::) se gli operatori finiscono con : sono associativi a destra
- identificatori_misti (unary_+, myVar_=)
- literali ('class')

var & val. VAL rappresenta una costante il cui valore  quindi non cambia. VAR invece identifica una variabile, il cui valore memorizzato può essere modificato.
Se la variabile viene inizializzata alla sua dichiarazione il tipo della variabile viene dedotto, mentre per dichiararlo esplicitamente si può usare questa espressione
```scala
var q : Boolean = true
```

La dichiarazione del tipo per una variabile è obbligatoria quando la variabile è un parametro formale di un METODO (all'interno di un metodo ogni variabile deve essere inizializzata)m.zaccaria@accenture.comm.zaccaria@accenture.com 
La dichiarazione è altresì obbligatoria quando è definita all'interno di una CLASSE. In quest'ultimo caso non è obbligatorio inizializzare la variabile

Quando il valore iniziale non viene dichiarato bisogna usare il costruttore 'new' nella dichiarazione della variabile. In questo modo i valori di deafult (0, false, null) vengono 
impiegati nella costruzione della variabile. Quando il valore iniziale della variabile è dichiarato non è permesso utilizzare la key-word 'new'

La dichiarazione dei tipi dei parametri viene chiusa tra parentesi quadre

```scala 
 val ary = new Array[Int](5); //Senza inizializzazione, l'array è riempito con zeri
 val ary2 = Array{3,1,4,1,6}; //Con inizializzazione
```

Tipo di ingresso e di uscita di una funzione è specificato come segue. Nel caso di funzione che prende un solo argomento le parantesi sono sempre necessarie:
```scala
 ...() => return_type; //nessun argomento d'ingresso
 ... arg_type => return_type; //singolo argomento d'ingresso
 ... (arg_type1, arg_type2, arg_type3,...) => return_type; //argomenti d'ingresso multipli
```

 SCALA NON HA PRIMITIVE, SOLO OGGETTI
 Any, AnyVal, Boolean, Char, Byte, Short, Int, Long, Float, Double, Unit (ha un solo valore possibile () che è ritornato dalle funzioni che non restituiscono niente),
 AnyRef (corrisponde a Object in Java), ScalaObject, Null, Nothing
 
Unit ha un solo valore possibile: () ed è ciò che viene restituito dalle funzioni che non ritornano niente. AnyRef corrisponde ad un oggetto Java

Tipi mutabili e non mutabili(comportamento di default). Le collezioni immutabili non posso essere modificate ma possono comunque essere simulate alcune operazioni su di esse 
ma il risultato ad ogni modo viene inserito all'interno di un nuovo oggetto lasciando quello precedente immutato
```scala
import scala.collection.mutable._; //per poter definire collezioni mutabili
```

Ogni VALORE in scala è considerato un oggetto, per cui è lecita la seguente dicitura con cui chiamiamo il metodo toString sul valore 1 (essendo questo un oggetto)

1.toString;

//Ogni OPERAZIONE è considerata invece la chiamata ad un metodo per cui è lecita anche questa dicitura per questa operazione

1 + 2 + 3;

(1).+ (2).+ (3); // ".+" è equivalente a scrivere "+" ma questo rende esplicito il fatto che Scala la interpreti come metodo


//Infix notation: comporta che la possibilità di omettere il punto "." e le parentesi tra cui dovrebbero essere racchiusi gli argomenti del metodo

"abc" charAt 1; //è equivalente a scrivere "abc".charAt(1);

//Tutti i valori numeri sono rappresentati internamente in formato binario ma possono essere scritti in diversi formati
0 - 9 //Decimal Integer
0 -7 //Rappresentazione ottale
//Esadecimale
//Decimale a doppia precisione
//Decisione a singola precisione

//Operatori oltre a quelli classici possiamo trovare
```scala
val num = 3;
num  << 1; //Shift SX di uno
num >> 3; //Shift DX di tre
num >>> 1; //Shit DX di tre con zero padding
true & false; //Bitwise AND
true | false; //Bitwise OR
true ^ false; //Bitwise Exclusive OR
```

### LISTE
Le liste sono IMMUTABILI e PERSISTENTI, cioè gli operatori che ritornano liste non modificano quella originale ma piuttosto ne condividono la struttura.
Le liste sono OMOGENEE, cioè tutti gli elementi che le compongono sono del medesimo tipo. Quando è necessario specificare il tipo dei valori si può usare la seguente scrittura.

```scala
List[Type] (value1, value2, value3);

/*Metodi sulle liste*/
list.head; //Primo elemento della lista
list.tail; //Il resto della lista meno l'head
list.last; //Ultimo elemento della lista
list.init //Restituisce una lista con l'ultimo elemento rimosso
isEpty // controlla che la lista sia vuota
lenght //restituisce il numero di elementi presenti nella lista
value :: list //aggiunge il valore nella nuova head della lista
list.take(n); //Restituisce i primi n elementi della lista
list.drop(n); //Restituisce una nuova lista meno i primi n elementi della lista originale
list1 :: list2; // Appende due liste
list.reverse; //Restituisce una lista con gli elementi posizionati in ordine opposto
list.splitAt(n); //restituisce due tuple (list.take(n), list.drop(n))
```

FUNZIONI DI ORDINE SUPERIORE
```
val plusOne = (x:Int) => x + 1;
val nums = List(1, 2, 3);

/*Map prende come argomenti una funzione: Int => T. Quindi "map" restituisce una lista in cui una funzione che prendere un solo argomento, viene applicata ad ogni elemento della lista*/
nums.map(plusOne);
nums.map(x => x + 1);
nums.map(_ + 1);
```

restituisce una lista in cui la funzione che prendere un solo argomento viene applicata ad ogni elemento di ogni sottolista
```scala
listOfLists.flatMap(function); 
```
/*Restituisce una lista con gli elementi della lista orginale per cui il predicato è true*/
```scala
list.filter(predicate);
```
/*Come prima ma quando il predicato è falso*/
```scala
list.filterNot(predicate);
```
/*Divide la lista in due tuple, quella con gli elementi per cui il predicato è vero e una con gli elementi per cui il predicato è falso*/
```scala
list.partition(predicate);
```
/*Applica la 'function' passata come argomento per ogni coppia di elementi presenti nella lista, utilizzando i risultati intermedi come nuovo argomento della funzione*/
```scala
list.foldLeft(value)(_function_);

numeri = List(4, 1, 2, 3);
numeri.foldLeft(0)((m,n) => m*2 + n) 
```
m ed n sono i primi due elementi della lista, calcola il doppio del primo e ne fa la somma. Dalla seconda iterazione, il primo elemento della coppia
è il risultato intermedio della funzione allo step precedente. Con foldRight la lista è parsata da destra verso sinistra.
```scala
list.find(predicate); //restituisce una lista con gli elementi di list per cui il predicato risulta vero

list.takeWhile(predicate); //scansiona la lista da sinistra verso destra e si ferma quando il predicato non è soddisfato da un elemento della lista. Restituisce la lista con gli elementi trovati
list.dropWhile(predicate); //simile a quello sopra gli elementi scansionati fino a quel momento sono esclusi dalla lista di ritorno 
```
### Tuple
può memorizzare da 2 fino a 22 valore separati da virgole; sono INMUTABILI. Per accedere all'ennesimo valore della tupla si può usare la seguente dicitura
t._n; //n è un letterale che rappresenta la posizione dell'elemento

Set sono immutable di default e per loro esiste una grande varietà di metodi built-in

Mappe per default sono inmutabili, ma possono essere anche definite mutabili tramite scala.collection.mutable. Sono rappresentate come lista di coppie (key, value)
```scala
Map((key1, value1), ..., (keyN, valueN));
Map(key1 -> value1,...,keyN -> valueN); //scrittura più veloce
map.get(key); //restituisce il valore associato con la key
map.getOrElse(key, deafult); //restituisce il valore associato con la chiave, se la chiave non esiste nella map il valore di default
map.put(key.value); //per le mappe mutabili, inserisce il valore specificato con la chiave specificata
```

Option sono usate quando un'operazione potrebbe riuscire a ritornare un valore o meno*. Sostanzialmente sono dei tipo
parametrizzati. I valori possibili sono Some(value), dove con value indichiamo il tipo corretto o None, nel caso nessun valore dovessere essere
trovato

Varie strutture di istruzioni condizionali

```scala
if (condition) expression
if (condition) expression else expression
if (condition) expression else if (condition) expression
if (condition) expression else if (condition) expression else expression
```
Se nessuna condizione è soddisfatta allora viene restituito il valore Unit

#### Cicli FOR
for(seq) yield expression

SEQ è un elenco, separato da ';' di GENERATORI, DEFINIZIONI  e FILTRI.
	- generatore ha la forma di sotto, dove list può essere anche una espressione che valutata restituisce una lista

		variabile <- list
		
		esempio: for(i <- 1 to 10) println(i);
		
	-definition ha la forma di sotto
	
		variable = expression

		esempio: for(i <- 1 to 10; x = 20) println(i + x);
		
	-filter ha la forma di una "if condition"
	
		if condition
		
		esempio: for(i <- 1 to 10; if i%2==0) println(i);
*/


for (v <- list) yield v //returns a copy of list.
for (v <- list) yield f(v) //is equivalent to list map f.
for (v <- list if p(v)) yield v //is equivalent to list filter p.
for (v <- list; w = 2 * v; x <- list; if x < w) yield (v, w, x)
	
/*Scala is Functional permette la definizione di funzioni anonime*/

(x:Int) => x + 1; //definizione della funzione anonima

val plusOne = (x:Int) => x + 1;
plusOne(5); //chiamata alla funzione, con risultato 6

//Funzioni come PARAMETRI
val plusOne = (x: Int) => x + 1
val perDue = (x:Int) => x*2

def chiamataFunzione(f: Int => Int) = f(2) //il parametro che do a questa funzione è la funzione da usare!!!

chiamataFunzione(plusOne) //3
chiamataFunzione(perDue) //4

/*Altro esempio figo di programmazione funzionale*/
def each(xs: List[Int], fun: Int => Unit) {
	if(!xs.isEmpty) {
	fun(xs.head)
	each(xs.tail, fun)
	}
}
/*Finche la lista xs non è vuota allora si esegue la funzione sulla testa della lista per poi richiamare la
stessa funzione sulla coda della lista, ossia tutti gli elementi che non sono ancora stati consumati*/
each(List(1,2,3), println)



/*
Classi: una classe descrive un oggetto proprio come in Java.
	
	-al contempo una classe può estenderne un'altra
	-ogni classe ha un costruttore primario dato dalla dichiarazione della stessa
	-parametri necessari sono inseriti dopo il nome della classe e il corpo del costrutto è il corpo della classe
	-tutti i parametri sono automaticamente dichiarati come 'PRIVATE FIELD'
	-by default una classe estende la classe AnyRef

*/

class Foo(name: String) { constructor_body }

class Foo(a: Int, var b: Int, val c:Int){...}

//Per a sono implementati i metodi "setter" e "getter" privati
//Per var b i metodi di "setter" e "getter" sono pubblici
//Per val c è definito solo un "getter" essendo una costante ed è un metodo pubblico
//Il metodo setter per un certo parametro v ha nome 'v_='

/*Definizione della classe persona in Scala*/
class Person(var name: String, private var _age: Int) {
	def age = _age
	def age_=(newAge:Int) {
		println("Changing age to: "+newAge)_age = newAge
	}	
}

/*Definizione di una equivalente classe in Java*/
public class Person {
	private String name;
	private int age;
	
	//Costruttore
	public Person(String name, Int age) { 
		this.name = name;
		this.age = age;
	}
	
	//Getter e Setter del Name
	public String getName() { 
		return name;
	}
	
	public int getAge() { 
		return age;}
	
	public void setName(String name) { 
		this.name = name;
	}
	
	//Setter pubblico per l'Age
	public void setAge(int age) { 
		this.age = age;
	}
}

/*
Method:

	-Se stiamo sovrascrivendo un metodo già esistente è necessaria la parola chiave 'override'
	-Al posto di Access possiamo usare uno dei seguenti modificatori di accesso: private, protected, or public by default
	-Proprio come in Java un metodo può essere sovraccaricato 'overloaded' significando che a seconda dei parametri con 
	 cui il metodo viene chiamato questo può fare cose diverse
	-Per ogni parametro che il metodo prende in input deve essere definito un tipo esplicito
	-I parametri di un metodo sono intepretati come se fosse stati dichiarati come val e quindi il loro valore non è alterbile
	-Il tipo di ritorno può essere solitamente omesso tranne quando è usata esplicitamente la key-word 'return', il metodo è
	 ricorsivo, il metodo richiama altri metodi, il tipo dedotto da Scala non è sufficientemente specifico
*/

//Due possibili modi per dichiarare un metodo in Scala. Nel primo caso è un metodo che non restituisce nulla
[override] [access] def methodName(arg: type, ..., arg: type) { body } // returns Unit
[override] [access] def methodName(arg: type, ..., arg: type): returnType = { body }


/*
Object:
	
	-Un oggetto può essere creato da una classe
	-Un oggetto può anche essere dichiarato allo stesso modo di un classe ma usato la parola chiave 'object' invece che 'class'
	-Un oggetto non può prendere parametri
	-La dichiarazione di un oggetto a differenza di una classe rappresenta la dichiarazione di UN SINGOLO OGGETTO
	-La dichiarazione di un oggetto è invece uno schema di progetto a partire da cui è possibile instaziare infiniti oggetti
	-Creare un oggetto da una dichiarazione di oggetto non richiede il 'new' come il crearlo dalla definizione di una classe

*/


