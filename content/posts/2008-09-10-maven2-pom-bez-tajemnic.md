---
author: splatch
category:
  - wiadomości
date: "2008-09-10T06:30:36+00:00"
draft: "true"
guid: http://blog.dywicki.pl/?p=226
title: Maven2, POM bez tajemnic
url: /

---
Maven 2, POM bez tajemnic

_Eric Redmond, swobodne tłumaczenie Łukasz Dywicki_

Budowanie projektów Javowych jest złożonym przedsięwzięciem. Dziesiątki zadań konwertujących mieszanki kodu, i dosłownie setki narzędzi stworzonych przez lata robią wszystko. Od generowania kodu przez kompilowanie po wysyłanie powiadomienia mailem o porannej przerwie na kawę (jeśli znajdziecie podobny skrypt, podeślij go mi). Naturalnym więc jest to, że większość narzędzi budujących projekty to zlepek setek ręcznie napisanych skryptów. Do teraz, ponieważ pojawiło się narzędzie znacznie bardziej ambitny, wszechstronny przyrząd służący do budowania – Maven 2. Problemem jest to, że zarządzanie pracą setek skryptów w jednym szkielecie jest trudne i wymaga bardzo wielu informacji by pracować poprawnie.

Na szczęście zespół tworzący Mavena 2, ucząc się na błędach pierwszej wersji Mavena, realizując niezliczone oczekiwania użytkowników, odniósł sukces. Maven 2 jest potężniejszy niż kiedykolwiek. Niestety z tą całą wspaniałością podąża wspaniała.. konfiguracja. Do tego aby artefakty Mavena były przenośne wszystkie te ustawienia muszą być zamknięte w jednym pliku – pom.xml. Tutaj nota dla użytkowników Mavena 1: pom.xml jest podobny do pliku project.xml.

Piszę to z założeniem, że czytelnik zna podstawy Mavena 2, czym on jest i co powinien robić, czym jest POM, plugin, repozytorium i tak dalej. Dodam, że nie jest to wprowadzenie koncentrujące się na używaniu konfiguracji poszczególnych rozszerzeń. Nie skupiamy się też nad problemami z cyklem budowania. Jakkolwiek mierzymy tutaj ponad poziom "witaj świecie", ale powinien być dosyć wszechstronny dla początkujących. Aby to udowodnić zacznijmy od pytania "Czym jest POM?".

# Czym jest POM?

POM jest skrótem od "Project Object Model" (ang. _obiektowy model projektu_). Jest to XMLowa reprezentacja projektu opartego o Mavena zapisana w pliku o nazwie pom.xml. Obecni członkowie społeczności Mavena rozmawiali o projekcie w "filozoficznym" sensie, poza zwykły zbiór plików ze źródłami. Projekt zawiera również pliki konfiguracyjne, informacje jak developerzy trafili do projektu, jakie role pełnią, kolejno informacje o tym jaki system śledzenia błędów jest używany, o samej organizacji, licencjach, adresie gdzie projekt jest prowadzony, zależnościach projektu oraz inne małe kawałki które wiążą się z życiem kodu. Jest to jedyne miejsce z informacjami dotyczącymi projektu. Faktem jest to, że w świecie Mavena projekt nie musi zawierać kodu, stąd społeczność wybrała inną, bardziej uniwersalną nazwę – artefakt.

Nie jest to oczywiście odpowiedź na wszystkie pytania związane z POMem. Pisałem tylko o pliku "pom.xml" samym w sobie pomijając wnętrze Mavena 2. Na pytania tyczące się rozwiązywanie zależności, optymalnej struktury projektu i inne odpowiedzi należy szukać na stronie projektu bądź liście dyskusyjnej.

Dla tych, którzy uczą się wzrokowo mamy diagram z sekcjami, które będą kolejno omawiane. Należy zapamiętać, że graf ten nie jest odpowiednikiem XSD POMa lecz podziałem na logiczne jednostki o których będziemy rozmawiać. Aktualny schemat XSD dla POMa znajdziesz na stronie Mavena.

![](/wp-content/uploads/2008/09/091008-0630-maven2pombe1.png)

Wszystkie sekcje są grupami elementów z XMLa. Etykiety zaczynające się od znaku # są zastępowane odpowiednimi sekcjami. Dla przykładu, wszystkie sekcje #ADDRESS mogą być zamienione na elementy:

<groupId/>

<artifactId/>

<version/>

# Szybkie spojrzenie

Jest to zestawienie elementów zawartych bezpośrednio w głównym znaczniku project. Próbowałem je podzielić tak by było je łatwo omówić. Poniższa lista oraz powyższy graf powinny dać obraz struktury POMa – i logicznej i psychicznej.

<project>

<modelVersion> _4.0.0_ </modelVersion>

<parent>...</parent>

<groupId>...</groupId>

<artifactId>...</artifactId>

<version>...</version>

<packaging>...</packaging>

<name>...</name>

<description>...</description>

<url>...</url>

<inceptionYear>...</inceptionYear>

<prerequisites>...</prerequisites>

<licenses>...</licenses>

<issueManagement>...</issueManagement>

<ciManagement>...</ciManagement>

<mailingLists>...</mailingLists>

<scm>...</scm>

<developers>...</developers>

<contributors>...</contributors>

<organization>...</organization>

<build>...</build>

<dependencies>...</dependencies>

<dependencyManagement>...</dependencyManagement>

<repositories>...</repositories>

<pluginRepositories>...</pluginRepositories>

<reporting>...</reporting>

<distributionManagement>...</distributionManagement>

<modules>...</modules>

<profiles>...</profiles>

<properties>...</properties>

</project>

# Podstawy

POM zawiera wszystkie konieczne informacje o projekcie oraz konfigurację pluginów uruchamianych podczas procesu budowania. Jest to efektywny manifest z tym "kto", "gdzie" i "co" powinien zrobić, oraz deklaracja "kiedy" i "jak". Nie można powiedzieć, że POM nie może wpływać na cykl budowania projektu – ponieważ może. Dla przykładu – konfigurując maven-ant-plugin możemy łatwo załączyć zadania ant wewnątrz deskryptora projektu. Jest to jednak tylko i wyłącznie deklaracja, podczas gdy build.xml mówi dokładnie ant co powinien uruchomić (proceduralnie). POM ogranicza się do życzeń (deklaracji). Jeśli jakaś zewnętrzna siła powstrzyma plugin ant od wywołania nie wstrzyma pozostałych pluginów przed wykonaniem ich magii. Nie tak jak w pliku build.xml gdzie jedne zadania zazwyczaj mają określone powiązania do innych.

<project>

<modelVersion> _4.0.0_ </modelVersion>

<groupId> _org.code-house_ </groupId>

<artifactId> _artifact_ </artifactId>

<version> _1_ </version>

</project>

POM przeznaczony dla Mavena 2 musi być w wersji nie mniejszej niż 4.0.0. Schemat XML dla tej wersji wymaga określenia wersji modelu. Krótko mówiąc linia:

<modelVersion> _4.0.0_ </modelVersion>

Jest wymagana we wszystkich POMach Mavena.

Maven 2 i Adres

Powyższy POM jest minimalnym jaki Maven 2 dopuszcza. Pola groupId:artifactId:version są wymagane (jakkolwiek groupId i version nie muszą być określone bezpośrednio jeśli są dziedziczone z rodzica – więcej o dziedziczeniu nieco później). Przyjrzyjmy się zatem chwale trójcy Mavena:

![](/wp-content/uploads/2008/09/091008-0630-maven2pombe2.png)

groupId: jest generalnie rzecz biorąc wyróżnikiem jakiejś organizacji bądź projektu. Przykładowo – wszystkie artefakty z rdzenia Mavena są (a przynajmniej powinny być) wewnątrz groupId_org.apache.maven_. ID grupy nie musi być rozdzielane kropkami, spójrzmy tylko na projekt _junit_. Warto też zauważyć, że kropkowane groupId nie musi przekładać się na strukturę paczek projektu, który zawiera. Oczywiście dobrą praktyką jest zachowanie tego powiązania. Po zapisaniu w repozytorium grupa zaczyna odgrywać rolę podobną do struktury paczek w Javie. Kropki są zastępowane separatorami ścieżek (np. / pod Unixem) stając się w ten sposób relatywną ścieżką od bazy repozytorium. Przykład użyty powyżej, znaczy grupa _org.apache.maven_ zostanie zapisana w katalogu $M2\_REPO/org/apache/maven.
