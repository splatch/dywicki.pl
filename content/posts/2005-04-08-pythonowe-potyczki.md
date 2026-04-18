---
author: splatch
category:
  - inne
date: "2005-04-08T14:42:22+00:00"
guid: http://blog2.dywicki.pl/2005/04/08/pythonowe-potyczki/
title: Pythonowe potyczki
url: /2005/04/08/pythonowe-potyczki/

---
W dalszej części posta kod bota napisanego przeze mnie w pythonie (wspomagałem się kodem beli).
```python

import sys
import socket
import string
import time
import re #wyrazenia regularne! ;)
 
#ustawienia :)
class MyIrcConfig:
  server = ‘ircnet.pl‘
  port = 6667
  nick = ‘tete‘
  ident = ‘MyIrc‘
  realname = ‘bot‘
  channel = ‘splatch‘
 
#klasa do wyswietlania tekstow #TODO: mozliwosc blokowania komunikatow (tryb cichy)
class Output:
  def __init__(self):
   self.printer(’Test wyjscia . . .‘)

  def printer(self,x):
   print x #klasa do komunikacji bot<>irc
 
class MyIrc(MyIrcConfig):
  socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  output = Output()
 
  def connect(self):
    #probuj sie polaczyc
    try:
      self.socket.connect((self.server, self.port))
    except:
      self.output.printer( ‘Nie moge sie polaczyc z serweremn‘ )

    #powiedz serwerowi kim jestes
    self.socket.send(’NICK %srn‘ % self.nick)
    self.socket.send(’USER %s %s bla: %srn‘ % (self.ident, self.server, self.realname))
    self.output.printer(’Connected to irc servern‘)
    self.output.printer(’user "%s" (%s@%s * %s)‘ % (self.nick, self.ident, self.server, self.realname))

    #odczyt serwera
    def read(self):
      data = self.socket.recv(1024)
      self.output.printer(data)
      return data
 
    #wejdz na kanal
    def join(self):
      self.socket.send(’JOIN #%srn ‘ % self.channel )
 
    #wykonaj
    def write(self,msg):
      self.output.printer(msg)
      self.socket.send(msg) #klasa zasadnicza
 
class PyAeewe:
  MyIrc = MyIrc()
 
  def start(self):
    self.MyIrc.connect()
    while 1:
      data = self.MyIrc.read()
      #tutaj odbieranie MOTD itp
      #jesli wszystko ok
      break;

    self.MyIrc.join() #wskocz na kanal

    while 1:
      data = self.MyIrc.read()
      #daj mozliwosc wykonywania komend

    try:
      #data = raw_input(’Komenda: ‘)
      #self.MyIrc.write(data)
      self.MyIrc.output.printer(data)
    except:
      self.MyIrc.output.printer(’Blad‘)
 
aeewe = PyAeewe()
aeewe.start()
```
