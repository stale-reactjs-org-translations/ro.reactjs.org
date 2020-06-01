---
id: forms
title: Formulare
permalink: docs/forms.html
prev: lists-and-keys.html
next: lifting-state-up.html
redirect_from:
  - "tips/controlled-input-null-value.html"
  - "docs/forms-zh-CN.html"
---

Formularele HTML funcționează un pic diferit față de alte elemente DOM din React, datorită faptului că elementele din formulare își păstrează o parte din stare în mod natural. De exemplu, următorul formular, scris in HTML simplu, acceptă un singur nume:

```html
<form>
  <label>
    Nume:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

Acest formular menține comportamentul natural al unui formular HTML de a naviga către o nouă pagină in momentul în care utilizatorul depune formularul. Daca se dorește acest comportament in React, el funcționează. Totuși, in cele mai multe cazuri, este mai convenabil să avem o funcție JavaScript care să se ocupe de procesul de depunere al formularului și care să aibă acces la datele pe care utilizatorul le-a introdus in formular. Modalitatea standard prin care se poate realiza acest lucru este o tehnică numită "componente controlate".

## Componente controlate {#controlled-components}

În HTML, elementele din formulare precum `<input>`, `<textarea>`, și `<select>` își mențin starea în mod natural și o actualizează pe baza informațiilor introduse de utilizator. În React, starea variabilă este in mod normal ținută in proprietatea de stare a unei componente, și actualizată numai prin [`setState()`](/docs/react-component.html#setstate).

Putem să combinăm cele doua abordări prin declararea stării din React ca fiind "singura sursă a adevărului". În acest caz, componenta din React care va randa formularul va controla și ce se va întampla în interiorul formularului în momentul în care informația este modificată. Un element al formularului a cărui valoare este controlată de React in acest mod se numește "componentă controlată".  

De exemplu, dacă ne dorim să modificăm codul anterior și să afișam numele la depunerea formularului, putem rescrie formularul ca pe o componentă controlată:

```javascript{4,10-12,24}
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Un nume a fost introdus: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Nume:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**Incearcă exemplul pe site-ul CodePen**](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

Din moment ce atributul `value` este configurat pe elementul nostru din formular, valoarea afișată va fi întotdeauna `this.state.value`, starea din React devenind sursa adevărului. Evenimentul `handleChange` va fi executat la fiecare apăsare de tastă pentru a actualiza starea din React și astfel valoarea afișată va fi actualizată în timp ce utilizatorul tastează. 

<<<<<<< HEAD
În componentele controlate, fiecare modificare a stării va avea asociată o funcție de manipulare a datelor. Acest lucru face foarte facilă modificarea sau validarea datelor introduse de utilizator. De exemplu, dacă ne dorim să impunem scrierea numelor cu caractere majuscule, putem să scriem funcția `handleChange` in felul următor:

```javascript{2}
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}
```
=======
With a controlled component, the input's value is always driven by the React state. While this means you have to type a bit more code, you can now pass the value to other UI elements too, or reset it from other event handlers.
>>>>>>> b4b59062e59d56da37274c6de1fa4a134d2d8f49

## Elementul textarea {#the-textarea-tag}

În HTML, un element `<textarea>` își definește textul prin elementele sale copil:

```html
<textarea>
  Salut, aceste este un text într-o zonă de text
</textarea>
```

În React, elementul `<textarea>` folosește în schimb atributul `value`. În acest fel, un formular care folosește un element `<textarea>` poate fi scris foarte asemănător cu unul care folosește un element `<input>` cu o singură linie de text:

```javascript{4-6,12-14,26}
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Te rog să scrii un eseu despre elementul tău favorit din DOM.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('S-a trimis un eseu: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Eseu:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

De remarcat că `this.state.value` este inițializată in constructor, și astfel textarea conține din start un text in interior.

## Elementul select {#the-select-tag}

În HTML, un element `<select>` crează o listă verticală. De exemplu, acest HTML crează o listă verticală de arome:

```html
<select>
  <option value="grapefruit">Grepfrut</option>
  <option value="lime">Lămâie verde</option>
  <option selected value="coconut">Nucă de cocos</option>
  <option value="mango">Mango</option>
</select>
```
De remarcat că opțiunea `Nuca de cocos` este selectată inițial, datorită atributului `selected`. În schimb, în React vom folosi un atribut `value` direct pe elementul rădăcină `select` în locul atributului `selected`. Această modalitate este mai convenabilă într-o componentă controlată datorită faptului că actualizarea se va face într-un singur loc. De exemplu:

```javascript{4,10-12,24}
class FlavorForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'coconut'};

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Aroma ta favorită este: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Alege aroma favorită:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grepfrut</option>
            <option value="lime">Lămâie verde</option>
            <option value="coconut">Nuca de cocos</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**Incearcă exemplul pe site-ul CodePen**](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

Această abordare are ca efect funcționarea similara a elementelor `<input type="text">`, `<textarea>`, și `<select>` - toate acceptă un atribut `value` care se poate folosi pentru a implementa o componentă controlată. 

> Notă
>
> Atributul `value` poate conține un vector, permițând selectarea mai multor opțiuni într-un element `select`:
>
>```js
><select multiple={true} value={['B', 'C']}>
>```

## Elementul input type="file" {#the-file-input-tag}

În HTML, un element `<input type="file">` permite utilizatorului să aleagă unul sau mai multe fișiere locale pentru a fi încărcate pe un server sau manipulate prin JavaScript prin [File API](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications).

```html
<input type="file" />
```

Din cauza faptului că valoarea acestui element nu poate fi decât citită, acesta este o componentă **necontrolată**. Utilizarea ei este discutată împreuna cu alte componente necontrolate [mai tarziu in documentatie](/docs/uncontrolled-components.html#the-file-input-tag).

## Operarea simultană mai multor elemente input {#handling-multiple-inputs}

Când se dorește operarea simultană a mai multor componenete controlate de tipul `input`, se poate adăuga un atribut `name` fiecărei componente și funcția de manipulare va decide execuția bazându-se pe valoarea din `event.target.name`.

De exemplu:

```javascript{15,18,28,37}
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      isGoing: true,
      numberOfGuests: 2
    };

    this.handleInputChange = this.handleInputChange.bind(this);
  }

  handleInputChange(event) {
    const target = event.target;
    const value = target.name === 'isGoing' ? target.checked : target.value;
    const name = target.name;

    this.setState({
      [name]: value
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleInputChange} />
        </label>
        <br />
        <label>
          Număr de invitați:
          <input
            name="numberOfGuests"
            type="number"
            value={this.state.numberOfGuests}
            onChange={this.handleInputChange} />
        </label>
      </form>
    );
  }
}
```

[**Incearcă exemplul pe site-ul CodePen**](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

A se nota folosirea sintaxei ES6 [nume de proprietăți generate](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names) pentru a actualiza cheia proprietății de stare care corespunde numelui elementului de input selectat:

```js{2}
this.setState({
  [name]: value
});
```

Codul de mai sus este echivalentul sintaxei ES5:

```js{2}
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```

De asemenea, din moment ce `setState()` în mod automat [contopește o stare parțială în starea curentă](/docs/state-and-lifecycle.html#state-updates-are-merged), o putem apela folosind ca argument doar ceea ce s-a modificat. 

## Componentă controlată cu valoare Null {#controlled-input-null-value}

Specificarea proprietații value pentru o [componentă controlată](/docs/forms.html#controlled-components) face ca modificarea valorii acesteia să fie posibilă doar dacă acest lucru se dorește explicit. Dacă proprietatea `value` este specificată, iar componenta rămâne editabilă, valoarea acesteia a fost cel mai probabil setată ca `undefined` sau `null`.

Codul următor demonstrează acest caz. (În primă fază, conținului elementului `input` nu poate fi modificat, dar devine editabil după o secundă. )

```javascript
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);

```

## Alternative pentru componentele controlate {#alternatives-to-controlled-components}

Uneori folosirea componentelor controlate poate să fie dificilă, deoarece necesită implementarea unei funcții de manipulare a datelor pentru fiecare modalitate în care valoarea elementelor din formular se poate schimba și rutarea stării elementului printr-o componentă React. Acestă abordare poate să devină foarte incomodă cand se dorește transformarea unei aplicații existente într-o aplicație de tip React sau integrarea unei aplicații React cu o librărie non-React. În aceste situații, o soluție poate fi folosirea [componentelor necontrolate](/docs/uncontrolled-components.html), o tehnică alternativă pentru implementarea formularelor de introducere a datelor. 

## Soluții complete {#fully-fledged-solutions}

Dacă se dorește folosirea unei soluții complete care să includă validare, memorarea câmpurilor vizitate și administrarea depunerii formularului, [Formik](https://jaredpalmer.com/formik) este una dintre cele mai populare alternative. De menționat totuși că și această soluție este implementată pe principiul componentelor controlate și al stării gestionate - deci nu neglijați studiul acestora. 
