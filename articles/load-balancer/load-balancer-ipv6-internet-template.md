---
title: "Implementar um balanceador de carga com acesso à Internet com o IPv6 - modelo Azure | Microsoft Docs"
description: Como implementar o suporte de IPv6 para o Azure Load Balancer e as VMs com balanceamento de carga.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-resource-manager
keywords: "IPv6, o Balanceador de carga do azure, pilha dupla, ip público, ipv6 nativo, móveis, iot"
ms.assetid: 2998e943-13fc-4ea9-a68c-875e53a08db3
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 90439d792eac618671a9de9938302d8930c986d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>Implementar um acesso à Internet Balanceador de carga solução com o IPv6 através de um modelo

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [CLI do Azure](load-balancer-ipv6-internet-cli.md)
> * [Modelo](load-balancer-ipv6-internet-template.md)

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Um balanceador de carga do Azure é um balanceador de carga de Camada 4 (TCP, UDP). O balanceador de carga fornece elevada disponibilidade, ao distribuir o tráfego de entrada entre instâncias de serviço com bom estado de funcionamento nos serviços cloud ou máquinas virtuais num conjunto de balanceador de carga. O Balanceador de Carga do Azure pode também apresentar esses serviços em várias portas, vários endereços IP ou ambos.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama seguinte ilustra a solução de balanceamento de carga a ser implementado utilizando o modelo de exemplo descrito neste artigo.

![Cenário do Balanceador de carga](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

Neste cenário, irá criar os seguintes recursos do Azure:

* interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuído
* um balanceador de carga com acesso à Internet com o endereço IP público de IPv6 e IPv4
* duas regras para mapear os VIPs públicos para os pontos finais privados de balanceamento de carga
* um conjunto de disponibilidade que contém as duas VMs
* duas máquinas virtuais (VMs)

## <a name="deploying-the-template-using-the-azure-portal"></a>Implementar o modelo utilizando o portal do Azure

Este artigo faz referência a um modelo que é publicado no [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) galeria. Pode transferir o modelo a partir da galeria ou iniciar a implementação no Azure diretamente a partir da galeria. Este artigo pressupõe que já tenha transferido o modelo para o seu computador local.

1. Abra o portal do Azure e inicie sessão com uma conta que tenha permissões para criar as VMs e recursos de rede dentro de uma subscrição do Azure. Além disso, a menos que estiver a utilizar recursos existentes, a conta necessita da permissão para criar um grupo de recursos e uma conta de armazenamento.
2. Clique em "+ nova" partir do menu, em seguida, o tipo "modelo" na caixa de pesquisa. Selecione "Implementação do modelo" os resultados da pesquisa.

    ![LB ipv6-portal step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. Em tudo, o painel, clique em "Implementação do modelo".

    ![LB-ipv6-portal-passo 3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. Clique em "Criar".

    ![LB ipv6-portal step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. Clique em "Editar o modelo". Elimine o conteúdo existente e copiar/colar em todo o conteúdo do ficheiro de modelo (para incluir o início e fim {}), em seguida, clique em "Guardar".

    > [!NOTE]
    > Se estiver a utilizar o Microsoft Internet Explorer, quando, colar recebem uma caixa de diálogo a pedir-lhe para permitir o acesso para a área de transferência do Windows. Clique em "Permitir o acesso."

    ![LB ipv6-portal step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. Clique em "Editar parâmetros". No painel parâmetros, especifique os valores de orientação na secção de parâmetros de modelo, em seguida, clique em "Guardar" para fechar o painel de parâmetros. No painel implementação personalizada, selecione a sua subscrição, um grupo de recursos existente ou criar um. Se estiver a criar um grupo de recursos, em seguida, selecione uma localização para o grupo de recursos. Em seguida, clique em **termos legais**, em seguida, clique em **Compra** para os termos legais. Azure começa a implementar os recursos. Demora alguns minutos para implementar todos os recursos.

    ![LB ipv6-portal step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    Para obter mais informações sobre estes parâmetros, consulte o [modelo parâmetros e variáveis](#template-parameters-and-variables) secção neste artigo.

7. Para ver os recursos criados pelo modelo, clique em Procurar, desloque para baixo a lista até ver "Grupos de recursos", em seguida, clique no mesmo.

    ![LB ipv6-portal step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. No painel de grupos de recursos, clique no nome do grupo de recursos que especificou no passo 6. É apresentada uma lista de todos os recursos que foram implementadas. Se todos os correu bem, deverá a indicar "Com êxito" em "Última implementação". Se não, certifique-se de que a conta que está a utilizar tem permissões para criar os recursos necessários.

    ![LB ipv6-portal step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > Se procurar os grupos de recurso imediatamente depois de concluir o passo 6, "Última implementação de" apresentará o estado de "Implementar" enquanto os recursos estão a ser implementados.

9. Clique em "myIPv6PublicIP" na lista de recursos. Pode ver que tem um endereço IPv6 no endereço IP e de que o respetivo nome DNS é o valor especificado para o parâmetro dnsNameforIPv6LbIP no passo 6. Este recurso se encontra o IPv6 anfitrião e endereço nome público que esteja acessível aos clientes de Internet.

    ![LB ipv6-portal step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>Valide a conectividade

Quando o modelo foi implementado com êxito, pode validar a conectividade, efetuando as seguintes tarefas:

1. Inicie sessão no portal do Azure e ligar a cada uma das VMs criadas pela implementação do modelo. Se tiver implementado uma VM do Windows Server, execute o ipconfig/todas as numa linha de comandos. Verá que as VMs têm endereços IPv4 e IPv6. Se tiver implementado as VMs do Linux, terá de configurar o SO Linux para receber dinâmicos endereços IPv6 utilizando as instruções fornecidas para a distribuição de Linux.
2. A partir de um cliente ligado à IPv6 Internet, inicie uma ligação para o endereço IPv6 público do Balanceador de carga. Para confirmar que o Balanceador de carga é balanceamento entre as duas VMs, pode instalar um servidor web, como os serviços de informação de Internet do Microsoft (IIS) em cada uma das VMs. A página da web predefinido em cada servidor foi contêm o texto "Server0" ou "Servidor1" para identificar exclusivamente. Em seguida, abra um browser de Internet num cliente ligados à IPv6 Internet e navegue para o nome do anfitrião especificado para o parâmetro dnsNameforIPv6LbIP do Balanceador de carga para confirmar a conetividade IPv6 de ponto a ponto para cada VM. Se apenas vir a página web de apenas um servidor, poderá ter limpar a cache do browser. Abra várias sessões de navegação privadas. Deverá ver uma resposta de cada servidor.
3. A partir de um cliente ligado à Internet do IPv4, inicie uma ligação para o endereço IPv4 público do Balanceador de carga. Para confirmar que o Balanceador de carga é duas VMs de balanceamento de carga, foi de teste utilizando o IIS, conforme detalhado no passo 2.
4. Cada VM, inicie uma ligação de saída para um dispositivo de IPv6 ou IPv4 ligados à Internet. Em ambos os casos, o IP de origem visto pelo dispositivo de destino é o endereço IPv4 ou IPv6 público do Balanceador de carga.

> [!NOTE]
> ICMP para IPv4 e IPv6 está bloqueado na rede do Azure. Como resultado, ferramentas ICMP como sempre um ping falharem. Para testar a conectividade, utilize uma alternativa TCP como TCPing ou o cmdlet Test-NetConnection do PowerShell. Tenha em atenção que os endereços IP mostrada no diagrama são exemplos de valores que poderá ver. Uma vez que os endereços IPv6 estão atribuídos dinamicamente, os endereços recebidos serão diferentes e podem variar consoante a região. Além disso, é comum para o endereço IPv6 público no balanceador de carga para começar com um prefixo diferentes que os endereços IPv6 privados no conjunto de back-end.

## <a name="template-parameters-and-variables"></a>Os parâmetros do modelo e as variáveis

Um modelo Azure Resource Manager contém várias variáveis e os parâmetros que pode personalizar para as suas necessidades. As variáveis são utilizadas para os valores de fixos que não pretende que um utilizador para alterar. Os parâmetros são utilizados para valores que pretende que um utilizador para fornecer ao implementar o modelo. O modelo de exemplo está configurado para o cenário descrito neste artigo. Pode personalizá-la para as necessidades do seu ambiente.

O modelo de exemplo utilizado neste artigo inclui os parâmetros e variáveis seguintes:

| Parâmetro / variável | Notas |
| --- | --- |
| adminUsername |Especifique o nome da conta de administrador utilizada para iniciar sessão para as máquinas virtuais com. |
| adminPassword |Especifique a palavra-passe para a conta de administrador utilizada para iniciar sessão para as máquinas virtuais com. |
| dnsNameforIPv4LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como o nome público do Balanceador de carga. Este nome é resolvido para o endereço de IPv4 público do Balanceador de carga. O nome tem de ser em minúsculas e corresponder o regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. |
| dnsNameforIPv6LbIP |Especifique o nome de anfitrião DNS que pretende atribuir como o nome público do Balanceador de carga. Este nome é resolvido para o endereço de IPv6 público do Balanceador de carga. O nome tem de ser em minúsculas e corresponder o regex: ^ [a-z][a-z0-9-]{1,61}[a-z0-9]$. Isto pode ser o mesmo nome que o endereço IPv4. Quando um cliente envia uma consulta DNS para este nome de Azure irá devolver de A e AAAA regista quando o nome é partilhado. |
| vmNamePrefix |Especifique o prefixo de nome VM. O modelo acrescenta um número (0, 1, etc.) para o nome quando são criadas as VMs. |
| nicNamePrefix |Especifique o prefixo de nome de interface de rede. O modelo acrescenta um número (0, 1, etc.) para o nome quando são criadas as interfaces de rede. |
| storageAccountName |Introduza o nome de uma conta de armazenamento existente ou especifique o nome de um novo a ser criado pelo modelo. |
| availabilitySetName |Em seguida, introduza nome da conjunto de disponibilidade para ser utilizado com as VMs |
| addressPrefix |O prefixo de endereço utilizado para definir o intervalo de endereços da rede Virtual |
| subnetName |O nome da sub-rede na criado a vnet |
| subnetPrefix |O prefixo de endereço utilizado para definir o intervalo de endereços da sub-rede |
| vnetName |Especifique o nome para a VNet utilizado por VMs. |
| ipv4PrivateIPAddressType |O método de alocação utilizado para o endereço IP privado (estático ou dinâmico) |
| ipv6PrivateIPAddressType |O método de alocação utilizado para o endereço IP privado (dinâmico). IPv6 só suporta a alocação dinâmica. |
| numberOfInstances |O número de instâncias com balanceamento de carga implementado pelo modelo |
| ipv4PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço IPv4 público do Balanceador de carga. |
| ipv4PublicIPAddressType |O método de alocação utilizado para o endereço IP público (estático ou dinâmico) |
| Ipv6PublicIPAddressName |Especifique o nome DNS que pretende utilizar para comunicar com o endereço de IPv6 público do Balanceador de carga. |
| ipv6PublicIPAddressType |O método de alocação utilizado para o endereço IP público (dinâmico). IPv6 só suporta a alocação dinâmica. |
| lbName |Especifique o nome do Balanceador de carga. Este nome é apresentado no portal ou utilizado ao fazer referência a ela com um comando da CLI ou PowerShell. |

As variáveis restantes no modelo contém valores derivados são atribuídos quando o Azure cria os recursos. Não altere essas variáveis.
