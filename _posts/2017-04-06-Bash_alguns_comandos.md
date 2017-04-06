---
layout: post
title: "Bash: alguns comandos resolvidos"
description: "Resolução de alguns exercícios em bash que me foram pedidos ESTG-IPLeiria"
tags: [Linux, Bash, Terminal]
---

Como já é hábito virem-me pedir para resolver exercícios em bash, fica aqui alguns que já resolvi.

<div class="divider"></div>

## Exercicios

Devolver o número de vezes que os caracteres 'A' e 'a' aparecem, no conteúdo codificado, em base 64, do ficheiro /etc/hosts.

###Abrimos o terminal :)

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ base64 /etc/hosts | grep -o '[aA]' |wc -l
~~~

Devolver o número de símbolos que compõem uma assinatura sha512sum

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ sha512sum photo.jpg | cut -d' ' -f1| tr -sd '\n' '' | wc -c
~~~
