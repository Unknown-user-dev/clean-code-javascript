# clean-code-javascript

## Table des matières

1. [Introduction](#introduction)
2. [Variables](#variables)
3. [Functions](#functions)
4. [Objects and Data Structures](#objects-and-data-structures)
5. [Classes](#classes)
6. [SOLID](#solid)
7. [Testing](#testing)
8. [Concurrency](#concurrency)
9. [Error Handling](#error-handling)
10. [Formatting](#formatting)
11. [Comments](#comments)
12. [Translation](#translation)

## Introduction

! [Image humoristique de l'estimation de la qualité du logiciel en fonction du nombre de jurons
vous criez en lisant le code] (https://www.osnews.com/images/comics/wtfm.jpg)

adapté pour JavaScript. Ce n'est pas un guide de style. C'est un guide pour produire
[lisible, réutilisable et refactorable] (https://github.com/ryanmcdermott/3rs-of-software-architecture) en JavaScript.

Tous les principes énoncés ici ne doivent pas être strictement suivis, et encore moins le seront
universellement accepté. Ce sont des lignes directrices et rien de plus, mais elles sont
ceux codifiés au cours de nombreuses années d'expérience collective 

Notre métier de génie logiciel a un peu plus de 50 ans, et nous sommes
apprend encore beaucoup. Quand l'architecture logicielle est aussi ancienne que l'architecture
lui-même, peut-être que nous aurons alors des règles plus difficiles à suivre. Pour l'instant, laissez ces
les lignes directrices servent de pierre de touche pour évaluer la qualité des
Code JavaScript que vous et votre équipe produisez.

Une dernière chose: savoir que cela ne fera pas immédiatement de vous un meilleur logiciel
développeur, et travailler avec eux pendant de nombreuses années ne signifie pas que vous ne ferez pas
des erreurs. Chaque morceau de code commence comme un premier brouillon, comme de l'argile humide
façonné dans sa forme finale. Enfin, nous ciselons les imperfections lorsque
nous l'examinons avec nos pairs. Ne vous en faites pas pour les premières ébauches qui ont besoin
amélioration. Battez le code à la place!

## **Variables**

### Utilisez des noms de variables significatifs et prononçables

**Mauvais:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**Mauvais:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

**[⬆ back to top](#table-of-contents)**

### Utilisez le même vocabulaire pour le même type de variable

**Mauvais:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Bon:**

```javascript
getUser();
```

**[⬆ back to top](#table-of-contents)**

### Utilisez des noms interrogeables

Nous lirons plus de code que nous n'écrirons jamais. Il est important que le code que nous
do write est lisible et consultable. En _pas_ nommant les variables qui finissent
étant utile pour comprendre notre programme, nous blessons nos lecteurs.
Rendez vos noms consultables. Des outils comme
[buddy.js] (https://github.com/danielstjules/buddy.js) et
[ESLint] (https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
peut aider à identifier les constantes sans nom.

**Mauvais:**

```javascript
// What the heck is 86400000 for?
setTimeout(blastOff, 86400000);
```

**Bon:**

```javascript
// Declare them as capitalized named constants.
const MILLISECONDS_IN_A_DAY = 86_400_000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);
```

**[⬆ back to top](#table-of-contents)**

### Utilisez des variables explicatives

**Mauvais:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**Bon:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

**[⬆ back to top](#table-of-contents)**

### Évitez la cartographie mentale

L'explicite vaut mieux que l'implicite.

**Mauvais:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(l => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // Wait, what is `l` for again?
  dispatch(l);
});
```

**Bon:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach(location => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

**[⬆ back to top](#table-of-contents)**

### N'ajoutez pas de contexte inutil

Si le nom de votre classe / objet vous dit quelque chose, ne le répétez pas dans votre
Nom de variable.

**Mauvais:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue"
};

function paintCar(car) {
  car.carColor = "Red";
}
```

**Bon:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue"
};

function paintCar(car) {
  car.color = "Red";
}
```

**[⬆ back to top](#table-of-contents)**

### Utilisez des arguments par défaut plutôt que des courts-circuits ou des conditions

Les arguments par défaut sont souvent plus propres que les courts-circuits. Sachez que si vous
utilisez-les, votre fonction ne fournira que les valeurs par défaut pour `undefined`
arguments. Autres valeurs "undefined" telles que "" "," "", "false", "null", "0" et
`NaN`, ne sera pas remplacé par une valeur par défaut.

**Mauvais:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**Bon:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **Functions**

### Arguments de fonction (2 ou moins idéalement)

Limiter la quantité de paramètres de fonction est extrêmement important car il
facilite le test de votre fonction. Avoir plus de trois conduit à un
explosion combinatoire où vous devez tester des tonnes de cas différents avec
chaque argument distinct.

Un ou deux arguments est le cas idéal, et trois devraient être évités si possible.
Rien de plus que cela devrait être consolidé. Habituellement, si vous avez
plus de deux arguments alors votre fonction essaie d'en faire trop. Dans les cas
là où ce n'est pas le cas, la plupart du temps, un objet de plus haut niveau suffira comme
argument.

Puisque JavaScript vous permet de créer des objets à la volée, sans beaucoup de classe
passe-partout, vous pouvez utiliser un objet si vous avez besoin d'un
beaucoup d'arguments.

Pour rendre évidentes les propriétés attendues par la fonction, vous pouvez utiliser l'ES2015 / ES6
syntaxe de déstructuration. Cela présente quelques avantages:

1. Lorsque quelqu'un regarde la signature de la fonction, il est immédiatement clair
   propriétés sont utilisées.
2. Il peut être utilisé pour simuler des paramètres nommés.
3. La destruction clone également les valeurs primitives spécifiées de l'argument
   objet passé dans la fonction. Cela peut aider à prévenir les effets secondaires. Remarque:
   les objets et tableaux qui sont déstructurés à partir de l'objet argument ne sont PAS
   cloné.
4. Les linters peuvent vous avertir des propriétés inutilisées, ce qui serait impossible
   sans déstructuration.

**Mauvais:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);

```

**Bon:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true
});
```

**[⬆ back to top](#table-of-contents)**

### Les fonctions doivent faire une chose

C'est de loin la règle la plus importante en génie logiciel. Quand fonctionne
faire plus d'une chose, ils sont plus difficiles à composer, à tester et à raisonner.
Lorsque vous pouvez isoler une fonction à une seule action, elle peut être refactorisée
facilement et votre code sera lu beaucoup plus proprement. Si vous ne prenez rien d'autre
ce guide autre que celui-ci, vous serez en avance sur de nombreux développeurs.

**Mauvais:**

```javascript
function emailClients(clients) {
  clients.forEach(client => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}
```

**Bon:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

**[⬆ back to top](#table-of-contents)**

### Les noms de fonction doivent dire ce qu'ils font

**Mauvais:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// It's hard to tell from the function name what is added
addToDate(date, 1);
```

**Bon:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

**[⬆ back to top](#table-of-contents)**

### Les fonctions ne doivent être qu’un niveau d’abstraction

Lorsque vous avez plus d'un niveau d'abstraction, votre fonction est généralement
en faire trop. Le fractionnement des fonctions conduit à la réutilisation et plus facile
essai.

**Mauvais:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach(token => {
    // lex...
  });

  ast.forEach(node => {
    // parse...
  });
}
```

**Bon:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

**[⬆ back to top](#table-of-contents)**

### Supprimer le code en double

Faites de votre mieux pour éviter le code en double. Le code dupliqué est Mauvais car il
signifie qu'il y a plus d'un endroit pour modifier quelque chose si vous avez besoin de changer
une certaine logique.

Imaginez que vous dirigiez un restaurant et que vous gardiez une trace de votre inventaire: tous vos
tomates, oignons, ail, épices, etc. Si vous avez plusieurs listes
vous gardez cela, alors tout doit être mis à jour lorsque vous servez un plat avec
tomates en eux. Si vous n'avez qu'une seule liste, il n'y a qu'un seul endroit pour mettre à jour!

Souvent, vous avez du code en double parce que vous en avez deux ou plus légèrement
des choses différentes, qui ont beaucoup en commun, mais leurs différences vous forcent
d'avoir deux ou plusieurs fonctions distinctes qui font à peu près les mêmes choses. Suppression
dupliquer du code signifie créer une abstraction capable de gérer cet ensemble de
différentes choses avec une seule fonction / module / classe.

Obtenir la bonne abstraction est essentiel, c'est pourquoi vous devez suivre
Principes SOLID exposés dans la section _Classes_. Les abstractions mauvaises peuvent être
pire que du code en double, alors soyez prudent! Cela dit, si vous pouvez faire
une bonne abstraction, faites-le! Ne te répète pas, sinon tu te retrouveras
mettre à jour plusieurs endroits à chaque fois que vous souhaitez modifier une chose.

**Mauvais:**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}
```

**Bon:**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

**[⬆ back to top](#table-of-contents)**

### Définissez les objets par défaut avec Object.assign

**Mauvais:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Bon:**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

**[⬆ back to top](#table-of-contents)**

### N'utilisez pas d'indicateurs comme paramètres de fonction

Les drapeaux indiquent à votre utilisateur que cette fonction fait plus d'une chose. Les fonctions doivent faire une chose. Divisez vos fonctions si elles suivent des chemins de code différents basés sur un booléen.

**Mauvais:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Bon:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

**[⬆ back to top](#table-of-contents)**

### Évitez les effets secondaires (partie 1)

Une fonction produit un effet secondaire si elle fait autre chose que prendre une valeur dans
et renvoyer une ou plusieurs autres valeurs. Un effet secondaire pourrait être l'écriture dans un fichier,
modifier une variable globale ou câbler accidentellement tout votre argent à un
étranger.

Maintenant, vous devez avoir des effets secondaires dans un programme à l'occasion. Comme le précédent
Par exemple, vous devrez peut-être écrire dans un fichier. Ce que tu veux faire, c'est
centraliser où vous faites cela. N'a pas plusieurs fonctions et classes
qui écrivent dans un fichier particulier. Ayez un service qui le fait. Seul et l'unique.

Le point principal est d'éviter les pièges courants comme le partage de l'état entre les objets
sans aucune structure, en utilisant des types de données mutables qui peuvent être écrits par n'importe quoi,
et ne pas centraliser là où vos effets secondaires se produisent. Si vous pouvez faire cela, vous
être plus heureux que la grande majorité des autres programmeurs.

**Mauvais:**

```javascript
// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Bon:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

**[⬆ back to top](#table-of-contents)**

### Évitez les effets secondaires (partie 2)

En JavaScript, les primitives sont passées par valeur et les objets / tableaux sont passés par
référence. Dans le cas des objets et des tableaux, si votre fonction fait un changement
dans un tableau de panier, par exemple, en ajoutant un article à acheter,
alors toute autre fonction qui utilise ce tableau `cart` sera affectée par cela
une addition. Cela peut être génial, mais cela peut aussi être Mauvais. Imaginons un Mauvais
situation:

L'utilisateur clique sur le bouton "Acheter" qui appelle une fonction "achat" qui
génère une requête réseau et envoie le tableau `cart` au serveur. Car
d'une connexion réseau Mauvais, la fonction `achat` doit réessayer
demande. Maintenant, que se passe-t-il si entre-temps l'utilisateur clique accidentellement sur "Ajouter au panier"
bouton sur un élément dont ils ne veulent pas réellement avant le début de la demande réseau?
Si cela se produit et que la demande réseau commence, cette fonction d'achat
enverra l'article ajouté accidentellement car il a une référence à un achat
tableau de panier que la fonction `addItemToCart` a modifié en ajoutant un
article.

Une excellente solution serait pour le `addItemToCart` de toujours cloner le` panier`,
modifiez-le et renvoyez le clone. Cela garantit qu'aucune autre fonction
le maintien d'une référence du panier sera affecté par tout changement.

Deux mises en garde à mentionner à cette approche:

1. Il peut y avoir des cas où vous souhaitez réellement modifier l'objet d'entrée,
   mais lorsque vous adoptez cette pratique de programmation, vous constaterez que ces cas
   sont assez rares. La plupart des choses peuvent être remodelées pour ne pas avoir d'effets secondaires!

2. Le clonage de gros objets peut être très coûteux en termes de performances. Heureusement,
   ce n'est pas un gros problème en pratique car il y a
   [grandes bibliothèques] (https://facebook.github.io/immutable-js/) qui permettent
   ce type d'approche de programmation doit être rapide et pas aussi gourmand en mémoire que
   ce serait à vous de cloner manuellement des objets et des tableaux.

**Mauvais:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Bon:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

**[⬆ back to top](#table-of-contents)**

### N'écrivez pas dans les fonctions globales

Polluer les globaux est une pratique Mauvais en JavaScript car vous pourriez entrer en conflit avec un autre
bibliothèque et l'utilisateur de votre API ne serait pas plus sage jusqu'à ce qu'ils obtiennent un
exception en production. Pensons à un exemple: et si vous vouliez
étendre la méthode native Array de JavaScript pour avoir une méthode `diff` qui pourrait
montrer la différence entre deux tableaux? Vous pourriez écrire votre nouvelle fonction
au `Array.prototype`, mais il pourrait entrer en conflit avec une autre bibliothèque qui a essayé
faire la même chose. Et si cette autre bibliothèque utilisait simplement `diff` pour trouver
la différence entre le premier et le dernier élément d'un tableau? C'est pourquoi il
serait bien préférable d'utiliser simplement les classes ES2015 / ES6 et d'étendre simplement le `Array` global.

**Mauvais:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Bon:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Privilégiez la programmation fonctionnelle par rapport à la programmation impérative

JavaScript n'est pas un langage fonctionnel comme Haskell, mais il a
une saveur fonctionnelle. Les langages fonctionnels peuvent être plus propres et plus faciles à tester.
Privilégiez ce style de programmation lorsque vous le pouvez.

**Mauvais:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Bon:**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

**[⬆ back to top](#table-of-contents)**

### Encapsuler les conditions

**Mauvais:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**Bon:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Évitez les conditions négatives

**Mauvais:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Bon:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Évitez les conditions

Cela semble être une tâche impossible. En entendant cela pour la première fois, la plupart des gens disent:
"Comment suis-je censé faire quoi que ce soit sans une déclaration` if`? " La réponse est que
vous pouvez utiliser le polymorphisme pour accomplir la même tâche dans de nombreux cas. La deuxième
La question est généralement, "eh bien c'est génial mais pourquoi voudrais-je faire ça?" le
La réponse est un concept de code propre que nous avons appris précédemment: une fonction ne doit faire que
une chose. Lorsque vous avez des classes et des fonctions qui ont des instructions `if`, vous
indiquent à votre utilisateur que votre fonction fait plus d'une chose. Rappelles toi,
faites juste une chose.

**Mauvais:**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Bon:**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Évitez la vérification de type (partie 1)

JavaScript n'est pas typé, ce qui signifie que vos fonctions peuvent accepter n'importe quel type d'argument.
Parfois vous êtes mordu par cette liberté et cela devient tentant de faire
vérification de type dans vos fonctions. Il existe de nombreuses façons d'éviter d'avoir à faire cela.
La première chose à considérer est la cohérence des API.

**Mauvais:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**Bon:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

**[⬆ back to top](#table-of-contents)**

### Évitez la vérification de type (partie 2)

Si vous travaillez avec des valeurs primitives de base telles que des chaînes et des entiers,
et vous ne pouvez pas utiliser le polymorphisme mais vous ressentez toujours le besoin de vérifier le type,
vous devriez envisager d'utiliser TypeScript. C'est une excellente alternative à la normale
JavaScript, car il vous fournit une saisie statique en plus du JavaScript standard
syntaxe. Le problème avec la vérification manuelle du code JavaScript normal est que
le faire bien nécessite tellement de verbiage supplémentaire que le faux «type-safety» que vous obtenez
ne compense pas la lisibilité perdue. Gardez votre JavaScript propre, écrivez
Bon tests, et avoir des critiques de code Bon. Sinon, faites tout cela mais avec
TypeScript (qui, comme je l'ai dit, est une excellente alternative!).

**Mauvais:**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}
```

**Bon:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

**[⬆ back to top](#table-of-contents)**

### Ne pas sur-optimiser

Les navigateurs modernes font beaucoup d'optimisation sous le capot au moment de l'exécution. Beaucoup de
fois, si vous optimisez, vous perdez simplement votre temps. [Il y a Bon
ressources] (https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)
pour voir où l'optimisation fait défaut. Ciblez-les en attendant, jusqu'à
ils sont fixes s'ils peuvent l'être.

**Mauvais:**

```javascript
// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Bon:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Supprimer le code mort

Le code mort est tout aussi Mauvais qu'un code dupliqué. Il n'y a aucune raison de le garder
votre base de code. S'il n'est pas appelé, débarrassez-vous-en! Ce sera toujours en sécurité
dans votre historique des versions si vous en avez toujours besoin.

**Mauvais:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**Bon:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**[⬆ back to top](#table-of-contents)**

## **Objects and Data Structures**

### Utilisez des getters et des setters

Utiliser des getters et des setters pour accéder aux données sur les objets pourrait être mieux que simplement
recherche d'une propriété sur un objet. "Pourquoi?" vous pourriez demander. Eh bien, voici un
liste non organisée de raisons pour lesquelles:

- Lorsque vous voulez faire plus que l'obtention d'une propriété d'objet, vous n'avez pas
  pour rechercher et modifier chaque accesseur de votre base de code.
- Simplifie l'ajout de la validation lors d'un `set`.
- Encapsule la représentation interne.
- Facile à ajouter la journalisation et la gestion des erreurs lors de l'obtention et du réglage.
- Vous pouvez charger paresseusement les propriétés de votre objet, disons l'obtenir à partir d'un
  serveur.

**Mauvais:**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Bon:**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);
```

**[⬆ back to top](#table-of-contents)**

### Faire en sorte que les objets aient des membres privés

Ceci peut être accompli par des fermetures (pour ES5 et inférieurs).

**Mauvais:**

```javascript
const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Bon:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```

**[⬆ back to top](#table-of-contents)**

## **Classes**

### Préférez les classes ES2015 / ES6 aux fonctions simples ES5

Il est très difficile d'obtenir un héritage de classe, une construction et une méthode lisibles
définitions des classes ES5 classiques. Si vous avez besoin d'héritage (et soyez conscient
que vous pourriez ne pas), alors préférez les classes ES2015 / ES6. Cependant, préférez les petites fonctions aux
classes jusqu'à ce que vous ayez besoin d'objets plus grands et plus complexes.

**Mauvais:**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Bon:**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Utiliser le chaînage de méthodes

Ce modèle est très utile en JavaScript et vous le voyez dans de nombreuses bibliothèques telles que
comme jQuery et Lodash. Cela permet à votre code d'être expressif et moins verbeux.
Pour cette raison, je dis, utilisez le chaînage de méthodes et regardez comment votre code est propre
sera. Dans vos fonctions de classe, renvoyez simplement `this` à la fin de chaque fonction,
et vous pouvez y enchaîner d'autres méthodes de classe.

**Mauvais:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();
```

**Bon:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

**[⬆ back to top](#table-of-contents)**

### Préférez la composition à l'héritage

Comme indiqué dans [_Design Patterns_] (https://en.wikipedia.org/wiki/Design_Patterns) par le Gang of Four,
vous devriez préférer la composition à l'héritage là où vous le pouvez. Il y a beaucoup de
De bonnes raisons d'utiliser l'héritage et de nombreuses bonnes raisons d'utiliser la composition.
Le point principal de cette maxime est que si votre esprit va instinctivement
héritage, essayez de penser si la composition pourrait mieux modéliser votre problème. Dans certaines
cas, il peut.

Vous vous demandez peut-être alors "quand dois-je utiliser l'héritage?" Il
dépend de votre problème, mais c'est une liste décente de quand l'héritage
a plus de sens que la composition:

1. Votre héritage représente une relation «est-un» et non un «a-a»
   relation (Humain-> Animal vs Utilisateur-> UserDetails).
2. Vous pouvez réutiliser le code des classes de base (les humains peuvent se déplacer comme tous les animaux).
3. Vous souhaitez apporter des modifications globales aux classes dérivées en modifiant une classe de base.
   (Changer la dépense calorique de tous les animaux lorsqu'ils se déplacent).

**Mauvais:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Mauvais because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Bon:**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}
```

**[⬆ back to top](#table-of-contents)**

## **SOLID**

### Principe de responsabilité unique (SRP)

Comme indiqué dans Clean Code, "Il ne devrait jamais y avoir plus d'une raison pour une classe
pour changer ". Il est tentant de confectionner une classe avec beaucoup de fonctionnalités, comme
lorsque vous ne pouvez prendre qu'une seule valise sur votre vol. Le problème avec ceci est
que votre classe ne sera pas conceptuellement cohérente et que cela lui donnera de nombreuses raisons
changer. Il est important de minimiser le nombre de fois que vous devez changer de classe.
C'est important car s'il y a trop de fonctionnalités dans une classe et que vous modifiez
un morceau de celui-ci, il peut être difficile de comprendre comment cela affectera d'autres
modules dépendants dans votre base de code.

**Mauvais:**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}
```

**Bon:**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Principe ouvert / fermé (OCP)

Comme le précise Bertrand Meyer, «les entités logicielles (classes, modules, fonctions,
etc.) devrait être ouvert pour extension, mais fermé pour modification.
signifie cependant? Ce principe stipule que vous devez autoriser les utilisateurs à
ajouter de nouvelles fonctionnalités sans changer le code existant.

**Mauvais:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}
```

**Bon:**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Principe de substitution de Liskov (LSP)

C'est un terme effrayant pour un concept très simple. Il est formellement défini comme "Si S
est un sous-type de T, alors les objets de type T peuvent être remplacés par des objets de type S
(c'est-à-dire que les objets de type S peuvent remplacer des objets de type T) sans altérer aucun
des propriétés souhaitables de ce programme (exactitude, tâche exécutée,
etc.) "C'est une définition encore plus effrayante.

La meilleure explication à cela est que si vous avez une classe parent et une classe enfant,
alors la classe de base et la classe enfant peuvent être utilisées de manière interchangeable sans obtenir
résultats incorrects. Cela peut encore être déroutant, alors jetons un coup d'œil à la
exemple classique de Square-Rectangle. Mathématiquement, un carré est un rectangle, mais
si vous le modélisez en utilisant la relation "est-un" via l'héritage, vous
avoir des problèmes.

**Mauvais:**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // Mauvais: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Bon:**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```

**[⬆ back to top](#table-of-contents)**

### Principe de séparation des interfaces (FAI)

JavaScript n'a pas d'interfaces donc ce principe ne s'applique pas aussi strictement
comme d'autres. Cependant, c'est important et pertinent même avec le manque de JavaScript
système de type.

Le FAI déclare que «les clients ne devraient pas être forcés de dépendre d’interfaces qui
ils n'utilisent pas. "Les interfaces sont des contrats implicites en JavaScript en raison de
typage de canard.

Un bon exemple à regarder qui démontre ce principe en JavaScript est pour
classes qui nécessitent des objets de paramètres volumineux. Ne nécessitant pas la configuration des clients
d'énormes quantités d'options sont bénéfiques, car la plupart du temps, elles n'auront pas besoin
tous les paramètres. Les rendre facultatifs permet d'éviter d'avoir un
"grosse interface".

**Mauvais:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**Bon:**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});
```

**[⬆ back to top](#table-of-contents)**

### Principe d'inversion de dépendance (DIP)

Ce principe énonce deux choses essentielles:

1. Les modules de haut niveau ne doivent pas dépendre de modules de bas niveau. Les deux devraient
   dépendent des abstractions.
2. Les abstractions ne devraient pas dépendre des détails. Les détails doivent dépendre de
   abstractions.

Cela peut être difficile à comprendre au début, mais si vous avez travaillé avec AngularJS,
vous avez vu une implémentation de ce principe sous la forme de dépendance
Injection (DI). Bien qu'il ne s'agisse pas de concepts identiques, DIP maintient un niveau élevé
modules de connaître les détails de ses modules de bas niveau et de les configurer.
Il peut accomplir cela grâce à DI. Un énorme avantage de ceci est qu'il réduit
le couplage entre les modules. Le couplage est un modèle de développement très mauvais car
cela rend votre code difficile à refactoriser.

Comme indiqué précédemment, JavaScript n'a pas d'interfaces donc les abstractions
qui dépendent des contrats implicites. C'est-à-dire les méthodes
et les propriétés qu'un objet / classe expose à un autre objet / classe. dans le
exemple ci-dessous, le contrat implicite est que tout module de demande pour un
`InventoryTracker` aura une méthode` requestItems`.

**Mauvais:**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // Mauvais: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**Bon:**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

**[⬆ back to top](#table-of-contents)**

## **Testing**

Les tests sont plus importants que l'expédition. Si vous n'avez aucun test ou
montant insuffisant, puis chaque fois que vous expédiez le code, vous ne serez pas sûr que vous
n'a rien cassé. Décider de ce qui constitue un montant adéquat est en place
à votre équipe, mais avoir une couverture à 100% (tous les relevés et succursales) est
vous obtenez une très grande confiance et une tranquillité d'esprit du développeur. Cela signifie que
en plus d'avoir un excellent cadre de test, vous devez également utiliser un
[Bon outil de couverture] (https://gotwarlost.github.io/istanbul/).

Il n'y a aucune excuse pour ne pas écrire de tests. Il existe [beaucoup de frameworks de test Bon JS] (https://jstherightway.org/#testing-tools), alors trouvez-en un que votre équipe préfère.
Lorsque vous en trouvez un qui fonctionne pour votre équipe, essayez de toujours écrire des tests
pour chaque nouvelle fonctionnalité / module que vous introduisez. Si votre méthode préférée est
Test Driven Development (TDD), c'est génial, mais l'essentiel est de simplement
assurez-vous d'atteindre vos objectifs de couverture avant de lancer une fonctionnalité,
ou refactoriser un existant.

### Concept unique par test

**Mauvais:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**Bon:**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});
```

**[⬆ back to top](#table-of-contents)**

## **Concurrency**

### Utilisez des promesses, pas des rappels

Les rappels ne sont pas propres et provoquent une imbrication excessive. Avec ES2015 / ES6,
Les promesses sont un type global intégré. Utilise les!

**Mauvais:**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);
```

**Bon:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**[⬆ back to top](#table-of-contents)**

### Async / Await sont encore plus propres que les promesses

Les promesses sont une alternative très propre aux rappels, mais ES2017 / ES8 apporte async et attend
qui offrent une solution encore plus propre. Tout ce dont vous avez besoin est une fonction préfixée
dans un mot-clé `async`, et alors vous pouvez écrire votre logique impérativement sans
une chaîne de fonctions «alors». Utilisez ceci si vous pouvez profiter des fonctionnalités ES2017 / ES8
aujourd'hui!

**Mauvais:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });
```

**Bon:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()
```

**[⬆ back to top](#table-of-contents)**

## **Error Handling**

Les erreurs lancées sont une bonne chose! Ils signifient que le runtime a réussi
identifié lorsque quelque chose dans votre programme a mal tourné et qu'il laisse
vous savez en arrêtant l'exécution de la fonction sur la pile actuelle, tuant le
processus (dans Node), et vous notifiant dans la console avec une trace de pile.

### N'ignorez pas les erreurs détectées

Ne rien faire avec une erreur détectée ne vous donne pas la possibilité de réparer
ou réagir à cette erreur. Journalisation de l'erreur sur la console (`console.log`)
n'est pas beaucoup mieux car souvent il peut se perdre dans une mer de choses imprimées
à la console. Si vous enveloppez un morceau de code dans un `try / catch`, cela signifie que vous
pensez qu'une erreur peut s'y produire et que vous devriez donc avoir un plan,
ou créez un chemin de code, pour quand cela se produit.

**Mauvais:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Bon:**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}
```

### N'ignorez pas les promesses rejetées

Pour la même raison, vous ne devriez pas ignorer les erreurs détectées
à partir de `try / catch`.

**Mauvais:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    console.log(error);
  });
```

**Bon:**

```javascript
getdata()
  .then(data => {
    functionThatMightThrow(data);
  })
  .catch(error => {
    // One option (more noisy than console.log):
    console.error(error);
    // Another option:
    notifyUserOfError(error);
    // Another option:
    reportErrorToService(error);
    // OR do all three!
  });
```

**[⬆ back to top](#table-of-contents)**

## **Formatting**

Le formatage est subjectif. Comme beaucoup de règles ici, il n'y a pas de dur et rapide
règle que vous devez suivre. Le point principal est de NE PAS ARGUER sur le formatage.
Il existe [des tonnes d'outils] (https://standardjs.com/rules.html) pour automatiser cela.
Utilisez-en un! C'est une perte de temps et d'argent pour les ingénieurs de se disputer sur le formatage.

Pour les choses qui ne relèvent pas de la mise en forme automatique
(indentation, tabulations vs espaces, guillemets doubles vs simples, etc.) regardez ici
pour quelques conseils.

### Utilisez une capitalisation cohérente


JavaScript n'est pas typé, donc la capitalisation vous en dit long sur vos variables,
fonctions, etc. Ces règles sont subjectives, votre équipe peut donc choisir
Ils veulent. Le fait est que, peu importe ce que vous choisissez tous, soyez cohérent.

**Mauvais:**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Bon:**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```

**[⬆ back to top](#table-of-contents)**

### Les appelants et les appelants de fonction doivent être proches

Si une fonction en appelle une autre, gardez ces fonctions verticalement proches dans la source
fichier. Idéalement, gardez l'appelant juste au-dessus de l'appelé. Nous avons tendance à lire le code
de haut en bas, comme un journal. Pour cette raison, faites en sorte que votre code soit lu de cette façon.

**Mauvais:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**Bon:**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```

**[⬆ back to top](#table-of-contents)**

## **Comments**

### Ne commentez que les choses qui ont une complexité de logique métier.

Les commentaires sont des excuses, pas une exigence. Bon code se documente presque tout seul.

**Mauvais:**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Bon:**

```javascript
function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**[⬆ back to top](#table-of-contents)**

### Ne laissez pas de code commenté dans votre base de code

Le contrôle de version existe pour une raison. Laissez l'ancien code dans votre historique.

**Mauvais:**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Bon:**

```javascript
doStuff();
```

**[⬆ back to top](#table-of-contents)**

### Je n'ai pas de commentaires dans le journal

N'oubliez pas, utilisez le contrôle de version! Il n'y a pas besoin de code mort, de code commenté,
et surtout les commentaires de journaux. Utilisez `git log` pour obtenir l'historique!

**Mauvais:**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}
```

**Bon:**

```javascript
function combine(a, b) {
  return a + b;
}
```

**[⬆ back to top](#table-of-contents)**

### Évitez les marqueurs de position

Ils ajoutent généralement du bruit. Laissez les fonctions et les noms de variables avec le
une indentation et une mise en forme appropriées donnent la structure visuelle de votre code.

**Mauvais:**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Bon:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};
```

**[⬆ back to top](#table-of-contents)**

## Translation

This is also available in other languages:

- ![am](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Armenia.png) **Armenian**: [hanumanum/clean-code-javascript/](https://github.com/hanumanum/clean-code-javascript)
- ![bd](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Bangladesh.png) **Bangla(বাংলা)**: [InsomniacSabbir/clean-code-javascript/](https://github.com/InsomniacSabbir/clean-code-javascript/)
- ![br](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Brazil.png) **Brazilian Portuguese**: [fesnt/clean-code-javascript](https://github.com/fesnt/clean-code-javascript)
- ![cn](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/China.png) **Simplified Chinese**:
  - [alivebao/clean-code-js](https://github.com/alivebao/clean-code-js)
  - [beginor/clean-code-javascript](https://github.com/beginor/clean-code-javascript)
- ![tw](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Taiwan.png) **Traditional Chinese**: [AllJointTW/clean-code-javascript](https://github.com/AllJointTW/clean-code-javascript)
- ![fr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/France.png) **French**: [GavBaros/clean-code-javascript-fr](https://github.com/GavBaros/clean-code-javascript-fr)
- ![de](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Germany.png) **German**: [marcbruederlin/clean-code-javascript](https://github.com/marcbruederlin/clean-code-javascript)
- ![id](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Indonesia.png) **Indonesia**: [andirkh/clean-code-javascript/](https://github.com/andirkh/clean-code-javascript/)
- ![it](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Italy.png) **Italian**: [frappacchio/clean-code-javascript/](https://github.com/frappacchio/clean-code-javascript/)
- ![ja](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Japan.png) **Japanese**: [mitsuruog/clean-code-javascript/](https://github.com/mitsuruog/clean-code-javascript/)
- ![kr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/South-Korea.png) **Korean**: [qkraudghgh/clean-code-javascript-ko](https://github.com/qkraudghgh/clean-code-javascript-ko)
- ![pl](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Poland.png) **Polish**: [greg-dev/clean-code-javascript-pl](https://github.com/greg-dev/clean-code-javascript-pl)
- ![ru](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Russia.png) **Russian**:
  - [BoryaMogila/clean-code-javascript-ru/](https://github.com/BoryaMogila/clean-code-javascript-ru/)
  - [maksugr/clean-code-javascript](https://github.com/maksugr/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Spain.png) **Spanish**: [tureey/clean-code-javascript](https://github.com/tureey/clean-code-javascript)
- ![es](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Uruguay.png) **Spanish**: [andersontr15/clean-code-javascript](https://github.com/andersontr15/clean-code-javascript-es)
- ![tr](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Turkey.png) **Turkish**: [bsonmez/clean-code-javascript](https://github.com/bsonmez/clean-code-javascript/tree/turkish-translation)
- ![ua](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Ukraine.png) **Ukrainian**: [mindfr1k/clean-code-javascript-ua](https://github.com/mindfr1k/clean-code-javascript-ua)
- ![vi](https://raw.githubusercontent.com/gosquared/flags/master/flags/flags/shiny/24/Vietnam.png) **Vietnamese**: [hienvd/clean-code-javascript/](https://github.com/hienvd/clean-code-javascript/)

**[⬆ back to top](#table-of-contents)**
