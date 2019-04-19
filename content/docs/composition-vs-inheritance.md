---
id: composition-vs-inheritance
titlu: Composition vs Inheritance
permalink: docs/composition-vs-inheritance.html
redirect_from:
  - "docs/multiple-components.html"
anterior: lifting-state-up.html
urmator: thinking-in-react.html
---

React are un model de compoziție puternic și vă recomandăm să utilizați compoziția în locul moștenirii pentru a reutiliza codul, între componente.

În această secțiune, vom analiza câteva probleme în care dezvoltatorii novici în React aleg adesea moștenirea și vom arata cum le putem rezolva prin compoziție.

## Izolare {#containment}

Unele componente nu își cunosc copiii înainte de timp. Acest lucru este valabil mai ales pentru componente precum `Sidebar` sau` Dialog` care reprezintă casete generice.

Recomandăm ca astfel de componente să utilizeze proprietățile specifice pentru `copii` astfel încât sa transmita elementele de copii direct în producția lor:

```js{4}
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}
```

Acest lucru permite altor componente să introducă in ele copii la întâmplare prin cuibărind JSX-ul:


```js{4-9}
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

**[Incercați in CodePen](https://codepen.io/gaearon/pen/ozqNOV?editors=0010)**

Orice element din interiorul tag-ul JSX `<FancyBorder>`devine trecut în componența` FancyBorder` ca si proprietăți de `copii` Din moment ce `FancyBorder` redă` {props.children} `in interiorul unui <div> `, elementele  apar în rezultatul final.


Chiar daca acest lucru este mai puțin frecvent, uneori este posibil să aveți nevoie de mai multe "găuri" într-o componentă. În astfel de cazuri, puteți veni cu propria dvs. convenție în loc să utilizați "copii":

```js{5,8,18,21}
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```

[**Incercați in CodePen**](https://codepen.io/gaearon/pen/gwZOJp?editors=0010)

Elementele React precum `<Contacts />` și `<Chat />` sunt doar obiecte, astfel le poți trece ca si proprietati, ca orice alte date. Această abordare vă poate aminti de "slots" din alte librari, dar nu există nicio limitare a ceea ce puteți trece ca elemente de recuzită în React.


## Specialization {#specialization}
Cateodată ne gândim la componente ca find "cazuri speciale" a altor comnponente. De exemplu am putea spunde ca un `WelcomeDialog` e un cas special a `Dialog`-ului.

In React, acest lucru este deasemenea arhivat de către compoziție,unde o componenta mai "specifica" reda una mai "generica" si o configureaza cu proprietati:

```js{5,8,16-18}
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```

[**Incercați in CodePen**](https://codepen.io/gaearon/pen/kkEaOZ?editors=0010)

Compoziția funcționează la fel de bine pentru componente definite ca si clase:


```js{10,27-31}
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.message}
      </p>
      {props.children}
    </FancyBorder>
  );
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  render() {
    return (
      <Dialog title="Mars Exploration Program"
              message="How should we refer to you?">
        <input value={this.state.login}
               onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    );
  }

  handleChange(e) {
    this.setState({login: e.target.value});
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }
}
```

[**Incercați in CodePen**](https://codepen.io/gaearon/pen/gwZbYa?editors=0010)

## Si despre mostenire? {#so-what-about-inheritance}

La Facebook, folosim React in mii de componente, si nu am nici un caz unde am recomanda crearea ierarhiilor de mostenire a componentelor.

Proprietațile și compoziția vă oferă toată flexibilitatea de care aveți nevoie pentru a particulariza aspectul și comportamentul unei componente într-un mod explicit și sigur. Amintiți-vă că, componentele pot accepta proprietați arbitrare, inclusiv valori primitive, elemente React sau funcții.

Daca doriți să refolosiți o funcționalitate non-UI intre componente, vă sugerăm să o extrageți într-un modul JavaScript separat.Componentele pot importa si folosi acea funcție,obiect sau clasă, fără să o extindă.
