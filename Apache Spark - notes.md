
# Apache Spark


**Distributed Computing**: Tramite la gestione di un 'master' i dati, al momento della memorizzazione sono distribuiti e replicati su diverse macchine(cluster).Il codice e quindi la computazione viene 'portata dove si trovano i dati' in modo da rendere il processing scalabile e resistente ai fault.

Spark Core cuore della piattaforma Spark:
- distributed data structure(RDD)
- metodi di transformazione estendibile con librerie

Spark SQL fornisce API per semplificare il lavoro su dati strutturati, consentendo l'uso di un linguaggio SQL su dataset Spark in memoria questo è fatto introducendo il Dataframe su un livello superiore al RDD:
- connettività JDBC
- compatibilità con Hive
- accesso uniforme ai dati

Spark's ML libraries fornisce una serie di utility per diversi sviluppi nell'ambito ML come Classificazione, Regressione, Clustering, Filtraggio, Dim. Reduction.

Scritto in linguaggio Scala, compilato ed eseguito su Java Virtual Machine. Spark applica diversi dei principi della programmazione funzionale OSSIA PROGRAMMARE USANDO ESCLUSIVAMENTE FUNZIONI SENZA AVERE EFFETTI COLLATERALI

```scala
	.map: funzione di ordine superiore che applica una data funzione ad ogni 
		elemento di un certo set di elementi
		
	.reduce: funzione di ordine superiore che analizza un struttura dati ricorsiva
		e tramite una operazione di combinazione, unisce i risultati dell'elaboraz.
		ricorsiva sui dati di cui parlavamo sopra e restituisce il valore costruito
		
		esempio:
		
		numList = (1, 2, 3, 4, 5)
		
		/*Itera su questa lista considerando due elementi(inizialmente i primi 
		della lista) uno dei due è il risultato parziale della combinazione
		all'iterazione precedente, l'altro è il primo elemento non considerato
		della lista*/
		
		result = numList.reduce(a,b => a+b)	//15
```

**MapReduce** su Hadoop è può essere un'operazione lenta perché ogni step (che sia il MAP, il REDUCE, lo SHUFFLE ogni macchina coinvolta è costretta ad un'operazione di lettura e scrittura su disco.

IN SPARK QUESTO è VELOCIZZATO PERCHé OGNI CLUSTER SI PORTA I DATI IN MEMORIA CENTRALE(RAM) DURANTE L'ELABORAZIONE

- Funzioni Anonime, ossia la definizione di funzioni locale e senza la necessità associargli un nome. Molto comodo dal momento che in programmazione funzionale le funzioni sono spesso passate proprio come argomenti a funzioni di ordine superiore
>
```scala

	//Esempio funzione NON anonima:
	
	def toUpper(s: String): String = {
		s.toUpperCase
	}
	stringList.map(toUpper)
	
	esempio funzione ANONIMA:
	
	stringList.map(x => x.toUpperCase)
```
>

- SparkContext è il punto di accesso di Spark in quanto abilita l'accesso al cluster al Spark driver Application. La configurazione del Context riguarda sia le proprietà del app del driver sia le risorse allocate al driver e all'esecutore JVM

- Ogni Spark driver application ha il proprio 'executor' sul cluster dover rimane in esecuzione fintanto che la Spark driver application ha uno SparkContext

- L'esecutore esegue il codice utente ed esegue dei calcoli e può mettere in cache dei dati per la sua applicazione. Lo SparkContext crea un JOB, che viene suddiviso in STAGES ogni stage viene diviso in TASK che vengono schedulati dallo SparkContext su di un esecutore
	
**RDD** (_Resilient Distributed Dataset_) se i dati nella memoria vengono persi, questi possono essere ricreati. I dati distribuiti in memoria possano venire da file o possono essere creati programmaticamente. RDD è L'UNITà FONDAMENTALE DI DATI USATA IN SPARK.
	
RDD creati da file, OGNI RIGA DEL FILE O DEI FILE UTILIZZATI SARà UN RECORD NEL RDD
```scala
	sc.textFile("myfile.txt")
	sc.textFile("mydata/*.log")
	sc.textFile("myfile1.txt, myfile2.txt")
```
RDD da una collezione
```scala
	sc.parallelize(collection)
```

Gli RDD non sono modificabili. Due diversi tipi di operazioni su RDD:
- AZIONI che restituiscono valori
- TRASFORMAZIONI definiscono un nuovo RDD basato su quello che gli passo come argomento
 
```scala
	count()				//restituisce il numero di elementi presenti nell'RDD
	take(n)				//restituisce un array con i primi n elementi nell'RDD
	collect()			//restituisce tutti gli elementi nell'RDD
	saveAsTextFile(fileName) 	//salva i dati in un file di testo
	map(function)			//crea un nuovo RDD applicando una funzione ad ogni elemento del RDD base
```
Crea un nuovo RDD che andrà a riempire con gli elementi che superano il controllo boolean. Saranno inseriti nel nuovo RDD solo i record per cui la funzione ha restituito un value TRUE
```scala
	filter(function)		
```
## Transformazioni:
RDD sono immutable, per cambiare i dati vengono usate delle TRANSFORMAZIONI.
Alcuni esempi di tranformazioni evidenti sono le funzioni:

- map "Crea un nuovo RDD eseguendo una funzione su tutti i record dell'RDD Base"

- filter "Crea un nuovo RDD includendo o escludendo dei record dall'RDD base in
	accordo con una funzione Booleana"

Le transformazioni possono essere concatenate insieme.
Quando invece eseguiamo delle AZIONI sugli RDD quello che otteniamo un VALUE.		
Spark memorizza la genealogia degli RDD e delle trasformazioni che accadono.

*Gran parte del codice gira sull'applicazione Driver(CLIENT). Le ACTION sui RDD girano sugli EXECUTOR(WORKER_NODE) e sul DRIVER, le TRANSFORMAZIONI solo sugli EXECUTOR*

Il codice può essere eseguito:
- LOCALMENTE, esclusivamente sul driver;
- DISTRIBUTO su diversi EXECUTORs; 
- entrambe.

 
## Pair RDD: 
sono forme speciali di RDD in cui ogni elemento deve essere una coppia key* value (una tupla di due elementi). Il perché sia stato introdotto questo concetto è presto detto: TORNA MOLTO UTILE NEGLI ALGORITMI DI MAP-REDUCE. 
```scala
	val users = sc.textFile(file).map(line => line.split('\t')).
	map(fields => (fields[0],fields[1]) 
```
Supponendo di avere un file con i due campi separati su due colonne diverse e una riga per ogni colonna. Questa riga di codice crea un pair RDD dal momento che genera un RDD in cui ogni record è una tupla con i due campi del record originale
```scala
	sc.textFile(logfile) \
	.keyBy(line => line.split(' ') [2])
```	
Questa sopra è un'altra possibile procedura per rendere un file di log un pair RDD a tutti gli effetti dal momento che stiamo scegliendo di scegliere come chiave il codice ID presente all'interno di ogni riga di log.
```scala
	sc.textFile(file) \
	.map(line => line.split("\t")) \
	.map(fields => (fields[0],(fields[1],fields[2]))
```

Come mappare singole righe su più coppie? Facciamo conto di partire da due colonne, ma la seconda colonna può contenere più voci(sku010,sku933,sku020). Io voglio che queste compaiano come coppie a se stanti con la stessa chiave
```scala
	sc.textFile(file) \
	.map(line => line.split('\t'))
	.map(fields => (fields(0), fields(1)))
	.flatMapValues(skus => (skus.split(':')))
```

Word Count tramite mapReduce e reduceByKey
```scala
	sc.textFile(file) \
		.flatMap(line => line.split()) 
		.map(word => (word,1)) 
		.reduceByKey((v1, v2) => v1+v2) 
```	
- flatMap è una map che agisce anche sugli ele.interni annidati ed elimina la struttura del dato precedentemente esistente
- riporta ogni elemento come una tupla con l'unità che rappresentano
- reduceByKey prendere l'iterazione (in questo caso 'ci sono altri elementi oltre questo che hanno la stessa key?') e riutilizza
ogni volta il risultato parziale trovato allo step precendente.	In generale le operazioni di analytics richiedono operazioni che
godono di proprietà associativa e commutativa
```scala
		countByKey	//return 1 mappa con occorrenze di ciascuna key
		groupByKey	//reggruppa tutti i valori per ogni key nell'RDD
		sortByKey	//sposta elementi in ordine discendente/ascendente

		join 		//return RDD delle coppie con medesima key nei due RDD
```

Tipico utilizzo di una Join nel workflow:
- mappare dataset separati in Pair RDD key-value;
- fare una join su key;
- mappare i dati risultati della join nel formato desiderato;
- salvare, visualizzare e continuare il processing

```scala
		rdd.keys //restituisce un RDD con solo le chiavi
		rdd.values //restituisce un RDD con solo i valori
		rdd.lookup(key) //restituisce i valori che sono associati con quella chiave

		/*join che però include nel risultato le chiavi definite nel RDD a sinistra o a destra*/
		leftOuterJoin | rightOuterJoin

		rdd.mapValues // esegue la funzione solo sui valori lasciando le chiavi identiche a prima
		rdd.flatMapValues
```
Spark può girare sia in locale che locale multi-thread che su di un cluster.

Per avviare Spark su di un cluster tramite la Spark Shell:
- Specificando l'URL del Cluster Manager
- Avvio in locale (local[*]) con tanti thread quanti sono i core della macchina
- Avvio in locale con n threat worker (local[n])
- Avvio locale senza avvalersi del processing distribuito
```bash
		spark-shell --master spark://masternode:7077
		spark-shell --master local[*] #esecuzione locale con tanti thread quanti sono i core
		spark-shell --master local[n] #esecuzione locale con n thread worker
		spark-shell --master local	#esecuzione locale senza calcolo distribuito
```

Quando Spark viene eseguito su cluster i dati vengono partizionati fra i vari WORKER_NODE. Questo partizionamento è fatto in automatico quando tramite le istruzioni che abbiamo visto in precedenza. Tuttavia questo processo può essere customizzato specificando come parametro il minimo numero di partizioni che da eseguire sul singolo file

```scala
	sc.textFile("myfile", 3)	//di default il valore di partizioni è fissato a 2
	sc.tetxtFile("mydir/*")		//ogni file della cartella diventa almeno una partizione
```
_SENSATO PER UN INSIEME DI TANTI PICCOLI FILE_:
Da tanti file di piccole dimensioni crea un Pair RDD in cui la KEY = NOME_FILE mentre il VALUE = CONTENUTO_FILE
```scala
	sc.wholeTextFiles("mydir")
```
LA MAGGIORANZA DELLE FUNZIONI AGISCE SUI SINGOLI ELEMENTI DI UN RDD. ALCUNE FUNZIONI INVECE AGISCONO DIRETTAMENTE SULLE SINGOLE PARTIZIONI DEI DATI.
```scala
	foreachPartition	//chiamata ad una funzione per ogni partizione
	mapPartitions		//crea un nuovo RDD eseguendo una funzione per ogni partizione del RDD
```
Più partizioni significa più task eseguibili in parallelo. Un cluster con un numero esiguo di partizioni non è utilizzato in modo efficiente. Tramite queste proprietà possiamo specificarlo esplicitamente quante partizioni utilizzare.
```scala
	spark.default.parallelism 10
	word.reduceByKey(lambda v1 , v2: v1 + v2, 15)
```
Le operazioni sugli RDD sono eseguite in parallelo su ogni partizione. Quando possibile i task sono eseguiti proprio sui nodi dove si trovano i dati in memoria. Inoltre alcune operazioni preservano il partizionamente, altre invece no
```scala
	//OPERAZIONI CHE PRESERVANO LA PARTIZIONE
	map
	flatMap
	filter

	//OPERAZIONI CHE EFFETTUANO UNA RIPARTIZIONE
	reduce
	sort
	group
```
Broadcast variables sono definite dal Driver e sono messe a disposizione dei worker i quali possono leggerne il valore una volta che è stato impostato e memorizzarlo nel worker node.

Al contrario un ACCUMULATORE è una variabile condivisa dai vari worker i quali possono aggiungere al suo valore mentre solo il driver può accedere al suo valore finale


### SPARK SQL:
- mette a disposizione dei DATAFRAME, ossia una collezione di dati distribuita ma con uno schema simile ad un db relazionale
- ad ogni modo SPARK mette a disposizione una serie di metodi per l'elaborazione di dati strutturati
```scala
		//SU DATI STRUTTURATI
		df.show()
		df.printSchema()
		df.select(df("name"), df("age") + 1)
		df.filter(df("age") > 21)
		df.groupBy("age").count()
```
**Funzione 'sql'**

Questa funzione su di un SQL Context permette all'applicazione di eseguire query SQL restituendo come risultato un DATAFRAME. Oltretutto questa funzione rende possibile effettuare delle query direttamente su files.
```scala
	df.registerTempTable("people")
	val youngPeople = sqlContext.sql("SELECT name, age FROM people WHERE age <= 39")
	val df = sqlContext.sql("SELECT * FROM parquet.'user.parquet'")
```