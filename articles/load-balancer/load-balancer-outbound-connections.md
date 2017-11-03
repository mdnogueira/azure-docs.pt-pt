---
title: "Noções sobre ligações de saída no Azure | Microsoft Docs"
description: "Este artigo explica como o Azure permite VMs a comunicar com serviços de Internet pública."
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 93e6c87a9d445ca448509a256247fb5e4749ec1c
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="understanding-outbound-connections-in-azure"></a>Compreender as ligações de saída no Azure

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Uma Máquina Virtual (VM) no Azure podem comunicar com pontos finais fora do Azure no espaço de endereços IP públicos. Quando uma VM inicia um fluxo de saída para um destino no espaço de endereço IP público, o Azure mapeia o endereço IP privado a VM para um endereço IP público e permite que o tráfego de retorno alcançar a VM.

O Azure oferece três métodos diferentes para atingir a conectividade de saída. Cada método tem as suas próprias capacidades e limitações. Reveja cada método cuidadosamente para escolher qual satisfaz as suas necessidades.

| Cenário | Método | Nota |
| --- | --- | --- |
| VM autónoma (nenhum Balanceador de carga, nenhum endereço IP público de nível de instância) |Predefinição realizar o SNAT |Azure associa um endereço IP público para realizar o SNAT |
| VM de balanceamento de carga (nenhum endereço IP público de nível de instância na VM) |Realizar o SNAT utilizando o Balanceador de carga |Azure utiliza um endereço IP público do Load Balancer para realizar o SNAT |
| VM com o endereço IP público de nível de instância (com ou sem Balanceador de carga) |Realizar o SNAT não é utilizado |Utiliza o IP público atribuído à VM do Azure |

Se não pretender que uma VM para comunicar com pontos finais fora do Azure no espaço de endereços IP públicos, pode utilizar grupos de segurança de rede (NSG) para bloquear o acesso. Utilizar NSGs é abordada mais detalhadamente na [impedir a conectividade pública](#preventing-public-connectivity).

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>VM autónoma com nenhum endereço IP público de nível de instância

Neste cenário, a VM não faz parte de um conjunto de Balanceador de carga do Azure e não tem um endereço de IP de público ao nível do instância (ILPIP) atribuído ao mesmo. Quando a VM cria um fluxo de saída, Azure traduz o endereço IP de origem privada do fluxo de saída para um endereço IP público de origem. O endereço IP público utilizado para este fluxo de saída não é configurável e não count contra o limite de recurso IP público da subscrição. Azure utiliza origem rede endereço tradução (realizar o SNAT) para executar esta função. Portas efémeras do endereço IP público são utilizadas para distinguir fluxos individuais teve originados pela VM. Realizar o SNAT atribui dinamicamente portas efémeras quando fluxos são criados. Neste contexto, as portas efémeras utilizadas para realizar o SNAT são referidas como portas de realizar o SNAT.

Portas de realizar o SNAT são um recurso finito que pode ser esgotado. É importante compreender a forma como são consumidos. Uma porta de realizar o SNAT é consumida por fluxo para um endereço IP de destino única. Para vários fluxos para o mesmo endereço IP de destino, cada fluxo consome uma única porta de realizar o SNAT. Isto garante que os fluxos são exclusivos quando teve o mesmo endereço IP público para o mesmo endereço IP de destino. Vários fluxos, cada um para um endereço IP de destino diferentes partilhar uma única porta de realizar o SNAT. O endereço IP de destino faz com que os fluxos exclusivo.

Pode utilizar [análise de registos para o Balanceador de carga](load-balancer-monitor-log.md) e [mensagens de esgotamento de porta de registos de eventos de alerta para monitor para realizar o SNAT](load-balancer-monitor-log.md#alert-event-log) para monitorizar o estado de funcionamento de ligações de saída. Quando os recursos de porta de realizar o SNAT ficam esgotados, fluxos de saída falharem até que as portas de realizar o SNAT lançadas pela fluxos existentes. Balanceador de carga utiliza um tempo de limite de inatividade de 4 minutos para as portas de realizar o SNAT de reclamação.  Reveja [VM com um endereço IP público de nível de instância (com ou sem Balanceador de carga)](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer) a seguinte secção, bem como [esgotamento de realizar o SNAT gerir](#snatexhaust).

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>VM de balanceamento de carga com nenhum endereço IP público de nível de instância

Neste cenário, a VM faz parte de um conjunto de Balanceador de carga do Azure.  A VM não tem um endereço IP público atribuído. O recurso de Balanceador de carga tem de ser configurado com uma regra para ligar o público front-end IP com o conjunto de back-end.  Se não concluir esta configuração, o comportamento é conforme descrito na secção acima para [autónoma de VM com nenhuma IP público de nível de instância](load-balancer-outbound-connections.md#standalone-vm-with-no-instance-level-public-ip-address).

Quando a VM de balanceamento de carga cria um fluxo de saída, Azure traduz o endereço IP de origem privada do fluxo de saída para o endereço IP público do front-end de Balanceador de carga público. Azure utiliza origem rede endereço tradução (realizar o SNAT) para executar esta função. Portas efémeras do endereço IP público do Balanceador de carga são utilizadas para distinguir fluxos individuais teve originados pela VM. Realizar o SNAT atribui dinamicamente portas efémeras quando são criados os fluxos de saída. Neste contexto, as portas efémeras utilizadas para realizar o SNAT são referidas como portas de realizar o SNAT.

Portas de realizar o SNAT são um recurso finito que pode ser esgotado. É importante compreender a forma como são consumidos. Uma porta de realizar o SNAT é consumida por fluxo para um endereço IP de destino única. Para vários fluxos para o mesmo endereço IP de destino, cada fluxo consome uma única porta de realizar o SNAT. Isto garante que os fluxos são exclusivos quando teve o mesmo endereço IP público para o mesmo endereço IP de destino. Vários fluxos, cada um para um endereço IP de destino diferentes partilhar uma única porta de realizar o SNAT. O endereço IP de destino faz com que os fluxos exclusivo.

Pode utilizar [análise de registos para o Balanceador de carga](load-balancer-monitor-log.md) e [mensagens de esgotamento de porta de registos de eventos de alerta para monitor para realizar o SNAT](load-balancer-monitor-log.md#alert-event-log) para monitorizar o estado de funcionamento de ligações de saída. Quando os recursos de porta de realizar o SNAT ficam esgotados, fluxos de saída falharem até que as portas de realizar o SNAT lançadas pela fluxos existentes. Balanceador de carga utiliza um tempo de limite de inatividade de 4 minutos para as portas de realizar o SNAT de reclamação.  Consulte a secção seguinte, bem como [esgotamento de realizar o SNAT gerir](#snatexhaust).

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>VM com um endereço IP público de nível de instância (com ou sem Balanceador de carga)

Neste cenário, a VM possui uma instância do nível pública IP (ILPIP) atribuída. Não importa se a VM está ou não de balanceamento de carga. Quando é utilizado um ILPIP, origem rede endereço tradução (realizar o SNAT) não é utilizado. A VM utiliza o ILPIP para todos os fluxos de saída. Se muitos fluxos de saída inicia a sua aplicação e experiência de esgotamento de realizar o SNAT, deve considerar a atribuir um ILPIP para mitigar restrições de realizar o SNAT.

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>Detetar o IP público utilizado por uma determinada VM

Existem várias formas para determinar o endereço IP de origem público de uma ligação de saída. OpenDNS fornece um serviço que pode apresentar-lhe o endereço IP público da sua VM. Utilizando o comando nslookup, pode enviar uma consulta DNS para o myip.opendns.com de nome para a resolução de OpenDNS. O serviço devolve o endereço IP de origem que foi utilizado para enviar a consulta. Quando executar a consulta seguinte da sua VM, a resposta é o IP público utilizado relativamente a essa VM.

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>Impedir a Conetividade pública

Por vezes, não é desejável para uma VM para a permissão para criar um fluxo de saída ou poderá haver um requisito para gerir os destinos que podem ser acedidos com fluxos de saída ou que destinos podem começar a fluxos de entrada. Neste caso, utilize [grupos de segurança de rede (NSG)](../virtual-network/virtual-networks-nsg.md) para gerir os destinos em que a VM pode aceder, bem como o destino público, pode iniciar os fluxos de entrada. Quando aplica um NSG a uma VM com balanceamento de carga, terá de prestar atenção ao [predefinido etiquetas](../virtual-network/virtual-networks-nsg.md#default-tags) e [predefinido regras](../virtual-network/virtual-networks-nsg.md#default-rules).

Tem de se certificar de que a VM pode receber pedidos de sonda de estado de funcionamento do Balanceador de carga do Azure. Se um NSG bloquear pedidos de sonda de estado de funcionamento da etiqueta predefinida AZURE_LOADBALANCER, a pesquisa de estado de funcionamento da VM falha e a VM está marcado como. Balanceador de carga para a enviar de novos fluxos para essa VM.

## <a name="snatexhaust"></a>Gestão de esgotamento de realizar o SNAT

Portas efémeras utilizadas para realizar o SNAT são um recurso exhaustible, conforme descrito em [autónoma de VM com nenhum endereço IP público de nível de instância](#standalone-vm-with-no-instance-level-public-ip-address) e [VM de balanceamento de carga com nenhum endereço IP público de nível de instância](#standalone-vm-with-no-instance-level-public-ip-address).  

Se sabe que irá ser iniciar muitas ligações de saída para o mesmo destino, observar a efetuar ligações de saída ou que aconselhado pelo suporte esgotar os portas de realizar o SNAT, tem várias opções de mitigação geral.  Reveja estas opções e decidir o que é melhor para o seu cenário.  É possível um ou mais podem ajudar a gerir este cenário.

### <a name="assign-an-instance-level-public-ip-to-each-vm"></a>Atribuir um IP público de nível de instância para cada VM
Isto altera o seu cenário para [IP público de nível de instância para uma VM](#vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer).  Todas as portas efémeras do IP público utilizado para cada VM estão disponíveis para a VM (por oposição a cenários em que a portas efémeras de um IP público são partilhadas com todas as VMS associado ao conjunto de back-end correspondentes).

### <a name="modify-application-to-use-connection-pooling"></a>Modificar a aplicação para utilizar o agrupamento de ligações
Pode reduzir a pedido para portas efémeras utilizada para realizar o SNAT utilizando o agrupamento de ligação na sua aplicação.  Fluxos adicionais para o mesmo destino irão consumir portas adicionais.  Se reutilizar o mesmo fluxo para vários pedidos, os pedidos de vários irão consumir uma única porta.

### <a name="modify-application-to-use-less-aggressive-retry-logic"></a>Modificar a aplicação para utilizar menos agressiva lógica de repetição
Pode reduzir a pedido para portas efémeras utilizando uma menor agressiva lógica de repetição.  Quando estão esgotadas portas efémeras utilizadas para realizar o SNAT, agressiva ou força bruta repete sem decay e término esgotamento de causa de lógica para manter.  Portas efémeras tem um minuto 4 tempo limite de inatividade (não ajustável) e se as repetições são demasiado agressiva, o esgotamento não tem nenhuma oportunidade de limpar no seu próprio.

## <a name="limitations"></a>Limitações

Se [vários endereços IP (públicos) estão associados um balanceador de carga](load-balancer-multivip-overview.md), os endereços estes IPs públicos são uma candidata para fluxos de saída.

Azure utiliza um algoritmo para determinar o número de portas de realizar o SNAT disponíveis com base no tamanho do conjunto.  Isto não é configurável neste momento.

Ligações de saída têm um tempo de limite de inatividade de 4 minutos.  Não é ajustável.

É importante rememember que o número de portas de realizar o SNAT disponíveis não traduzir diretamente para o número de ligações. Consulte acima para informações específicas sobre quando e como portas de realizar o SNAT são alocadas e como gerir este recurso exhaustible.
