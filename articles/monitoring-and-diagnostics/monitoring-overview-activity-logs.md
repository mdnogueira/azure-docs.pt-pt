---
title: "Descrição geral do registo de atividade do Azure | Microsoft Docs"
description: "Saiba o que é o registo de atividade do Azure e como pode utilizá-lo a compreender de eventos ocorridos na sua subscrição do Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: johnkem
ms.openlocfilehash: 4a796920d5ff76d4ff4d41afe2ec14aa89ae2265
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Monitorizar a atividade de subscrição com o registo de atividade do Azure
O **registo de atividade do Azure** é um registo de subscrição que fornece informações sobre os eventos de nível de subscrição ocorridos no Azure. Isto inclui uma série de dados, a partir dos dados operacionais do Azure Resource Manager para atualizações de eventos de estado de funcionamento do serviço. O registo de atividade era anteriormente conhecido como "Registos de auditoria" ou "Registos operacionais," dado que os eventos de controlo plane de relatórios de categoria administrativa para as suas subscrições. Utilizar o registo de atividade, poderá determinar o ' que, quem e quando ' para quaisquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição de escrita. Também pode compreender o estado da operação e outras propriedades relevantes. O registo de atividade não incluir operações de leitura (GET) ou as operações para recursos que utilizam clássica / modelo "RDFE".

![Atividade registos vs outros tipos de registos ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Figura 1: Registos de atividade vs outros tipos de registos

O registo de atividade é diferente do [os registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md). Registos de atividade fornecem dados sobre as operações num recurso do exterior (o "plane de controlo"). Registos de diagnóstico são emitidos por um recurso e fornecem informações sobre o funcionamento do recurso de (o "plane de dados").

Pode obter eventos a partir do seu registo de atividade no portal do Azure, CLI, cmdlets do PowerShell e a API de REST de Monitor do Azure.


> [!WARNING]
> O registo de atividade do Azure é principalmente para atividades que ocorrem no Gestor de recursos do Azure. Controlar recursos utilizando o modelo clássico/RDFE. Alguns tipos de recurso clássico tem um fornecedor de recursos de proxy no Azure Resource Manager (por exemplo, Microsoft. classiccompute). Se o utilizador interage com um tipo de recurso clássica através do Azure Resource Manager utilizando estes fornecedores de recursos de proxy, as operações de aparecem no registo de atividade. Se interagir com um tipo de recurso clássico clássica portal ou de outra forma fora proxies de Gestor de recursos do Azure, as suas ações só são registadas no registo de operação. O registo de operações pode ser navegado numa secção separada do portal.
>
>

Veja o vídeo seguinte introduzindo o registo de atividade.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="categories-in-the-activity-log"></a>Categorias de registo de atividade
O registo de atividade contém várias categorias de dados. Para obter detalhes completos na esquemas destas categorias, [consulte este artigo](monitoring-activity-log-schema.md). Estas incluem:
* **Administrativos** -esta categoria contém o registo de todos os criar, as operações de atualização, eliminação e a ação executada através do Resource Manager. Os exemplos dos tipos de eventos que poderá ver nesta categoria incluem "criar a máquina virtual" e "Eliminar grupo de segurança" cada ação executada por um utilizador ou aplicação utilizando o Gestor de recursos é modelada como uma operação num tipo de recurso específico. Se o tipo de operação é escrita, Delete ou ação, os registos de início e de êxito ou falha dessa operação é registadas no ficheiro a categoria administrativa. A categoria administrativa também inclui todas as alterações para o controlo de acesso baseado em funções numa subscrição.
* **Estado de funcionamento do serviço** -esta categoria contém o registo de qualquer incidentes de estado de funcionamento do serviço ocorridas no Azure. Um exemplo do tipo de evento que vir esta categoria é de "Azure SQL Server nos EUA Leste estão a ocorrer um período de indisponibilidade." Eventos do serviço do Estado de funcionamento são fornecidos em cinco varieties: ação necessária, recuperação assistido, incidentes, manutenção, informações ou segurança e surgem apenas se tiver um recurso na subscrição que seria afetada pelo evento.
* **Alerta** -esta categoria contém o registo de todas as ativações de alertas do Azure. Um exemplo do tipo de evento que vir esta categoria é "% de CPU no myVM foi superior a 80 para os últimos 5 minutos." Um conceito de alerta de ter uma variedade de sistemas do Azure – pode definir uma regra de algumas ordenação e receber uma notificação quando as condições corresponderem dessa regra. Sempre que um tipo de alerta do Azure suportado 'ativa,' ou as condições são cumpridas para gerar uma notificação, um registo da ativação também é enviado para esta categoria do registo de atividade.
* **Dimensionamento automático** -esta categoria contém o registo de quaisquer eventos relacionados com a operação do motor de dimensionamento automático com base em quaisquer definições de dimensionamento automático que definiu na sua subscrição. Um exemplo do tipo de evento que vir esta categoria é "Escala de dimensionamento automático ação falhou". Utilizar o dimensionamento automático, pode automaticamente aumentar ou reduzir horizontalmente em número de instâncias de um tipo de recurso suportados com base na hora do dia e/ou carga dados (métricos) através de uma definição de dimensionamento automático. Quando as condições são cumpridas para escala ou reduzir verticalmente, o início e de eventos com êxito ou falha são gravadas nesta categoria.
* **Recomendação** -esta categoria contém eventos de recomendação de determinados tipos de recursos, tais como web sites e servidores SQL. Estes eventos oferecem recomendações sobre como utilizar melhor os recursos. Eventos deste tipo receber apenas se tiver recursos que emitir recomendações.
* **Segurança** -esta categoria contém o registo de quaisquer alertas gerados pelo centro de segurança do Azure. Um exemplo do tipo de evento que vir esta categoria é "ficheiro de extensão duplo suspeita executado".
* **Política e o estado de funcionamento do recurso** -destas categorias não contêm quaisquer eventos; estão reservados para utilização futura.

## <a name="event-schema-per-category"></a>Esquema de eventos por categoria
[Consulte este artigo para compreender o esquema de eventos de registo de atividades por categoria.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>O que pode fazer com o registo de atividade
Seguem-se algumas das ações que pode fazer com o registo de atividade:

![Registo de atividade do Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Consultar e vê-la no **portal do Azure**.
* [Crie um alerta num evento de registo de atividade.](monitoring-activity-log-alerts.md)
* [Transmitir para um **Hub de eventos** ](monitoring-stream-activity-logs-event-hubs.md) para ingestão por um serviço independente ou uma solução de análise personalizada, tais como o Power BI.
* Analisá-lo no Power BI utilizando o [ **pacote de conteúdos do Power BI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Guardá-lo para um **conta de armazenamento** de inspeção de arquivo ou manual](monitoring-archive-activity-log.md). Pode especificar o tempo (em dias) de retenção utilizando o **registo perfil**.
* Consultá-lo através do Cmdlet do PowerShell, a CLI ou a REST API.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Consultar o registo de atividade no portal do Azure
No portal do Azure pode ver o registo de atividades em vários locais:
* O **painel do registo de atividade**, que pode aceder ao procurar o registo de atividade em "Mais serviços" no painel de navegação esquerdo.
* O **painel Monitor**, que é apresentado por predefinição no painel de navegação esquerdo. O registo de atividade é uma secção deste painel de monitorização do Azure.
* Qualquer recurso **painel de recursos**, por exemplo, o painel de configuração para uma Máquina Virtual. O registo de atividade está a ser uma das secções na maioria nestes painéis de recursos e clicar nela automaticamente filtra os eventos para os relacionados com esse recurso específico.

No portal do Azure, pode filtrar o registo de atividade por estes campos:
* TimeSpan - a hora de início e de fim de eventos.
* Categoria - a categoria de evento, conforme descrito acima.
* Subscrição - um ou mais nomes de subscrição do Azure.
* Grupo de recursos – grupos de recursos de um ou mais dentro nessas subscrições.
* Recursos (nome) - o nome de um recurso específico.
* Tipo de recurso - o tipo de recurso, por exemplo, Microsoft.Compute/virtualmachines.
* Nome da operação - o nome de uma operação do Gestor de recursos do Azure, por exemplo, Microsoft.SQL/servers/Write.
* Gravidade - o nível de gravidade do evento (informativo, aviso, erro, crítico).
* Evento iniciado por ' - ' o 'chamador' ou o utilizador que executou a operação.
* Abra pesquisa - esta é uma caixa de pesquisa de texto aberto que procura essa cadeia em todos os campos em todos os eventos.

Depois de ter definido um conjunto de filtros, pode guardá-lo como uma consulta que é persistente entre sessões, se alguma vez precisar de efetuar a mesma consulta com os filtros aplicados novamente no futuro. Também pode afixar uma consulta ao dashboard do Azure sempre atento sobre eventos específicos.

Clicar em "Aplicar" é executada a consulta e mostrar todos os eventos correspondentes. Clicar em qualquer evento na lista mostra o resumo de que o evento, bem como o JSON completa em bruto de que o evento.

Para ainda mais energia, pode clicar no **pesquisa registo** ícone, que apresenta os dados de registo de atividade no [solução de análise de registo de atividade de análise de registos](../log-analytics/log-analytics-activity.md). O painel de registo de atividade oferece uma experiência de filtro básico/procurar nos registos, mas a análise de registos permite-lhe dinâmico, consultar e visualizar os dados das formas mais poderosas.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportar o registo de atividade com um perfil de registo
A **registo perfil** controla a forma como o registo de atividade é exportado. Utilizar um perfil de registo, pode configurar:

* Em que o registo de atividade deve ser enviado (conta de armazenamento ou Event Hubs)
* As categorias de eventos (ação de escrita, eliminar,) devem ser enviadas. *O significado dos "categoria" nos perfis de registo e eventos de registo de atividade é diferente. No perfil de registo, "Categoria" representa o tipo de operação (ação de escrita, eliminar,). Um evento de registo de atividade, a propriedade "categoria" representa a origem ou o tipo de evento (por exemplo, administração, ServiceHealth, alerta e mais).*
* Em que regiões (localizações) devem ser exportados. Certifique-se incluir "global," conforme muitos eventos no registo de atividade são globais.
* Quanto o registo de atividade deve ser mantido na conta de armazenamento.
    - Uma retenção de zero dias significa que os registos são mantidos indefinidamente. Caso contrário, o valor pode ser qualquer número de dias entre 1 e 2147483647.
    - Se as políticas de retenção estão definidas, mas armazenar os registos numa conta do Storage está desativada (por exemplo, se apenas as opções de Event Hubs ou OMS estão selecionadas), as políticas de retenção não tem qualquer efeito.
    - As políticas de retenção são aplicada por-dia, no fim do dia (UTC), registos a partir do dia em que é agora a retenção política são eliminadas. Por exemplo, se tiver uma política de retenção de um dia, no início do dia de hoje os registos de ontem de antes do dia seriam eliminados.

Pode utilizar um armazenamento conta ou event hub espaço de nomes que não se encontra na mesma subscrição que aquele emitir os registos. O utilizador que configura a definição tem de ter o acesso adequado do RBAC para ambas as subscrições.

Estas definições podem ser configuradas através da opção "Export" no painel do registo de atividade no portal. Estes também podem ser configuradas através de programação [utilizando a API de REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), cmdlets do PowerShell, ou a CLI. Uma subscrição só pode ter um perfil de registo.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configurar perfis de registo no portal do Azure
Pode transmitir o registo de atividade para um Hub de eventos ou armazená-las numa conta do Storage utilizando a opção "Export" no portal do Azure.

1. Navegue para o **registo de atividade** painel utilizando o menu no lado esquerdo do portal.

    ![Navegue até ao registo de atividade no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Clique em de **exportar** botão na parte superior do painel.

    ![Botão de exportação no portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. No painel que aparece, pode selecionar:  
  * regiões para o qual pretende exportar eventos
  * a conta de armazenamento para o qual gostaria de guardar eventos
  * o número de dias que pretenda manter estes eventos no armazenamento. Uma definição de 0 dias mantém os registos indefinidamente.
  * o Service Bus espaço de nomes no qual gostaria de um Hub de eventos seja criado para estes eventos de transmissão em fluxo.

     ![Painel de registo de atividade de exportação](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Clique em **guardar** para guardar estas definições. As definições são imediatamente ser aplicadas à sua subscrição.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configurar perfis de registo utilizando os Cmdlets do PowerShell do Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de registo existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Adicionar um perfil de registo
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| Nome |Sim |Nome do perfil do registo. |
| StorageAccountId |Não |ID de recurso da conta do Storage para o qual deverá ser guardado o registo de atividade. |
| serviceBusRuleId |Não |ID de regra de barramento de serviço para o espaço de nomes do Service Bus que gostaria de ter os event hubs criados no. É uma cadeia com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Localizações |Sim |Lista separada por vírgulas das regiões para as quais pretende recolher eventos de registo de atividade. |
| retentionInDays |Sim |Número de dias para que eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente (indefinidamente). |
| Categorias |Não |Lista separada por vírgulas das categorias de evento que deve ser recolhidas. Os valores possíveis são escrita, a eliminação e a ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configurar perfis de registo utilizando a CLI de plataforma do Azure
#### <a name="get-existing-log-profile"></a>Obter o perfil de registo existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
O `name` propriedade deve ser o nome do perfil do registo.

#### <a name="add-a-log-profile"></a>Adicionar um perfil de registo
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propriedade | Necessário | Descrição |
| --- | --- | --- |
| nome |Sim |Nome do perfil do registo. |
| storageId |Não |ID de recurso da conta do Storage para o qual deverá ser guardado o registo de atividade. |
| serviceBusRuleId |Não |ID de regra de barramento de serviço para o espaço de nomes do Service Bus que gostaria de ter os event hubs criados no. É uma cadeia com este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| localizações |Sim |Lista separada por vírgulas das regiões para as quais pretende recolher eventos de registo de atividade. |
| retentionInDays |Sim |Número de dias para que eventos devem ser mantidos, entre 1 e 2147483647. Um valor de zero armazena os registos indefinidamente (indefinidamente). |
| Categorias |Não |Lista separada por vírgulas das categorias de evento que deve ser recolhidas. Os valores possíveis são escrita, a eliminação e a ação. |

#### <a name="remove-a-log-profile"></a>Remover um perfil de registo
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>Passos Seguintes
* [Saiba mais sobre o registo de atividade (anteriormente os registos de auditoria)](../azure-resource-manager/resource-group-audit.md)
* [Fluxo de registo de atividade do Azure para os Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
