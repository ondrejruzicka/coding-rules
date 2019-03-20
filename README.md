# Pravidla pro psaní frontend kódu
Aneb co by kodéři měli dodržovat. Obsahuje hlavně pravidla pro práci se styly.

 Tato pravidla nejsou dogmatem, ale pokud chceš něco udělat jinak než je domluveno, dobře to promysli, zda to za to stojí (jsou situace, kdy ano), případně to s někým prober. Nápady na zlepšení jsou vítány.

# HTML
- Odsazení zanořených prvků pomocí dvou mezer (ne tabulátoru)
- Atributy tagu oblopujeme dvojitýmí uvozovkami (_př._ `<div class="article">`)
- U nepárových tagů nepoužíváme zavírací lomítko. (Dobře: `<br>`, špatně: `<br />`)
- Používáme sémantické HTML5 tagy (rozumně). _Př._ `<header> <section> <nav> <article>`...

# CSS
Pro psaní css využíváme preprocesor SASS v syntaxi SCSS.

Snažíme se dodržovat tato obecná pravidla
- Každý selektor v deklaraci patří na vlastní řádku.
- Mezi deklaracemi je vždy jeden volný řádek.
- Za čárkami v hodnotách bude vždy mezera.
- Jednotlivé deklarace a zavináčová pravidla oddělujeme v kódu volným řádkem.
- Nejsou povoleny vendor prefixy (na to používáme autoprefixer v gulpu)
- Nejsou povoleny id selektory (např. #container).


## SASS
- Pokud to není nezbytně nutné nebo to výrazně nepomáhá čitelnosti kódu, netvoříme proprietární funkce. CSS umí číst všichni, na rozdíl od specifických konstrukcí. V deklarativní části píšeme jednoduchý kód co nejvíce podobný běžnému CSS.
- Př.
```scss
// Dobre:
.box {
  font-size: 1.25rem;
  line-height: 1.4;
}

// Spatne:
.box {
  @include fs-lh(1.25rem, 1.4);  // Mixin neznameho obsahu
}

```

### Proměnné
- definujeme v souboru `core/_variables.scss`
- název proměnné by měl být logický a čitelný i pro ostatní
- barvy píšeme do proměnné `$color--red` nebo `$color--primary`


## Zanořování

- Nezanořujeme do vyšší než druhé úrovně.
- Povoleno je jen zanoření pro:
    - pseudotřídy (`:hover`, `:focus`...)
    - stavové třídy (`.is-active`)
    - stavové třídy rodiče (`.article:hover & {...}`)
    - Media Queries
- Ampersandové (&__element) zanoření v selektorech pro vyšší než druhou úroveň nepoužíváme. Komplikuje nalezení správného selektoru a kodér ztrácí jistotu nad názvem a specificitou selektoru, který vytváří.
- Vyhýbáme se dlouhým zanořeným deklaracím – monolitům.
    ```scss
    // Spatne:

    .box {
    border: 1px solid $color--border;

      &__heading {
          font-size: 6rem;

        @media(max-width: $screen-md) {
          &--large {
                  font-size: 4rem;
          }
        }
      }
    }

    // Dobre:

    .box {
      border: 1px solid $color--border;
    }

    .box__heading {
      font-size: 6rem;
    }

    .box__heading--large {
      @media(max-width: $screen-md) {
        font-size: 4rem;
      }
    }
    ```

---

## Pomocné nástroje pro psaní požadovaného kódu
TODO: dodělat podrobné nastavení nástrojů

- Prittier
- Stylelink
- Editorconfig


---


## Obecné třídy
V projektu se snažíme hledat společné prvky napříč stánkami ale i komponentami na jedné stránce. Ušetříme si tak čas a nemusíme vše strkat do BEMu.

### Pravidla
- Identifikujeme elemrntární prvky. Nejčastěji je se jedná o jeden HTML tag.
- Píšeme tak, aby byl vzhled nezívilý na kontextu (zanoření v ostatních prvcích).

### Příkady:
__Typy popisů (mimo obecné nadpisy h1, h2...):__
- Příklady tříd `.note`, `sectionTitle`
- Deklarace umístíme do souboru `/core/_typography.scss` _(viz __Struktura složek a souborů__)_

__Odkazy:__
- Příklad: odkaz se šipkou. Vyskytuje se na různých místech stránky.
![next-link](./images/next-link.png)
- pojmenuji `.nextLink`, deklaraci dám do `/modules/_nextLink.scss` _(viz __Struktura složek a souborů__)_


### Tipy
- Do obecných tříd většinou nedefinujeme odsazení od ostatních prvků, protože se může lišit v různých výskytech. Odsazení a pozici vyřešíme v rámci BEMu nebu utility tříd.
- víceslovné názvy píšeme pomocí __camelCase__.

---




## BEM
Metodika pojmenování tříd v CSS
BEM = Blok, Element, Modifikátor
Projdi si: https://www.vzhurudolu.cz/prirucka/bem

Přínosy BEMu:
- Usnadňuje čitelnost kódu.
- Na první pohled jasná struktura komponenty.
- Řeší problém specificity CSS selektorů.
- Nutí nás psát mudulární(komponentový) kód.

| Typ třídy  | Pojmenování   |
|---|---|
| Blok  | `.article`   |
| Element  | `.article__title`   |
| Modifikátor  | `.article--highlight`   |


### Pravidla
- nepoužíváme zanoření pomocí elementů - nekopírovat strukturu DOMu. CSS selektor je nezávislý na struktuře DOM
    ```html
    // Spatne:

    <div class="card">
    <div class="card__header">
        <h2 class="card__header__title">
        …
        </h2>
    </div>
    </div>

    // Dobre:

    <div class="card">
    <div class="card__header">
        <h2 class="card__title">
        …
        </h2>
    </div>
    </div>

    ```
    Proč? Zanořování elementů i v selektorech by se totiž dříve nebo později vymklo z rukou a selektory by se staly nesnesitelně dlouhými.
    Respektování struktury DOMu nemá v CSS žádné výhody.

- víceslovné názvy bloků/elementů/modifikátorů píšeme pomocí __camelCase__. Nemá to žádný význam pro hierarchii. Pouze významné pro čitelnost
  Př. `.list__subItem`

- Při psaní elementů nezanořujeme pomocí ampersandů
     ```scss
    // Tohle ne:
    .list {
        &__item { }
        &--secondary { }
    }
    ```
    ```scss
    // Raději:
    .list {}
    .list__item {}
    .list__secondary {}
    ```

- modifikátory používáme v HTML vždy společně se základní třídou
  ```html
  <!-- Dobře -->
  <table class="table table--responsive">
  ...
  </table>
  ```
  ```html
  <!-- Špatně -->
  <table class="table--responsive">
  ...
  </table>
  ```


### Tipy
- Nepojmenováváme komponenty nepochopitelnými zkratkami. My si je možná zapamatujeme, ale situaci zase zkomplikujeme ostatním.
- Modifikátor může být použit pro celý blok (`.article--simple`) nebo pro element (`.article__title--simple`). Záleží na situaci.
- BEM používáme s rozumem - tam kde se hodí. Nelpíme na tom, aby každý prvek na stránce byl součástí nějakého modulu.


---


## Stavové třídy
- Příznak změny stavu prvku/komponenty.
- jsou pak čitelnější pro nás i pro backend programátory
- pokud v scss používáme selektor rodiče, rodiče vypíšeme (viz příklad)

### Pravidla
- prefixujeme slovesy `.is-*`  `.has-*` atd.

### Příklad
```html
<nav class="menu">
    <a href="#" class="menu__item">
    <a href="#" class="menu__item">
    <a href="#" class="menu__item is-active">
</nav>
```
```scss

.menu__item {
    padding: 0 2rem;
    border-bottom: 3px solid transparent;

    &.is-active {
        border-bottom-color: $color--gold;
    }

    // selektor rodiče
    .menu.is-open & {
        display: block;
    }
}

```

### Tipy
- Někdy je těžké určit, jestli použít BEM modifikátor nebo stavovou třídu. Obecně používáme stavové třídy pro dynamické změny prvků (třeba pomocí JS). Jsou také pochopitelnější pro vývojáře.
- Naopak BEM modifikátory se snažíme používat pro neměnné stavy (př. `.article__text--last`)


---
## Třídy navázané na JS
Třídy navázané na funkčnost v JavaScriptu. Buď jsou na ně navázány event listenery nebo se s nimi bude manipulovat v budoucnu.

### Pravidla
- Prefixované `.js-*`
- Používají je i vývojáři. Nemazat, pokud nevím co dělám!

### Příklady

```html
    <a href="#" class="btn js-contact-send">
```

```js
   $('.js-contact-send').on('click', function(event) {
       ...
   });
```

---

## Utility (užitkové) třídy
Projdi si: https://www.vzhurudolu.cz/prirucka/bootstrap-4-utility

### Pravidla
- Používáme základní utility třídy z Bootstrap 4: https://getbootstrap.com/docs/4.1/utilities/
- V případě potřeby si můžeme dopsat vlastní třídy v souboru `core/_utility.scss`
- Nejčastější použití:
  - Odsazování bloků mezi sebou
  - Skrývání prvků - mobil/desktop
  - Flex
  - Zarovnání textu

### Příklady

- Spodní odsazení celého bloku od následujících ( mb-8 = margin-bottom: 8*$space;)
  proměnná $space je definovaná v Boostrap variables

    ```html
    <div class="reason mb-8">
        <svg class="reason__ico">
            <use xlink:href="#name"></use>
        </svg>
        <h2>Title</h2>
        <p class="reason__text">...</p>
    </div>
    ```

- Verikální zarovnání prvků pomocí Flexu
  ```html
  <div class="d-flex align-items-center">
      <div class="mr-5">Obsah</div>
      <div> Další obsah</div>
  </div>
  ```

### Tipy
- S užitkovými třídami to také nepřehánět, př:
`<div class="d-block d-md-flex py-3 py-6 mx-4">`
když se třídy začnou kupit snižuje se čitelnost -> zvážit přepsání do BEM modulu


# JavaScript
TODO


# Struktura složek a souborů


Soubory pro frontend jsou uloženy v složce `static/assets`, která je v rootu projektu
Obvykle obsahuje složky:
- `js` - javascripty obsluhující interakci (ty, do kterých nemusí sahat backend vývojáři)
- `sass` - soubory stylů viz níže
- `svg` - svg, které se využijí v projektu pomocí SVG symbolů (nejčastěji ikony)

## Struktura a pojmenování SASS souborů

Složka `sass` obsahuje nejčastěji soubory v těchto složkách:

### bootstrap
Pokud projekt, využívá Bootstrap, jsou zde komplet SASS zdrojová data Bootsrapu.

__Důležité:__ V souboru `bootstrap.scss` jsou zakomentovány všechny moduly, které nevužijeme. Zpravidla jsou je povolen jen reset, grid a užitkové (utility) třídy.

V souboru `_variables.scss` jsou nastaveny proběnné, aby odpovídaly projektu a týkají se bootstrap modulů, které máme vybrané. _Př. šířka sloupce, počet sloupců v gridu, velikosti mezer..._

### core
Základní nastylování prvků a definice "kostry" webu.
Nejčastěji obsahuje soubory:
- `_base.scss` - základní styly pro `<body>`, odkazy, obecké prvky jako `<hr>`, případně globání třídy pro daný projekt
- `_fonts.scss` - definice fontů použitých na webu (obsahem je jen fefinice fotnů, ne velikosti atd!)
- `_typography.scss` - styly pro nadpisy, odstavce, seznamy, ale i custom třídy (_př._ `.note`, `.section-title`...)
- `_forms.scss` - vzhled formulářů napříč webem
- `_variables.scss` - SASS proměnné - barvy, breakpointy...
- `_layout.scss` - speciality layoutu (střídání barev pozadí, šířka containeru) nemusí být využito
- `_svg-ico.scss` - styly pro opakující se SVG ikony
- `_utility.scss` - vlastní utility třídy
- `_sprites.scss` - pouze při použití PNG spritů
- `_mixins.scss`

### external
Styly knihoven třetích stran. Ve složce mohou být dále zanořovány dle potřeby. Pokud instalujeme třetí knihovny pomocí npm. Neimportujeme je z `node_modules` ale ručně vytáhneme do složky external.

### modules
Do této složky patří všechny soubory, které se týkají BEM bloků (komponent) a obecných tříd na webu. Mělo by platit co blok, to scss soubor. Jeden problém řeší jeden soubor. Vyhýbáme se souborům míchajícím více komponent.
Důvodem je:
-  __rychlá orientace v projektu__
    _Př. potřebuji změnit vzhled prvku `.article__title` automaticky jdu do souboru `.../modules/_article.scss`_
- __modulárnost__ - potřebuju použít stejný blok v jiném projektu -> jednoduše zkopíruji soubor



# Nástroje pro preprocessing
Aktuálně využíváme __gulp__ s nastavenými tasky hlavně pro:
- převod SCSS do CSS
- generování souborus SVG ikonami `defs.svg` s ikonami (ze složky `/assets/svg/defs`)
- automatický refresh stránky při vývoji
- browsersync pro vytvoření proxy a testování na mobilech
- minifikaci CSS
- spojení a minifikaci JS

Tasky jsou popsany v souboru `gulpfile.js`.

Tyto informace se často mění podle potřeby, pro aktuálnost se pobav s ostatními kodéry.


Zdroje a více informací:
- https://www.vzhurudolu.cz/prirucka/rcss-zasady
- https://www.vzhurudolu.cz/prirucka/bem
- https://cssguidelin.es/
- http://codeguide.co/
