---
layout: post
title: "Remover \"Tearing\" do Linux Mint/Ubuntu!"
description: "Como resolver o problema das linhas verticais, que se apresentam nos vídeos quando se usa gráficas intel."
tags: [Fix, Linux Mint]
---

Um dos problemas que tenho tido no Linux é relativamente às placas gráficas, principalmente em portáteis, pois têm aqueles sistema Híbrido (duas Gráficas).

No Arch linux quando usava o bumblebee, consegui resolver através do xorg.conf, adicionando umas linhas extras na configuração da placa Intel.

Aqui no Mint é um pouco diferente, pois tenho usado o sistema PRIME de nvidia, este sistema vêm com uma aplicação que se chama gpu-manager, esta aplicação irá sempre rescrever o xorg.conf sempre que alternarmos as gráficas, o problema em questão é que ele remove as opções que permite remover o tearing dos vídeos.

<div class="divider"></div>

## Solução

Fiz várias pesquisas na Internet, mas não encontrei algo que fosse realmente funcional, então fiz um pequeno script para resolver o problema.

###Abrimos o terminal :)

Começamos por remover o gpu-manager do arranque.

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ sudo nano /etc/default/grub
~~~

Modificamos a linha!

>GRUB_CMDLINE_LINUX_DEFAULT="quiet splash **nogpumanager**"

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ sudo update-grub
~~~

O ficheiro xorg criado pelo gpu-manager já tem quase a configuração toda, é só fazer algumas alterações.

####Nvidia

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~bash
emanuelx@terminator ~ $ sudo -s
emanuelx@terminator ~ $ cd /etc/X11/
emanuelx@terminator ~ $ cp xorg.conf.08192016 xorg.conf.nvidia
emanuelx@terminator ~ $ nano xorg.conf.nvidia
~~~

**É só adicionar o que está em falta!**

~~~
Section "Device"
    Identifier "intel"
    Driver "modesetting"
    BusID "PCI:0@0:2:0"
    Option "AccelMethod" "sna"
    Option "TearFree" "True"
    Option "SwapbuffersWait" "True"
EndSection

Section "Device"
    Identifier "nvidia"
    Driver "nvidia"
    BusID "PCI:1@0:0:0"
    Option "ConstrainCursor" "off"
    Option "CoolBits" "1"
    Option "NoLogo" "True"
    Option "TripleBuffer" "True"
EndSection
~~~

###Intel

<i class="fa fa-terminal" aria-hidden="true">Terminal</i>

~~~ bash
emanuelx@terminator ~ $ echo -e 'Section "Device"\n Identifier "Intel Graphics"\n Driver "Intel"\n Option "AccelMethod" "sna"\n Option "TearFree" "true"\nEndSection' | sudo tee /etc/X11/xorg.conf.d/20-intel.conf.bak
~~~

###Script :)

Agora o script, que faz o resto da magia!


~~~bash
#!/bin/bash

# Make sure only root can run our script
if [[ $EUID -ne 0 ]]; then
   echo "This script must be run as root" 1>&2
   exit 1
fi
if [ $# -eq 0 ]
  then
  	echo "use intel_prime.sh [intel| nvidia]";
    exit 1;
fi

if [ $1 == "nvidia" ] ; then
	echo "Changing to nvidia...";
	echo "update xorg.conf...";

	if [ ! -f /etc/X11/xorg.conf.nvidia ]; then
    	echo "xorg.conf.nvidia not found exit script...";
    	exit 1;
	fi

	cp /etc/X11/xorg.conf.nvidia /etc/X11/xorg.conf
	echo "delete 20-intel.conf...";

	if [ -f /etc/X11/xorg.conf.d/20-intel.conf ]; then
    	rm /etc/X11/xorg.conf.d/20-intel.conf
	fi
	prime-select nvidia
elif [[ $1 == "intel" ]]; then
	echo "Changing to intel...";
	echo "delete nvidia xorg.conf...";
	if [ -f /etc/X11/xorg.conf ]; then
    	rm /etc/X11/xorg.conf
	fi

	echo "Update 20-intel.conf ...";
	if [ ! -f /etc/X11/xorg.conf.d/20-intel.conf.bak ]; then
    	echo "20-intel.conf.bak not found exit script...";
    	exit 1;
	fi
    cp /etc/X11/xorg.conf.d/20-intel.conf.bak /etc/X11/xorg.conf.d/20-intel.conf
    prime-select intel
else
	echo "bad arguments";
fi
~~~

<div class="divider"></div>

####Utilização

~~~bash
emanuelx@terminator ~ $ chmod +x intel_nvidia.sh

emanuelx@terminator ~ $ ./intel_nvidia.sh intel --> para mudar para a intel
emanuelx@terminator ~ $ ./intel_nvidia.sh nvidia --> para mudar para a nvidia
~~~