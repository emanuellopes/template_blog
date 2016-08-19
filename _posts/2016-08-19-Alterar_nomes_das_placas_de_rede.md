---
layout: post
title: "Alterar o nome das placas de Rede Arch Linux/Linux Mint"
description: "Este tutorial ensina a remover aqueles nomes esquisitos das placas de rede, e a substitui-los, pelos clássicos nomes eth0 e wlan0."
tags: [Fix, Linux Mint, Arch Linux]
---

Na distribuição Arch linux já se sabe que o nome das placas de rede tem de ser configurado, pois vem sempre com aquele nome esquisito ensp4... wlsnp..., não estamos própriamente habituados a ver esses nomes para as placas de rede.
Sempre usei wlan0 e eth0, mas para isso é necessário que seja configurado.

O mais estranho foi quando instalei o Linux Mint e tinha o mesmo problema, pensava que já vinha configurado mas pelos vistos não!

<div class="divider"></div>

###Abrimos o terminal :)

No meu caso já tenho os nomes corretos!

Primeiro obtêm-se o endereço mac das placas.

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ ifconfig | grep HWaddr
~~~

![Terminal](/assets/images/2016_08_19-terminal_placas__rede.jpg)

Criamos um novo ficheiro, com as linhas em baixo, mas com o endereço mac que foi obtido anteriormente.
eth0 - ethernet
wlan0 - wireless

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ sudo nano /etc/udev/rules.d/10-network.rules
~~~

~~~
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="74:d0:2b:10:af:d5", NAME="eth0"
SUBSYSTEM=="net", ACTION=="add", ATTR{address}=="6c:71:d9:8a:1b:01", NAME="wlan0"
~~~

Reiniciamos o pc e o problema fica resolvido!