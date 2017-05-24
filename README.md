# Xamarin-style-guide
Guida di riferimento per i progetti Xamarin Forms di Tiknil.<br>
Ogni consiglio o critica è ben accetta se contribuisce a migliorare la qualità del codice 👍

## SOMMARIO
1. [Pattern di sviluppo](#pattern-di-sviluppo)
	* [Inversion of Control e Dependency Injection](#inversion-of-control-e-dependency-injection)
	* [MVVM](#mvvm-model---view---viewmodel)
2. [XAML o C#?](#xaml-o-c)
3. [Naming conventions](#naming-conventions)
4. [Struttura e organizzazione della soluzione](#struttura-e-organizzazione-della-soluzione)
	* [Cartelle e contenuti](#cartelle-e-contenuti)
	* [Classi utili secondo Tiknil](#classi-utili-secondo-tiknil)
	* [Gestione assets](#gestione-assets)
5. [Tool e pacchetti NuGet usati e testati](#tool-e-pacchetti-nuget-usati-e-testati)

### Pattern di sviluppo
In questa sezione analizziamo un paio di pattern che abbiamo deciso di utilizzare nelle nostre applicazioni Xamarin Forms con l'obiettivo di migliorare **riusabilità** e **manutenibilità** del codice con conseguente agevolazione al lavoro in team.

#### Inversion of Control e Dependency Injection
L'*Inversion of Control* (**IoC**) è un *software design pattern* secondo il quale ogni componente dell'applicazione deve ricevere il **controllo** da un componente appartenente ad una **libreria riusabile**.<br>
L'obiettivo è quello di rendere ogni componente il più indipendente possibile dagli altri in modo che ognuno sia modificabile singolarmente con conseguente maggior riusabilità e manutenibilità.

La *Dependency Injection* (**DI**) è una forma di *IoC* dove l'implementazione del pattern avviene stabilendo le dipendenze tra un componente e l'altro tramite delle *interfacce* (chiamate **interface contracts**).<br>
A tali interfacce viene associata un'implementazione in fase di istanziazione del componente (nel *costruttore*) oppure in un secondo momento tramite *setter*.<br>
In ogni caso è generalmente presente un oggetto **container** che si occupa di creare le istanze di ogni *interfaccia*; la configurazione di tale *container* può così influenzare le dipendenze tra i vari componenti.<br>
L'utilizzo della *DI* è molto utile per la realizzazione di test automatici, infatti modificando il *container* è possibile *mockare* le dipendenze che non si desidera testare.

References:

* [Semplice video che chiarisce il concetto di DI](https://www.youtube.com/watch?v=IKD2-MAkXyQ)
* [Esempio di applicazione della DI in Xamarin](https://xamarinhelp.com/xamarin-forms-dependency-injection/)
* [Unity: tool per DI by Microsoft in Xamarin](https://www.youtube.com/watch?v=B-L6OE3AKXQ)

#### MVVM (Model - View - ViewModel)
L'*MVVM* è un *pattern architetturale* che facilita la separazione tra interfaccia grafica (**GUI**) e la **business logic** tramite l'utilizzo di **bindings** tra *view* e *viewmodel*.<br>

* **Model:** implementazione del *domain model* che include modelli di dati, logiche di business e validazione.
* **View:** struttura, layout e aspetto di ciò che l'utente vede su schermo.
* **ViewModel:** il *viewmodel* è un'astrazione di ciò che visualizza la *view* relativa ed espone *properties* e *commands* che vengono collegati alla *view* tramite **binding**.
![MVVM Pattern](https://upload.wikimedia.org/wikipedia/commons/8/87/MVVMPattern.png)

[Qui](https://www.youtube.com/watch?v=sbZRtrKj3Ds) c'è un esempio di applicazione del pattern *MVVM* utilizzando solo l'sdk di Xamarin.Forms. Il ragazzo non è bravissimo a presentare, ma l'esempio è utile per capire il concetto.

#### Considerazioni su frameworks MVVM
In seguito ad un'analisi dei vari framework MVVM disponibili per Xamarin abbiamo individuato **FreshMVVM** come il più adatto al nostro tipo di utilizzo.<br>
Di seguito riportiamo una lista di pro e contro per ogni framework aggiornata ad Aprile 2017:

---
#### [FreshMVVM](https://github.com/rid00z/FreshMvvm)
**PRO**

* Lightweight: leggero e poco invasivo. Si è più liberi di applicarlo o meno dove si desidera
* Navigazione VM-VM buona
* Specifico per Xamarin.Forms
* [Video esplicativi esaurienti](https://github.com/rid00z/FreshMvvm#related-videosquick-start-guides)
* Pattern testabile con UnitTest

**CONTRO**

* Per ora è necessario adattarsi alle sue naming conventions
	* *View => Page*
	* *ViewModel => PageModel*

---
#### [Prism](https://github.com/PrismLibrary/Prism)
**PRO**

* Interessante il sistema di navigazione VM-VM tramite url/path
* Possibilità di scegliere il proprio framework di Dependency Injection

**CONTRO**

* Codice un po’ troppo “invadente”; è necessario che molti elementi siano sottoclassi di quelle fornite dal framework
* I template forniti sulla creazione dei file si basano tutti su XAML
* Documentazione non troppo esauriente

**Materiale**<br>
[Video](https://www.youtube.com/watch?v=DYRLcqG2BAY) di presentazione del framework tenuto dall’ideatore. Ne dimostra le potenzialità.

---
#### [MVVMCross](https://www.mvvmcross.com/) (non testato)
**Pro**

* Sembrerebbe ben documentato
* Offre molti plugin con funzionalità varie accessorie

**Contro**

* Xamarin.Forms non sembra molto supportato

---
#### [MVVMLight](http://www.mvvmlight.net/) (non testato)
**Pro**

* Sembrerebbe parecchio utilizzato per sviluppo winzozz

**Contro**

* Documentazione assente

---

### XAML o C#?
Le *views* in Xamarin.Forms si chiamano `Page` e rappresentano una singola schermata visualizzata dall'utente; si possono realizzare in due modi:

1. C#: classe erede di `ContentPage`.
2. XAML: file XML di concetto molto simile ai `layout` Android che viene trasformato in una classe C# (chiamata **code behind**) build time.

Idealmente sarebbe meglio utilizzare XAML perché aiuta a mantenere un maggior decoupling tra *view* e *viewmodel* e perché l'IDE può fornire un'anteprima della visualizzazione, ma per ora preferiamo utilizzare solo C# per i seguenti motivi:

1. Attualmente *Xamarin Studio* e *Visual Studio* su Mac hanno un sistema di completamento automatico (**IntelliSense**) carente soprattuto nei *binding*.
2. XAML non è un linguaggio di programmazione, quindi impedisce di far ereditare una *page* da un'altra complicando il molto frequente utilizzo di una `BasePage` per l'implementazione di un'iterfaccia comune a tutta l'applicazione. Es: navigation bar customizzata.

### Naming conventions
* I nomi delle classi e dei campi `public` vanno espressi in [UpperCamelCase](https://en.wikipedia.org/wiki/Camel_case). Es: `AwesomeClass`, `AwesomePublicProperty`.
* I campi `private` o `protected` iniziano con l'underscore `_` e vanno espressi in [lowerCamelCase](https://en.wikipedia.org/wiki/Camel_case). Es: `_awesomePrivateProperty`.
* Le interfacce iniziano con la `I` maiuscola. Es: `IAwesomeInterface`.
* Naming conventions ereditate da **FreshMVVM**:
	* Le *views* del pattern *MVVM* hanno suffisso `Page`. Es: `AwesomePage`.
	* I *viewmodels* del pattern *MVVM* hanno suffisso `PageModels`. Es: `AwesomePageModel`.

### Struttura e organizzazione della soluzione

#### Cartelle e contenuti
* *Helpers*: qualsiasi classe o interfaccia che sia generalmente utile in ogni punto del progetto senza essere strettamente legata a nulla. Namespace: `NomeProgetto.Helpers`. Es: `IHudProvider`.
* *Libraries*: librerie varie sviluppate ad-hoc e non integrate tramite NuGet. Namespace customizzati per liberia. Es: `Verscker`.
* *Models*: *modelli* e business logic del pattern MVVM. Namespace: `NomeProgetto.Models`.
* *PageModels*: *viewmodels* del pattern MVVM. Namespace: `NomeProgetto.PageModels`.
* *Pages*: *views* del pattern MVVM basate sulle `Page` di Xamarin.Forms. Namespace: `NomeProgetto.Pages`.<br>Questa cartella può contenere le seguenti sottocartelle che mantengono lo stesso namespace:
	* *Cells*: classi eredi di `ViewCell` di Xamarin.Forms.
* *Properties*: file di info degli *assembly* generato automaticamente da Xamarin.
* *Renderers*: classi con customizzazione grafica per piattaforma tramite i *renderers* di Xamarin.Forms.
* *Resources*: nella PCL eventuali assets di tipo *Embedded*, nelle piattaforme gli assets di tipo *Local*.
* *Services*: interfacce e implementazioni dei servizi generalmente passati ai *viewmodels* nel costruttore tramite *DI*.<br>Sono generalmente sempre presenti le seguenti interfacce:
	* *IDataServices*: definisce i servizi di recupero dati dai modelli. Sarà compito dell'implementazione definire da dove recuperare i dati; ad esempio da *memoria* (singleton), *database* o altro.
	* *IRestServices*: definisce i servizi di recupero dati da webservices.
	* *ICacheServices*: definisce i servizi di storicizzazione di dati in cache. L'implementazione classica è il salvataggio di informazioni nella cache d'installazione dell'applicazione in una classe `Settings`.

#### Classi utili secondo Tiknil
* *UIConstants*: nella cartella *Pages* creiamo questa classe che conterrà proprietà statiche rappresentanti:
	* Colori
	* Misure (padding, ecc)
	* Stili (`Style` di Xamarin.Forms)
* *Converters*: nella cartella *Pages* creiamo questo file che conterrà le varie classi che implementano `IValueConverter` di Xamarin.Forms utilizzate nelle varie *page* del progetto.
* *BaseModel (opzionale)*: classe di base dei *models* con implementazioni comuni a tutti i *models*.
* *BasePageModel*: classe di base dei *pagemodels* erede di `FreshBasePageModel` con implementazioni comuni a tutti i *pagemodels*. Es: costruttore con `IDataServices` per il recupero dei dati dai model.
* *BasePage*: classe di base delle *pages* erede di `FreshBaseContentPage` con implementazioni comuni a tutte le *pages*. Es: navigation bar customizzata presente in ogni schermata con relativi metodi di controllo.

#### Gestione assets
Le risorse, in Xamarin.Form, si possono gestire in maniera [Local](https://developer.xamarin.com/guides/xamarin-forms/user-interface/images/#Local_Images) o [Embedded](https://developer.xamarin.com/guides/xamarin-forms/user-interface/images/#Embedded_Images).<br>
Gli assets *Local* li posizioniamo nelle cartelle *Resources* dei progetti specifici di piattaforma, mentre le gli assets *Embedded* li posizioniamo nella cartella *Resources* della PCL.

La quasi totalità degli asset saranno ti tipo *Local* per riuscire a sfruttare i sistemi proprietari delle piattaforme di gestione delle risoluzioni.

In **iOS** inseriamo direttamente nella cartella *Resources* tutti i png con suffissi delle risoluzioni (`@2x`, `@3x`) in modo che da PCL possiamo scrivere semplicemente il nome del file per referenziare la correta risorsa. Es: mettiamo in *Resources* `awesome_image.png`, `awesome_image@2x.png`, `awesome_image@3x.png` e nella PCL possiamo usare semplicemente `awesome_image.png`; si occupa iOS di selezionare la corretta risoluzione per il device.<br>
Nella cartella *Resources* ci sono anche:

* `Assets.xcassets`: in cui gestiamo l'icona dell'app
* `LaunchScreen.storyboard`: in cui possiamo customizzare la launch screen

In **Android** tutti gli assets li inseriamno nelle sottocartelle `drawable` della cartella *Resources*.

### Tool e pacchetti NuGet usati e testati
[Qui](https://github.com/MarcBruins/awesome-xamarin) è disponibile una lista generale di pacchetti utili.

#### Pacchetti NuGet testati e utilizzati da Tiknil

* Newtonsoft.Json => parsing json-oggetti
* Microsoft.Net.Http => client per request HTTP
* Plugin.Permissions => gestione delle richieste dei permessi
	* [https://blog.xamarin.com/new-ios-10-privacy-permission-settings/](https://blog.xamarin.com/new-ios-10-privacy-permission-settings/)
* Xam.Plugins.Settings => caching di informazioni in stile StandardUserDefaults perché quello integrato di Xamarin non funziona
* Xam.Plugin.Media => caricamento immagini da rullino o scatto da fotocamera
* Xamarin.FFImageLoading => caricamento delle immagini dal web
* Xam.Plugin.Geolocator => geolocalizzazione
* Xamarin.Forms.Maps => ufficiale xamarin per la visualizzazione di mappe
* Xam.Plugin.PushNotification => per la gestione delle notifiche push
* Xam.Plugin.DeviceInfo => info sul dispositivo
* PropertyChanged.Fody => implementazione automatica dell’INotifyPropertyChanged<br>**NB:** l'ultima versione testata (*1.53.0*) da un errore in compilazione quindi per ora questo pacchetto non è stato utilizzato
* HockeySDK.Xamarin => crash reporting e beta distribution tramite il servizio *HockeyApp*
* ZXing.Net.Mobile.Forms => Scan di barcode e/o QR code
