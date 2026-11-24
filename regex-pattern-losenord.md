# Regex och `pattern`-attributet i HTML

Mål: förstå hur man validerar ett lösenord direkt i HTML, utan JavaScript.

```
^(?=.*[A-Za-z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}$
```

---

## 1. Vad är `pattern`?

`pattern` är ett attribut på `<input>` som innehåller ett reguljärt uttryck (regex). Webbläsaren kontrollerar att värdet matchar mönstret innan formuläret skickas.

```html
<form>
  <label for="losen">Lösenord</label>
  <input
    type="password"
    id="losen"
    name="losen"
    required
    pattern="(?=.*[A-Za-z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}"
    title="Minst 8 tecken, med minst en bokstav, en siffra och ett specialtecken (!@#$%^&*)"
  >
  <button>Skicka</button>
</form>
```

### Regler att komma ihåg

| Regel | Förklaring |
|---|---|
| Inga snedstreck | Skriv `\d+`, inte `/\d+/`. |
| Inga flaggor | Man kan inte skriva `/i` eller `/g`. |
| Matchar hela värdet | Webbläsaren lägger automatiskt till `^` och `$` runt mönstret. Man behöver alltså inte skriva dem. |
| Backslash skrivs som vanligt | `\d` fungerar rakt av i attributet. Ingen dubbel backslash som i JavaScript-strängar. |
| Tomt fält godkänns | Är fältet tomt och saknar `required` räknas det som giltigt. |
| `title` förklarar kravet | Texten visas i felmeddelandet. Skriv alltid en. |
| Vilka `type`? | `text`, `search`, `url`, `tel`, `email` och `password`. |

---

## 2. Grunderna i regex

### Teckenklasser

| Uttryck | Betyder |
|---|---|
| `.` | Vilket tecken som helst (utom radbrytning) |
| `\d` | En siffra, 0-9 |
| `\w` | Ett "ordtecken": `a-z`, `A-Z`, `0-9` och `_` |
| `\s` | Mellanslag, tab, radbrytning |
| `[abc]` | Ett av tecknen a, b eller c |
| `[a-z]` | Ett tecken i intervallet a till z |
| `[^abc]` | Ett tecken som *inte* är a, b eller c |

### Kvantifierare (hur många?)

| Uttryck | Betyder |
|---|---|
| `*` | 0 eller fler |
| `+` | 1 eller fler |
| `?` | 0 eller 1 |
| `{8}` | Exakt 8 |
| `{8,}` | 8 eller fler |
| `{8,16}` | Mellan 8 och 16 |

### Ankare

| Uttryck | Betyder |
|---|---|
| `^` | Början på strängen |
| `$` | Slutet på strängen |

---

## 3. Fällan med `[A-z]`

Tecken i ett intervall räknas efter sin ASCII-kod:

| Intervall | Koder |
|---|---|
| `[A-Z]` | 65-90 |
| `[a-z]` | 97-122 |
| `[0-9]` | 48-57 |

Mellan `Z` (90) och `a` (97) ligger sex andra tecken:

| Kod | Tecken |
|---|---|
| 91 | `[` |
| 92 | `\` |
| 93 | `]` |
| 94 | `^` |
| 95 | `_` |
| 96 | `` ` `` |

Därför matchar `[A-z]` mer än bara bokstäver. Skriv alltid **`[A-Za-z]`**.

> `[a-Z]` är ett ogiltigt intervall, eftersom `a` (97) ligger efter `Z` (90).

---

## 4. Lookahead: nyckeln till lösenordsregler

En **positiv lookahead** `(?=...)` kollar framåt i strängen utan att flytta positionen och utan att "äta upp" några tecken.

```
(?=.*\d)
```

Läses: "någonstans framåt i strängen måste det finnas en siffra".

Eftersom ingen lookahead flyttar positionen kan man ställa flera krav efter varandra, och alla testas från samma startpunkt. Ordningen spelar ingen roll.

---

## 5. Bygg lösenordsmönstret steg för steg

| Steg | Del | Krav |
|---|---|---|
| 1 | `(?=.*[A-Za-z])` | Minst en bokstav |
| 2 | `(?=.*\d)` | Minst en siffra |
| 3 | `(?=.*[!@#$%^&*])` | Minst ett specialtecken |
| 4 | `.{8,}` | Minst 8 tecken totalt |

Sätts ihop till:

```
(?=.*[A-Za-z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}
```

I ett vanligt regex (t.ex. i JavaScript) skriver man dessutom `^` först och `$` sist. I `pattern`-attributet behövs de inte.

### Testexempel

| Lösenord | Resultat | Orsak |
|---|---|---|
| `Hej123!x` | Giltigt | Uppfyller alla krav |
| `Hej123!` | Ogiltigt | Bara 7 tecken |
| `Lösenord1` | Ogiltigt | Saknar specialtecken |
| `12345678!` | Ogiltigt | Saknar bokstav |

---

## 6. Vanliga variationer

**Både stor och liten bokstav:**

```
(?=.*[a-z])(?=.*[A-Z])(?=.*\d).{8,}
```

**Svenska tecken räknas som bokstäver:**

```
(?=.*[A-Za-zÅÄÖåäö])(?=.*\d).{8,}
```

**Begränsa längden till 8-32 tecken:**

```
(?=.*[A-Za-z])(?=.*\d).{8,32}
```

---

## 7. Att tänka på

- **Modern webbläsare och `v`-flaggan:** Moderna webbläsare tolkar `pattern` med flaggan `v`. Inuti `[...]` måste då tecknen `( ) [ ] { } / - \ |` skrivas med backslash. Listan `[!@#$%^&*]` fungerar utan ändringar.
- **Visa felmeddelandet snyggt:** Med CSS-pseudoklasserna `:valid` och `:invalid` kan du ge användaren visuell feedback.
- **Validera alltid på servern också.** Valideringen i HTML kan kringgås och är bara till för användarens skull.
- **Längd före komplexitet:** Aktuell vägledning (t.ex. NIST) förordar långa lösenord framför krav på många olika teckentyper.

---

## 8. Övningar

1. Skriv ett `pattern` för ett svenskt postnummer, t.ex. `371 30` eller `37130`.
2. Skriv ett `pattern` för ett användarnamn med 3-16 tecken som bara får innehålla `\w`.
3. Utöka lösenordsmönstret så att det kräver både stor och liten bokstav.
4. Förklara varför `[A-z]` är ett dåligt val.

<details>
<summary>Förslag på svar</summary>

1. `\d{3} ?\d{2}`
2. `\w{3,16}`
3. `(?=.*[a-z])(?=.*[A-Z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}`
4. Intervallet 65-122 innehåller även `[ \ ] ^ _ `` ` ``.

</details>
