---
author: splatch
category:
  - agavi
  - eclipse
  - wiadomości
date: "2007-12-01T23:54:39+00:00"
guid: http://blog.dywicki.pl/?p=203
summary: |-
  [![Stored agavi project :)](http://blog.dywicki.pl/wp-content/uploads/2007/12/manager.thumbnail.PNG)](http://blog.dywicki.pl/wp-content/uploads/2007/12/manager.PNG "Stored agavi project :)") Cały dzisiejszy dzień poświęciłem na zbudowanie szkieletu modelu pod Agavi IDE. W trakcie dnia zaszło kilka nagłych zwrotów akcji na miarę dobrego filmu sensacyjnego ;). Początkowo pragnąłem wykorzystać [Eclipse Modeling Framework](http://www.eclipse.org/modeling/) do zarządzania obiektami. Wszystko szło gładko, dopóki nie spróbowałem wygenerować kodu wolnego od zależności EMF. Koniec końców dowiedziałem się, że z EMF nie da się wygenerować zwykłych Java Beanów. Niestety, ale zarządzanie wszystkim poprzez otoczkę EMF niezbyt mi się uśmiechało. Model trzymany w pamięci nie potrzebuje wodotrysków, mogą to być zwykle [POJO](http://en.wikipedia.org/wiki/POJO).
  Przepisałem więc cały kod sam, na nowo. Diagram, który początkowo ilustrował całą strukturę, troszkę się zmienił, nie mniej wersję bazową załączam na obrazku.
title: Agavi IDE, prace trwają
url: /2007/12/02/agavi-ide-prace-trwaja/

---
[![Stored agavi project :)](/wp-content/uploads/2007/12/manager.thumbnail.PNG)](/wp-content/uploads/2007/12/manager.PNG "Stored agavi project :)") Cały dzisiejszy dzień poświęciłem na zbudowanie szkieletu modelu pod Agavi IDE. W trakcie dnia zaszło kilka nagłych zwrotów akcji na miarę dobrego filmu sensacyjnego ;). Początkowo pragnąłem wykorzystać [Eclipse Modeling Framework](http://www.eclipse.org/modeling/) do zarządzania obiektami. Wszystko szło gładko, dopóki nie spróbowałem wygenerować kodu wolnego od zależności EMF. Koniec końców dowiedziałem się, że z EMF nie da się wygenerować zwykłych Java Beanów. Niestety, ale zarządzanie wszystkim poprzez otoczkę EMF niezbyt mi się uśmiechało. Model trzymany w pamięci nie potrzebuje wodotrysków, mogą to być zwykle [POJO](http://en.wikipedia.org/wiki/POJO).
Przepisałem więc cały kod sam, na nowo. Diagram, który początkowo ilustrował całą strukturę, troszkę się zmienił, nie mniej wersję bazową załączam na obrazku.

[![Agavi IDE Model Draft](/wp-content/uploads/2007/12/default.thumbnail.png)](/wp-content/uploads/2007/12/default.png "Agavi IDE Model Draft") Kolejny problem jaki wylazł to zapisywanie obiektów w db4o. Początkowo założyłem sobie, że niektórym elementom będę przypisywał lokalizacje (na diagramie interfejs IResourceLink). Okazało się jednak, że pochodne IResource (IFolder, IFile) w Eclipse zawierają sporo informacji, z których zapisaniem był problem (PreferencesStore), a które z punktu widzenia AgaviIDE były zbyteczne. Ilość zapisanych obiektów była przytłaczająca, już po chwili pracy baza projektu miała ponad 1 megabajt. Biorąc pod uwagę to, że jest tam zapisywany model, który zmienia się często i w którym wyszukiwanie powinno być możliwie najszybsze sytuacja taka była nie do przyjęcia. Znowu przekopałem cały kod modelu, tym razem po to by korzystać ze klasy java.io.File. Masakra, koniec końców udało mi się całość uruchomić. Builder działa, obiekty są zapisywane (drugi obrazek). Teraz dorzucić jeszcze wyciąganie informacji z plików php i powinno być ok..

Oto output, którym w tej chwili częstuje builder.
```
incremental build
create new project
project location E:\agavi-ide\org.agavi.editor.module\workspace\Agavi test
project modules []
change: Agavi test, resource type: Project
change: Admin, resource type: Folder
change: actions, resource type: Folder
change: ErrorAction.class.php, resource type: File
```

Pomyślcie jak fajnie będzie się edytować routing.. podanie ścieżki, wskazanie akcji i gotowe.. albo konfiguracja walidatorów na podobna do tej z [JSF Tools](/wp-content/uploads/2007/12/facesconfig-validator.png "JSF Tools…")
