# UltraBox
Documentação e manual de como qualquer pessoa pode tornar um TvBox em um sistema multi-tarefas e prático

# ISENÇÃO DE RESPONSABILIDADE (LEIA POR FAVOR): tudo o que você pode encontrar neste manual (binários, textos, trechos de código, etc...) é fornecido COMO ESTÁ e não faz parte do projeto oficial da Armbian. Por esta razão, nem as pessoas do projeto Armbian nem eu somos responsáveis pelo uso indevido ou perda de funcionalidade do hardware.

Por favor, não pergunte sobre suporte ou assistência em outros fóruns fora da comunidade nem no repositório github oficial da Armbian, em vez disso, poste suas perguntas neste tópico, na seção do fórum TV Boxes (relacionado a hardware) ou no suporte ponto a ponto seção (relacionado a linux/software geral).

Obrigado!

 

Seguindo o tópico recente no fórum LibreElec da comunidade Armbian.org sobre uma imagem não oficial para dispositivos rk3229, gostaria de tornar público o trabalho feito por mim e @fabiobassa sobre trazer suporte rk322x para armbian.

O projeto está agora em -> [´mainline Armbian´](https://www.armbian.com/rk322x-tv-box/)<- e o fork de desenvolvimento estará disponível no github -> [´aqui´](https://github.com/paolosabatino/armbian-build/tree/rk3229) <-

A maior parte do amor foi dedicada ao suporte e criação do legacy do kernel rockchip 4.4, mas em um futuro próximo o objetivo é oferecer suporte total ao kernel mainline!

O kernel mainline é totalmente suportado e receberá mais suporte no futuro. O kernel legacy não receberá mais grandes atualizações, mas é mantido lá apenas para fins especiais.

 

O que funciona (ou não...):

     -> Deve inicializar e funcionar perfeitamente em todas as placas com RK3228a, RK3228b e RK3229, com memórias DDR2 e DDR3.
     -> Mainline u-boot
     -> OPTEE proprietário fornecido como ambiente de execução confiável (necessário para escalonamento de frequência DRAM)
     -> Todos os 4 núcleos estão funcionando
     -> cabo Ethernet
     -> Serial UART (configurado em 115200 bps, não 1,5 Mbps!)
     -> Térmicas, escala de frequência de CPU e DRAM
     -> Porta OTG USB 2.0 (também como dispositivo de inicialização!)
     -> Portas EHCI/OHCI USB 2.0
     -> Subsistema MMC (incluindo dispositivos eMMC, SD e sdio)
     -> Aceleração de vídeo por hardware (tanto no kernel principal via hantro/rkvdec quanto no kernel legado via RKMPP)
     -> NAND está disponível apenas no kernel legacy. Para inicializar totalmente a partir do NAND, use o Multitool e sua instalação passo a passo (as instruções estão abaixo)
     -> Vários WIFI sobre SDIO são suportados (SSV6051P, SSV6256P, ESP8089, chips Realtek, etc...), o driver ssv6256p está disponível apenas no kernel legacy
     -> Aceleração total da GPU no kernel principal e legacy (embora o principal seja muito melhor).
     -> Prioridade da ordem de inicialização do U-boot: primeiro o sdcard, depois a porta USB OTG e, eventualmente, o eMMC interno; você pode instalar o u-boot (e todo o sistema) no eMMC interno e o u-boot sempre verificará primeiro as imagens no cartão SD/USB externo.

 

Desbrickar:

Tecnicamente, os dispositivos rockchip não podem ser brickados. Se o flash interno não contiver um sistema inicializável, eles sempre inicializarão a partir do cartão SD. Se, por algum motivo, o sistema inicializável no flash interno estiver corrompido ou não conseguir inicializar corretamente, você sempre poderá forçar o modo maskrom encurtando o pino do relógio eMMC no PCB. [´Aqui está o procedimento´](https://forum.armbian.com/topic/12656-wip-armbian-for-rk322x-devices/?do=findComment&comment=99167), mas você também pode pesquisar no Google se ficar preso em um bootloader com defeituoso, a técnica é bem simples e requer uma chave de fenda simples. 

 

<h3>No entanto, existem alguns casos infelizes (placas especialmente mais novas) em que o curto-circuito do pino do relógio eMMC é difícil ou impossível, como chips eMMC ou eMCP BGA sem pinos expostos. Nesses casos preste atenção redobrada ao gravar algo nos chip's MMC/eMCP interno e sempre teste primeiro a imagem do SDcard para ter certeza que funciona antes de gravar qualquer coisa no eMMC/eMCP.<h3/>

 

Algumas informações úteis com pins, pads ou procedimentos para algumas placas:

     [´Procedimento genérico para placas com eMMC não BGA´](https://forum.armbian.com/topic/12656-wip-armbian-for-rk322x-devices/?do=findComment&comment=99167)
     [´MXQPRO_V71´](eMCP https://forum.armbian.com/topic/12656-csc-armbian-for-rk322x-tv-boxes/?do=findComment&comment=157061)

 

NAND vs eMMC vs diferença eMCP:

os seguintes TvBox's RK3228 e RK3229 vêm com três chips de memória flash diferentes: eMMC, NAND e eMCP.

Não depende do nome de mercado da TvBox e nem da placa interna; os fabricantes colocam o que acharem mais barato quando compram os componentes.

 

     O chip NAND é apenas a memória não volátil
     O chip eMMC contém a memória não volátil e um controlador.
     O chip eMCP contém a memória não volátil, um controlador para a memória não volátil (como eMMC), mas também contém um banco de memória DDR SDRAM no mesmo chip físico.
