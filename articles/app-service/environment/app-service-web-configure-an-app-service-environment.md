---
title: "Como configurar um serviço de aplicações ambiente v1"
description: "Configuração, gestão e monitorização do v1 ambiente de serviço de aplicações"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: b5a1da49-4cab-460d-b5d2-edd086ec32f4
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: ccompy
ms.openlocfilehash: 34fb3f15c03a3d3ef5f0a27081539bf0a6d19c5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-an-app-service-environment-v1"></a>Configurar uma aplicação do serviço ambiente v1

> [!NOTE]
> Este artigo é sobre o ambiente de serviço de aplicações v1.  Há uma versão mais recente do ambiente de serviço de aplicações que são mais fáceis de utilizar e é executada na infraestrutura mais poderosa. Para saber mais sobre o novo início de versão com o [introdução ao ambiente de serviço de aplicações](intro.md).
> 

## <a name="overview"></a>Descrição geral
Um nível elevado, um ambiente de serviço de aplicações do Azure é composta por vários componentes principais:

* Recursos de computação que estão em execução no serviço alojado de ambiente de serviço de aplicações
* Armazenamento
* Uma base de dados
* Uma Classic(V1) ou recurso Manager(V2) Azure rede Virtual (VNet) 
* Uma sub-rede com o serviço de ambiente de serviço de aplicações alojadas em execução no mesmo

### <a name="compute-resources"></a>Recursos de computação
Utilize os recursos de computação para os agrupamentos de recursos de quatro.  Cada ambiente de serviço de aplicações (ASE) tem um conjunto de front-ends e três conjuntos de trabalho possíveis. Não precisa de utilizar todos os conjuntos de trabalho três – se quiser, apenas pode utilizar um ou dois.

Os anfitriões nos agrupamentos de recursos (front-ends e trabalhadores) não estão acessíveis diretamente aos inquilinos. Não é possível utilizar o protocolo RDP (Remote Desktop Protocol) para ligar aos mesmos, altere o respetivo tipo de aprovisionamento ou atuar como um administrador nos mesmos.

Pode definir a quantidade de agrupamento de recursos e o tamanho. Está num ASE, terá de quatro opções de tamanho, que são etiquetadas P1 através de P4. Para obter detalhes sobre os tamanhos e os seus preços, consulte [preços do serviço de aplicações](https://azure.microsoft.com/pricing/details/app-service/).
Alterar a quantidade ou o tamanho é designado por uma operação de escala.  Operação de dimensionamento apenas um pode estar em curso cada vez.

**Extremidades do FrontPage**: os front-ends são pontos finais de HTTP/HTTPS para as aplicações que são guardados na sua ASE. Não executar cargas de trabalho nos front-ends.

* ASE começa com dois P2s, que é suficiente para cargas de trabalho de programador/teste e cargas de trabalho de produção de baixo nível. Recomendamos vivamente que P3s para moderada para cargas de trabalho pesadas de produção.
* Para moderada para cargas de trabalho pesadas de produção, recomendamos que tem, pelo menos, quatro P3s para assegurar que existem suficientes extremidades front-em execução quando ocorre a manutenção agendada. Atividades de manutenção agendada serão apresentada para baixo um front-end cada vez. Esta reduz global capacidade disponível de front-end decorrer das atividades de manutenção.
* Front-ends podem demorar até uma hora para aprovisionar. 
* Para mais fine-tuning escala, deve monitorizar a percentagem de CPU, percentagem de memória e métricas de pedidos ativos para o conjunto de front-end. Se o percentagens de CPU ou memória acima 70 por cento durante a execução P3s, adicione mais extremidades front. Se o valor de pedidos ativos averages para 15 000 para 20.000 pedidos por front-end, deverá ainda adicionar mais extremidades front. O objetivo geral é manter 70% de CPU e memória percentagens abaixo, e de fim de pedidos ativos uma média de para abaixo 15 000 pedidos por frente quando estiver a executar o P3s.  

**Os funcionários**: os trabalhadores são em que efetivamente executam as suas aplicações. Quando dimensionar os planos de serviço de aplicações, que utiliza os funcionários do conjunto de trabalho associados.

* De forma instantânea não é possível adicionar workers. Pode demorar uma hora para aprovisionar.
* O tamanho de um recurso de computação para qualquer conjunto de dimensionamento irão demorar < 1 hora por domínio de atualização. Existem 20 domínios de atualização está num ASE. Se ampliada o tamanho de computação de um conjunto de trabalho com 10 instâncias, pode demorar até 10 horas a concluir.
* Se alterar o tamanho dos recursos de computação que são utilizadas num conjunto de trabalho, irá causar arranques das aplicações que estão em execução nesse conjunto de trabalho.

A forma mais rápida para alterar o tamanho de recursos de computação de um conjunto de trabalho que não está em execução quaisquer aplicações é:

* Reduzir verticalmente a quantidade de funcionários 2.  A escala mínimo baixo tamanho no portal é 2. Irá demorar alguns minutos a Desalocação suas instâncias. 
* Selecione o novo tamanho de computação e o número de instâncias. Aqui, irá demorar até duas horas a concluir.

Se as aplicações requerem um tamanho de recursos de computação maior, não é possível tirar partido da documentação de orientação anterior. Em vez de alterar o tamanho do conjunto de trabalho que está a alojar essas aplicações, pode preencher o outro conjunto de trabalho com trabalhadores do tamanho pretendido e mova as suas aplicações sobre nesse agrupamento.

* Crie as instâncias adicionais do tamanho da computação necessários noutro conjunto de trabalho. Isto irá demorar até uma hora para concluir.
* Reatribua os planos de serviço de aplicações que alojam as aplicações que necessitam de um tamanho maior para o conjunto de trabalho recentemente configurada. Esta é uma operação rápida que deve demorar menos de um minuto.  
* Reduzir verticalmente o primeiro conjunto de trabalho se não precisar já essas instâncias não utilizadas. Esta operação demora alguns minutos a concluir.

**Dimensionamento automático**: uma das ferramentas que podem ajudá-lo a gerir o seu consumo de recursos de computação é que o dimensionamento automático. Pode utilizar o dimensionamento automático de front-end ou os conjuntos de trabalho. Pode fazer coisas como aumento as instâncias de qualquer tipo de agrupamento da manhã e reduzir o evening. Ou, talvez, pode adicionar instâncias quando o número de funcionários que estão disponíveis num conjunto de trabalho descerem abaixo de um determinado limiar.

Se pretender definir regras de dimensionamento automático à volta de métricas de agrupamento de recursos de computação, em seguida, tenha em atenção o tempo que necessita de aprovisionamento. Para obter mais detalhes sobre o dimensionamento automático ambientes do App Service, consulte [como configurar o dimensionamento automático num ambiente de serviço de aplicações][ASEAutoscale].

### <a name="storage"></a>Armazenamento
Cada ASE está configurado com 500 GB de armazenamento. Este espaço é utilizado em todas as aplicações a ASE. Este espaço de armazenamento é uma parte de ASE e atualmente não pode ser mudado para utilizar o seu espaço de armazenamento. Se estiver a efetuar ajustes à sua rede virtual encaminhamento ou a segurança, terá de permitir que o acesso ao armazenamento do Azure ou o ASE não pode funcionar.

### <a name="database"></a>Base de Dados
A base de dados mantém as informações que define o ambiente, bem como os detalhes sobre as aplicações que estão em execução dentro da mesma. Isto é demasiado uma parte da subscrição do Azure-contido. Não é um problema que tem uma capacidade para manipular direta. Se estiver a efetuar ajustes à sua rede virtual encaminhamento ou a segurança, é necessário permitir que o acesso ao Azure SQL Server – ou o ASE não pode funcionar.

### <a name="network"></a>Rede
A VNet que é utilizada com o ASE pode ser um que introduziu quando criou o ASE ou que efetuou antecedência. Quando criar a sub-rede durante a criação de ASE, força o ASE estar no mesmo grupo de recursos de rede virtual. Se precisar do grupo de recursos utilizado pelo seu ASE ser diferente da VNet, terá de criar a sua ASE utilizando um modelo do resource manager.

Existem algumas restrições na rede virtual que é utilizado para ASE:

* A rede virtual tem de ser uma rede virtual regional.
* Tem de haver uma sub-rede com 8 ou mais endereços de onde o ASE é implementado.
* Depois de uma sub-rede é utilizada para alojar ASE, não é possível alterar o intervalo de endereços da sub-rede. Por este motivo, recomendamos que a sub-rede contém, pelo menos, 64 endereços para contemplar qualquer crescimento futuro da ASE.
* Pode ser mais na sub-rede, mas o ASE nada.

Ao contrário do serviço alojado que contém o ASE o [rede virtual] [ virtualnetwork] e sub-rede estão sob o controlo de utilizador.  Pode administrar a sua rede virtual através da IU de rede Virtual ou do PowerShell.  ASE pode ser implementado num clássico ou VNet do Resource Manager.  O portal e as experiências de API são ligeiramente diferentes entre VNets do Resource Manager e o clássico, mas a experiência de ASE é o mesmo.

A VNet que é utilizada para alojar ASE pode utilizar qualquer um dos endereços RFC1918 IP privados ou pode utilizar endereços IP públicos.  Se pretender utilizar um intervalo IP que não é abrangido por RFC1918 (10.0.0.0/8 172.16.0.0/12, 192.168.0.0/16), em seguida, terá de criar as suas VNet e sub-rede a serem utilizadas pela sua ASE antes da criação de ASE.

Porque esta capacidade coloca o App Service do Azure na sua rede virtual, significa que as aplicações que estão alojadas na sua ASE podem agora aceder aos recursos que são disponibilizados através do ExpressRoute ou de site para site redes privadas virtuais (VPNs) diretamente. As aplicações que estejam dentro do seu ambiente de serviço de aplicações não necessitam de funcionalidades de rede adicionais para aceder aos recursos disponíveis para a rede virtual que está a alojar o ambiente de serviço de aplicações. Isto significa que não é necessário utilizar a integração de VNET ou ligações híbridas para obter a recursos em ou com ligação à sua rede virtual. Pode continuar a utilizar ambas as funcionalidades para aceder a recursos em redes que não estão ligados à sua rede virtual.

Por exemplo, pode utilizar a integração de VNET para integrar com uma rede virtual que está na sua subscrição, mas não está ligada à rede virtual que está a ser o ASE. Ainda também pode utilizar ligações híbridas para aceder a recursos que estão em outras redes, tal como o pode fazer normalmente.  

Se tiver a rede virtual configurada com uma VPN ExpressRoute, deve ter conhecimento de algumas as necessidades de encaminhamento que tenha ASE. Existem algumas configurações de rota definida pelo utilizador (UDR) que são incompatíveis com ASE. Para obter mais detalhes sobre como executar ASE numa rede virtual com o ExpressRoute, consulte [com um ambiente de serviço de aplicações numa rede virtual com o ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Proteger o tráfego de entrada
Existem dois métodos principais para controlar o tráfego de entrada para a sua ASE.  Pode utilizar Groups(NSGs) de segurança de rede para controlar qual IP endereços podem aceder ao seu ASE conforme descrito aqui [como controlar o tráfego de entrada num ambiente de serviço de aplicações](app-service-app-service-environment-control-inbound-traffic.md) e também pode configurar o seu ASE com um Balancer(ILB) de carga interno.  Estas funcionalidades também podem ser utilizadas em conjunto se pretender restringir o acesso utilizando NSGs para ASE o ILB.

Quando cria ASE, criará um VIP na sua VNet.  Existem dois tipos de VIP, internos e externos.  Quando cria ASE com um VIP externo as suas aplicações na sua ASE será acessíveis através de um endereço IP encaminhável internet. Quando seleciona interno sua ASE será configurado com um ILB e não será diretamente acessíveis de internet.  ILB ASE ainda necessita de um VIP externo, mas só é utilizado para acesso de gestão e manutenção do Azure.  

Durante a criação do ILB ASE fornecer o subdomínio utilizado pelo ASE ILB e vai ter de gerir os seus próprios DNS para o subdomínio que especificar.  Porque é definir o nome de subdomínio que terá também de gerir o certificado utilizado para acesso HTTPS.  Após a criação de ASE lhe for pedido que forneça o certificado.  Para obter mais informações sobre como criar e utilizar ILB ASE ler [através de um balanceador de carga interno com um ambiente de serviço de aplicações][ILBASE]. 

## <a name="portal"></a>Portal
Pode gerir e monitorizar o seu ambiente de serviço de aplicações através da IU no portal do Azure. Se tiver ASE, em seguida, é provável que ver o símbolo de serviço de aplicações na sua barra lateral. Este símbolo é utilizado para representar os ambientes de serviço de aplicação no portal do Azure:

![Símbolo de ambiente de serviço de aplicações][1]

Para abrir a IU que lista todos os seus ambientes do App Service, pode utilizar o ícone ou selecione na divisa (">" símbolo) na parte inferior da barra lateral para selecionar os ambientes do App Service. Ao selecionar uma das ASEs listados, abra a IU do que é utilizada para monitorizar e geri-lo.

![IU para monitorizar e gerir o ambiente de serviço de aplicações][2]

Este primeiro painel mostra algumas propriedades do seu ASE, juntamente com um gráfico de métrico por agrupamento de recursos. Algumas das propriedades que são apresentadas as **Essentials** bloco também são hiperligações que irão abrir o painel que está associado este. Por exemplo, pode selecionar o **rede Virtual** nome para abrir a IU associada com a rede virtual que está a executar no seu ASE. **Planos do App Service** e **aplicações** Abra cada painéis listam estes itens que estão na sua ASE.  

### <a name="monitoring"></a>Monitorização
Os gráficos permitem-lhe ver várias métricas de desempenho em cada agrupamento de recursos. Para o conjunto de front-end, pode monitorizar a média da CPU e memória. Para conjuntos de trabalho, pode monitorizar a quantidade que é utilizada e a quantidade que está disponível.

Serviço de aplicações de vários planos podem tornar a utilização dos trabalhadores de um conjunto de trabalho. A carga de trabalho não é distribuída de forma a mesma como com os servidores front-end, por isso, a utilização da CPU e memória não fornecer muito in the way of informações úteis. É mais importante para controlar quantas trabalhadores que utilizou e estão disponível, especialmente se estiver a gerir este sistema para que outros a utilizar.  

Também pode utilizar todas as métricas que podem ser controladas nos gráficos para configurar alertas. Como configurar alertas aqui funciona tal como noutro local no App Service. Pode definir um alerta a partir de **alertas** IU parte ou a partir de desagregação em qualquer métricas da IU e selecionando **Adicionar alerta**.

![Métricas da IU][3]

As métricas que foram abordadas apenas são as métricas de ambiente de serviço de aplicações. Também existem métricas que estão disponíveis ao nível do plano de serviço de aplicações. Este é onde a monitorização da CPU e memória torna muito sentido.

Está num ASE, todos os planos de serviço de aplicações são dedicados planos de serviço de aplicações. Isto significa que as únicas aplicações que estão em execução em anfitriões atribuídos a que o plano do App Service são as aplicações que plano de serviço de aplicações. Para ver detalhes sobre o seu plano de serviço de aplicações, trazer o plano de serviço de aplicações de qualquer uma das listas na IU do ASE ou a partir de **planos do App Service/procurar** (que apresenta uma lista de todos eles).   

### <a name="settings"></a>Definições
No painel de ASE, há um **definições** secção que contém várias funcionalidades importantes:

**Definições** > **propriedades**: O **definições** painel abre automaticamente quando trazer o seu painel ASE. Na parte superior é **propriedades**. Existem um número de itens de sessão aqui redundante veem no **Essentials**, mas o que é muito útil **endereço IP Virtual**, bem como **saída endereços IP**.

![Painel de definições e propriedades][4]

**Definições** > **endereços IP**: quando criar uma aplicação de IP Secure Sockets Layer (SSL) no seu ASE, terá um endereço de IP SSL. Para obter um, o ASE tem endereços de IP SSL que lhe pertençam que podem ser atribuídos. Quando é criado ASE, tem um endereço de IP SSL para esta finalidade, mas pode adicionar mais. Há uma cobrança para endereços de SSL de IP adicionais, conforme mostrado no [preços do serviço de aplicações] [ AppServicePricing] (na secção de ligações de SSL). O preço adicional é o preço de SSL de IP.

**Definições** > **conjunto de Front-End** / **conjuntos de trabalho**: cada nestes painéis de agrupamento de recursos oferece a capacidade para ver informações apenas nesse agrupamento de recursos, além de proporcionarem controlos totalmente Dimensionar nesse agrupamento de recursos.  

O painel base para cada agrupamento de recursos fornece um gráfico com a métrica para esse agrupamento de recursos. Tal como com os gráficos no painel ASE, pode ir para o gráfico e configurar alertas conforme pretendido. Definir um alerta a partir do painel ASE para um agrupamento de recursos específico efetua a mesma coisa como fazê-lo do agrupamento de recursos. Conjunto de trabalho da **definições** painel, tem acesso a todas as aplicações ou planos de serviço de aplicações que estão em execução neste conjunto de trabalho.

![Definições do conjunto de trabalho da IU][5]

### <a name="portal-scale-capabilities"></a>Capacidades de dimensionamento de portal
Existem três operações de dimensionamento:

* Alterar o número de endereços IP a ASE que estão disponíveis para utilização de SSL de IP.
* Alteração do tamanho do recurso de computação que é utilizado no agrupamento de recursos.
* Alterar o número de recursos de computação que são utilizados no agrupamento de recursos, manualmente ou através de dimensionamento automático.

No portal, existem três formas de controlar o número de servidores que tenham nos seus conjuntos de recursos:

* Uma operação de escala no painel principal ASE na parte superior. Pode efetuar a escala várias alterações de configuração para os conjuntos de front-end e de trabalho. Estas são todos aplicadas como uma única operação.
* Uma operação de dimensionamento manual do agrupamento de recursos individuais **escala** painel, que está sob **definições**.
* Dimensionamento automático, o que é configurado o agrupamento de recursos individuais **escala** painel.

Para utilizar a operação de dimensionamento no painel ASE, arraste o controlo de deslize para a quantidade que pretende e guarda. Este IU também suporta a alteração do tamanho.  

![Escala da IU][6]

Para utilizar as capacidades de manual ou de dimensionamento automático num agrupamento de recursos específico, aceda a **definições** > **conjunto de Front-End** / **conjuntos de trabalho** conforme adequado. Em seguida, abra o conjunto que pretende alterar. Aceda a **definições** > **aumentar horizontalmente** ou **definições** > **aumentar verticalmente**. O **aumentar horizontalmente** painel permite-lhe controlar a quantidade de instância. **Aumentar verticalmente** permite-lhe controlar o tamanho do recurso.  

![Definições de dimensionamento da IU][7]

## <a name="fault-tolerance-considerations"></a>Considerações de tolerância a falhas
Pode configurar um ambiente de serviço de aplicação para utilizar até 55 recursos de computação total. Esses 55 de recursos de computação, 50 só pode ser utilizado para alojar cargas de trabalho. O motivo para isto é tem dois objetivos. Não há um mínimo de 2 recursos de computação de front-end.  Que deixa até 53 para suportar a alocação de conjunto de trabalho. Para fornecer tolerância a falhas, tem de ter um recurso de computação adicional que é atribuído, de acordo com as seguintes regras:

* Cada conjunto de trabalho tem de, pelo menos, 1 recursos de computação adicional que não estão disponível para ser atribuída uma carga de trabalho.
* Quando a quantidade de recursos de computação num conjunto de trabalho ultrapassar um determinado valor, é necessário para tolerância a falhas outro recurso de computação. Não é este o caso no conjunto de front-end.

Dentro de qualquer conjunto de trabalho único, os requisitos de tolerância a falhas são que para um determinado valor de X recursos atribuídos a um conjunto de trabalho:

* Se X é entre 2 e 20, a quantidade de recursos de computação utilizável que pode utilizar para cargas de trabalho é X-1.
* Se X for entre 21 e 40, a quantidade de recursos de computação utilizável que pode utilizar para cargas de trabalho é X-2.
* Se X for entre 41 e 53, a quantidade de recursos de computação utilizável que pode utilizar para cargas de trabalho é X-3.

Os requisitos de espaço mínimo tem 2 servidores front-end e 2 workers.  Com as instruções acima, em seguida, seguem-se alguns exemplos para esclarecer:  

* Se tiver 30 trabalhadores num único conjunto, 28 deles pode ser utilizado para alojar cargas de trabalho.
* Se tiver 2 trabalhadores num único conjunto, 1 pode ser utilizado para alojar cargas de trabalho.
* Se tiver 20 trabalhadores num único conjunto, 19 pode ser utilizado para alojar cargas de trabalho.  
* Se tiver 21 trabalhadores num conjunto único, em seguida, ainda 19 só pode ser utilizado para alojar cargas de trabalho.  

O aspeto de tolerância a falhas é importante, mas tem de guardá-lo em mente, à medida que acima determinados limiares. Se pretende adicionar mais capacidade que vai da 20 instâncias, em seguida, aceda a 22 ou superior porque 21 não adiciona quaisquer mais capacidade. O mesmo se aplica a vai acima 40, onde o número seguinte que adiciona a capacidade é 42.  

## <a name="deleting-an-app-service-environment"></a>Eliminar um ambiente de serviço de aplicações
Se pretender eliminar ambiente de serviço de aplicações, em seguida, basta utilizar o **eliminar** ação na parte superior do painel de ambiente de serviço de aplicações. Ao fazê-lo, será solicitado para introduzir o nome do ambiente de serviço de aplicações para confirmar que pretende realmente fazê-lo. Tenha em atenção que ao eliminar ambiente de serviço de aplicações, elimine todo o conteúdo dentro da mesma bem.  

![Eliminar um ambiente de serviço de aplicações da IU][9]  

## <a name="getting-started"></a>Introdução
Para começar com ambientes do App Service, consulte o artigo [como criar um ambiente de serviço de aplicações](app-service-web-how-to-create-an-app-service-environment.md).

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[Appserviceplans]: ../azure-web-sites-web-hosting-plans-in-depth-overview.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoScale]: app-service-web-scale-a-web-app-in-an-app-service-environment.md
[ControlInbound]: app-service-app-service-environment-control-inbound-traffic.md
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[ASEAutoscale]: app-service-environment-auto-scale.md
[ExpressRoute]: app-service-app-service-environment-network-configuration-expressroute.md
[ILBASE]: app-service-environment-with-internal-load-balancer.md
