---
title: "Administração de serviço para a Azure Search no portal do Azure"
description: "Gerir a Azure Search, um serviço de pesquisa em nuvem alojado no Microsoft Azure, utilizando o portal do Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: c87d1fdd-b3b8-4702-a753-6d7e29dbe0a2
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: 916a08aacca428530bc4f728d5de422e04bed8bc
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="service-administration-for-azure-search-in-the-azure-portal"></a>Administração de serviço para a Azure Search no portal do Azure
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> * [SDK do .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.search)
> * [Python](https://pypi.python.org/pypi/azure-mgmt-search/0.1.0)> 

A pesquisa do Azure é um serviço completamente gerido, baseado na nuvem de pesquisa utilizado para criar uma experiência de pesquisa avançada em aplicações personalizadas. Este artigo abrange o *service administração* tarefas que pode efetuar no [portal do Azure](https://portal.azure.com) para um serviço de pesquisa tenha sido já aprovisionada. *Serviço de administração* é simples por predefinição, limitada até as seguintes tarefas:

* Gerir e proteger o acesso ao *chaves de api* utilizado para leitura ou de acesso de escrita ao seu serviço.
* Ajuste a capacidade de serviço alterando a atribuição de partições e réplicas.
* Monitorizar a utilização de recursos, relativo aos limites máximos de camada de serviços.

Repare que *atualizar* não está indicada como uma tarefa administrativa. Porque os recursos são atribuídos quando o serviço é aprovisionado, a mudança para uma camada diferente exige um novo serviço. Para obter mais informações, consulte [criar um serviço da Azure Search](search-create-service-portal.md).

> [!Tip]
> Está à procura de ajuda sobre como analisar o desempenho de tráfego ou consulta de pesquisa? Ganhos aprofundadas volume de consulta, quais as pessoas de termos de pesquisa para, e os resultados da pesquisa como êxito são ao servir pelos clientes a documentos específicos no seu índice. Para obter orientações, consulte [análise de tráfego de pesquisa do Azure Search](search-traffic-analytics.md), [monitorizar métricas de utilização e a consulta](search-monitor-usage.md), e [desempenho e a otimização de](search-performance-optimization.md).

<a id="admin-rights"></a>

## <a name="administrator-rights"></a>Direitos de administrador
Aprovisionamento ou desativar o próprio serviço pode ser efetuada por um administrador de subscrição do Azure ou coadministrador.

Dentro de um serviço, qualquer pessoa com acesso para o URL do serviço e uma chave de api de administrador tem acesso de leitura / escrita para o serviço. Acesso de leitura e escrita fornece a capacidade de adicionar, eliminar ou modificar objetos de servidor, incluindo as chaves de api, índices, indexadores, origens de dados, as agendas e atribuições de funções conforme implementado através do [funções do RBAC](#rbac).

Todos os interação do utilizador com a Azure Search fica dentro destes modos: acesso de leitura / escrita para o serviço (direitos de administrador) ou acesso só de leitura para o serviço (direitos de consulta). Para obter mais informações, consulte [gerir as chaves de api](#manage-keys).

<a id="sys-info"></a>

## <a name="set-rbac-roles-for-administrative-access"></a>Definir funções do RBAC para acesso administrativo
O Azure oferece uma [modelo de autorização baseada em funções globais](../active-directory/role-based-access-control-configure.md) para todos os serviços geridos através do portal ou APIs do Resource Manager. Funções de proprietário, leitor e contribuinte determinam o nível de administração do serviço para os utilizadores, grupos e atribuídos a cada direito de principais de segurança do Active Directory. 

Para a Azure Search, permissões de RBAC determinam as seguintes tarefas administrativas:

| Função | Tarefa |
| --- | --- |
| Proprietário |Criar ou eliminar o serviço ou de qualquer objeto no serviço, incluindo as chaves de api, índices, indexadores, origens de dados de indexador e agendas de indexador.<p>Ver o estado do serviço, incluindo contagens e tamanho de armazenamento.<p>Adicionar ou eliminar o membro da função (apenas um proprietário pode gerir a associação de função).<p>Os administradores da subscrição e proprietários de serviço tem associação automática na função de proprietários. |
| Contribuinte |Mesmo nível de acesso como proprietário, menos a gestão de funções RBAC. Por exemplo, um contribuinte pode ver e voltar a gerar `api-key`, mas não é possível modificar as associações de função. |
| Leitor |Visualizar chaves de consulta e de estado do serviço. Os membros desta função não é possível alterar a configuração do serviço, nem pode ver a chaves de administração. |

Funções não conceda direitos de acesso ao ponto final do serviço. Pesquisa operações de serviço, tal como gestão de índice, população do índice e consultas em dados de pesquisa, são controladas através de chaves de api, funções não. Para obter mais informações, consulte "Autorização para gestão versus operações de dados" no [que é o controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md).

<a id="secure-keys"></a>
## <a name="logging-and-system-information"></a>Informações de registo e sistema
A pesquisa do Azure não expõe os ficheiros de registo para um serviço individual ou através do portal ou interfaces programáticas. Na camada básica e superior, Microsoft monitoriza todos os serviços de pesquisa do Azure para disponibilidade de 99,9% por contratos de nível de serviço (SLA). Se o serviço estiver lento ou débito de pedidos está abaixo de limiares de SLA, as equipas de suporte rever os ficheiros de registo disponíveis e resolver o problema.

Em termos de informações gerais sobre o serviço, pode obter as informações das seguintes formas:

* No portal, no dashboard do serviço, através de notificações, propriedades e as mensagens de estado.
* Utilizar [PowerShell](search-manage-powershell.md) ou [API de REST de gestão](https://docs.microsoft.com/rest/api/searchmanagement/) para [obter propriedades de serviço](https://docs.microsoft.com/rest/api/searchmanagement/services), ou o estado na utilização dos recursos de índice.
* Através de [pesquisar a análise de tráfego](search-traffic-analytics.md), conforme indicado anteriormente.

<a id="manage-keys"></a>

## <a name="manage-api-keys"></a>Gerir chaves de api
Todos os pedidos para um serviço de pesquisa tem uma chave de api que foi gerada especificamente para o seu serviço. Esta chave de api é o mecanismo único para a autenticação de acesso para o ponto final de serviço de pesquisa. 

Uma chave de api é uma cadeia composta por letras e números gerados aleatoriamente. Através de [permissões RBAC](#rbac), pode eliminar ou ler as chaves, mas não é possível substituir uma chave com uma palavra-passe definida pelo utilizador. 

Dois tipos de chaves são utilizados para aceder ao seu serviço de pesquisa:

* Admin (válido para todas as operações de leitura e escrita contra o serviço)
* Consulta (válida para operações só de leitura como consultas em relação a um índice)

Uma chave de api de administração é criada quando o serviço está aprovisionado. Existem duas chaves de administração, designadas como *primário* e *secundário* mantê-los diretamente, mas na realidade são permutáveis. Cada serviço tem duas chaves de administração para que pode implementar um através de sem perderem o acesso ao seu serviço. Pode voltar a gerar a chave de administrador, mas não é possível adicionar a contagem de chave de administrador total. Não há um máximo de duas chaves de administração por serviço de pesquisa.

Chaves de consulta foram concebidas para as aplicações cliente que chamam diretamente a pesquisa. Pode criar até 50 chaves de consulta. No código da aplicação, especifique o URL de pesquisa e uma chave de api de consulta para permitir o acesso só de leitura para o serviço. O código da aplicação também especifica o índice utilizado pela sua aplicação. Em conjunto, o ponto final, um índice de destino e uma chave de api para acesso só de leitura, definem o nível de acesso e o âmbito da ligação da sua aplicação de cliente.

Para obter ou voltar a gerar chaves de api, abra o dashboard de serviço. Clique em **chaves** para abrir gradualmente a página de gestão de chaves. Comandos para voltar a gerar ou criação de chaves estão na parte superior da página. Por predefinição, são criadas apenas as chaves de administração. Chaves de api de consulta tem de ser criadas manualmente.

 ![][9]

<a id="rbac"></a>

## <a name="secure-api-keys"></a>Proteger chaves de api
Segurança de chave é certificar-se ao restringir o acesso através do portal ou interfaces de Gestor de recursos (PowerShell ou interface de linha de comandos). Conforme indicado, os administradores da subscrição podem ver e voltar a gerar chaves de api todos os. Como precaução, reveja as atribuições de funções para compreender quem tem acesso às chaves de administração.

1. No dashboard de serviço, clique no ícone de acesso para abrir gradualmente o painel de utilizadores.
   ![][7]
2. Os utilizadores, reveja as atribuições de funções existente. Conforme esperado, os administradores da subscrição já tem acesso total para o serviço através da função de proprietário.
3. Para obter mais, clique em **administradores da subscrição** e, em seguida, expanda a lista de atribuição de função para ver quem tem os direitos de administração conjunta no seu serviço de pesquisa.

Ver as permissões de acesso de outra forma consiste em clicar em **funções** no painel de utilizadores. Se o fizer, apresenta as funções disponíveis e o número de utilizadores ou grupos atribuídos a cada função.

<a id="sub-5"></a>

## <a name="monitor-resource-usage"></a>Monitorizar a utilização de recursos
No dashboard de monitorização de recursos está limitada às informações mostradas no dashboard do serviço e métricas alguns que pode obter consultando o serviço. No dashboard do serviço, na secção de utilização, pode determinar rapidamente se os níveis de recursos de partição são adequadas para a sua aplicação.

Utilizando a API de REST do serviço de pesquisa, pode obter uma contagem em índices e documentos através de programação: 

* [Obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)
* [Contagem de documentos](https://docs.microsoft.com/rest/api/searchservice/count-documents)

## <a name="disaster-recovery-and-service-outages"></a>Falhas de serviço e de recuperação de desastre

Embora iremos pode salvage os dados, da Azure Search não fornece instantânea ativação pós-falha do serviço se houver uma falha ao nível do centro do cluster ou de dados. Se falhar um cluster no Centro de dados, a equipa de operações irá detetar e de trabalho para restaurar o serviço. Irá ocorrer um período de indisponibilidade durante o restauro de serviço. Pode pedir os créditos de serviço para compensar para a indisponibilidade do serviço pelo [contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Se for necessária em caso de falhas catastrófica fora do controlo da Microsoft contínua do serviço, foi [aprovisionar um serviço adicional](search-create-service-portal.md) numa região diferente e implementar uma estratégia de georreplicação para garantir que os índices são totalmente redundantes em todos os serviços.

Os clientes que utilizam [indexadores](search-indexer-overview.md) preencher e atualizar os índices pode processar a recuperação após desastre através de indexadores georreplicação específicos tirar partido da mesma origem de dados. Dois serviços em regiões diferentes, cada um indexador em execução foi índice da mesma origem de dados para alcançar a redundância geográfica. Se estiver a indexação de origens de dados que também são georredundante, lembre-se de que os indexadores de pesquisa do Azure só podem efetuar a indexação incremental de réplicas primárias. Um evento de ativação pós-falha, lembre-se de que aponte novamente o indexador para a nova réplica primária. 

Se não utilizar indexadores, utilizaria dados nos serviços de pesquisa diferentes e de código da aplicação para objetos de push em paralelo. Para obter mais informações, consulte [desempenho e a Otimização da Azure Search](search-performance-optimization.md).

## <a name="backup-and-restore"></a>Cópia de segurança e restauro

Porque a Azure Search não é uma solução de armazenamento de dados principal, não podemos fornecer um mecanismo formal self-service a cópia de segurança e restauro. O código da aplicação utilizado para criar e preencher um índice é a opção de restauro de facto, se eliminar um índice por engano. 

Para reconstruir um índice, teria de eliminá-lo (partindo do princípio de que existe), recrie o índice no serviço e recarregar ao obter dados a partir do seu arquivo de dados principal. Em alternativa, pode aceder à [suporte ao cliente]() para índices residual se existir uma falha regional.


<a id="scale"></a>

## <a name="scale-up-or-down"></a>Aumentar ou reduzir verticalmente
Cada serviço de pesquisa começa com um mínimo de uma réplica e uma partição. Se iniciou a cópia de segurança para um [escalão que fornece recursos dedicados](search-limits-quotas-capacity.md), clique em de **escala** mosaico no dashboard de serviço para ajustar a utilização de recursos.

Quando adicionar capacidade através de qualquer um dos recursos, o serviço de as utilizar automaticamente. Não é necessária nenhuma ação adicional da sua parte, mas não existe um ligeiro atraso antes do impacto do novo recurso é realizado. Pode demorar 15 minutos ou mais para aprovisionar os recursos adicionais.

 ![][10]

### <a name="add-replicas"></a>Adicionar réplicas
Aumentar consultas por segundo (QPS) ou alcançar a elevada disponibilidade é feito adicionando as réplicas. Cada réplica tem uma cópia de um índice, para que adicionar uma réplica mais traduz-se um índice mais disponível para processar pedidos de consulta do serviço. Um mínimo de 3 réplicas são necessários para elevada disponibilidade (consulte [planeamento de capacidade](search-capacity-planning.md) para obter detalhes).

Um serviço de pesquisa ter mais de réplicas pode carregar saldo os pedidos de consulta através de um grande número de índices. Tendo em conta um nível de volume de consulta, débito de consulta vai ser mais rápida quando existirem mais cópias do índice disponível para processar o pedido. Se ocorrerem latência de consulta, que pode esperar um impacto positivo no desempenho, uma vez que as réplicas adicionais estão online.

Embora o débito de consulta fica cópias de segurança à medida que adiciona réplicas, não precisamente duplo ou tripla à medida que as réplicas que adiciona ao seu serviço. Todas as aplicações de pesquisa estão sujeitos a fatores externos que podem impinge no desempenho das consultas. Consultas complexas e a latência de rede são dois fatores contribuem para variações no tempo de resposta de consulta.

### <a name="add-partitions"></a>Adicionar partições
A maioria das aplicações de serviço têm uma necessidade incorporada de mais réplicas em vez de partições. Para os casos em que é necessária uma contagem de documentos maior, pode adicionar partições se se inscreveu no serviço padrão. O escalão básico fornecem para partições adicionais.

No escalão Standard, as partições são adicionadas em múltiplos de 12 (especificamente, 1, 2, 3, 4, 6 ou 12). Este é um artefacto de fragmentação. É criado um índice em 12 shards, que podem todos ser armazenadas na partição de 1 ou igualmente divididos em 2, 3, 4, 6 ou 12 partições (um ID de partição horizontal por partição).

### <a name="remove-replicas"></a>Remover as réplicas
Após os períodos de volumes de consulta elevado, pode reduzir as réplicas depois de cargas de consulta de pesquisa tem normalizado (por exemplo, depois de vendas feriado são).

Para tal, regresse o controlo de deslize de réplica para um número inferior. Não existem não existem passos adicionais necessários da sua parte. Reduzir a contagem de réplica relinquishes máquinas virtuais no Centro de dados. As operações de ingestão de dados e a consulta irão agora ser executada em VMs menos que antes. O limite mínimo é uma réplica.

### <a name="remove-partitions"></a>Remova partições
Em contraste com réplicas, a remover que não necessita de nenhum esforço adicional da sua parte, poderá ter alguns cálculos para fazer se estiver a utilizar o armazenamento mais do que pode ser reduzido. Por exemplo, se a sua solução utilizar três partições, downsizing para uma ou duas partições irá gerar um erro se o novo espaço de armazenamento é menor que o necessário. Como seria de esperar, as suas escolhas estão a eliminar índices ou documentos dentro de um índice associado para libertar espaço ou manter a configuração atual.

Não há nenhum método de deteção que indica que shards índice são armazenados em partições específicas. Cada partição fornece cerca de 25 GB no armazenamento, por isso terá de reduzir o armazenamento para um tamanho que pode ser acomodado pelo número de partições que tem. Se pretender reverter para uma partição, todos os 12 shards terá de ajustar.

Para ajudar a planear futuros, pode querer verifique armazenamento (utilizando [obter estatísticas de índice](https://docs.microsoft.com/rest/api/searchservice/Get-Index-Statistics)) para ver a quantidade realmente utilizado. 

<a id="advanced-deployment"></a>

## <a name="best-practices-on-scale-and-deployment"></a>Melhores práticas em escala e implementação
Este vídeo de 30 minutos revê as melhores práticas para cenários de implementação avançadas, incluindo geo-distribuição cargas de trabalho. Também pode ver [desempenho e a Otimização da Azure Search](search-performance-optimization.md) para páginas de ajuda que abrangem os mesmos pontos.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<a id="next-steps"></a>

## <a name="next-steps"></a>Passos seguintes
Assim que compreender os conceitos atrás de administração do serviço, considere a utilização de [PowerShell](search-manage-powershell.md) para automatizar tarefas.

Também recomendamos que reveja o [desempenho e a otimização de artigo](search-performance-optimization.md).

Outra recomenda-se ver o vídeo indicado na secção anterior. Fornece mais profunda cobertura de técnicas mencionados nesta secção.

<!--Image references-->
[7]: ./media/search-manage/rbac-icon.png
[8]: ./media/search-manage/Azure-Search-Manage-1-URL.png
[9]: ./media/search-manage/Azure-Search-Manage-2-Keys.png
[10]: ./media/search-manage/Azure-Search-Manage-3-ScaleUp.png



