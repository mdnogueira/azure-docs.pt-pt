---
title: "Descrição geral do padrão de Balanceador de carga do Azure | Microsoft Docs"
description: "Descrição geral das funcionalidades padrão de Balanceador de carga do Azure"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2017
ms.author: kumud
ms.openlocfilehash: 08e4e22ae7e5d6f6efad458b4240a6d57090e865
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Descrição geral do padrão de Balanceador de carga do Azure (pré-visualização)

O SKU Standard do Balanceador de carga do Azure e o SKU de padrão de IP público em conjunto permitem-lhe criar arquiteturas altamente escaláveis e fiáveis. As aplicações que utilizam o padrão de Balanceador de carga podem tirar partido das novas capacidades. Baixa latência, débito alto e escala estão disponíveis para milhões de fluxos de todas as aplicações de TCP e UDP.

>[!NOTE]
> O SKU de padrão de Balanceador de carga está atualmente em pré-visualização. Durante a pré-visualização, a funcionalidade não pode ter o mesmo nível de disponibilidade e fiabilidade como versão de funcionalidades que estão em geral disponibilidade. Para obter mais informações, consulte [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Utilizar geralmente disponível [SKU básico de Balanceador de carga](load-balancer-overview.md) para os serviços de produção. As funcionalidades que estão associadas esta pré-visualização, [disponibilidade zonas](https://aka.ms/availabilityzones), e [HA portas](https://aka.ms/haports), requerem separado inscrição neste momento. Siga as instruções para a respetivas inscrição para essas funcionalidades, para além de inscrever-se para o Balanceador de carga [pré-visualização padrão](#preview-sign-up).

## <a name="why-use-load-balancer-standard"></a>Porquê utilizar o padrão de Balanceador de carga?

Pode utilizar o padrão de Balanceador de carga para o intervalo completo de centros de dados virtual. A partir de implementações de pequena escala para arquiteturas de zona multi grande e complexas, utilize padrão de Balanceador de carga para tirar partido das seguintes funcionalidades:

- [Escala empresarial que](#enterprisescale) pode ser conseguido com o padrão de Balanceador de carga. Esta funcionalidade pode ser utilizada com qualquer instância de máquina virtual (VM) dentro de uma rede virtual, até 1000 instâncias de VM.

- [Informações de diagnóstico novas](#diagnosticinsights) estão disponíveis para o ajudar a compreender, gerir e resolver problemas relacionados com este componente vital do seu centro de dados virtual. Utilize o Monitor de Azure (pré-visualização) para mostrar, filtrar e grupo nova métrica multidimensional para valores de estado de funcionamento do caminho de dados contínuos. Monitorize os seus dados de front-end para a VM, sondas de estado de funcionamento do ponto final, para as tentativas de ligação de TCP e ligações de saída.

- [Grupos de segurança de rede](#nsg) são agora necessárias para qualquer instância VM que está associada SKUs de padrão de Balanceador de carga ou SKUs de padrão de IP público. Grupos de segurança de rede (NSGs) fornecem a segurança avançada para o seu cenário.

- [Elevada disponibilidade (ed) portas fornecem elevada fiabilidade](#highreliability) e escala para aplicações de rede virtuais (NVAs) e outros cenários de aplicação. Balanceamento de carga de portas HA todas as portas num Azure interno carregar balanceador (ILB) front-end a um conjunto de instâncias VM.

- [Ligações de saída](#outboundconnections) agora utilizam um modelo de alocação porta nova origem rede endereço tradução (realizar o SNAT) fornece maior resiliência e a escala.

- [Carregar balanceador padrão com disponibilidade zonas](#availabilityzones) podem ser utilizados para construir arquiteturas com redundância de zona e zonal. Ambos estes arquiteturas podem incluir o balanceamento de carga entre zona. Pode conseguir redundância de zona sem uma dependência de registos DNS. Um único endereço IP é com redundância de zona por predefinição.  Um único endereço IP pode aceder qualquer VM numa rede virtual numa região entre todas as zonas de disponibilidade.


Pode utilizar padrão de Balanceador de carga numa configuração pública ou interna para suportar os seguintes cenários fundamentais:

- Tráfego de entrada para instâncias de back-end em bom estado de balanceamento de carga.
- Porta reencaminhar tráfego de entrada para uma única instância de back-end.
- Converte o tráfego de saída de um endereço IP privado dentro da rede virtual para um endereço IP público.

### <a name = "enterprisescale"></a>Escala empresarial

 Utilize o padrão de Balanceador de carga para estruturar o Centro de dados de virtual de elevado desempenho e suportar qualquer aplicação TCP ou UDP. Utilize as instâncias VM autónoma ou até 1000 instâncias da máquina virtual de conjuntos de dimensionamento num conjunto back-end. Continue a utilizar o reencaminhamento de baixa latência, débito elevado desempenho e dimensionamento para milhões de fluxos num serviço completamente gerido do Azure.
 
Padrão de Balanceador de carga pode reencaminhar tráfego para qualquer instância VM numa rede virtual numa região. Tamanhos de conjunto back-end podem ser até 1000 instâncias com qualquer combinação dos seguintes cenários VM:

- VMs de autónomo sem conjuntos de disponibilidade
- VMs de autónomo com conjuntos de disponibilidade
- Conjuntos de dimensionamento de máquina virtual, até 1000 instâncias
- Conjuntos de dimensionamento de máquina virtual vários
- Blends de VMs e conjuntos de dimensionamento de máquina virtual

Não existe já não é um requisito para conjuntos de disponibilidade. Pode optar por utilizar conjuntos de disponibilidade para as vantagens que fornecem.

### <a name = "diagnosticinsights"></a>Informações de diagnóstico

Padrão de Balanceador de carga fornece novas funcionalidades de diagnóstico multidimensionais para configurações de Balanceador de carga internas e públicas. Estas métricas novo são fornecidas por meio do Monitor do Azure (pré-visualização) e utilizam todas as capacidades relacionadas, incluindo a capacidade para integrar com a jusante consumidores.

| Métrica | Descrição |
| --- | --- |
| Disponibilidade de VIP | Padrão de Balanceador de carga continuamente exercises o caminho de dados de dentro de uma região para o Balanceador de carga front-end para a pilha SDN que suporte o VM. Bom estado de funcionamento instâncias permanecerem, desde que a medição segue o mesmo caminho que o tráfego com balanceamento de carga da sua aplicação. O caminho de dados que é utilizado pelos seus clientes também está a ser validado. A medida é invisível à sua aplicação e não interfere com outras operações.|
| Disponibilidade do DIP | Padrão de Balanceador de carga utiliza um serviço que monitoriza o estado de funcionamento do ponto final da aplicação, de acordo com as definições de configuração de pesquisa de estado de funcionamento distribuído. Esta métrica fornece um agregado ou por ponto final-vista filtrada de cada ponto final de instância individual no balanceador de carga do agrupamento.  Pode ver como o Balanceador de carga visualiza o estado de funcionamento da sua aplicação, como indicado pela sua configuração de pesquisa do Estado de funcionamento.
| Pacotes de SIN | Padrão de Balanceador de carga não terminar ligações TCP ou interagir com fluxos de pacotes TCP ou UDP. Fluxos e os respetivos handshakes são sempre entre a origem e a instância VM. Para resolver melhor os cenários de protocolo TCP, pode efetuar a utilização de SIN pacotes para compreender quantos ligação de TCP são realizadas tentativas. A métrica reporta o número de pacotes de TCP SIN que foram recebidos. A métrica também pode apresentar os clientes que tentam estabelecer uma ligação ao seu serviço.|
| Ligações de realizar o SNAT | Padrão de Balanceador de carga reporta o número de ligações de saída que são masqueraded para o endereço IP público front-end. Portas de realizar o SNAT são um recurso exhaustible. Esta métrica pode fornecem uma indicação da forma como descontos elevados a aplicação é depender de realizar o SNAT para ligações de saída teve origem.|
| Contadores de bytes | Padrão de Balanceador de carga relatórios os dados processados por front-end.|
| Contadores de pacote | Padrão de Balanceador de carga relatórios pacotes processados por front-end.|

### <a name = "highreliability"></a>Fiabilidade elevada

Configure regras para efetuar o dimensionamento de aplicações e altamente fiáveis de balanceamento de carga. Pode configurar regras para portas individuais ou pode utilizar as portas HA para equilibrar o tráfego de todos os independentemente do número de porta TCP ou UDP.  

Pode utilizar a nova funcionalidade de portas HA para desbloquear uma variedade de cenários, incluindo elevada disponibilidade e dimensionamento para NVAs internos. A funcionalidade é útil para outros cenários onde é impractical ou indesejáveis para especificar portas individuais. Portas HA proporcionar redundância e escala, permitindo que o número de instâncias conforme necessário. A configuração já não está restrita a cenários de ativo/passivo. As configurações de sonda de estado de funcionamento proteger o seu serviço tráfego apenas para instâncias de bom estado de funcionamento de reencaminhamento.

Os fornecedores NVA podem fornecer cenários totalmente suportadas de fornecedor, resilientes para os seus clientes. O ponto único de falha é removido e várias instâncias do Active Directory são suportadas para a escala. Pode dimensionar a duas ou mais instâncias, consoante as capacidades do seu dispositivo. Contacte o fabricante NVA para orientações adicionais para estes cenários.

### <a name = "availabilityzones"></a>Zonas de disponibilidade

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Produzir resiliência da sua aplicação com a utilização das zonas de disponibilidade em regiões suportadas. Zonas de disponibilidade estão atualmente em pré-visualização em regiões específicas e requerem adicional optar ativamente por participar-in.

### <a name="automatic-zone-redundancy"></a>Redundância de zona automática

Pode escolher se Balanceador de carga deve fornecer uma zona redundante ou zonal front-end para cada uma das suas aplicações. É fácil criar redundância de zona com o padrão de Balanceador de carga. Um único endereço IP Front-end é automaticamente com redundância de zona. Uma zona com redundância de front-end é servido por todas as zonas de disponibilidade numa região em simultâneo. Um caminho de dados com redundância de zona é criado para ligações de entrada e saídas. Redundância de zona no Azure não necessita de vários endereços IP e registos DNS. 

Redundância de zona está disponível para a frente-ends de públicos ou internos. O IP privado front-end para o Balanceador de carga interno e um endereço IP público podem ser com redundância de zona.

Utilize o seguinte script para criar um endereço IP público com redundância de zona para o Balanceador de carga interno. Se estiver a utilizar modelos de Gestor de recursos existentes na sua configuração, adicione o **sku** secção para estes modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Utilize o seguinte script para criar um IP de front-end com redundância de zona para o Balanceador de carga interno. Se estiver a utilizar modelos de Gestor de recursos existentes na sua configuração, adicione o **sku** secção para estes modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Se o IP público front-end com redundância de zona, ligações de saída que são efetuadas a partir de instâncias de VM automaticamente tornar-se com redundância de zona. O front-end se encontra protegido contra a falha de zona. A alocação de portas de realizar o SNAT survives também falha de zona.

#### <a name="cross-zone-load-balancing"></a>Balanceamento de carga entre zona

Balanceamento de carga entre zona está disponível dentro de uma região para o conjunto de back-end e oferece flexibilidade máxima para as instâncias de VM. Um front-end disponibiliza fluxos em qualquer VM na rede virtual, independentemente da zona de disponibilidade da instância de VM.

Também pode especificar uma zona específica para as instâncias de front-end e back-end, alinhar o caminho de dados e recursos com uma zona específica.

Redes virtuais e sub-redes nunca estão restritos por uma zona. Definir apenas um conjunto de back-end com as instâncias de VM pretendidas e a configuração está concluída.

#### <a name="zonal-deployments"></a>Implementações zonal

Como opção, pode alinhar seu Balanceador de carga front-end para uma zona específica, definindo um zonal front-end. Um zonal front-end servidos pelo designado único disponibilidade zona apenas. Quando o front-end é combinado com zonal instâncias VM, pode alinhar recursos para zonas específicos.

Existe um endereço de IP público é sempre criado numa zona específica apenas em que zona. Não é possível alterar o horário de um endereço IP público. Para um endereço IP público que pode ser anexado aos recursos em várias zonas, crie um IP público com redundância de zona em vez disso.

Utilize o seguinte script para criar um endereço IP público zonal na disponibilidade zona 1. Se estiver a utilizar modelos de Gestor de recursos existentes na sua configuração, adicione o **sku** secção para estes modelos.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Utilize o seguinte script para criar um balanceador de carga interno front-end para 1 de zona de disponibilidade.

Se estiver a utilizar modelos de Gestor de recursos existentes na sua configuração, adicione o **sku** secção para estes modelos. Além disso, defina o **zonas** propriedade da configuração de IP Front-end para o recurso subordinado.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Adicione entre zona balanceamento de carga para o conjunto de back-end colocando as instâncias de VM que estão numa rede virtual ao conjunto.

O recurso de Balanceador de carga padrão é sempre regional e com redundância de zona onde as zonas de disponibilidade são suportadas. Pode implementar um endereço IP público ou o Balanceador de carga interno front-end que não tem uma zona atribuída em qualquer região. Suporte para as zonas de disponibilidade não afeta a capacidade de implementação. Se uma região mais tarde obtiver zonas de disponibilidade, implementado anteriormente os IPs públicos ou Balanceador de carga internos frente-ends automaticamente tornar-se com redundância de zona. Um caminho de dados com redundância de zona não implica a perda de pacotes de 0%.

### <a name = "nsg"></a>Grupos de segurança de rede

Carregar balanceador padrão e Public IP Standard totalmente carregar para a rede virtual, o que requer a utilização de grupos de segurança de rede (NSGs). Os NSGs tornam possível para o fluxo de tráfego da lista branca. Pode utilizar os NSGs para obter o controlo total sobre o tráfego para a sua implementação. Já não terá de aguardar que outros fluxos de tráfego concluir.

Associe NSGs a sub-redes ou as interfaces de rede (NICs) de instâncias de VM no conjunto de back-end. Utilize esta configuração com o padrão de Balanceador de carga e o padrão de IP público, quando é utilizado como um IP público de nível de instância. O NSG tem de ser explicitamente o tráfego que pretende permitir, por ordem para esse fluxo do tráfego da lista de permissões.

Para saber mais sobre NSGs e como aplicá-las para o seu cenário, consulte [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Ligações de saída

Padrão de Balanceador de carga fornece ligações para as VMs que estão dentro da rede virtual, quando um balanceador de carga utiliza realizar o SNAT masquerading de porta de saída. O algoritmo de realizar o SNAT masquerading de porta fornece maior robustez e escala.

Quando um recurso de Balanceador de carga público está associado com instâncias VM, cada origem de ligação de saída é foi reescrita. A origem é rescrita do espaço de endereços IP privado da rede virtual para o endereço IP público front-end de Balanceador de carga.

Quando as ligações de saída são utilizadas com uma zona com redundância de front-end, as ligações são também com redundância de zona e alocações de porta de realizar o SNAT continuam a vigorar após falha de zona.

O novo algoritmo na padrão de Balanceador de carga preallocates realizar o SNAT portas para o NIC de cada VM. Quando um NIC é adicionado ao conjunto, as portas de realizar o SNAT são preallocated com base no tamanho do conjunto. A tabela seguinte mostra os preallocations de porta de camadas de seis de tamanhos de conjunto back-end:

| Tamanho do conjunto (instâncias de VM) | Porta de realizar o SNAT preallocated |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

Portas de realizar o SNAT diretamente não convertidos para o número de ligações de saída. Uma porta de realizar o SNAT pode ser reutilizada vários destinos exclusivo. Para obter mais informações, consulte o [ligações de saída](load-balancer-outbound-connections.md) artigo.

Se o tamanho do conjunto de back-end aumenta e passa para um escalão superior, são recuperados metade do seu portas alocadas. Ligações que estão associados um tempo limite de porta recuperada e tem de ser restabelecer. Novas tentativas de ligação com êxito imediatamente. Se o tamanho do conjunto de back-end diminui e passa para um escalão inferior, aumenta o número de portas de realizar o SNAT disponíveis. Neste caso, as ligações existentes não são afetadas.

Padrão de Balanceador de carga tem uma opção de configuração adicionais que pode ser utilizada numa base por regras. Pode controlar o front-end que é utilizado para realizar o SNAT masquerading de porta quando várias-ends de frente estão disponíveis.

Quando apenas carga balanceador padrão serve instâncias de VM, ligações de saída realizar o SNAT não estão disponíveis. Pode restaurar esta capacidade explicitamente atribuindo também as instâncias de VM a um balanceador de carga público. Pode também diretamente atribuir IPs públicos como IPs públicos de nível de instância para cada instância VM. Esta opção de configuração pode ser necessária para alguns cenários de aplicação e o sistema operativo. 

### <a name="port-forwarding"></a>Reencaminhamento de porta

Básico e padrão balanceadores de carga fornece a capacidade de configurar regras NAT de entrada para mapear uma porta de front-end para uma instância de back-end individuais. Ao configurar estas regras, pode expor os pontos finais de protocolo de ambiente de trabalho remoto e os pontos finais SSH ou efetuar outros cenários de aplicação.

Padrão de Balanceador de carga continua a fornecer a capacidade de reencaminhamento da porta através de regras NAT de entrada. Quando utilizado com frente-ends com redundância de zona, regras NAT de entrada fiquem com redundância de zona e continuam a vigorar após falha de zona.

### <a name="multiple-front-ends"></a>Vários frente-ends

Configure vários frente-ends de flexibilidade de conceção quando as aplicações requerem vários endereços IP individuais de ser expostos, tais como sites TLS ou pontos finais do grupo de disponibilidade do AlwaysOn de SQL. 

Padrão de Balanceador de carga continua a fornecer vários-ends de frente onde é necessário expor um ponto final de aplicação específica num endereço IP exclusivo.

Para obter mais informações sobre como configurar vários IPs front-end, consulte [configuração de IP de vários](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Sobre SKUs

SKUs só estão disponíveis no modelo de implementação Azure Resource Manager. Esta pré-visualização introduz dois SKUs de recursos de Balanceador de carga e um IP público: básico e padrão. Os SKUs diferem no capacidades, características de desempenho, limitações e algumas comportamento intrínseco. Máquinas virtuais pode ser utilizadas com o SKU. Para obter recursos Balanceador de carga e um IP público, SKUs permanecem atributos opcionais. Quando estão omitidos SKUs numa definição de cenário, a configuração por predefinição, utiliza a SKU básico.

>[!IMPORTANT]
>O SKU de um recurso não é mutável. Não pode alterar o SKU de um recurso existente.  

### <a name="load-balancer"></a>Balanceador de Carga

O [recurso de Balanceador de carga existente](load-balancer-overview.md) torna-se a SKU básico e permanece geralmente disponível e não alterado.

SKU Standard de Balanceador de carga é novo e atualmente em pré-visualização. O 1 de Agosto de 2017, versão de API para Microsoft.Network/loadBalancers adiciona o **sku** propriedade para a definição do recurso:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Padrão de Balanceador de carga é automaticamente zona resiliente em regiões que oferecem zonas de disponibilidade. Se o Balanceador de carga foi declarado zonal, não é automaticamente zona resiliente.

### <a name="public-ip"></a>IP público

O [recurso de IP público existente](../virtual-network/virtual-network-ip-addresses-overview-arm.md) torna-se a SKU básico e permanece geralmente disponível com todas as respetivas capacidades, características de desempenho e limitações.

SKU Standard de IP público é novo e atualmente em pré-visualização. O 1 de Agosto de 2017, versão de API para Microsoft.Network/publicIPAddresses adiciona o **sku** propriedade para a definição do recurso:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Ao contrário do público IP básica, que oferece vários métodos de alocação, Public IP Standard utiliza sempre alocação estática.

Padrão de IP público é automaticamente zona resiliente em regiões que oferecem zonas de disponibilidade. Se o IP público foi declarado zonal, não é automaticamente zona resiliente. Um IP público zonal não é possível alterar a partir de uma zona para outro.

## <a name="migration-between-skus"></a>Migração entre os SKUs

SKUs não são mutável. Siga os passos nesta secção para mover de um recurso SKU para outro.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrar de básica para o Standard SKU

1. Crie um novo padrão recurso (Balanceador de carga e IPs públicos, conforme necessário). Recriar as regras e definições de pesquisa.

2. Remova os recursos de SKU básico (Balanceador de carga e IPs públicos, conforme aplicável) todas as instâncias VM. Lembre-se de que também remover todas as instâncias VM de um conjunto de disponibilidade.

3. Anexe todas as instâncias VM para os novos recursos de Standard SKU.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrar de padrão para o SKU básico

1. Crie um novo recurso básico (Balanceador de carga e IPs públicos, conforme necessário). Recriar as regras e definições de pesquisa. 

2. Remova os recursos de Standard SKU (Balanceador de carga e IPs públicos, conforme aplicável) todas as instâncias VM. Lembre-se de que também remover todas as instâncias VM de um conjunto de disponibilidade.

3. Anexe todas as instâncias VM para os novos recursos de SKU básico.

>[!IMPORTANT]
>
>Existem limitações à utilização do básico e padrão SKUs.
>
>HA portas e ao diagnóstico do Standard SKU só está disponível no Standard SKU. Não é possível migrar a partir do SKU Standard para o SKU básico e também retêm estas funcionalidades.
>
>SKUs de correspondência tem de ser utilizado para recursos de Balanceador de carga e um IP público. Não é possível ter uma mistura de recursos de SKU básico e recursos de Standard SKU. Não é possível anexar uma VM, VMs num conjunto de disponibilidade, ou um máquina virtual do conjunto de dimensionamento para ambos os SKUS em simultâneo.
>

## <a name="region-availability"></a>Disponibilidade de região

Padrão de Balanceador de carga está atualmente disponível nestas regiões:
- EUA Leste 2
- EUA Central
- Europa do Norte
- EUA Centro-Oeste
- Europa Ocidental
- Sudeste Asiático

## <a name="sku-service-limits-and-abilities"></a>Limites de serviço SKU e capacidades

Azure [limites de serviço para funcionamento em rede](https://docs.microsoft.com/en-us/azure/azure-subscription-service-limits#networking-limits) aplicam-se por região por subscrição. 

A tabela seguinte compara os limites e capacidades do Balanceador de carga básico e padrão SKUs:

| Balanceador de Carga | Básica | Standard |
| --- | --- | --- |
| Tamanho do conjunto de back-end | até 100 | até 1000 |
| Limite de conjunto back-end | Conjunto de Disponibilidade | rede virtual, a região |
| Estrutura do conjunto back-end | VMs do conjunto de disponibilidade, dimensionamento da máquina virtual definido no conjunto de disponibilidade | Qualquer instância VM na rede virtual |
| HA portas | Não suportado | Disponível |
| Diagnóstico | Limitado, pública apenas | Disponível |
| Disponibilidade de VIP  | Não suportado | Disponível |
| Mobilidade de IPs rápida | Não suportado | Disponível |
|Cenários de zonas de disponibilidade | Apenas zonal | Zonal, com redundância de zona, balanceamento de carga entre zona |
| Algoritmo de realizar o SNAT saído | Pedido | Preallocated |
| Seleção de front-end de realizar o SNAT saída | Não configurável candidatos vários | Configuração opcional para reduzir candidatos |
| Grupo de segurança de rede | Opcional numa sub-rede/NIC | Necessário |

A tabela seguinte compara os limites e capacidades do IP público básico e padrão SKUs:

| IP público | Básica | Standard |
| --- | --- | --- |
| Cenários de zonas de disponibilidade | Apenas zonal | Com redundância de zona (predefinição), zonal (opcional) | 
| Mobilidade de IPs rápida | Não suportado | Disponível |
| Disponibilidade de VIP | Não suportado | Disponível |
| Contadores | Não suportado | Disponível |
| Grupo de segurança de rede | Opcional no NIC | Necessário |


## <a name="preview-sign-up"></a>Pré-visualização inscrição

Participar na pré-visualização para SKU Standard de Balanceador de carga e o complemento SKU Standard de IP público, registe a sua subscrição.  Registar o fornecem de subscrição, que aceder a partir do PowerShell ou do Azure CLI 2.0. Para registar, execute os seguintes passos:

>[!NOTE]
>Registo da funcionalidade do padrão de Balanceador de carga pode demorar até uma hora entram em vigor global. Se pretender utilizar carga balanceador Standard com [disponibilidade zonas](https://aka.ms/availabilityzones) e [HA portas](https://aka.ms/haports), um separado inscrição é necessário para que estas pré-visualizações são. Siga as instruções para a respetivas inscrição para essas funcionalidades.

### <a name="sign-up-by-using-azure-cli-20"></a>Inscrever-se utilizando o Azure CLI 2.0

1. A funcionalidade com o fornecedor do registo:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    ```
    
2. A operação pode demorar até 10 minutos a concluir. Pode verificar o estado da operação com o seguinte comando:

    ```cli
    az feature show --name AllowLBPreview --namespace Microsoft.Network
    ```
    
    Quando o estado de registo de funcionalidade devolve 'Registada', avance para o passo seguinte:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
3. Conclua a inscrição de pré-visualização ao registar novamente a sua subscrição com o fornecedor de recursos:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    
### <a name="sign-up-by-using-powershell"></a>Inscrever-se utilizando o PowerShell

1. A funcionalidade com o fornecedor do registo:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```
    
2. A operação pode demorar até 10 minutos a concluir. Pode verificar o estado da operação com o seguinte comando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    ```

    Quando o estado de registo de funcionalidade devolve 'Registada', avance para o passo seguinte:
   
    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Conclua a inscrição de pré-visualização ao registar novamente a sua subscrição com o fornecedor de recursos:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Preços

Faturação de SKU Standard do Balanceador de carga é baseada em regras configuradas e os dados processados. Não existem custos são cobrados durante o período de pré-visualização. Para obter mais informações, consulte o [Balanceador de carga](https://aka.ms/lbpreviewpricing) e [IP público](https://aka.ms/lbpreviewpippricing) preços páginas.

Os clientes continuam a desfrutar SKU básico de Balanceador de carga, sem encargos.

## <a name="limitations"></a>Limitações

As seguintes limitações aplicam-se no momento da pré-visualização e estão sujeitos a alterações:

- Não não possível localizar instâncias de back-end de Balanceador de carga em redes virtuais em modo de peering, neste momento. Todas as instâncias de back-end tem de ser na mesma região.
- SKUs não são mutável. Não pode alterar o SKU de um recurso existente.
- Ambos os SKUs podem ser utilizados com uma autónoma VM, instâncias de VM num conjunto de disponibilidade ou conjunto de dimensionamento da máquina virtual. Combinações de VM não podem ser utilizadas em simultâneo com ambos os SKUs. Não é permitida uma configuração que contém uma mistura de SKUs.
- Desativa a utilização de um padrão de Balanceador de interno de carga com uma instância VM (ou qualquer parte de um conjunto de disponibilidade) [predefinido ligações de saída de realizar o SNAT](load-balancer-outbound-connections.md). Pode restaurar esta capacidade de VM autónoma, as instâncias de VM um conjunto de disponibilidade ou um conjunto de dimensionamento de máquina virtual. Também pode restaurar a capacidade de tornar ligações de saída. Para restaurar estas capacidades, atribua em simultâneo um padrão de Balanceador de carga público ou um padrão de IP público como um IP público de nível de instância, para a mesma instância VM. Depois de concluída a atribuição, realizar o SNAT masquerading de porta para um endereço IP público é fornecido novamente.
- Instâncias de VM poderão ter de ser agrupados em conjuntos de disponibilidade para alcançar a escala do conjunto completo de back-end. Até 150 disponibilidade conjuntos e as VMs de autónomo podem ser colocadas para um único conjunto de back-end.
- IPv6 não é suportado.
- No contexto das zonas de disponibilidade, um front-end não está mutável de zonal com redundância de zona, ou vice-versa. Depois de um front-end é criado como com redundância de zona, permanece com redundância de zona. Depois de um front-end é criado como zonal, continua a ser zonal.
- No contexto das zonas de disponibilidade, não é possível mover um endereço IP público zonal de uma zona para outro.


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [básico de Balanceador de carga](load-balancer-overview.md).
- Saiba mais sobre [disponibilidade zonas](../availability-zones/az-overview.md).
- Saiba mais sobre algumas da outra chave [capacidades de rede](../networking/networking-overview.md) no Azure.

