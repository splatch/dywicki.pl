---
author: splatch
category:
  - framework
  - mojavi4
  - tlumaczenia
date: "2006-05-06T00:44:08+00:00"
guid: http://blog.dywicki.pl/2006/05/06/osiagi-i-marzenia/
title: Osiągi i marzenia
url: /2006/05/06/osiagi-i-marzenia/

---
Mojavi 4 przeszło długą drogę - od [początkowych diagramów uml](http://forum.mojavi.org/index.php?showtopic=1236). Obecnie oferuje pełną oraz elastyczną konfigurację XML, modularną architekturę, komponenty, buforowanie, obsługę datasource'ów, wstrzykiwanie zależności, walidację oraz wiele, wiele innych. Wszystko to przechodzi na końcu przez lekkie [TDD (test driven design)](http://www.lastcraft.com/simple_test.php) testy sprawdzające. Wsród innych zamiarów bądź zaimplementowanych możliwości/podstawowych aspektów Mojavi 4 jest:

- [Model View Controller](http://trac.mojavi.org/wiki/Mojavi4/MVC) \- Najważniejsza część architektury, podstawa poprzednich frameworków Mojavi.
- [Multi-project support](http://trac.mojavi.org/wiki/Mojavi4/Mutliproject) \- Posiadanie różnych projektów działających na tym samym kodzie bazowym.
- [End-user controlled filters](http://trac.mojavi.org/wiki/Mojavi4/EndUserFilters) \- Zawiera konfigurowalną obsługę wyjątków.
- [Global and Forward Filters](http://trac.mojavi.org/wiki/Mojavi4/GlobalAndForwardFilters)
- [Application Controllers](http://trac.mojavi.org/wiki/Mojavi4/ApplicationControllers) \- Używane do obsługi wielu następujących po sobie stron do skomplikowanych formularzy bądź wizzardów _(kreatorów)_.
- [Sophisticated autoload support](http://trac.mojavi.org/wiki/Mojavi4/Autoloader) \- bardzo, bardzo przydatny mechanizm do automatczynego dołączania klas/interfejsów, również z Twoich własnych bibliotek!
- [Easy Validation?](http://trac.mojavi.org/wiki/Mojavi4/Validators) \- Walidacja zawsze jest trudnym elementem do wprowadzenia w aplikacji. Myślimy, że mamy za sobą pracę, która sprawia, że ten częsty i niemiły element staje się łatwy do oprogramowania.
- [Helpers](http://trac.mojavi.org/wiki/Mojavi4/Helpers) \- Pomocnicy są zbiorem szybkich bibliotek do użytku w całym frameworku (częston ich użycie oczyszcza logikę aplikacji).
- [Renderers?](http://trac.mojavi.org/wiki/Mojavi4/Renderers) \- Wspólny interfejs dla silników szablonów. Renderery umożliwiają zmiany pomiędzy silnikami w prosty sposób.
- [Logger?](http://trac.mojavi.org/wiki/Mojavi4/Logging) \- Tak jak wszystko, aplikacje w Mojavi potrzebują logowania!
- [Datastructures?](http://trac.mojavi.org/wiki/Mojavi4/Datastructures) \- Średniej wielkości zbiór klas, które dostarczają implementacji wspólnych wzorców struktór danych, takich jak stack _stos_, queue _kolejka_ lub _chain_ łańcuch.
- [Service Locator](http://trac.mojavi.org/wiki/Mojavi4/ServiceLocator) \- Wstrzykiwanie zależności jest gorącym tematem. W skrócie, domyślny Service Locator z Mojavi 4 jest stworzony na bazie wstrzykiwania zależności. Można z niego korzystać w całej aplikacji bez żadnych zobowiązań w kodzie.
- [Containers](http://trac.mojavi.org/wiki/Mojavi4/Containers) \- Wszyscy potrzebujemy kontenerów. _;)_
- [Modules?](http://trac.mojavi.org/wiki/Mojavi4/Module) \- the Module series classes provide a programmic way to interface with the various pieces of your application. _tego nie jestem w stanie przetłumaczyć._
- [Easy Layouts?](http://trac.mojavi.org/wiki/Mojavi4/Layouts) \- layouty umożliwiają stworzenie wspólnego wyglądu i wykorzystanie go na wielu stronach _w wielu widokach_ bez kopiowania kodu.
- [Internationalization](http://trac.mojavi.org/wiki/Mojavi4/i18n) \- Internationalization, otherwise known as i18n, or l10n, is a term used to refer to a process revolving around the end goal of creating an application which can be easily ported to a new audience in terms of content language and data formatting. _tego również nie jestem w stanie przetłumaczyć._

Oryginał [http://trac.mojavi.org/wiki/](http://trac.mojavi.org/wiki/)
