---
id: thinking-in-react
title: Cum să gândeşti în React
permalink: docs/thinking-in-react.html
redirect_from:
  - 'blog/2013/11/05/thinking-in-react.html'
  - 'docs/thinking-in-react-zh-CN.html'
prev: composition-vs-inheritance.html
---

React este, în opinia noastră, modalitatea principală de a construi aplicații Web mari și rapide folosind JavaScript. A făcut faţă foarte bine nevoilor pe care le-am avut la Facebook și Instagram.

Una dintre multiplele părți importante din React este cum te face să te gândești la aplicații pe măsură ce le construiești. În acest articol, vă vom îndruma prin procesul de creare al unui tabel de produse ce permite funcţionalitatea de căutate, folosind React.

## Porneşte de la o schiţă {#start-with-a-mock}

Imaginați-vă că avem deja un API care returnează un JSON și o schiţă de la designerul nostru. Schiţa arată astfel:

![Mockup](../images/blog/thinking-in-react-mock.png)

API-ul nostru JSON returnează date care arată în felul următor:

```
[
  {category: "Sporting Goods", price: "$49.99", stocked: true, name: "Football"},
  {category: "Sporting Goods", price: "$9.99", stocked: true, name: "Baseball"},
  {category: "Sporting Goods", price: "$29.99", stocked: false, name: "Basketball"},
  {category: "Electronics", price: "$99.99", stocked: true, name: "iPod Touch"},
  {category: "Electronics", price: "$399.99", stocked: false, name: "iPhone 5"},
  {category: "Electronics", price: "$199.99", stocked: true, name: "Nexus 7"}
];
```

## Pasul 1: Împărţiţi UI-ul într-o ierarhie de componente {#step-1-break-the-ui-into-a-component-hierarchy}

Primul lucru pe care vreţi să-l faceţi este să desenaţi dreptunghiuri în jurul fiecarei componente (şi subcomponente) in schiţă şi daţi-le un nume tuturor. Dacă lucreaţi cu un artist grafic, este posibil ca ei sa fi facut deja asta, asa ca vorbiţi cu ei! Numele layer-elor de Photoshop ar putea fi numele componetelor dumneavoastră de React!

Dar cum vă daţi seama ce ar trebui sa fie o componenta de sine statoare? Utilizați aceleași tehnici pentru a decide dacă trebuie să creați o nouă funcție sau un obiect. O astfel de tehnică este [single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle), care reprezintă faptul că o componentă trebuie sa facă un singur lucru, ideal. Dacă tinde să crească, ar trebui să fie descompusă în subcomponente mai mici.

Deoarece afișați adesea un model de date JSON unui utilizator, veți observa că, dacă modelul dvs. a fost construit corect, interfața dvs. de utilizator (și, prin urmare, structura dvs. de componente) se va potrivi de minune. Asta deoarece UI-ul și modelele de date tind să adere la aceeași *arhitectură de informații*. Separați-vă UI-ul în componente, în așa fel încât fiecare componentă se potrivește cu o singură parte din modelul dvs. de date.

![Component diagram](../images/blog/thinking-in-react-components.png)

Puteti observa aici că avem cinci componente într-o singură aplicație. Am redactat italic datele pe care fiecare componentă le reprezintă.

  1. **`FilterableProductTable` (orange):** conţine exemplul în întregime
  2. **`SearchBar` (blue):** primește toate *datele introduse de utilizator*
  3. **`ProductTable` (green):** afişeaza şi filtrează *colectia de date* pe baza *datele introduse de utilizator*
  4. **`ProductCategoryRow` (turquoise):** afișează un cap de tabel pentru fiecare *categorie*
  5. **`ProductRow` (red):** afișează un rând pentru fiecare *produs*

Dacă vă uitați la `ProductTable`, veţi vedea că antetul tabelului (care conține etichetele `Name` și `Price`) nu este o componentă proprie. Aceasta este o chestiune de preferință și există un argument care poate fi făcut în oricare dintre cazuri. Pentru acest exemplu, l-am lăsat ca parte a componentei `ProductTable` deoarece face parte din redarea *colecției de date*, care este responsabilitatea componentei `ProductTable`. Cu toate acestea, dacă acest antet devine complex (dacă am fi adăugat butoane pentru sortare), ar fi cu siguranță logic să-i faci o componentă proprie `ProductTableHeader`.

Acum, că am identificat componentele în schiţa noastră, să le aranjăm într-o ierarhie. Componentele care apar într-o altă componentă a modelului ar trebui să apară ca un copil în ierarhie:

  * `FilterableProductTable`
    * `SearchBar`
    * `ProductTable`
      * `ProductCategoryRow`
      * `ProductRow`

## Pasul 2: Construiţi o versiune statică în React {#step-2-build-a-static-version-in-react}

<p data-height="600" data-theme-id="0" data-slug-hash="BwWzwm" data-default-tab="js" data-user="lacker" data-embed-version="2" class="codepen">See the Pen <a href="https://codepen.io/gaearon/pen/BwWzwm">Thinking In React: Step 2</a> on <a href="https://codepen.io">CodePen</a>.</p>
<script async src="https://production-assets.codepen.io/assets/embed/ei.js"></script>

Acum, că aveți ierarhia dvs. componentă, este timpul să implementați aplicația. Cea mai ușoară cale este să construiți o versiune care să vă ia modelul de date și să afişeze interfața utilizator dar fără a avea interactivitate. Cel mai bine este să decuplați aceste procese deoarece construirea unei versiuni statice necesită multă tastare și fără prea multa gândire, iar adăugarea de interactivitate necesită mai multă gândire și mai puţin de scris. Vom vedea de ce.

Pentru a construi o versiune statică a aplicației dvs. care redă modelul dvs. de date, veți dori să construiți componente care reutilizează alte componente și care transmit date prin *props*. *props* sunt o modalitate de a transmite date de la părinte la copil. Dacă sunteți familiarizat cu conceptul *state*, **nu utilizați deloc state-ul** pentru a construi această versiune statică. State este rezervat numai pentru interactivitate, adică date care se schimbă în timp. Deoarece aceasta este o versiune statică a aplicației, nu aveți nevoie de aceasta.

Puteți construi de sus în jos sau de jos în sus. Adică, puteți începe fie să construiți componentele din vârful ierarhie (adică începând cu `FilterableProductTable`), fie cu cele inferioare în ea (`ProductRow`). În exemplele mai simple, este de obicei mai ușor să mergeți de sus în jos, iar în cazul proiectelor mai mari, este mai ușor să mergeți de jos în sus și să scrieți teste pe măsură ce construiți.

La sfârșitul acestei etape, veți avea o bibliotecă de componente reutilizabile care vă vor reda într-un mod grafic modelul de date. Componentele vor avea numai metode `render()`, deoarece aceasta este o versiune statică a aplicației. Componenta din vârful ierarhiei (`FilterableProductTable`) vă va lua modelul de date ca un prop. Dacă faceţi o modificare de bază a modelului de date și apelaţi din nou `ReactDOM.render()`, interfața grafică va fi actualizată. Puteți să vedeți cum este actualizat interfața grafică și unde să faceți modificări, deoarece nu se întâmplă nimic complicat. **One-way data flow** din React (numit și *one-way binding*) păstrează totul modular și rapid.

Consultaţi vă rog [documentaţia React](/docs/) dacă aveți nevoie de ajutor pentru completarea acestui pas.

### Un scurt interludiu: Props vs State {#a-brief-interlude-props-vs-state}

Există două tipuri de date "model" în React: props și state. Este important să înțelegeți distincția dintre cele două; parcurge-ţi [documentaţia oficială React](/docs/interactivity-and-dynamic-uis.html) dacă nu sunteți sigur ce este diferența. Citiți si [FAQ: What is the difference between state and props?](/docs/faq-state.html#what-is-the-difference-between-state-and-props)

## Pasul 3: Identificaţi reprezentarea minimă (dar completă) a State-ului interfeţei grafice {#step-3-identify-the-minimal-but-complete-representation-of-ui-state}

Pentru a face interfața grafică interactivă, trebuie să puteți declanșa modificări ale modelului de date care stau la baza acesteia. React face acest lucru cu **state**.

Pentru a construi corect aplicația, trebuie mai întâi să vă gândiți la numarul minim de schimbări in state de care aplicația dvs. are nevoie. Cheia aici este [DRY: *Don't Repeat Yourself*](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself). Aflați reprezentarea minimală absolută a state-ului de care are nevoie aplicația dvs. și calculați tot ce aveți nevoie la cerere. De exemplu, dacă construiți o listă TODO, păstrați un array de elemente TODO; nu păstrați o variabilă de state separată pentru numărul elementelor. În schimb, atunci când doriți să redați numărul de elemente. În schimb, când vreți să randați lungimea listei TODO,  luați lungimea array-ului de elemente TODO.

Gândiți-vă la toate fragmentele de date din exemplul aplicației noastre. Vom avem:

  * Lista originală de produse
  * Textul de căutare introdus de utilizator
  * Valoarea checkbox-ului
  * Lista de produse filtrate

Să trecem prin fiecare și să ne dăm seama care este state. Puneţi-vă trei întrebări despre fiecare bucată de date:

  1. Este pasat de la un părinte prin props? Dacă da, probabil că nu este state.
  2. Rămâne neschimbată în timp? Dacă da, probabil că nu este state.
  3. Puteți să o calculați pe baza oricărui alt state sau props în componenta dvs.? Dacă da, nu este de state.

Lista originală de produse este transmisă ca props, deci nu este state. Textul căutării și checkbox-ul par a fi state, deoarece se schimbă în timp și nu pot fi calculate din nimic. În sfârșit, lista de produse filtrate nu este state, deoarece poate fi calculată prin combinarea listei originale de produse cu textul de căutare și cu valoarea checkbox-ul.

În sfarşit, state-ul nostru este:

  * Textul căutării introdus de utilizator
  * Valoarea checkbox-ului

## Pasul 4: Identificaţi unde ar trebui să existe State-ul dumneavoastră {#step-4-identify-where-your-state-should-live}

<p data-height="600" data-theme-id="0" data-slug-hash="qPrNQZ" data-default-tab="js" data-user="lacker" data-embed-version="2" class="codepen">See the Pen <a href="https://codepen.io/gaearon/pen/qPrNQZ">Thinking In React: Step 4</a> on <a href="https://codepen.io">CodePen</a>.</p>

OK, deci am identificat care este numarul minim de schimbări in state-ul aplicaţiei. Apoi, trebuie să identificăm ce componentă se schimbă sau *deține* aceast state.

Amintiți-vă: React utilizeaza fluxul de date într-o singură direcție în josul ierarhiei de componente. S-ar putea să nu fie clar imediat ce componentă ar trebui să dețină un anumit state. **Aceasta este adesea partea cea mai grea de înţeles pentru noii veniți,** deci urmați acești pași pentru a înţelege:

Pentru fiecare parte de state din aplicatia dumneavoastra:

  * Identificați fiecare componentă care randeaza pe baza acelui state.
  * Găsiți o componentă părinte comună (o singură componentă mai presus de toate componentele care are nevoie de state în ierarhie).
  * Fie parintele comun, fie o altă componentă superioară în ierarhie, ar trebui să dețină state-ul.
  * Dacă nu puteți găsi o componentă în care este logic să dețineți state-ul, creați o componentă nouă pur și simplu pentru a ține state-ul și a adăugaţi-o undeva în ierarhia deasupra componentei comune parinte.

Să trecem prin această strategie pentru aplicația noastră:

  * `ProductTable` trebuie să filtreze lista de produse în funcție de state și "SearchBar" trebuie să afișeze textul de căutare și state-ul checkbox-ului.
  * Componenta părinte comună este `FilterableProductTable`.
  * În mod conceptual, are sens ca textul după care se face filtrarea și valoarea checkbox-ului să se afle în `FilterableProductTable`

Foarte bine, deci am decis că state-ul nostru se află în `FilterableProductTable`. Mai întâi, adăugați o proprietate de instanță `this.state = {filterText: '', inStockOnly: false}` în `constructor`ul componentei `FilterableProductTable` pentru a reflecta state-ul inițială al aplicației. Apoi, transmiteţi `filterText` și `inStockOnly` catre `ProductTable` și `SearchBar` ca un prop. În cele din urmă, utilizați aceste props pentru a filtra rândurile din `ProductTable` și setați valorile câmpurilor din formular în `SearchBar`.

Puteți începe a vedea cum se va comporta aplicația dvs.: setați `filterText` cu `"bilă"` și reîncărcaţi aplicația. Veți vedea că tabelul de date este actualizat corect.

## Pasul 5: Adaugaţi flux de date invers {#step-5-add-inverse-data-flow}

<p data-height="600" data-theme-id="0" data-slug-hash="LzWZvb" data-default-tab="js,result" data-user="rohan10" data-embed-version="2" data-pen-title="Thinking In React: Step 5" class="codepen">See the Pen <a href="https://codepen.io/gaearon/pen/LzWZvb">Thinking In React: Step 5</a> on <a href="https://codepen.io">CodePen</a>.</p>

Până acum, am construit o aplicație care se randează corect în funcție de props și state care parcurg ierarhia. Acum este momentul să adăugăm flux de date în sens invers: componentele de formular de la baza ierarhiei trebuie să actualizeze state-ul în `FilterableProductTable`.

React face ca acest flux de date să fie explicit pentru a te ajuta să înțelegi cum funcționează programul dvs., dar necesită puțin mai mult cod scris decât tradiționala two-way data binding.

Dacă încercați să tastați sau să bifați caseta din versiunea curentă a exemplului, veți vedea că React ignoră input-ul dvs. Acest lucru este intenționat, întrucât am setat ca prop-ul `value` al campului `input` să fie întotdeauna egal cu `state`-ul pasat de la `FilterableProductTable`.

Să ne gândim la ce vrem să se întâmple. Vrem să ne asigurăm că, de fiecare dată când utilizatorul schimbă formularul, actualizăm state-ul pentru a reflecta input-ul utilizatorului. Întrucât componentele ar trebui să-și actualizeze propriul state, `FilterableProductTable` va transmite callback-uri către `SearchBar` care vor fi apelate ori de câte ori state-ul trebuie actualizat. Putem folosi event-ul `onChange` al input-urilor pentru a fi notificat. Callback-urile pasate de `FilterableProductTable` vor apela `setState()`, iar aplicația va fi actualizată.

## S-a terminat {#and-thats-it}

Sperăm că acest lucru vă oferă o idee despre cum să vă gândiți la construcția de componente și aplicații cu React. Deși este posibil să tastați ceva mai mult decât obișnuiați, amintiți-vă că, în general, cod este citit mult mai mult decât este scris și este extrem de ușor să citiți acest cod modular şi explicit. Pe măsură ce începeți să construiți mari librării de componente, veți aprecia această explicitate și modularitate, iar odată cu reutilizarea codului, liniile dvs. de cod vor începe să se micșoreze. :)
