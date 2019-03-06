---
id: forms
title: Forms
permalink: docs/forms.html
prev: lists-and-keys.html
next: lifting-state-up.html
redirect_from:
  - "tips/controlled-input-null-value.html"
  - "docs/forms-zh-CN.html"
---

Deoarece elementele din form păstrează în mod natural o stare internă, elementele din form-ul HTML funcționează puțin diferit fata de celelalte elemente DOM din React. De exemplu, acest formular în HTML acceptă un singur nume:

```html
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

Când utilizatorul submite acest form va avea comportamentul implicit al unui form HTML de navigare către o pagină nouă. Dacă doriți acest comportament în React, cu siguranta va functiona. Totusi în majoritatea cazurilor, este convenabil să existe o funcție in JavaScript care sa gestioneaze submiterea formului și accesul la datele introduse de utilizator în form. Modul standard pentru a realiza acest lucru este cu o tehnică numită "componente controlate".

## Controlled Components {#controlled-components}

În HTML, elementele specifice formului, cum ar fi `<input>`, `<textarea>`, și `<select>` îsi păstrează în mod obișnuit starea și o actualizează în funcție de datele introduse de utilizator. În React, starea mutabilă este de obicei păstrată în proprietatea state a componentelor și este actualizată doar cu [`setState()`](/docs/react-component.html#setstate).

Putem combina cele două, făcând ca starea React să fie "singura sursă de adevăr". In felul acesta componenta React care redă un form va controla și ceea ce se întâmplă în acel form  pe baza datelor introduse ulterior de utilizator. Astfel un element de intrare al formului a cărui valoare este controlată de React se numește "componentă controlată".

De exemplu, dacă dorim ca in exemplul anterior să logăm numele la momentul trimiterii, putem implementa formul ca o componentă controlată:

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
    alert('A name was submitted: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**Try it on CodePen**](https://codepen.io/gaearon/pen/VmmPgp?editors=0010)

Deoarece atributul `value` este setat in elementul form, valoarea afișată va fi întotdeauna `this.state.value`, făcând starea React sursa de adevăr. Valoarea afișată va fi actualizată pe masura ce  utilizatorul introduce datele deoarece `handleChange` se executa cu fiecare apăsare de tastă pentru a actualiza starea React.

Pentru o componentă controlată, fiecare stare mutată va avea asociată o funcție de manipulare. Acest lucru face ca modificarea sau validarea intrărilor utilizatorului se fie directa. De exemplu, dacă vrem să fortam că numele sa fie scrise cu toate literele majuscule, putem implementa  `handleChange` astfel:

```javascript{2}
handleChange(event) {
  this.setState({value: event.target.value.toUpperCase()});
}
```

## The textarea Tag {#the-textarea-tag}

În HTML, un element `<textarea>` definește textul prin copii(children) săi::

```html
<textarea>
  Buna, aceasta este o zona pentru introducerea textului.
</textarea>
```

În React, un `<textarea>` foloseste în schimb un atribut `value`. În felul acesta, un form ce utilizează `<textarea>` poate fi implementat foarte asemănător cu un form care utilizează o intrare cu o singură linie:

```javascript{4-6,12-14,26}
class EssayForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 'Va rugam sa scrieti un eseu despre elementul DOM favorit.'
    };

    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }

  handleSubmit(event) {
    alert('Eseul a fost trimis: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Essay:
          <textarea value={this.state.value} onChange={this.handleChange} />
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

Observați `this.state.value` este inițializat în constructor, astfel încât zona text să fie presentata cu acelasi text.

## The select Tag {#the-select-tag}

În HTML, `<select>` creează o listă derulantă. De exemplu, acest HTML creează o listă drop-down de arome:

```html
<select>
  <option value="grapefruit">Grapefruit</option>
  <option value="lime">Lime</option>
  <option selected value="coconut">Coconut</option>
  <option value="mango">Mango</option>
</select>
```

Rețineți că opțiunea Coconut este selectată inițial datorită atributului `selected`. React, în loc să utilizeze atributul `selected`, utilizează un atributul `value` pe rădăcină `select` tag. Acest lucru este mai convenabil într-o componentă controlată, deoarece trebuie doar să o actualizați într-un singur loc. De exemplu:

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
    alert('Aroma ta preferată e: ' + this.state.value);
    event.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          Alege aroma preferata:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="grapefruit">Grapefruit</option>
            <option value="lime">Lime</option>
            <option value="coconut">Coconut</option>
            <option value="mango">Mango</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

[**Try it on CodePen**](https://codepen.io/gaearon/pen/JbbEzX?editors=0010)

În general, acest lucru face ca `<input type="text">`, `<textarea>`, and `<select>` să funcționeze foarte asemănător - toate acceptă un atribut `value` pe care îl puteți utiliza pentru a implementa o componentă controlată.

> Notă
>
> Pentru a selecta mai multe opțiuni cu o eticheta `select`, puteți pasapentru atributul `value` un array(o matrice):
>
>```js
><select multiple={true} value={['B', 'C']}>
>```

## The file input Tag {#the-file-input-tag}

În HTML, un `<input type="file">` permite utilizatorului să aleagă unul sau mai multe fișiere din spațiul de stocare al dispozitivului pentru a fi încărcat pe un server sau pentru a fi manipulate de JavaScript prin [API-ul de fișiere](https://developer.mozilla.org/en-US/docs/Web/API/File/Using_files_from_web_applications).

```html
<input type="file" />
```

Deoarece valoarea sa este doar pentru citire, este o componentă **necontrolată** în React. Va fi presentata in detaliu [mai tirziu în documentație](/docs/uncontrolled-components.html#the-file-input-tag) împreună cu alte componente necontrolate.

## Handling Multiple Inputs {#handling-multiple-inputs} / Manipulare de intrări multiple

Atunci când aveți nevoie să gestionați mai multe elemente de intrare controlate (`input`), puteți adăuga un atribut `name` pentru fiecare element si lasati funcția de manipulare să aleagă ce trebuie să facă în funcție de valoarea lui `event.target.name`.

exemplu:

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
    const value = target.type === 'checkbox' ? target.checked : target.value;
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
          Number of guests:
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

[**Try it on CodePen**](https://codepen.io/gaearon/pen/wgedvV?editors=0010)

Rețineți modul în care am folosit sintaxa ES6 pentru [proprietatea procesata nume](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Object_initializer#Computed_property_names) pentru a actualiza starea cheii corespunzătoare datei de intrare furnizate:

```js{2}
this.setState({
  [name]: value
});
```

Este echivalent cu codul ES5 urmator:

```js{2}
var partialState = {};
partialState[name] = value;
this.setState(partialState);
```

De asemenea, deoarece `setState()` [imbina automat o stare parțială cu o starea curenta](/docs/state-and-lifecycle.html#state-updates-are-merged), trebuie doar să o chemăm cu parțiile schimbate.

## Controlled Input Null Value {#controlled-input-null-value} / Valoarea  de Intrare Controlată Nulă 

Specificînd proprietatea value pentru o [componentă controlată](/docs/forms.html#controlled-components), va împiedica utilizatorul să schimbe intrarea, doar dacă doriți acest lucru. Dacă ați specificat o `value`, dar intrarea este încă editabilă, este posibil să fi setat `value` fara sa va dati seama `undefined` sau `null`.

Următorul cod demonstrează acest lucru. (Intrarea este blocată la început, dar devine editabilă după o scurtă întârziere.)

```javascript
ReactDOM.render(<input value="hi" />, mountNode);

setTimeout(function() {
  ReactDOM.render(<input value={null} />, mountNode);
}, 1000);

```

## Alternatives to Controlled Components {#alternatives-to-controlled-components} / Alternative pentru componentele controlate 

Uneori este dificil să folosiți componente controlate, deoarece trebuie să implementati un handler(manipulant) pentru evenimente pentru fiecare situatie în care datele dvs. se pot schimba și să conducă toate starile de intrare printr-o componentă React. Acest lucru poate deveni deosebit de enervant când convertiți un cod de  bază preexistentă în React sau integrați o aplicație React cu o bibliotecă non-React. În aceste situații, vă recomandăm să verificați [componentele necontrolate](/docs/uncontrolled-components.html), o tehnică alternativă pentru implementarea formul de intrare.

## Fully-Fledged Solutions {#fully-fledged-solutions} / Soluții complete

Dacă doriti o soluție completă inclusînd validarea, pastrarea câmpurilor vizitate și manipularea formului submis, atunci [Formik](https://jaredpalmer.com/formik) este una dintre alegerile populare. In orice caz, este construită pe aceleași principii ale componentelor controlate și ale starii gestionate - deci nu neglijați să le învățați.
