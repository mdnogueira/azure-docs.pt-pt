---
title: "Como atualizar um serviço em nuvem | Microsoft Docs"
description: "Saiba como atualizar serviços de nuvem no Azure. Saiba como uma atualização num serviço em nuvem prossegue para garantir a disponibilidade."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: adegeo
ms.openlocfilehash: 36d4ee9dabd39f4103d17455e47521b378af6ebb
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-update-a-cloud-service"></a>Como atualizar um serviço em nuvem

Atualizar um serviço de nuvem, incluindo as respetivas funções e o convidado SO, é um processo de três passos. Em primeiro lugar, os binários e ficheiros de configuração para o novo serviço em nuvem ou a versão do SO devem ser também carregados. Em seguida, o Azure reserva recursos de rede e computação para o serviço de nuvem com base nos requisitos da nova versão do serviço em nuvem. Por fim, o Azure executa uma atualização sem interrupção incrementalmente atualizar o inquilino para a nova versão ou do convidado SO, preservando a sua disponibilidade. Este artigo aborda os detalhes neste último passo – a atualização sem interrupção.

## <a name="update-an-azure-service"></a>Atualizar um serviço do Azure
Azure organiza as instâncias da função em agrupamentos lógicos chamados domínios de atualização (UD). Domínios de atualização (UD) são conjuntos de lógicos de instâncias de função que são atualizadas como um grupo.  Atualizações do Azure, uma nuvem de serviço um UD cada vez, que permite que as instâncias em outros UDs para continuar a funcionar de tráfego.

O número predefinido de domínios de atualização é 5. Pode especificar um número diferente de domínios de atualização, incluindo o atributo upgradeDomainCount no ficheiro de definição do serviço (. csdef). Para mais informações sobre o atributo upgradeDomainCount, consulte [WebRole esquema](https://msdn.microsoft.com/library/azure/gg557553.aspx) ou [WorkerRole esquema](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Quando efetuar uma atualização direta de uma ou mais funções no seu serviço, o Azure atualiza conjuntos de instâncias de função, de acordo com o domínio de atualização ao qual pertencem. Atualizações do Azure todas as instâncias de um determinado domínio de atualização – a parar, atualizando-as, colocando-as fazer uma cópia de online – em seguida, avança para o domínio seguinte. Parando apenas as instâncias em execução no domínio de atualização atual, o Azure certifica-se de que uma atualização, ocorre o menor impacto possível no serviço em execução. Para obter mais informações, consulte [como a atualização continua](#howanupgradeproceeds) posteriormente neste artigo.

> [!NOTE]
> Enquanto os termos de licenciamento **atualizar** e **atualizar** têm um significado ligeiramente diferente no contexto do Azure, pode ser utilizados-no alternadamente para os processos e as descrições das funcionalidades neste documento.
>
>

O serviço tem de definir, pelo menos, duas instâncias de uma função para essa função ser atualizada no local sem períodos de indisponibilidade. Se o serviço é composta por uma única instância de uma função, o serviço estará indisponível até que concluiu a atualização no local.

Este tópico inclui as seguintes informações sobre as atualizações do Azure:

* [Permitidas alterações de serviço durante uma atualização](#AllowedChanges)
* [Como uma atualização continua](#howanupgradeproceeds)
* [Reversão de uma atualização](#RollbackofanUpdate)
* [Iniciar múltiplas operações mutating uma implementação em curso](#multiplemutatingoperations)
* [Distribuição das funções em vários domínios de atualização](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Permitidas alterações de serviço durante uma atualização
A tabela seguinte mostra as alterações a um serviço permitidas durante uma atualização:

| Alterações permitidas de alojamento, serviços e funções | Atualização no local | Faseada (alternância de VIP) | Eliminar e voltar a implementar |
| --- | --- | --- | --- |
| Versão do sistema operativo |Sim |Sim |Sim |
| Nível de fidedignidade de .NET |Sim |Sim |Sim |
| Tamanho da máquina virtual<sup>1</sup> |Sim<sup>2</sup> |Sim |Sim |
| Definições de armazenamento local |Aumentar apenas<sup>2</sup> |Sim |Sim |
| Adicionar ou remover funções num serviço |Sim |Sim |Sim |
| Número de instâncias de função específica |Sim |Sim |Sim |
| Número ou tipo de pontos finais para um serviço |Sim<sup>2</sup> |Não |Sim |
| Os nomes e valores das definições de configuração |Sim |Sim |Sim |
| Os valores (mas não os nomes) das definições de configuração |Sim |Sim |Sim |
| Adicionar novos certificados |Sim |Sim |Sim |
| Alterar certificados existentes |Sim |Sim |Sim |
| Implementar o novo código |Sim |Sim |Sim |

<sup>1</sup> tamanho alteração limitada para o subconjunto de tamanhos disponíveis para o serviço em nuvem.

<sup>2</sup> requer o Azure SDK 1.5 ou versões posteriores.

> [!WARNING]
> Alterar tamanho da máquina virtual irá destruir dados locais.
>
>

Os itens seguintes não são suportados durante uma atualização:

* Alteração do nome de uma função. Remover e, em seguida, adicione a função com o novo nome.
* A alteração da contagem de domínio de atualização.
* Diminuir o tamanho dos recursos locais.

Se estiver a efetuar outras atualizações à definição do serviço, por exemplo, a diminuir o tamanho do recurso local, tem de efetuar uma atualização de alternância de VIP em vez disso. Para obter mais informações, consulte [comutação implementação](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Como uma atualização continua
Pode decidir se pretende atualizar todas as funções no seu serviço ou uma única função no serviço. Em ambos os casos, todas as instâncias de cada função que está a ser atualizado e que pertencem ao primeiro domínio de atualização são paradas, atualizadas e novamente colocadas online. Assim que estiverem novamente online, as instâncias do segundo domínio de atualização são paradas, atualizadas e novamente colocadas online. Um serviço em nuvem pode ter no máximo uma atualização do Active Directory a uma hora. A atualização é sempre executada relativamente a versão mais recente do serviço de nuvem.

O diagrama seguinte ilustra a forma como a atualização continua se estiver a atualizar todas as funções de serviço:

![Atualizar o serviço](media/cloud-services-update-azure-service/IC345879.png "atualizar o serviço")

Este diagrama seguinte ilustra a forma como a atualização continua se estiver a atualizar uma única função:

![Atualizar função](media/cloud-services-update-azure-service/IC345880.png "função atualização")  

Durante uma atualização automática, o controlador de recursos de infraestrutura do Azure avalia periodicamente o estado de funcionamento do serviço de nuvem para determinar quando é seguro guiá-lo a UD seguinte. Esta avaliação de estado de funcionamento é executada numa base por função e considera apenas instâncias na versão mais recente (ou seja, instâncias de UDs que já tenham sido walked). Verifica que um número mínimo de instâncias de função, para cada função, tem de atingir um Estado terminal satisfatório.

### <a name="role-instance-start-timeout"></a>Tempo limite de início de instância de função
O controlador de recursos de infraestrutura será Aguarde 30 minutos para cada instância de função ao atingir um Estado iniciado. Se a duração de limite de tempo decorrido, o controlador de recursos de infraestrutura continuará walking para a instância de função seguinte.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Atualiza o impacto para os dados de unidade durante o serviço em nuvem

Ao atualizar um serviço a partir de uma única instância para várias instâncias do serviço será reencaminhado para baixo enquanto a atualização é executada devido aos serviços de atualizações do Azure de forma. A disponibilidade de serviço de guaranteeing de contrato de nível do serviço só se aplica aos serviços que são implementados com mais do que uma instância. A lista seguinte descreve a forma como os dados em cada unidade são afetados por cada cenário de atualização de serviço do Azure:

|Cenário|Unidade C|Unidade D|Unidade de i|
|--------|-------|-------|-------|
|Reinício VM|Preservados|Preservados|Preservados|
|Reinício de portal|Preservados|Preservados|Destruído|
|Portal recriação de imagem|Preservados|Destruído|Destruído|
|Atualização no local|Preservados|Preservados|Destruído|
|Migração de nó|Destruído|Destruído|Destruído|

Tenha em atenção que, na lista acima, a unidade e representa a unidade de raiz da função e não deve ser hard-coded. Em alternativa, utilize o **% RoleRoot %** variável de ambiente para representar a unidade.

Para minimizar o período de indisponibilidade quando atualizar um serviço de instância única, implemente um novo serviço de várias instâncias para o servidor de teste e efetuar uma alternância de VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Reversão de uma atualização
O Azure oferece flexibilidade para gerir serviços durante uma atualização, permitindo-lhe iniciar operações adicionais num serviço, após o pedido de atualização inicial é aceite pelo controlador de recursos do Azure. Só é possível efetuar uma reversão, quando uma atualização (alteração de configuração) ou a atualização está no **em curso** estado na implementação. Uma atualização ou a atualização é considerada em curso, desde que não há, pelo menos, uma instância do serviço que ainda não foi atualizado para a nova versão. Para testar se é permitida uma reversão, verifique o valor do sinalizador RollbackAllowed, devolvido pelo [obter implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [obter propriedades de serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx) operações, está definido como true.

> [!NOTE]
> Apenas faz sentido chamar reversão um **no local** de atualização porque as atualizações de alternância de VIP envolvem substituir uma instância em execução toda do seu serviço com o outro.
>
>

A reversão de uma atualização em curso tem os seguintes efeitos na implementação:

* Quaisquer instâncias de função que ainda não tinham sido atualizadas ou atualizadas para a nova versão não são atualizadas ou atualizadas, porque as instâncias já estão a executar a versão de destino do serviço.
* Quaisquer instâncias de função que já tinham sido atualizadas ou atualizadas para a nova versão do pacote de serviço (\*. cspkg) ou a configuração do serviço de ficheiro (\*. cscfg) ficheiro (ou ambos os ficheiros) são revertidos para a versão pré-atualização destes ficheiros.

Isto é funcionalmente fornecido pelas seguintes funcionalidades:

* O [reversão atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) operação, o que pode ser chamada para uma atualização da configuração (acionada chamando [alterar a configuração de implementação](https://msdn.microsoft.com/library/azure/ee460809.aspx)) ou uma atualização (acionada chamando [ Atualizar implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx)), desde que não há, pelo menos, uma instância de serviço que ainda não foi atualizado para a nova versão.
* O elemento de está bloqueado e o elemento de RollbackAllowed, que são devolvidas como parte do corpo de resposta a [obter implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [obter propriedades de serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx) operações:

  1. O elemento de está bloqueado permite-lhe detetar quando uma operação mutating pode ser invocada numa implementação especificada.
  2. O elemento de RollbackAllowed permite-lhe detetar quando o [reversão atualização ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) operação pode ser chamada para uma determinada implementação.

  Para efetuar uma reversão, não é necessário que verificar o está bloqueado e os elementos de RollbackAllowed. -Sufixos para confirmar que RollbackAllowed está definido como true. Estes elementos apenas são devolvidos se estes métodos são invocados utilizando o cabeçalho do pedido definido como "x-ms-version: 2011-10-01" ou uma versão posterior. Para obter mais informações sobre cabeçalhos de controlo de versões, consulte [controlo de versões do serviço de gestão](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Existem algumas situações em que uma reversão de uma atualização ou não é suportada a atualização, estes são os seguintes:

* Redução de recursos locais - se a atualização aumenta os recursos locais para uma função de plataforma do Azure não permite a reversão.
* Limitações de quota - se a atualização foi uma escala para baixo da operação poderá já não tem quota suficiente de computação para concluir a operação de reversão. Cada subscrição do Azure tem uma quota associada que especifica o número máximo de núcleos que pode ser utilizada por todos os serviços alojados que pertencem a essa subscrição. Se efetuar uma reversão de uma actualização de determinado seria colocar a sua subscrição através de quota, em seguida, que uma reversão não será ativada.
* Condição provável de antecipação - se a atualização inicial foi concluída, não é possível uma reversão.

Um exemplo de quando a reversão de uma atualização pode ser útil é se estiver a utilizar o [atualizar implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx) operação no modo manual para controlar a velocidade a que uma atualização no local principal para o seu Azure o serviço alojado é implementada.

Durante a implementação da atualização chamar [atualizar implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx) no modo manual e começar a guiá-lo de domínios de atualização. Se em algum momento, a atualização, para monitorizar, tenha em atenção algumas instâncias de função nos domínios de atualização do primeiro que é examinar tem deixar de responder, pode chamar o [reversão atualização ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) operação na implementação, o que deixará inalterados as instâncias que ainda não tinham sido atualizadas e as instâncias de reversão que tinham sido atualizadas para o anterior pacote de serviço e a configuração.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciar múltiplas operações mutating uma implementação em curso
Em alguns casos poderá iniciar múltiplas operações mutating simultâneas numa implementação em curso. Por exemplo, pode efetuar uma atualização de serviço e, enquanto que a atualização está a ser implementada em todo o serviço, o que pretende efetuar algumas alterações, por exemplo, para implementar a atualização anterior, aplicar uma atualização diferente ou mesmo eliminar a implementação. Caso em que esta situação pode ser necessária é se uma atualização do serviço contém código buggy que faz com que uma instância de função atualizado a falhar repetidamente. Neste caso, o controlador de recursos de infraestrutura do Azure não será possível efetuar o progresso na aplicação que atualizar, porque um número insuficiente de instâncias no domínio atualizado está em bom estado. Este estado é referido como um *bloqueada implementação*. Pode unstick a implementação, a reverter a atualização ou aplicar uma nova atualização ao longo da parte superior a falha de um.

Depois do pedido inicial para o serviço de atualização foi recebido pelo controlador de recursos de infraestrutura do Azure, pode começar a operações a mutação subsequentes. Ou seja, não dispõe de espera para a operação inicial seja concluída antes de poder iniciar outra operação mutating.

Iniciar uma segunda operação de atualização durante a primeira atualização está em curso efetuará semelhante para a operação de reversão. Se a atualização segundo estiver no modo automático, o primeiro domínio de atualização será atualizado imediatamente, possivelmente à esquerda para instâncias de vários domínios de atualização estar offline ao mesmo ponto no tempo.

As operações mutating são os seguintes: [alterar a configuração de implementação](https://msdn.microsoft.com/library/azure/ee460809.aspx), [atualizar implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx), [estado de implementação de atualização](https://msdn.microsoft.com/library/azure/ee460808.aspx), [eliminar a implementação ](https://msdn.microsoft.com/library/azure/ee460815.aspx), e [reversão atualização](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Duas operações, [obter implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [obter propriedades de serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx), devolver o sinalizador de está bloqueado que pode ser examinado para determinar se uma operação mutating pode ser invocada numa implementação especificada.

Para chamar a versão destes métodos devolve o sinalizador de está bloqueado, tem de definir o cabeçalho do pedido "x-ms-version: 2011-10-01" ou uma posterior. Para obter mais informações sobre cabeçalhos de controlo de versões, consulte [controlo de versões do serviço de gestão](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição das funções em vários domínios de atualização
Azure distribui instâncias de uma função uniformemente por um número definido de domínios de atualização, que pode ser configurado como parte do serviço (. csdef) do ficheiro de definição. O número máximo de domínios de atualização é 20 e a predefinição é 5. Para obter mais informações sobre como modificar o ficheiro de definição de serviço, consulte [esquema de definição de serviço do Azure (. csdef ficheiro)](cloud-services-model-and-package.md#csdef).

Por exemplo, se a sua função tiver dez instâncias, por predefinição cada domínio de atualização contém duas instâncias. Se a sua função tem 14 instâncias, em seguida, quatro dos domínios de atualização contêm três instâncias e um domínio quinto contém dois.

Domínios de atualização são identificados com um índice baseado em zero: o primeiro domínio de atualização tem o ID de 0 e o segundo domínio de atualização tem o ID de 1 e assim sucessivamente.

O diagrama seguinte ilustra como um serviço que contém duas funções são distribuídas quando o serviço define dois domínios de atualização. O serviço está em execução oito instâncias de função da web e nove instâncias da função de trabalho.

![Distribuição de domínios de atualização](media/cloud-services-update-azure-service/IC345533.png "distribuição de domínios de atualização")

> [!NOTE]
> Tenha em atenção que o Azure controla como instâncias são alocadas em domínios de atualização. Não é possível especificar as instâncias estão atribuídas à qual o domínio.
>
>

## <a name="next-steps"></a>Passos seguintes
[Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)  
[Como monitorizar serviços Cloud](cloud-services-how-to-monitor.md)  
[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)  
