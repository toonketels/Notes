LEARN YOU SOME ERLANG FOR GREAT GOOD!
=====================================

## Introduction

Erlang is a functional language:
*  referential transparency: but is pragmatic about it and breaks it if
   makes sence
*  immutable data

Central has a focus on high reliability and concurrently and uses the
[actor model][].

Code is divided into "players" and each "player" has its own very specific
task. Players live in their own process and can only communicate via
sending messages. This makes it that they have not access to any information
except those send to them.

To achieve this, threads are very lightweight as compared to other languages,
which means there is not much overhead associated in creating those. Also
sending messages are very cheap. Still, they do cost something so don't
go overboard.

Compare this to traditional threads (like in php) which are processes
which do everything in a sequential order. These can access a lot of data
and are (mostly) not specialized in anything.

Erlangs policy goes "Let it crash" which means we don't need to check
for every possible error condition.


## [Starting for real][]




## LINKS

[actor model]: http://en.wikipedia.org/wiki/Actor_model


### Book parts

[Starting for real]: http://learnyousomeerlang.com/starting-out-for-real


## RESOURCES

*  [http://learnyousomeerlang.com/](learn you some erlang for great good!)
*  [http://trapexit.org/](Erlang community site)