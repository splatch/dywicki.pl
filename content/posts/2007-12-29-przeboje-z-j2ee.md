---
author: splatch
category:
  - java
date: "2007-12-29T20:52:15+00:00"
guid: http://blog.dywicki.pl/?p=208
summary: |-
  Nigdy bym nie pomyślał, że stworzenie nowej aplikacji w oparciu o J2EE będzie tak trudne. A jednak, mimo wszystko, jest!

  Zachwycony prostotą rozwiązania wbudowanego w standard, mianowicie roli i mapowania adresów, postanowiłem je wykorzystać. JSF mimo całej swej wspaniałości, mechanizmu autoryzacji nie dostarcza.
title: Przeboje z J2EE
url: /2007/12/29/przeboje-z-j2ee/

---
Nigdy bym nie pomyślał, że stworzenie nowej aplikacji w oparciu o J2EE będzie tak trudne. A jednak, mimo wszystko, jest!

Zachwycony prostotą rozwiązania wbudowanego w standard, mianowicie roli i mapowania adresów, postanowiłem je wykorzystać. JSF mimo całej swej wspaniałości, mechanizmu autoryzacji nie dostarcza.

## Autoryzacja i deskryptor

Może teraz nieco więcej o tym jak wygląda autoryzacja...
```xml
<web_app>
  <security_constraint>
    <web_resource_collection>
      <web_resource_name>Admin</web_resource_name>
      <url_pattern>/admin/*</url_pattern>
    </web_resource_collection>    
    <auth_constraint>
      <role_name>ADMIN</role_name>
    </auth_constraint>
  </security_constraint>    

  <security_constraint>
    <web_resource_collection>
      <web_resource_name>Student</web_resource_name>
      <url_pattern>/student/*</url_pattern>
    </web_resource_collection>        
    <auth_constraint>
      <role_name>STUDENT</role_name>
    </auth_constraint>
  </security_constraint>

  <security_role>
    <role_name>ADMIN</role_name>
  </security_role>
  <security_role>
    <role_name>STUDENT</role_name>
  </security_role>

  <login_config>
    <auth_method>FORM</auth_method>
    <realm_name>swe-realm</realm_name>
    <form_login_config>
      <form_login_page>/login.jsp</form_login_page>
      <form_error_page>/login.jsp</form_error_page>
    </form_login_config>    
  </login_config>
</web_app>
```

Taki fragment w pliku web.xml oraz odpowiedni formularz umożliwiają wykorzystanie autoryzacji, którą obsługuje serwer. Jedyne co trzeba skonfigurować to **swe-realm**, tak by serwer wiedział skąd pobierać dane o użytkownikach i rolach.

## Był sobie realm

Ano był sobie realm. Większość serwerów ma wbudowany zestaw realmów, które wystarczy skonfigurować by ich używać. Bilans kilkudniowych walk w przypadku Glassfisha i Geronimo jest zbieżny - żaden ze standardowych mechanizmów nie potrafi sobie poradzić z PostgreSQL. Zadziwiające nieprawdaż? W tym gąszczu standardów implementacje chyba się rozjechały, albo są po prostu kiepsko napisane, bo kwestia rozbija się o złe quoty w zapytaniach (wszędzie na chama lecą cudzysłowy, a dla PostgreSQL tak są 'cytowane' kolumny). Zatem zarówno Geronimo i Glassfish nie obsługuje poprawnie autoryzacji pod wybraną przeze mnie bazą danych. Znalazłem obejście dla Glassfisha - ktoś sam zaimplementował JDBCRealm. Po drobnych poprawkach i przekompilowaniu, tak by był on zgodny z aktualną wersją serwera uruchomiłem serwer.. Właściwie chciałbym go uruchomić, bo cóż się okazało? Ano problemy z classpathem. O zgrozo!

## To już jest koniec historii

Żeby było zabawniej, można stworzyć sobie własny moduł logowania w oparciu o JAAS, wystarczy implementacja jednego interfejsu, jakkolwiek Glassfish wymusza rozszerzanie własnych klas. Z Geronimo póki co mam problem, ponieważ te wymaga bym tworzył dodatkowe deskryptory, czego ja oczywiście nie potrafię..

Był sobie standard, były sobie implementacje, pozostał zrozpaczony developer. :\|
