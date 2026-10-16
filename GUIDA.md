# UnrealSense — guida rapida

UnrealSense aggiunge a Visual Studio (2022 e 2026) un aiuto pensato per i progetti Unreal: ti dice **dove le tue
classi C++ sono usate nei Blueprint**, ti suggerisce gli specifier di `UPROPERTY`/`UFUNCTION` e ti avvisa degli errori
tipici di Unreal mentre scrivi. Funziona anche con l'editor di Unreal chiuso.

## Installare e aggiornare

1. Chiudi Visual Studio.
2. Scarica `UnrealSense-<versione>.vsix` dalle [release](https://github.com/AssoAndrea/Unreal-Sense/releases) e fai
   doppio clic sul file.
3. Nella finestra che si apre spunta le versioni di Visual Studio in cui la vuoi (2022, 2026) e premi **Install**.

Per aggiornare si fa la stessa cosa con il file nuovo: non serve disinstallare.

**Aggiornamenti automatici**: in Visual Studio apri Tools › Options › Environment › Extensions, in *Additional Extension
Galleries* premi **Add**, scrivi un nome (UnrealSense) e come URL `https://assoandrea.github.io/Unreal-Sense/gallery/atom.xml`,
poi **Apply**. Da quel momento Visual Studio trova da solo le versioni nuove e le installa quando lo chiudi.

Se nell'elenco non compare la tua versione di Visual Studio, metti `Install-UnrealSense.ps1` nella stessa cartella del
`.vsix` e fai clic destro sullo script › **Esegui con PowerShell**: usa l'installer della versione più recente.

## Primo avvio

Apri la soluzione del tuo progetto come fai di solito. UnrealSense riconosce il progetto da solo e in pochi secondi
legge le classi C++ e i Blueprint (la prima volta può volerci un po' di più). Da lì in poi tutto funziona in automatico.

## Cosa trovi nel codice

### Quanti Blueprint usano una classe

Accanto a classi, funzioni e proprietà esposte ai Blueprint compare una scritta grigia, per esempio
`12 derived Blueprints` o `3 Blueprint usages`. **Cliccala** per vedere l'elenco dei Blueprint.

Il conteggio include anche i Blueprint che derivano da altri Blueprint o da classi C++ figlie (indicati con "via …").

### Cercare gli usi nei Blueprint

Metti il cursore sul nome di una classe, funzione o proprietà e premi **Alt+Shift+B** (oppure clic destro ›
**Find Blueprint Usages**). Si apre la finestra **Blueprint Usages** con l'elenco: doppio clic su una riga per
mostrare il file dell'asset in Esplora file.

### Cercare un testo dentro i Blueprint

**Extensions › UnrealSense › Search Text in Blueprints...** (o dal Quick Menu, Alt+Shift+Q) apre la finestra
**Search in Blueprints**: scrivi un testo qualsiasi (almeno 2 caratteri) e vengono elencati tutti i Blueprint e gli asset di
dati che lo contengono, non solo i riferimenti a classi o variabili. Se nel codice hai del testo selezionato, la ricerca parte
con quello. Per ogni risultato vedi **cosa** è il testo e **dove** si trova:

- commento di un riquadro (comment box) o di un singolo nodo, con il grafo (`EventGraph › comment box`);
- valore scritto in un pin di un nodo (`EventGraph › Call PrintString › pin In String`);
- valore di default di una variabile, o un suo tooltip/categoria/nome visualizzato (`Variable Health › tooltip`);
- testo di un widget (`Widget QuitButton › ButtonText`), riga di una DataTable (`Row X › DisplayName`), voce di una
  String Table, proprietà di un Data Asset o di un attore in un livello;
- con l'opzione *Names*, anche nomi di variabili, funzioni, pin e gameplay tag.

Per i testi localizzabili (FText) il tooltip della riga mostra namespace e chiave. Doppio clic apre l'asset nell'Unreal
Editor; ci sono anche *Show in Explorer*, *Copy reference* e *Copy text*. Opzioni: *Match case*, *Whole word*,
*All assets* (cerca anche in texture, mesh, suoni e animazioni, esclusi di default) ed *Editor caches* (testi che l'editor
rigenera da solo, come i tooltip dei pin del motore o i messaggi del compilatore, nascosti di default). La ricerca legge gli
asset direttamente dal disco (anche con l'editor chiuso) e su un progetto come Lyra impiega meno di un secondo.

### Informazioni al passaggio del mouse

Passa il mouse su uno specifier (es. `BlueprintReadWrite`) per leggerne la spiegazione, o sul nome di una classe/
funzione/proprietà per vedere dove è usata nei Blueprint.

### Suggerimenti mentre scrivi

Dentro `UPROPERTY(...)`, `UFUNCTION(...)`, `UCLASS(...)` ecc. Visual Studio ti propone gli specifier validi per la tua
versione di Unreal, con la spiegazione. Funziona anche per i valori, per esempio le `Category` già usate nel progetto
o la funzione da usare in `ReplicatedUsing`.

### Completamento dei membri (`.` e `->`)

Scrivi il nome di una variabile e un punto: Visual Studio elenca le funzioni e le variabili della sua classe (comprese
quelle delle classi base dell'engine), e la lista si restringe mentre scrivi. Funziona su variabili locali, parametri,
`this`, risultati di chiamate (`GetOwner()->`) e membri; i membri privati e protetti compaiono solo dove sono accessibili.

- Se la variabile è un **puntatore**, il punto diventa da solo `->`.
- Con i puntatori di Unreal (`TObjectPtr`, `TWeakObjectPtr`, `TSharedPtr`, `TUniquePtr`...) il punto resta: la lista
  mostra sia le funzioni del puntatore (`Get`, `IsValid`, `Reset`) sia quelle dell'oggetto puntato (segnate con `->`);
  se scegli una di queste ultime il punto diventa `->`.

Funziona anche nei file dell'engine e dei plugin: quando ne apri uno UnrealSense lo aggiunge all'indice con i suoi
header (pochi secondi). Un membro appena aggiunto in un `.h` compare nel `.cpp` subito, anche prima di salvare l'header. Usa l'indice C++ di UnrealSense (vedi le impostazioni): se lo spegni, il completamento non c'è.

### Completamento dei nomi (locali, membri, tipi, macro)

Ovunque inizi a scrivere un nome, nei `.cpp` e nei `.h` (corpo di funzione, corpo di classe, livello di file o di
namespace, parametri, argomenti template come `TArray<TS`, basi `: public AAc`, initializer del costruttore, valori di
default dei campi, argomenti delle macro `DECLARE_*` e di `UE_LOG`), Visual Studio elenca in quest'ordine:

- i **parametri** e le **variabili locali** dichiarate fin lì (anche dentro le lambda), con il loro tipo;
- le **funzioni, variabili e tipi annidati della classe** e di tutte le classi da cui eredita, con le stesse regole di
  accesso; in una funzione `static` solo i membri statici;
- i **valori dell'enum atteso** dopo `case `, `Stato = `, `Stato == ` e negli argomenti di una funzione che vuole un enum
  (`Nome.Find(TEXT("x"), ` propone `ESearchCase::IgnoreCase`), scritti `EMioStato::Valore`;
- **tipi, funzioni, variabili e namespace** (`TSoftObjectPtr`, `AActor`, `LogLyra`, `NAME_None`, gli enum dell'engine come
  `ESearchCase`...), anche quelli dichiarati nel file e non ancora salvati (per esempio un delegate appena dichiarato sopra) e
  quelli creati dalle macro (`GetHealth`/`SetHealth` di `ATTRIBUTE_ACCESSORS`);
- le **macro** con i loro parametri (`UE_LOG`, `DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam`, `GENERATED_BODY`...);
- le **parole chiave** del C++.

Dopo `::` elenca i nomi dentro quello scope: `UE::Math::`, `EMioEnum::` (i valori), `Super::` e `AMiaBase::` (membri e
tipi annidati); in un `.cpp` fuori dalle funzioni `void AMioActor::` elenca tutte le funzioni della classe, per scriverne
la definizione. Dopo `class ` o `struct ` (forward declaration) propone i nomi di classe senza selezionarne nessuno, così
Invio non sostituisce il nome di una classe nuova. Dentro `#include "` propone cartelle e header. Su un delegate dinamico
il punto elenca anche `AddDynamic`, `AddUniqueDynamic`, `RemoveDynamic`, `IsAlreadyBound` (o `BindDynamic`).

**Include automatici**: se scegli un tipo, una funzione o una variabile il cui header il file non vede (né direttamente
né tramite altri include), UnrealSense aggiunge l'`#include` dopo l'ultimo (prima del `.generated.h`), scritto come nel
codice Unreal: relativo alla cartella `Public`/`Classes`/`Private` del modulo (`"GameFramework/Actor.h"`) o alla cartella
del file; un percorso assoluto solo se nessuna cartella di include contiene l'header (il suggerimento lo dice). Un solo
Ctrl+Z toglie nome e include. Con una forward declaration `class X;` nel file l'include di `X` non si aggiunge.

La lista non si apre quando stai dichiarando qualcosa (`FVector Posizione`), nei commenti e nelle stringhe. Anche
**Ctrl+Spazio** la apre.

### Avvisi e correzioni rapide

Gli errori tipici di Unreal vengono sottolineati mentre scrivi, prima di compilare: include `.generated.h` mancante o
sbagliato, `GENERATED_BODY()` mancante, prefissi A/U/F/E/I sbagliati, `_Implementation` mancante, puntatori a UObject
senza `UPROPERTY` né smart pointer (il garbage collector non li vede), e altri. Anche errori C++ generici, solo dove i tipi sono
certi: scrittura su una variabile, un membro o un oggetto `const` (anche dentro un metodo `const`), chiamata di un metodo non
`const` su un oggetto `const`, membro che non esiste, numero di argomenti sbagliato, membro `private`/`protected` usato dove
non è accessibile (friend, classi derivate e annidate rispettate); e avvisi per `if (A = B)`, `;` subito
dopo `if`/`for`/`while`, `X = X;` e `UE_LOG`/`FString::Printf` con un numero di `%` diverso dagli argomenti. Quando compare la **lampadina** puoi
applicare la correzione con un clic (per esempio crea il corpo di `_Implementation` nel `.cpp`, o aggiunge `UPROPERTY()`
con `TObjectPtr`). Gli errori sono sottolineati in rosso, gli avvisi in giallo e i suggerimenti in blu (colori in
*Strumenti › Opzioni › Tipi di carattere e colori*, voci "UnrealSense - ..."). Errori e avvisi sono anche nell'Elenco
errori.

IntelliSense di Visual Studio, invece, segnala in Unreal molti errori che non esistono. UnrealSense li nasconde (sottolineature
ed Elenco errori) quando li riconosce come falsi:

- **IntelliSense non è riuscito a configurare il file**: non apre un `#include` di un header che esiste (per esempio
  `E1696` su `"CoreMinimal.h"`) e poi sottolinea tutto il file (`UCLASS`, `UPROPERTY`, `DECLARE_...`). Restano solo gli
  include che mancano davvero.
- **Il `.generated.h` manca o è vecchio** (UHT lo rifà alla prossima build): errori sulle macro di riflessione, su `Super`,
  `ThisClass`, `StaticClass`, e su `Nome_Implementation`, `Nome_Validate`, `Execute_Nome` quando l'header dichiara l'UFUNCTION
  giusto.

Gli errori veri restano. L'opzione **IntelliSense errors in Unreal files** sceglie: `HideFalse` (default), `HideAll` (nessun
errore di IntelliSense: restano gli avvisi di UnrealSense e gli errori di compilazione) o `ShowAll`.

### Andare alla definizione (Ctrl+clic, F12)

**Ctrl+clic** su un nome (oppure **F12**, o **Alt+G**) nei file C++ del progetto. Se in Tools › Options › Text Editor ›
General hai scelto un altro tasto per "Enable mouse click to perform Go to Definition" (Alt o Ctrl+Alt), vale quello:

- sulla **chiamata** di una funzione porta alla sua implementazione nel `.cpp`;
- sull'**implementazione** porta alla dichiarazione nell'header, e dalla dichiarazione all'implementazione;
- su una **variabile** (membro, globale, locale, parametro) porta alla sua dichiarazione;
- su un **tipo** porta alla definizione della classe (non alle dichiarazioni `class AFoo;`).

Funziona anche sul codice appena scritto e non ancora salvato. Per le `UFUNCTION` BlueprintNativeEvent e RPC porta a
`_Implementation` (o `_Validate`) e ritorno, anche dalla chiamata. Sui nomi creati da una macro (`NAME_None`, `GetHealth`
di `ATTRIBUTE_ACCESSORS`) porta alla riga della macro. Usa l'indice C++ di UnrealSense: se lì non sa niente, parte il Go To
Definition normale di Visual Studio (o di Visual Assist). Con Alt+G, se il cursore non è su un nome noto, passa
al file corrispondente (come Alt+O). Se ci sono più destinazioni possibili apre la prima e lo dice la barra di stato.

### Colori del codice

UnrealSense colora da sé i file C++ del progetto, con colori suoi. Non dipende da IntelliSense né da Visual Assist.

**Mentre scrivi:**
- keyword (anche `int32`, `uint8`…), commenti e commenti di documentazione (`///`, `/** */`);
- stringhe (anche il percorso di `#include <…>`), numeri, direttive `#`;
- **macro di reflection** (`UCLASS`, `UPROPERTY`, `GENERATED_BODY`…) in grassetto, con i loro specifier
  (`BlueprintCallable`), le chiavi di `meta=(…)` in corsivo e i valori.

**Dall'indice C++ di UnrealSense**, che legge anche il codice non salvato:
- i **tipi UObject** (`A…`/`U…`, che derivano da `UObject`) hanno un colore diverso dai tipi valore (`FVector`, `TArray`);
- interfacce, enum e loro valori (anche gli enum Unreal "vecchio stile" come `ESearchCase` e la verbosità di `UE_LOG`),
  typedef, namespace;
- funzioni, campi, variabili globali, **locali** e **parametri**, ognuno con il suo colore (quelli statici in corsivo);
- le macro.

Finché l'indice non ha risposto, i nomi vengono colorati in base al prefisso Unreal.

I membri esposti ai Blueprint (BlueprintCallable, BlueprintReadWrite…) sono in **grassetto**.

**Cambiare i colori:** Tools › Options › Environment › Fonts and Colors, voci `UnrealSense - …`. I colori predefiniti
seguono il tema (scuro o chiaro) attivo all'avvio di Visual Studio.

### Cercare un simbolo o un file (Alt+Shift+T, Ctrl+Shift+T)

**Alt+Shift+T** (Extensions › UnrealSense › **Go to Symbol...**) apre una finestra di ricerca su classi, funzioni,
variabili, valori di enum, macro e delegate del progetto, dei suoi plugin e degli header dell'engine. Basta scrivere
le iniziali (`GAL` trova `GetActorLocation`) o `AFoo::Bar` per cercare dentro una classe. **Ctrl+Shift+T** (**Go to
File...**) fa lo stesso con i file; `File.cpp:120` apre alla riga 120. Nella finestra **Ctrl+1/2/3/4** (o Tab) passano
fra Symbols, Types, Files e In File (i simboli del file aperto), **Invio** apre, **Esc** chiude. Alla prima apertura del progetto l'indice si costruisce in
qualche secondo, poi resta su disco. L'opzione *Include engine symbols and files* toglie l'engine dai risultati.

**Alt+M** o **Alt+Shift+M** (**Go to Symbol in File...**) apre la stessa finestra sulla tab In File e cerca solo nel file aperto: classi, funzioni, campi, macro e anche le
variabili locali e i parametri dentro le funzioni (con il loro tipo e la funzione a cui appartengono). Senza testo
mostra tutto il file in ordine, partendo dal simbolo dove si trova il cursore; `Tick::` mostra solo i locali di `Tick`.
I locali arrivano dall'indice C++ di UnrealSense: se è spento o ancora in costruzione, la barra in basso lo dice e la
lista mostra solo le dichiarazioni.

### Tutti i comandi in un menu (Alt+Shift+Q)

Premi **Alt+Shift+Q** (o Extensions › UnrealSense › **Quick Menu**): dove si trova il mouse si apre un menu con tutti i
comandi di UnrealSense, divisi in ricerca, codice, progetto e strumenti, ognuno con la sua scorciatoia. I comandi agiscono
comunque sul cursore di testo: in cima, in piccolo, il menu scrive il nome su cui si trova (*At caret: ...*). Puoi scrivere per filtrare i comandi, scegliere con le frecce e premere **Invio**; **Esc**
chiude. Il menu mostra solo i comandi che hanno senso dove si trova il cursore: per esempio **Declare Delegate...** solo in un
header, fuori dai corpi delle funzioni, dai commenti e dalle stringhe; **Create Implementation** solo su una dichiarazione
che non ha ancora il corpo; **Add Forward Declaration** solo su un tipo in un header; **GoTo Implementation/Declaration**
solo sul nome di una funzione. Le scorciatoie e il menu Extensions › UnrealSense funzionano ovunque e, nel posto
sbagliato, la barra di stato spiega perché.

Le finestre di UnrealSense (Unreal Explorer, Find Usages, Blueprint Usages, Go to Symbol/File, Quick Menu e i dialoghi
New Unreal Class, Override e Declare Delegate) hanno due temi, da **Tools › Options › UnrealSense › General › UI theme**:
**Unreal** (di default, lo stile dell'editor di Unreal Engine 5, uguale con ogni tema di Visual Studio) o **Classic** (i
colori, il font e le barre del titolo di Visual Studio, che seguono subito il tema scuro, chiaro, blu o personalizzato). Le
finestre aperte cambiano tema quando salvi le opzioni.

### Passare dal .h al .cpp e viceversa

Premi **Alt+O** (o clic destro › **Switch Header/Source**) per aprire il `.cpp` dell'header che stai guardando,
o l'header del `.cpp`. Segue la struttura dei moduli Unreal (`Public\Ui\Menu.h` ⇄ `Private\Ui\Menu.cpp`, anche
`Classes`) e funziona anche nei file dell'engine e dei plugin. Se il file corrispondente non esiste lo dice la barra di
stato in basso.

### Override delle funzioni virtuali

Con il cursore dentro una classe (nel `.h`, o in una sua funzione nel `.cpp`) premi **Alt+Shift+V** (o clic destro ›
**Override Virtual Functions...**): si apre l'elenco delle funzioni virtuali che la classe eredita (dall'engine, dai
plugin, dal progetto e dalle interfacce). Spunta quelle che vuoi: UnrealSense aggiunge le dichiarazioni nel `.h` e, se
lo lasci spuntato, le definizioni che chiamano `Super::` nel `.cpp`. Le classi dell'engine si caricano in background
quando apri il progetto; se premi Alt+Shift+V subito dopo, la barra di stato dice che le sta caricando.

### Creare l'implementazione di una funzione (Create Implementation)

Scrivi la dichiarazione nel `.h`, lascia il cursore sulla riga (anche sulla riga dell'`UFUNCTION(...)`) e premi
**Alt+Shift+D**, oppure `Ctrl+.` › **Create implementation of ...** (la lampadina compare solo se la definizione non
esiste ancora). UnrealSense scrive il corpo nel `.cpp` vicino alle definizioni delle funzioni dichiarate prima o dopo,
dentro lo stesso `#if`, e ci porta il cursore. Selezionando più dichiarazioni le crea tutte. Segue le regole di Unreal:

- `BlueprintNativeEvent` e le RPC `Server`/`Client`/`NetMulticast` diventano `Nome_Implementation`; con
  `WithValidation` aggiunge anche `bool Nome_Validate(...)` che restituisce `true`;
- un override chiama la versione della classe padre (`Super::Nome(...)`, con `return` se restituisce un valore) solo se
  un antenato la implementa davvero (non per le interfacce o le funzioni pure);
- un costruttore con `const FObjectInitializer&` riceve `: Super(ObjectInitializer)`;
- `BlueprintImplementableEvent`, funzioni pure, inline e gli eventi delle interfacce non hanno definizione: la barra di
  stato spiega perché. Se la definizione c'è già, il cursore va lì.

### Dichiarare un delegato

Metti il cursore nell'header, dove vuoi il membro (di solito una riga vuota nella sezione `public:` della classe), e
apri **Alt+Shift+Q › Declare Delegate...** (o Extensions › UnrealSense › **Declare Delegate...**). Nel pannello scegli:

- il **tipo**: Single, Multicast, Dynamic, Dynamic Multicast, Sparse o Event (Sparse ed Event solo con il cursore dentro
  la classe che li possiede);
- il **nome** (`OnScoreChanged`) e il **tipo** del delegato, che segue il nome (`FOnScoreChangedSignature`) finché non
  lo cambi;
- il **tipo di ritorno**, solo per Single e Dynamic (gli altri non possono restituire valori);
- fino a nove **parametri** (tipo e nome; per i delegati dinamici il nome è obbligatorio);
- se dichiarare anche il **membro** e la **firma dell'handler** da collegare.

L'anteprima mostra il codice mentre scrivi e avvisa degli errori che darebbe Unreal (per esempio una virgola in un tipo
come `TMap<int32, FString>`, che spezza la macro). **Create** scrive la macro `DECLARE_..._DELEGATE...` giusta dopo gli
`#include`, sopra la classe, e il membro al cursore (`UPROPERTY(BlueprintAssignable)` per i dynamic multicast e gli
sparse; per un Event la macro, un accessor pubblico e il campo privato dentro la classe); un solo Ctrl+Z annulla tutto.
**Copy** copia lo stesso codice negli appunti senza toccare il file. Sotto l'anteprima una nota spiega a cosa serve il tipo
scelto, e **Usage example** si espande con un esempio di come collegarlo, chiamarlo e scollegarlo, scritto con i nomi del
tuo delegato.

### Gerarchia di una classe

Con il cursore sul nome di una classe o di una struct apri **Alt+Shift+Q › Class Hierarchy...** (o Extensions › UnrealSense ›
**Class Hierarchy...**; la voce compare solo sul nome di una classe). Si apre un menu a tendina, dove si trova il mouse, con la classe, le sue
**classi base** dalla più vicina (per esempio `ACharacter`, `APawn`, `AActor`, `UObject`, interfacce comprese) e le
**classi derivate** ad albero, con le linee dei livelli e accanto a ogni classe quante sottoclassi ha. Le classi con
sottoclassi si chiudono e si aprono con un clic sulla freccia o con **←/→** (**Ctrl+←/→** tutte); con più di 40 derivate
all'apertura è aperto solo il primo livello. Clic su una (o frecce su/giù e **Invio**) apre il suo file sulla classe;
scrivendo filtri l'elenco (le classi trovate restano sotto le loro classi padre).

Le classi dell'engine vengono dall'indice C++ di UnrealSense, che conosce gli header dell'engine inclusi dal progetto: le
basi sono sempre complete, le derivate dell'engine sono quelle di quegli header (tutte con *Index the whole engine*). Le
derivate di classi molto comuni come `UObject` si fermano a 2.000. Finché l'indice si costruisce compaiono solo le classi
UCLASS/USTRUCT del progetto.

### Funzione base e override

Con il cursore su una funzione `virtual`, su un `override` o su un evento `UFUNCTION`, al posto di *Class Hierarchy...*
il Quick Menu mostra **Base and Overrides of X...**. Finché il menu a tendina è aperto, il nome a cui si riferisce
(classe o funzione) è evidenziato in arancione nell'editor. Il menu a tendina elenca la funzione, le **funzioni base** che
sovrascrive fino a quella che la introduce (segnata *origin*, per esempio `AActor::BeginPlay`) e gli **override** nelle
sottoclassi, ad albero. Una `BlueprintNativeEvent` viene seguita attraverso i suoi `_Implementation`: dal cursore su
`ChoosePlayerStart` si vedono la sua implementazione e gli `ChoosePlayerStart_Implementation` delle sottoclassi, e da uno di
questi si risale alla `UFUNCTION`. Per gli eventi compaiono anche le **Blueprint che li implementano**; su una
`BlueprintImplementableEvent` la voce si chiama **Blueprint Implementations of X...**. Clic su una funzione apre il suo
file, clic su una Blueprint la apre nell'Unreal Editor. Serve l'indice C++ di UnrealSense.

### Aggiungere un #include o una forward declaration

Con il cursore su un nome apri **Alt+Shift+Q › Add #include**: UnrealSense aggiunge l'`#include` del suo header scritto come nel
codice Unreal (`"GameFramework/Actor.h"`), dopo gli altri include e prima del `.generated.h`. Per una classe, struct o enum
è il suo header; per una variabile o una funzione l'header che la dichiara se il file non lo vede ancora, altrimenti quello
del suo tipo (`TObjectPtr<UMyComponent> Comp` → l'header di `UMyComponent`).

In un header compare anche **Add Forward Declaration**: scrive `class UMyComponent;` (o `struct`, `enum class EMode : uint8;`,
dentro il suo namespace) dopo gli include o vicino alle altre forward declaration. Template, classi annidate ed enum senza
tipo sottostante non si possono dichiarare così: lo dice la barra di stato. Serve l'indice C++ di UnrealSense.

### Unreal Explorer

Menu **Extensions › UnrealSense › Unreal Explorer** (o **View › Other Windows**). Mostra i moduli del progetto, i
plugin, i Blueprint raggruppati per classe C++ di origine e i file di configurazione, con una casella di ricerca.

### Creare una nuova classe Unreal

In Esplora soluzioni fai clic destro su un progetto o una cartella › **Aggiungi › Unreal Class...** (oppure
**Extensions › UnrealSense › Unreal Class...**, o il pulsante **New class** dell'Unreal Explorer). Scegli:

- il **nome** della classe (il prefisso `A`, `U`, `F`, `E` lo aggiunge UnrealSense);
- la **classe padre**: in **Common** le più usate (Actor, Character, Actor Component, Interface, Struct, Enum, Empty,
  subsystem, User Widget...), in **All Classes** tutte quelle del progetto, dei plugin e dell'engine (la prima volta
  la lista si riempie dopo qualche secondo);
- la **cartella**: Public mette il `.h` in `Public` e il `.cpp` in `Private`.

Il percorso può essere in qualsiasi modulo (una cartella con un `.Build.cs`, o sotto): progetto, suoi plugin, engine o
plugin dell'engine. Parte dalla cartella del file aperto nell'editor (o da quella selezionata in Esplora soluzioni); il
pulsante **...** apre un selettore con le cartelle dei sorgenti di progetto, plugin ed engine ad albero, con moduli e plugin
segnati, e una ricerca per nome di modulo.

UnrealSense crea i file come l'Unreal Editor (con `<MODULO>_API`, gli include giusti e le funzioni tipiche del padre,
per esempio `BeginPlay` e `Tick` per un Actor), li aggiunge al progetto e li apre. Struct ed enum hanno solo il `.h`.
Se il modulo non dipende dal modulo della classe padre (per esempio `UMG` per un User Widget), una casella propone di
aggiungerlo al `.Build.cs`.

Gli include dei file nuovi restano sottolineati in rosso finché non esegui **Generate Project Files**: è un limite dei
progetti generati da Unreal, la compilazione funziona comunque. Il file `.generated.h` esiste solo dopo la prima
compilazione.

## Impostazioni

**Tools › Options › UnrealSense**. In Visual Studio 2026 è una pagina della nuova finestra delle impostazioni
(*Tutte le impostazioni › UnrealSense*, divisa per sezioni, con i valori che avevi già); in Visual Studio 2022 resta la
pagina classica. Le più utili:

- **Show Blueprint usage hints**: accende/spegne le scritte grigie nel codice.
- **Show hints for unused Blueprint API**: mostra anche "no Blueprint usages" sulle funzioni e proprietà esposte ai
  Blueprint ma mai usate (utile per fare pulizia).
- **Unreal project**: da impostare solo se UnrealSense apre il progetto sbagliato (capita con soluzioni che ne
  contengono più di uno).
- **IntelliSense errors in Unreal files** (sezione *Inspections*): `HideFalse` di default nasconde gli errori falsi di
  IntelliSense; `HideAll` li nasconde tutti; `ShowAll` lascia quelli di Visual Studio.
- **Member completion after . and ->** (sezione *Code completion*): accesa di default; spegne solo il completamento dei
  membri.
- **Name completion (locals, members, types, macros)** (sezione *Code completion*): accesa di default; spegne il
  completamento dei nomi mentre li scrivi, dopo `::` e dentro `#include`.
- **Add missing #include** (sezione *Code completion*): accesa di default; spenta, scegliere un nome non aggiunge
  l'include che manca.
- **UI theme**: `Unreal` di default (stile dell'editor di Unreal); `Classic` usa i colori di Visual Studio e ne segue il tema.
- **UnrealSense colorization** e **Semantic colours from the own index** (sezione *Colorization*): accese di default.
  - La prima spegne tutti i colori di UnrealSense (tornano quelli di Visual Studio): vale subito per i file aperti, appena
    premi OK.
  - La seconda lascia solo i colori che non vengono dall'indice: token e prefissi Unreal.
- **Go to definition (Ctrl+click, F12)** (sezione *Find Usages (own index)*): accesa di default; spenta,
  Ctrl+clic e F12 tornano a Visual Studio.
- **Use the own C++ index** (sezione *Find Usages (own index)*): accesa di default. Serve al
  completamento dei membri e delle variabili locali (spenta, il completamento non c'è) e accende **Find Usages** (menu contestuale dell'editor e Extensions › UnrealSense): trova dove un simbolo C++
  è usato con un indice scritto da noi, senza clangd, anche dentro le macro (argomenti di `UE_LOG`/`check`/`AddDynamic`, corpo
  di un `#define` del progetto, `UPROPERTY(ReplicatedUsing = OnRep_X)`). La prima volta servono pochi secondi; poi l'indice resta su disco
  e dopo ogni salvataggio di un file C++ rilegge solo i file cambiati (se non hai aggiunto o tolto dichiarazioni riusa i simboli già noti: con Lyra e tutto l'engine circa 1 secondo, circa 4 se una dichiarazione cambia). *Index the whole engine* (attiva di default) aggiunge tutti i
  sorgenti dell'engine (circa 100.000 file); se la spegni indicizza solo gli header dell'engine inclusi dal progetto. In Task Manager compare come `usindex`: durante una build usa più memoria,
  poi la restituisce (per un progetto come Lyra circa 0,3 GB; con tutto l'engine circa 3,5 GB in Task Manager, di più con un engine più grande). Nel log le righe iniziano con
  `OwnIndex:`.

## Se qualcosa non va

- **Non vedo i contatori**: aspetta qualche secondo dopo l'apertura della soluzione. Se hai appena salvato dei
  Blueprint in Unreal, usa **Extensions › UnrealSense › Rebuild UnrealSense Index**.
- **Un contatore sembra sbagliato**: annota la classe e un Blueprint che la usa, e manda il file di log (sotto).
- **"Trova tutti i riferimenti" di Visual Studio non funziona più**: se nel menu Extensions › UnrealSense compare
  **Restore Visual Studio Indexing**, eseguilo e riavvia Visual Studio.
- **Una scorciatoia (Alt+Shift+B, I, V, K, M) non fa niente o fa altro**: un'altra estensione usa lo stesso tasto. Nel log
  la riga `KeyBindingCheck:` dice quale comando lo usa; puoi cambiare tasto in **Tools › Options › Environment ›
  Keyboard** cercando `UnrealSense.`.
- **Log da allegare a una segnalazione**: la cartella `%LOCALAPPDATA%\UnrealSense\Logs` (incolla il percorso nella
  barra di Esplora file) contiene un file per ogni sessione di Visual Studio, `UnrealSense_<data>_<ora>_pid<id>.log`:
  allega quello della sessione con il problema (di solito il più recente). Vengono tenute le ultime cinque sessioni.
  Il log della sessione corrente è visibile anche in Visual Studio in **Output › UnrealSense**.
- **Visual Studio si blocca o si chiude da solo**: allega anche `Hangs_<data>_<ora>_pid<id>.log` della stessa sessione: per i blocchi di 5 secondi o più contiene anche lo stack del thread UI, che mostra dove è fermo Visual Studio anche quando la colpa non è di UnrealSense.
  Dice se il blocco era in codice UnrealSense e, con la riga `WHOLE PROCESS STALLED`, se si è fermato l'intero processo
  (di solito memoria: ogni riga riporta la memoria di Visual Studio e quella libera del PC).
