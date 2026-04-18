---
author: splatch
category:
  - ogólne
date: "2005-09-08T09:46:53+00:00"
guid: http://blog2.dywicki.pl/2005/09/08/po-prostu-xml/
summary: |-
  ![Po prostu?](http://helion.pl/okladki/181x236/ppxml.jpg)Chciałem Was ostrzec przed nabywaniem czy nawet zbliżaniem się do tego tytułu. Książka zaczyna od omówienia XMLa, którego nie czytałem, bo jak XML wygląda to wiem. Pożyczając tą książkę chciałem lepiej poznać DTD oraz XSD. Pomimo szczerych chęci nie udało się.{{ double-space-with-newline }}
  Autorka tak pokrętnie wyjaśnia wszytko, że nie sposób to zrozumieć. Z wielkimi trudami przyswoiłem wiadomości o DTD, podobnie jak o XSL, ale XSD to męka.. nie wiem, może to zmęczenie po pracy działa na mnie tak otępiająco? Chyba nie, bo komentarze na helionie też nie są zbyt pochlebne. W każdym razie, czekam na kolejną pozycję o XMLu którą, będę miał pożyczoną - mianowicie “XML. Na poważnie”, która zapowiada się świetnie. Jutro to zweryfikuję. Tym czasem powracam do bojów z Propelem i PHPUnit.{{ double-space-with-newline }}
  // właśnie mam przed sobą XML na poważnie, za gruba to ona nie jest, ale zobaczymy co ciekawego piszą o XMLu :)

  Linki do plików:{{ double-space-with-newline }}[config.xml](http://splatch.php.org.pl/_img/config.xml){{ double-space-with-newline }}[test.dtd](http://splatch.php.org.pl/_img/test.dtd){{ double-space-with-newline }}[test.xsl](http://splatch.php.org.pl/_img/test.xsl)
title: Po prostu. XML
url: /2005/09/08/po-prostu-xml/

---
![Po prostu?](http://helion.pl/okladki/181x236/ppxml.jpg)Chciałem Was ostrzec przed nabywaniem czy nawet zbliżaniem się do tego tytułu. Książka zaczyna od omówienia XMLa, którego nie czytałem, bo jak XML wygląda to wiem. Pożyczając tą książkę chciałem lepiej poznać DTD oraz XSD. Pomimo szczerych chęci nie udało się.  

Autorka tak pokrętnie wyjaśnia wszytko, że nie sposób to zrozumieć. Z wielkimi trudami przyswoiłem wiadomości o DTD, podobnie jak o XSL, ale XSD to męka.. nie wiem, może to zmęczenie po pracy działa na mnie tak otępiająco? Chyba nie, bo komentarze na helionie też nie są zbyt pochlebne. W każdym razie, czekam na kolejną pozycję o XMLu którą, będę miał pożyczoną - mianowicie “XML. Na poważnie”, która zapowiada się świetnie. Jutro to zweryfikuję. Tym czasem powracam do bojów z Propelem i PHPUnit.  

// właśnie mam przed sobą XML na poważnie, za gruba to ona nie jest, ale zobaczymy co ciekawego piszą o XMLu :)

Linki do plików:  
[config.xml](http://splatch.php.org.pl/_img/config.xml)  
[test.dtd](http://splatch.php.org.pl/_img/test.dtd)  
[test.xsl](http://splatch.php.org.pl/_img/test.xsl)

**config.xml**

```
<?xml version="1.0"?> <!--  conf for web application --> <config cache="false"> 	<plugins> 		<plugin name="Request1" class="WebRequest1" /> 		<plugin name="Request2" class="WebRequest2" /> 	</plugins> 	<database driver="PDODatabase"> 		<type>PgSQL</type> 		<user>root</user> 		<pass>Splatch22</pass> 		<name>test_</name> 		<host>localhost</host> 		<!-- 		This parameter is optional 		<port>5502</port> 		--> 	</database> 	<modules default="HomePage"> 		<module name="Default" default="Index" /> 		<module name="City" default="UserCites" /> 	</modules> 	<mapping> 		<pattern module="Default">/HomePage</pattern> 		<!-- propozycja --> 		<variable module="Default" equals="home">module</variable> 	</mapping> </config>
```

 **test.dtd** **test.dtd**

```
<!-- Typ BOOL przyjmuje tylko wartosci true/false --> <!ENTITY % BOOL "(true|false)"> <!ELEMENT config (plugins,database,mapping)?> <!ATTLIST config cache %BOOL; #FIXED "true"> <!ELEMENT plugins (plugin*)> <!ELEMENT plugin EMPTY> <!ATTLIST plugin 			name 	CDATA		#REQUIRED 			class 	CDATA		#REQUIRED> <!ELEMENT database (type,user,pass,name,host,port)> <!ATTLIST database 			driver	CDATA #REQUIRED> <!ELEMENT type (#PCDATA)> <!ELEMENT user (#PCDATA)> <!ELEMENT pass (#PCDATA)> <!ELEMENT name (#PCDATA)> <!ELEMENT host (#PCDATA)> <!ELEMENT port (#PCDATA)> <!ELEMENT mapping (pattern,variable)*> <!ELEMENT pattern (#PCDATA)> <!ATTLIST pattern 			module	CDATA	#REQUIRED 			action	CDATA	#IMPLIED> <!ELEMENT variable (#PCDATA)> <!ATTLIST variable 			equals	CDATA	#REQUIRED 			action	CDATA	#IMPLIED>
```

**text.xsl**

```
<?xml version="1.0"?> <xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform" version="1.0"> 	<xsl:template match="/"> 		<h2>Framework config</h2><hr /> 		Buforowanie: <b> 		<xsl:choose> 				<xsl:when test="config/@cache"> 					<xsl:apply-templates select="config/@cache" /> 				</xsl:when> 				<xsl:otherwise>domyslne - true</xsl:otherwise> 		</xsl:choose> 		</b><br /> 		<h2>Wtyczki:</h2> 		Razem: <xsl:value-of select="count(.//plugins/*)" /> 		<table border="1" width="500px"> 			<tr> 				<th>Nazwa</th> 				<th>Klasa</th> 			</tr> 			<xsl:for-each select=".//plugins/plugin"> 				<tr> 					<td width="50%"><xsl:value-of select="@name" /></td> 					<td><xsl:value-of select="@class" />.php</td> 				</tr> 			</xsl:for-each> 		</table> 		<h2>Moduly</h2> 		Razem: <xsl:value-of select="count(.//modules/*)" />, domyslny <xsl:value-of select=".//modules/@default" /><br /> 		<table border="1" width="500px"> 			<tr> 				<th>Modul</th> 				<th>Domyslna akcja</th> 			</tr> 			<xsl:for-each select=".//modules/module"> 				<tr> 					<td width="50%"><xsl:value-of select="@name" /></td> 					<td><xsl:value-of select="@default" /></td> 				</tr> 			</xsl:for-each> 		</table> 		<h2>Mapowanie</h2> 		Razem <xsl:value-of select="count(.//mapping/*)" /> (wzorce: <xsl:value-of select="count(.//mapping/pattern)" />) 		<table border="1" width="500px"> 			<tr> 				<th>Wzorzec</th> 				<th>Akcja</th> 			</tr> 			<xsl:for-each select=".//mapping/pattern"> 				<tr> 					<td width="50%"><xsl:value-of select="." /></td> 					<td><xsl:value-of select="@module" /></td> 				</tr> 			</xsl:for-each> 		</table> 	</xsl:template> </xsl:stylesheet>
```
