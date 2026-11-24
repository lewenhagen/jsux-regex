# Form tagg

```html
<form>
<form action="/action">
<form method="GET, POST">
```

# Label

`<label>`

Antingen med `for` och `id` eller input innanför `label`.

(användbarhet) Gör att man kan klicka på texten, svårt att klicka på liten checkbox
(tillgänglighet) Skärmläsare talar om att man ska editera, tex "email, edit text". Utan <label> "edit text"

```html
<label for="fname">Förnamn</label>
<input id="fname" name="fname" type="text" required>


<label for="lname">Efternamn</label>
<input id="lname" name="lname" type="text" required>
<button id="myBtn" type="submit">Registrera</button>
```

```html
maxlength="5"
minlength="3"
required
```

# Regex

* Regex flavours. HTML regex är en förenklad JS Flavour.
* Fungerar på text, search, url, tel, email och password

```js
[a-zA-Z]+ // bara bokstäver
pattern="[a-z]+" // en eller fler
pattern="[a-z]*" // noll eller fler
pattern="[a-z].+" // Följs av en eller fler
pattern="[a-z].*" // Följs av noll eller fler
pattern="[a-z]*" // noll eller fler

pattern="([0-9]{3,})[a-z]{3,}"
pattern="^.*(?=[3-7]).*$"
pattern="^.*(?=\d)(?=.*[!@#$%^&*]).*$"

?= är "positive lookahead", konsumerar inga characters. Därför behöver vi .+ eller .*

``` 

* Webbläsaren lägger automatiskt till ^ och $
* använd `title="Minst 8 tecken, med minst en bokstav, en siffra och ett specialtecken (!@#$%^&*)"` 
* \d = digits
* \w = a-zA-Z0-9_
* `[a-z]` är ASCII koderna 97-122
* `[A-Z]` är ASCII koderna 65-90
  
```code
91-96 är tecken som:

[, \, ], ^, _, `
```

inte a-Z, däremellan finns andra ASCII tecken som ^ (går efter kod intervall, där finns andra tecken med, tex ASCII kod )


# password

```html
pattern="^(?=.*[A-Za-z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}$"
placeholder="Minst 8 tecken, 1 siffra, 1 specialtecken"
```


# Form with JS
```js
<script>
    let form = document.getElementById("myForm")

    form.addEventListener("submit", function(event) {
      event.preventDefault()
      
      const data = new FormData(form)
        
      console.log(data)
    })
</script>
```
