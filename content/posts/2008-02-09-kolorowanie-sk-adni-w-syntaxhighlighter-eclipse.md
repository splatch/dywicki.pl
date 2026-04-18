---
author: splatch
category:
  - inne
  - javascript
date: "2008-02-09T19:46:43+00:00"
guid: http://blog.dywicki.pl/2008/02/09/kolorowanie-skladni-w-syntaxhighlighter-eclipse/
summary: Jakiś czas temu zmieniłem narzędzie do kolorowania składni z [GeSHI](http://qbnz.com/highlighter/) na [SyntaxHighlighter](http://code.google.com/p/syntaxhighlighter/). Największa różnica to wykonanie. Starsza biblioteka parsuje wszystko po stronie serwera i pakuje to w HTML, nowsza jest uruchamiana z poziomu przeglądarki i kolorowanie odbywa się tuż po załadowaniu strony.
title: Kolorowanie składni w SyntaxHighlighter - Eclipse.
url: /2008/02/09/kolorowanie-skladni-w-syntaxhighlighter-eclipse/

---
Jakiś czas temu zmieniłem narzędzie do kolorowania składni z [GeSHI](http://qbnz.com/highlighter/) na [SyntaxHighlighter](http://code.google.com/p/syntaxhighlighter/). Największa różnica to wykonanie. Starsza biblioteka parsuje wszystko po stronie serwera i pakuje to w HTML, nowsza jest uruchamiana z poziomu przeglądarki i kolorowanie odbywa się tuż po załadowaniu strony.

Oczywiście nie był bym sobą gdybym nie zmienił kolorowania składni na bardziej przyjazne, co oczywiście można zaobserwować we wcześniejszych notach. :)
Dla zainteresowanych załączam - zmienione pliki. Zmodyfikowałem czcionkę z wygładzanej, na szorstkiego "Courier New", jak i kolorowanie słów kluczowych zarówno w Javie jak i w XML.

Instalacja - bajecznie prosta - skopiować pliki oraz zmienić nazwy na "garbate" (shBrushXml.js, shBrushJava.js). Całość bez problemów współpracuje z [pluginem do Wordpressa](http://wordpress.org/extend/plugins/syntaxhighlighter/).

[Klorowanie XML](/wp-content/uploads/2008/02/shbrushxml.js "Klorowanie XML") [Kolorowanie Javy](/wp-content/uploads/2008/02/shbrushjava.js "Kolorowanie Javy") [Arkusz CSS](/wp-content/uploads/2008/02/syntaxhighlighter.css "Arkusz CSS")
