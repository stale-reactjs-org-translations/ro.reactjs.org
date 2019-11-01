---
id: react-api
title: API Top-Level
layout: docs
category: Reference
permalink: docs/react-api.html
redirect_from:
  - "docs/reference.html"
  - "docs/clone-with-props.html"
  - "docs/top-level-api.html"
  - "docs/top-level-api-ja-JP.html"
  - "docs/top-level-api-ko-KR.html"
  - "docs/top-level-api-zh-CN.html"
---

`React` este punctul de intrare în biblioteca React. Dacă încarci React dintr-un tag `<script>`, aceste API-uri top level sunt disponibile în variabila globală `React`. Dacă folosești ES6 cu npm, poți să scrii `import React from 'react'`. Dacă folosești ES5 cu npm, poți să scrii `var React = require('react')`.

## Prezentare generală {#overview}

### Componente {#components}

Componentele React iți permit împărțirea UI-ului în părți independente și refolosibile, și să te gândești la fiecare parte în mod izolat. Componentele React pot fi definite moștenind `React.Component` sau `React.PureContent`.

 - [`React.Component`](#reactcomponent)
 - [`React.PureComponent`](#reactpurecomponent)

Dacă nu folosești clase ES6, poți folosi modulul `create-react-class`. Vezi [React fără ES6](/docs/react-without-es6.html) pentru mai multe informații.

Componentele React pot fi de asemenea definite ca funcții care pot fi impachetate:

- [`React.memo`](#reactmemo)

### Crearea elementelor React {#creating-react-elements}

Recomandăm [folosirea JSX](/docs/introducing-jsx.html) pentru a descrie cum ar trebui să arate UI-ul. Fiecare element JSX este doar syntactic sugar pentru apelul [`React.createElement()`](#createelement). Nu vei folosi următoarele metode în mod frecvent dacă folosești JSX.

- [`createElement()`](#createelement)
- [`createFactory()`](#createfactory)

Vezi [React fără JSX](/docs/react-without-jsx.html) pentru mai multe informații.

### Transformarea elementelor {#transforming-elements}

`React` oferă câteva API-uri pentru manipularea elementelor:

- [`cloneElement()`](#cloneelement)
- [`isValidElement()`](#isvalidelement)
- [`React.Children`](#reactchildren)

### Fragmente {#fragments}

`React` oferă de asemenea o componentă pentru randarea de elemente multiple fără un container.

- [`React.Fragment`](#reactfragment)

### Refs {#refs}

- [`React.createRef`](#reactcreateref)
- [`React.forwardRef`](#reactforwardref)

### Suspense {#suspense}

Suspense oferă componentelor posibilitatea de a "aștepta" ceva înainte de randare. În prezent, Suspense permite doar un caz de utilizare: [încărcarea dinamică a componentelor cu `React.lazy`](/docs/code-splitting.html#reactlazy). În viitor, va suporta alte cazuri de utilizare precum cererea datelor pe rețea.

- [`React.lazy`](#reactlazy)
- [`React.Suspense`](#reactsuspense)

### Hooks {#hooks}

*Hooks* sunt un adaos nou în React 16.8. Îti permit să folosești state și alte funcționalități ale React fără a scrie o clasă. Hooks au o [secțiune dedicata a documentației](/docs/hooks-intro.html) și o referință separată a API-ului:

- [Hooks de Bază](/docs/hooks-reference.html#basic-hooks)
  - [`useState`](/docs/hooks-reference.html#usestate)
  - [`useEffect`](/docs/hooks-reference.html#useeffect)
  - [`useContext`](/docs/hooks-reference.html#usecontext)
- [Hooks Adiționale](/docs/hooks-reference.html#additional-hooks)
  - [`useReducer`](/docs/hooks-reference.html#usereducer)
  - [`useCallback`](/docs/hooks-reference.html#usecallback)
  - [`useMemo`](/docs/hooks-reference.html#usememo)
  - [`useRef`](/docs/hooks-reference.html#useref)
  - [`useImperativeHandle`](/docs/hooks-reference.html#useimperativehandle)
  - [`useLayoutEffect`](/docs/hooks-reference.html#uselayouteffect)
  - [`useDebugValue`](/docs/hooks-reference.html#usedebugvalue)

* * *

## Referință {#reference}

### `React.Component` {#reactcomponent}

`React.Component` este clasa de bază pentru componentele React atunci când sunt definite folosind [clase ES6](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Classes):

```javascript
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

Vezi [Referință API pentru React.Component](/docs/react-component.html) pentru o lista de metode și prorietăți ale clasei de bază `React.Component`.

* * *

### `React.PureComponent` {#reactpurecomponent}

`React.PureComponent` este similar cu [`React.Component`](#reactcomponent). Diferența dintre ele este că [`React.Component`](#reactcomponent) nu implementează [`shouldComponentUpdate()`](/docs/react-component.html#shouldcomponentupdate), iar `React.PureComponent` o implementează cu un shallow prop și compararea state-ului. 

Dacă metoda `render()` a unei componente React randează același rezultat considerând aceleași props și același state, poți folosi `React.PureComponent` pentru o creștere de performanță în unele cazuri.

> Notă
>
> Metoda `shouldComponentUpdate()` a clasei `React.PureComponent` compară obiectele doar pe primul nivel. Dacă acestea conțin structuri complexe de date, metoda poate produce false-negatives pentru diferențe pe nivele mai joase. Moștenește `PureComponent` doar atunci când te aștepți să ai props și state simple, sau foloseste [`forceUpdate()`](/docs/react-component.html#forceupdate) când știi că datele s-au schimbat. Sau consideră să folosești [obiecte imutabile](https://facebook.github.io/immutable-js/) pentru a facilita compararea datelor imbricate.
>
> În plus, metoda `shouldComponentUpdate()` a clasei `React.PureComponent` sare peste actualizarea props pentru întregul subtree. Asigură-te că toate componentele copil sunt "pure".

* * *

### `React.memo` {#reactmemo}

```javascript
const MyComponent = React.memo(function MyComponent(props) {
  /* randează folosind props */
});
```

`React.memo` este un [component higher order](/docs/higher-order-components.html). Este similar cu [`React.PureComponent`](#reactpurecomponent), dar pentru componente definite prin funcții.

Dacă componentul funcție randează același rezultat considerând aceleași props, îl poți împacheta într-un apel către `React.memo` pentru o îmbunătățire a performaneței în unele cazuri, prin memorarea rezultatului. Asta înseamnă că React nu va randa din nou componenta, ci va folosi ultimul rezultat randat.

În mod implicit, va compara obiectele din props doar pe primul nivel. Dacă vrei să controlezi logica comparației, poți scrie o funcție personalizată de comparație ca al doilea argument.

```javascript
function MyComponent(props) {
  /* randează folosind props */
}
function areEqual(prevProps, nextProps) {
  /*
  întoarce true dacă nextProps și prevProps randează
  același rezultat, altfel întoarce false
  */
}
export default React.memo(MyComponent, areEqual);
```

Această metodă există doar pentru **[optimizarea performanței](/docs/optimizing-performance.html).** Nu te baza pe ea pentru a "preveni" o randare, deoarece poate duce la bug-uri.

> Notă
>
> Spre deosebire de metoda [`shouldComponentUpdate()`](/docs/react-component.html#shouldcomponentupdate) din componentele clasă, funcția `areEqual` întoarce `true` dacă props-urile sunt egale și `false` data props-urile nu sunt egale. Este comportamentul opus al metodei `shouldComponentUpdate`.

* * *

### `createElement()` {#createelement}

```javascript
React.createElement(
  type,
  [props],
  [...children]
)
```

Crează și întoarce un nou [element React](/docs/rendering-elements.html) în funcție de tipul dat. Argumentul type poate fi un tag sub formă de string (precum `'div'` sau `'span'`), un [component React](/docs/components-and-props.html) (o clasă sau o funcție), sau un [fragment React](#reactfragment).

Codul scris cu [JSX](/docs/introducing-jsx.html) va fi convertit către `React.createElement()`. Nu vei apela de obicei `React.createElement()` în mod direct dacă folosești JSX. Vezi [React Fara JSX](/docs/react-without-jsx.html) pentru a afla mai multe.

* * *

### `cloneElement()` {#cloneelement}

```
React.cloneElement(
  element,
  [props],
  [...children]
)
```

Cloneaza și întoarce un nou element React folosind `element` ca punct de plecare. Elementul rezultat va avea props-urile elementului original cu noile props îmbinate doar pe primul nivel. Noii copii vor înlocui copiii existenti. Vor fi păstrate `key` și `ref` de la elementul original.

`React.cloneElement()` este aproape echivalent cu:

```js
<element.type {...element.props} {...props}>{children}</element.type>
```

Cu toate acestea, pastreaza `ref`urile. Asta înseamnă că dacă un copil are `ref`, nu îl vei fura accidental de la parinte. Vei avea același `ref` atașat la noul element.

Acest API a fost introdus pentru a inlocui metoda depreciată `React.addons.cloneWithProps()`.

* * *

### `createFactory()` {#createfactory}

```javascript
React.createFactory(type)
```

Intoarce o funcție care produce elemente React de un tip dat. Precum [`React.createElement()`](#createelement), argumentul type poate fi de tipul tag sub formă de string (precum `'div'` sau `'span'`), de tipul [component React](/docs/components-and-props.html) (o clasă sau o funcție), sau de tipul [fragment React](#reactfragment).

Acest helper este considerat învechit și te încurajăm să folosești JSX sau direct `React.createElement()`.

Nu vei apela de obicei `React.createFactory()` în mod direct dacă folosești JSX. Vezi [React fără JSX](/docs/react-without-jsx.html) pentru a afla mai multe.

* * *

### `isValidElement()` {#isvalidelement}

```javascript
React.isValidElement(object)
```

Verifică dacă un obiect este un element React. Intoarce `true` sau `false`.

* * *

### `React.Children` {#reactchildren}

`React.Children` oferă utilități pentru lucrul cu structura de date opaca `this.props.children`.

#### `React.Children.map` {#reactchildrenmap}

```javascript
React.Children.map(children, function[(thisArg)])
```

Invocă o funcție pe fiecare copil conținut de `children` cu `this` aplicat pe `thisArgs`. Dacă `children` este un array, va fi traversat și funcția va fi aplicată pe fiecare copil în array. Dacă `children` este `null` sau `undefined`, această metodă o să întoarcă `null` sau `undefined` în locul unui array.

> Notă
>
> Dacă `children` este un `Fragment` va fi tratat ca un copil unic și nu va fi traversat.

#### `React.Children.forEach` {#reactchildrenforeach}

```javascript
React.Children.forEach(children, function[(thisArg)])
```

Precum [`React.Children.map()`](#reactchildrenmap) dar nu întoarce un array.

#### `React.Children.count` {#reactchildrencount}

```javascript
React.Children.count(children)
```

Intoarce numărul total de componente din `children`, egal cu numărul de invocari ale callback-ului pasat către `map` sau `forEach`.

#### `React.Children.only` {#reactchildrenonly}

```javascript
React.Children.only(children)
```

Verifică dacă `children` are doar un singur copil (un element React) și îl întoarce. Altfel, această metodă declanșează o eroare.

> Notă:
>
>`React.Children.only()` nu acceptă valoare returnata de [`React.Children.map()`](#reactchildrenmap) pentru că este defapt un array și nu un element React.

#### `React.Children.toArray` {#reactchildrentoarray}

```javascript
React.Children.toArray(children)
```

Intoarce structura de date opaca `children` ca un array liniar cu cheile asociate fiecărui copil. Este utila dacă vrei să manipulezi colecții de copii în metoda render, mai ales dacă vrei să reordonezi sau să selectezi anumiți copii din `this.props.children` înainte de a-i pasa mai departe.

> Notă:
>
> `React.Children.toArray()` schimbă cheile pentru a păstra semantica array-urilor imbricate atunci când se liniarizeaza listele de copii. Asta înseamnă că `toArray` prefixează fiecare cheie din array-ul rezultat în așa fel incât cheia fiecărui element este atribuită array-ului inițial care a conținut-o.

* * *

### `React.Fragment` {#reactfragment}

Componenta `React.Fragment` iți permite să întorci mai multe elemente în metoda `render()` fără a crea elemente DOM adiționale:

```javascript
render() {
  return (
    <React.Fragment>
      Niște text.
      <h2>Un heading</h2>
    </React.Fragment>
  );
}
```

De asemenea poți folosi sintaxa scurtă `<></>`. Pentru mai multe informații, vezi [React v16.2.0: Improved Support for Fragments](/blog/2017/11/28/react-v16.2.0-fragment-support.html).


### `React.createRef` {#reactcreateref}

`React.createRef` crează un [ref](/docs/refs-and-the-dom.html) care poate fi atașat elementelor React folosind atributul ref.
`embed:16-3-release-blog-post/create-ref-example.js`

### `React.forwardRef` {#reactforwardref}

`React.forwardRef` crează o componentă React care dă mai departe atributul [ref](/docs/refs-and-the-dom.html) pe care îl primește, unei alte componente mai jos în ierarhie. Această tehnică nu este foarte comună, dar este folositoare în mod special în doua scenarii:

* [Pasarea refs către DOM components](/docs/forwarding-refs.html#forwarding-refs-to-dom-components)
* [Pasarea refs în higher-order-components](/docs/forwarding-refs.html#forwarding-refs-în-higher-order-components)

`React.forwardRef` acceptă o funcție care randează ca argument. React o să apeleze această funcție cu `props` și `ref` ca argumente. Această funcție trebuie să întoarcă un nod React.

`embed:reference-react-forward-ref.js`

În exemplul de mai sus, React pasează un `ref` dat pentru elementul `<FancyButton ref={ref}>` ca al doilea argument al funcției de randare din apelul `React.forwardRef`. Această funcție de randare pasează `ref` către elementul `<button ref={ref}>`.

Ca rezultat, dupa ce React atașează ref-ul, `ref.current` va indica direct către instanța DOM `<button>`.

Pentru mai multe informații vezi [pasarea refs](/docs/forwarding-refs.html).

### `React.lazy` {#reactlazy}

`React.lazy()` iți permite să definesti o componentă care este încărcată dinamic. Asta ajută la reducerea dimensiunii bundle-ului și întârzie încărcarea componentelor care nu sunt folosite la randarea inițială.

Poți învăța cum să îl folosești din [documentația code splitting](/docs/code-splitting.html#reactlazy). Te poți uita de asemenea și pe [acest articol](https://medium.com/@pomber/lazy-loading-and-preloading-components-în-react-16-6-804de091c82d) care arată cum să îl folosești mai în detaliu.

```js
// Acestă componentă este încărcată dinamic
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

Reține că pentru randarea componentelor `lazy` este necesară randarea unei componente`<React.Suspense>` mai sus în ierarhia de randare. Așa specifici un indicator de încărcare.

> **Notă**
>
> Folosirea `React.lazy` cu importuri dinamice face obligatorie disponibilitatea Promise-urilor în mediul JS. Acest lucru cere un polyfill pe IE11 și pe versiuni mai vechi.

### `React.Suspense` {#reactsuspense}

`React.Suspense` iți permite să specifici indicatorul de încărcare în cazul în care unele componente din ierarhia copil nu sunt gata pentru randare. În prezent, componentele lazy loading sunt **singurele** cazuri suportate de `<React.Suspense>`:

```js
// Acestă componentă este încărcată dinamic
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // Afișează <Spinner> până când OtherComponent se incarcă
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

Este documentat în [ghidul de code splitting](/docs/code-splitting.html#reactlazy). Reține ca `lazy` se pot afla adânc în ierarhia `Suspense` -- nu trebuie să le împacheteze pe toate. Cea mai buna practică este să pui `<Suspense>` unde vrei să vezi un indicator de încărcare, dar să folosești `lazy()` unde vrei să faci code splitting.

Deși nu este acoperit în prezent, în viitor planificam ca `Suspense` să acopere scenarii precum requesturi pe rețea. Poți să citești despre asta în [roadmap-ul nostru](/blog/2018/11/27/react-16-roadmap.html).

>Notă:
>
>`React.lazy()` și `<React.Suspense>` nu sunt înca suportate de `ReactDOMServer`. Este o limitare știută care va fi rezolvată în viitor.
