---
title: "IT Service conector de gestão na análise de registos do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral de TI serviço de gestão. o conector (ITSMC) e informações sobre como utilizar esta solução para monitorizar e gerir o ITSM centralmente itens de trabalho na análise de registos do OMS e resolva os problemas rapidamente."
services: log-analytics
documentationcenter: 
author: JYOTHIRMAISURI
manager: riyazp
editor: 
ms.assetid: 0b1414d9-b0a7-4e4e-a652-d3a6ff1118c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: v-jysur
ms.openlocfilehash: bd384255b3c46b3ae88b1269ab26e0ddaa6f6e77
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="centrally-manage-itsm-work-items-using-it-service-management-connector-preview"></a>Gerir centralmente a itens de trabalho ITSM utilizando o conector de gestão de serviços de TI (pré-visualização)

![Símbolo do conector de gestão do serviço IT](./media/log-analytics-itsmc/itsmc-symbol.png)

O conector de gestão do serviço de TI (ITSMC) fornece uma integração bidirecional entre um produtos/serviços de gestão de serviço de TI (ITSM) e análise de registos suportados.  Através desta ligação, pode criar incidentes, alertas ou eventos no produto ITSM com base na análise de registos alertas, registos ou alertas do Azure. O conector também importa dados, tais como incidentes e pedidos de alteração do produto ITSM para análise de registos do OMS.

Com ITSMC, pode:

  - Alertas operacionais integram-se a sua práticas de gestão de incidentes na ferramenta de ITSM à sua escolha.
    - Crie itens de trabalho (por exemplo, o alerta do evento, incidente) no ITSM de alertas do OMS e através de pesquisa de registo.
    - Crie itens de trabalho com base nos alertas de registo de atividade do Azure através da ação de ITSM em grupos de ação.

  - Unificar a monitorização, registo e dados de gestão de serviço utilizados em toda a organização.
    - Correlacionar incidente e dados de pedido de alteração do seu ITSM ferramentas com dados de registo relevantes na área de trabalho de análise de registos.   
    - Ver os dashboards de nível superior para uma descrição geral sobre incidentes, pedidos de alteração e sistemas afetados.
    - Escreva consultas de análise de registos para obter informações sobre os dados de gestão de serviço.

## <a name="adding-the-it-service-management-connector-solution"></a>Adicionar o IT Service a solução de gestão de conector

Adicionar a solução de conector de gestão de serviços de TI para o seu espaço de trabalho de análise de registos, utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).

Eis o mosaico ITSMC como ver na Galeria de soluções:

![mosaico de conector](./media/log-analytics-itsmc/itsmc-solutions-tile.png)

Após a adição com êxito, verá que o conector de gestão do serviço de TI em **OMS** > **definições** > **origens ligadas.**

![ITSMC ligado](./media/log-analytics-itsmc/itsmc-overview-solution-in-connected-sources.png)

> [!NOTE]

> Por predefinição, o ITSMC atualiza os dados da ligação uma vez em cada 24 horas. Para atualizar os dados da ligação de forma instantânea para qualquer modelo ou as edições de atualizações que efetuar, clique no botão de "Atualizar" apresentado junto a ligação.

 ![Atualização ITSMC](./media/log-analytics-itsmc/itsmc-connection-refresh.png)


## <a name="configuring-the-itsmc-connection-with-your-itsm-productsservices"></a>Configurar a ligação de ITSMC com os produtos/serviços ITSM

ITSMC suporta ligações a **System Center Service Manager**, **ServiceNow**, **Provance**, e **Cherwell**.

Utilize os seguintes procedimentos conforme aplicável para si:

- [O System Center Service Manager (SCSM)](log-analytics-itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-oms)

- [ServiceNow](log-analytics-itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-oms)

- [Provance](log-analytics-itsmc-connections.md#connect-provance-to-it-service-management-connector-in-oms)  

- [Cherwell](log-analytics-itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-oms)

## <a name="using-the-solution"></a>Utilizar a solução

Assim que tiver configurado o conector, inicia a recolha de dados de produtos/serviços ITSM ligado. Dependendo do número de incidentes e pedidos de alteração de ITSM produtos/serviços, a sincronização inicial deve ser concluída dentro de alguns minutos.

> [!NOTE]
> - Dados importados a partir do produto ITSM pela solução ITSMC aparecem na análise de registos, como registos do tipo **ServiceDesk_CL**.
> - Registo contém um campo com o nome **ServiceDeskWorkItemType_s**, que é um incidente ou pedido de alteração, os dois tipos de dados importados a partir do produto ITSM.

## <a name="data-synced-from-itsm-product"></a>Dados sincronizados a partir do produto ITSM
Incidentes e pedidos de alteração são sincronizados a partir do seu produto ITSM à sua área de trabalho de análise de registos.
As seguintes informações mostram exemplos de dados recolhidos pelo ITSMC:

> [!NOTE]

> Dependendo do tipo de item de trabalho importados para análise de registos, **ServiceDesk_CL** contém os seguintes campos:

**Item de trabalho:** **incidentes**  
ServiceDeskWorkItemType_s = "Incidente"

**Campos**

- ServiceDeskConnectionName
- ID do serviço de suporte técnico
- Estado
- Urgência
- Impacto
- Prioridade
- Escalamento
- Criado Por
- Resolvido pelo
- Fechada pelo
- Origem
- Atribuído a
- Categoria
- Título
- Descrição
- Data de criação
- Data de fecho
- Data de resolução
- Data da última modificação
- Computador


**Item de trabalho:** **pedidos de alteração**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Campos**
- ServiceDeskConnectionName
- ID do serviço de suporte técnico
- Criado Por
- Fechada pelo
- Origem
- Atribuído a
- Título
- Tipo
- Categoria
- Estado
- Escalamento
- Estado de conflito
- Urgência
- Prioridade
- Risco
- Impacto
- Atribuído a
- Data de criação
- Data de fecho
- Data da última modificação
- Data de pedido
- Data de início planeada
- A ser planeada a data de fim
- Data de início do trabalho
- Data de fim de trabalho
- Descrição
- Computador

## <a name="output-data-for-a-servicenow-incident"></a>Dados de saída para um incidente de ServiceNow

| Campo do OMS | Campo ITSM |
|:--- |:--- |
| ServiceDeskId_s| Número |
| IncidentState_s | Estado |
| Urgency_s |Urgência |
| Impact_s |Impacto|
| Priority_s | Prioridade |
| CreatedBy_s | Abrir por |
| ResolvedBy_s | Resolvido pelo|
| ClosedBy_s  | Fechada pelo |
| Source_s| Tipo de contacto |
| AssignedTo_s | Atribuído a  |
| Category_s | Categoria |
| Title_s|  Breve descrição |
| Description_s|  Notas |
| CreatedDate_t|  Aberturas |
| ClosedDate_t| Fechado|
| ResolvedDate_t|Resolvido|
| Computador  | item de configuração |

## <a name="output-data-for-a-servicenow-change-request"></a>Pedido de alteração de dados de saída para um ServiceNow

| Campo do OMS | Campo ITSM |
|:--- |:--- |
| ServiceDeskId_s| Número |
| CreatedBy_s | Pedido por |
| ClosedBy_s | Fechada pelo |
| AssignedTo_s | Atribuído a  |
| Title_s|  Breve descrição |
| Type_s|  Tipo |
| Category_s|  Categoria |
| CRState_s|  Estado|
| Urgency_s|  Urgência |
| Priority_s| Prioridade|
| Risk_s| Risco|
| Impact_s| Impacto|
| RequestedDate_t  | Pedido por data |
| ClosedDate_t | Data de fecho |
| PlannedStartDate_t  |     Data de início planeada |
| PlannedEndDate_t  |   Data de fim planeada |
| WorkStartDate_t  | Data de início real |
| WorkEndDate_t | Data de fim real|
| Description_s | Descrição |
| Computador  | Item de configuração |

**Ecrã de análise de registos de exemplo para dados ITSM:**

![Ecrã de análise do registo](./media/log-analytics-itsmc/itsmc-overview-sample-log-analytics.png)

## <a name="itsmc-integration-with-other-oms-solutions"></a>Integração de ITSMC com outras soluções do OMS

Conector ITSM atualmente suporta a integração com a solução de mapa de serviço.

Mapa de serviço automaticamente Deteta os componentes da aplicação em sistemas Windows e Linux e mapeia a comunicação entre os serviços. Permite-lhe ver os servidores que acha que deles – como interligados sistemas que fornecem serviços críticos. Mapa de serviço mostra as ligações entre servidores, processos, e portas em qualquer arquitetura TCP ligados sem qualquer configuração necessária à instalação de um agente.

Obter mais informações: [mapa de serviço](../operations-management-suite/operations-management-suite-service-map.md).

Se também utilizar a solução de mapa de serviço, pode ver os itens de suporte técnico de serviço criados nas soluções ITSM conforme mostrado no exemplo seguinte:

![Integração de ServiceMap](./media/log-analytics-itsmc/itsmc-overview-integrated-solutions.png)
## <a name="create-itsm-work-items-for-oms-alerts"></a>Criar itens de trabalho ITSM para alertas do OMS

Com a solução ITSMC no local, pode configurar alertas do OMS para acionar a criação de itens de trabalho na sua ferramenta ITSM ligado. Utilize o seguinte procedimento:

1. De **pesquisa registo** janela, executar uma consulta de pesquisa de registo para ver os dados. Os resultados da consulta são a origem para itens de trabalho.
2. No **pesquisa registo**, clique em **alerta** para abrir o **Adicionar regra de alerta** página.

    ![Ecrã de análise do registo](./media/log-analytics-itsmc/itsmc-work-items-for-oms-alerts.png)

3. No **Adicionar regra de alerta** janela, forneça os detalhes necessários para **nome**, **gravidade**, **consulta de pesquisa**, e **alerta critérios** (medida janela/métrica de tempo).
4. Selecione **Sim** para **ITSM ações**.
5. Selecione a ligação ITSM a partir de **selecione ligação** lista.
6. Forneça os detalhes conforme necessário.
7. Para criar um item de trabalho separada para cada entrada de registo deste alerta, selecione o **criar itens de trabalho individuais para cada entrada de registo** caixa de verificação.

    Ou

    Deixe esta caixa de verificação desmarcada para criar apenas um item de trabalho para qualquer número de entradas de registo neste alerta.

7. Clique em **Guardar**.

O alerta do OMS criou pode ser visto em **definições**>**alertas**. Itens de trabalho a ligação ITSM correspondentes são criados quando condição o alerta especificado for satisfeita.

## <a name="create-itsm-work-items-from-oms-logs"></a>Criar itens de trabalho ITSM a partir de registos do OMS

Também pode criar itens de trabalho nas origens ITSM ligadas diretamente a partir de um registo. Utilize o seguinte procedimento:

1. De **pesquisa registo**, procure os dados necessários, selecione os detalhes e clique em **Criar item de trabalho**.

    O **Criar Item de trabalho ITSM** surge a janela:

    ![Ecrã de análise do registo](media/log-analytics-itsmc/itsmc-work-items-from-oms-logs.png)

2.   Adicione os seguintes detalhes:

  - **Título do item de trabalho**: título para o item de trabalho.
  - **Descrição do item de trabalho**: uma descrição para o novo item de trabalho.
  - **Afetados computador**: nome do computador em que estes dados de registo foi encontrados.
  - **Selecione a ligação**: ligação ITSM no qual pretende criar este item de trabalho.
  - **Item de trabalho**: tipo de item de trabalho.

3. Para utilizar um modelo de item de trabalho existente para um incidente, clique em **Sim** em **item de trabalho gerar baseado num modelo** opção e, em seguida, clique em **criar**.

    Ou,

    Clique em **não** se pretender fornecer os valores personalizados.

4. Forneça os valores adequados no **contacte tipo**, **impacto**, **urgência**, **categoria**, e **Sub categoria** caixas de texto e, em seguida, clique em **criar**.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Criar itens de trabalho ITSM a partir dos alertas do Azure

ITSMC está integrado com grupos de ação.

[Grupos de ação](../monitoring-and-diagnostics/monitoring-action-groups.md) proporcionam uma forma modular e reutilizável de a acionar ações para os alertas do Azure. Ao utilizar a ação de ITSM em grupos de ação, pode criar itens de trabalho no seu produto ITSM que tenha uma ligação à solução de conector ITSM existente.

Utilize o seguinte procedimento:

1. No portal do Azure, clique em **Monitor**.
2. No painel esquerdo, clique em **grupos ação**. O **adicionar grupo de ação** surge a janela.

    ![Grupos de Ação](media/log-analytics-itsmc/action-groups.png)

3. Fornecer **nome** e **ShortName** para o grupo de ação. Selecione o **grupo de recursos** e **subscrição** onde pretende criar o grupo de ação.

    ![Detalhe de grupos de ação](media/log-analytics-itsmc/action-groups-details.png)

4. Na lista de ações, selecione **ITSM** no menu pendente para **tipo de ação**. Forneça um **nome** para a ação e clique em **editar detalhes**.
5. Selecione o **subscrição** onde está localizada a sua área de trabalho de análise de registos. Selecione o **ligação** name (nome do seu conector ITSM), seguido pelo nome da sua área de trabalho. Por exemplo, "MyITSMMConnector(MyWorkspace)".

    ![Detalhes de ação de ITSM](./media/log-analytics-itsmc/itsm-action-details.png)

6. Selecione **Item de trabalho** tipo a partir do menu pendente.
   Optar por utilizar um modelo existente ou preencher os campos necessários pelo seu produto ITSM.
7. Clique em **OK**.

Quando a criação/edição de uma regra de alerta do Azure, utilize um grupo de ação, que tem uma ação de ITSM. Quando o alerta é acionado, o item de trabalho é criado na ferramenta de ITSM.

>[!NOTE]

> Atualmente, apenas alertas de registo de atividade suportam a ação de ITSM, alertas outras do Azure não aceita a situação.


## <a name="troubleshoot-itsm-connections-in-oms"></a>Resolver problemas de ligações de ITSM no OMS
1.  Se a ligação falhar da IU da origem ligados com um **erro na ligação de guardar** da mensagem, siga os passos seguintes:
 - Para ligações de ServiceNow, Cherwell e Provance,-certifique-se corretamente introduziu o nome de utilizador, palavra-passe, ID de cliente e segredo do cliente para cada uma das ligações.
        -Verifique se tem privilégios suficientes no produto ITSM correspondente para efetuar a ligação.
 - Para ligações do Service Manager,-certifique-se de que a aplicação Web é implementada com êxito e é criada a ligação híbrida. Para verificar a ligação é estabelecida com êxito com a máquina do Service Manager no local, visite o URL da aplicação Web como detalhadas da documentação para efetuar o [da ligação híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection).

2.  Se não está a obter sincronizados dados a partir do ServiceNow ao Log Analytics, certifique-se de que o ServiceNow instância não está suspenso. Instâncias de desenvolvimento do ServiceNow, por vezes, aceda a suspensão quando inativo durante um longo período. Relatório de outra forma, o problema.
3.  Se OMS alertas acionados mas funcionar itens não são criados no produto ITSM ou itens de configuração não são criados/ligados a itens de trabalho ou para outras informações genéricos, procure nos seguintes locais:
 -  ITSMC: A solução mostra um resumo ligações/computador de trabalho itens/etc. Clique em Mostrar o mosaico **estado do conector**, que demora a **pesquisa registo** com a consulta relevante. Observe os registos de registo com LogType_S como erro para obter mais informações.
 - **Registo de pesquisa** página: ver as informações relacionadas com erros/diretamente utilizando a consulta *tipo = ServiceDeskLog_CL*.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Resolver problemas de implementação de aplicação de Web do Service Manager
1.  Em caso de problemas de implementação de aplicações web, certifique-se de que tem permissões suficientes na subscrição mencionada para criar/implementar recursos.
2.  Se obtiver um **"Referência não foi definida para a instância de um objeto de objeto"** Ocorreu um erro ao executar o [script](log-analytics-itsmc-service-manager-script.md), certifique-se de que introduziu valores válidos em **configuração do utilizador** secção .
3.  Se falhar criar o espaço de nomes do service bus reencaminhamento, certifique-se de que o fornecedor de recursos necessários está registado na subscrição. Se não registado, crie manualmente o espaço de nomes do service bus reencaminhamento do portal do Azure. Também pode criá-la ao [criar a ligação híbrida](log-analytics-itsmc-connections.md#configure-the-hybrid-connection) do portal do Azure.


## <a name="contact-us"></a>Contacte-nos

Para consultas ou comentários sobre o conector de gestão do serviço de TI, contacte-nos [ omsitsmfeedback@microsoft.com ](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passos seguintes
[Adicionar ITSM produtos/serviços para o conector de gestão do serviço de TI](log-analytics-itsmc-connections.md).
