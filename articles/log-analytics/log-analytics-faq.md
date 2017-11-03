---
title: "Iniciar análise FAQ | Microsoft Docs"
description: "Respostas a perguntas mais frequentes sobre o serviço de análise de registos do Azure."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: magoedte
ms.openlocfilehash: 0b27386cd0f9f3ae50314b8c5d7708aea3e3d028
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-faq"></a>FAQ do Log Analytics
Estas FAQ Microsoft é uma lista de perguntas mais comuns sobre a análise de registos no Microsoft Azure. Se tiver alguma questão adicional sobre a análise de registos, vá para o [fórum de discussão](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) e publique as suas perguntas. Quando uma pergunta é colocada frequentemente, adicionamo-la a este artigo para que possam ser localizada forma rápida e fácil.

## <a name="general"></a>Geral

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>Q. A análise de registos utiliza o mesmo agente como o Centro de segurança do Azure?

A. No precoce de Junho de 2017, o Centro de segurança do Azure começou a utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, consulte [FAQ de migração de plataforma do Centro de segurança do Azure](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>Q. São efetuadas as verificações do AD e soluções de avaliação do SQL Server?

A. A consulta seguinte mostra uma descrição de todas as verificações executadas atualmente:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Os resultados podem ser exportados para o Excel para revisão adicional.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>P: por que razão vejo algo diferente do OMS na consola do System Center Operations Manager?

R: dependendo do que Update Rollup do Operations Manager estiver numa, poderá ver um nó para *System Center Advisor*, *das informações operacionais*, ou *Log Analytics*.

A atualização de cadeia de texto para *OMS* está incluído num pacote de gestão, que tem de ser importado manualmente. Para ver o texto atual e a funcionalidade, siga as instruções no artigo mais recente do System Center Operations Manager Update Rollup KB e atualize a consola.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>P: existe uma versão no local de análise de registos?

R: um número de Análise de registos é um serviço em nuvem dimensionáveis que processa e armazena grandes quantidades de dados. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>Q. Como resolver se a análise de registos já não está a recolher dados?

R: Se estiver a livre no escalão de preço e ter enviado a mais do que 500 MB de dados dentro de um dia, deixa de recolha de dados para o resto do dia. Atingir o limite diário é um motivo comum que Log Analytics interrompe a recolha de dados ou dados parecem estar em falta.

Análise de registos cria um evento do tipo *operação* quando é iniciado e deixa de recolha de dados. 

Execute a seguinte consulta em pesquisa para verificar se está a atingir o limite diário e dados em falta:`Type=Operation OperationCategory="Data Collection Status"`

Quando parar a recolha de dados, o *OperationStatus* é **aviso**. Quando inicia a recolha de dados, o *OperationStatus* é **com êxito**. 

A tabela seguinte descreve as razões que deixa de recolha de dados e uma ação sugerida para retomar a recolha de dados:

| Interrompe a recolha de dados de razão                       | Para retomar a recolha de dados |
| -------------------------------------------------- | ----------------  |
| Atingiu o limite diário de dados gratuitos<sup>1</sup>       | Aguarde até ao dia seguinte para a coleção reiniciar automaticamente, ou<br> Alterar para um escalão de preço pago |
| Subscrição do Azure está num estado suspenso devido a: <br> Avaliação gratuita terminada <br> Passagem do Azure expirou <br> Limite de gastos mensalmente atingido (por exemplo, numa subscrição MSDN ou Visual Studio)                          | Converter uma subscrição paga <br> Converter uma subscrição paga <br> Remover o limite ou aguarde que o limite repõe |

<sup>1</sup> se a sua área de trabalho é no escalão de preço gratuito, está limitado a enviar 500 MB de dados por dia para o serviço. Quando atingir o limite diário, deixa de recolha de dados até ao dia seguinte. Dados enviados enquanto a recolha de dados está parada não estão indexados e não estão disponíveis para pesquisa. Quando é retomada a recolha de dados, o processamento ocorre apenas para novos dados enviados. 

Análise de registos utiliza a hora UTC e começam a todos os dias à meia-noite UTC. Se a área de trabalho atingir o limite diário, retoma o processamento durante a primeira hora do dia seguinte UTC.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>Q. Como pode posso ser notificado quando parar a recolha de dados?

R: utilizar os passos descritos no [criar uma regra de alerta](log-analytics-alerts-creating.md#create-an-alert-rule) para ser notificado quando parar a recolha de dados.

Quando criar o alerta para quando interrompe a recolha de dados, defina o:
- **Nome** para *parada a recolha de dados*
- A **gravidade** como *Aviso*
- A **consulta de pesquisa** como `Type=Operation OperationCategory="Data Collection Status" OperationStatus=Warning`
- **Janela de tempo** para *2 horas*.
- A **frequência do alerta** como uma hora, pois os dados de utilização só são atualizados uma vez por hora.
- **Gerar alerta com base em** como o *número de resultados*
- O **número de resultados** como *Superior a 0*

Utilize os passos descritos em [Add actions to alert rules](log-analytics-alerts-actions.md) (Adicionar ações a regras de alertas) para configurar uma ação de e-mail, webhook ou runbook para a regra de alerta.


## <a name="configuration"></a>Configuração
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>Q. Pode alterar o nome do contentor de blob/tabela utilizado para ler a partir do diagnóstico do Azure (WAD)?

A. Não, não é atualmente possível ler a partir de tabelas arbitrários ou contentores no armazenamento do Azure.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>Q. Que endereços IP é a utilização do serviço de análise de registos? Como garantir que a minha firewall permite apenas tráfego para o serviço de análise de registos?

A. O serviço de análise de registos é desenvolvido do Azure. Endereços de IP de análise do registo estão a ser o [intervalos de IP do Microsoft Azure Datacenter](http://www.microsoft.com/download/details.aspx?id=41653).

Como implementações de serviços são efetuadas, alterar os endereços IP reais do serviço de análise de registos. Os nomes DNS para permitir a sua firewall estão documentados em [configurar as definições de proxy e de firewall na análise de registos](log-analytics-proxy-firewall.md).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>Q. Utilizar o ExpressRoute para ligar ao Azure. A minha tráfego de análise de registos utiliza a ligação do ExpressRoute?

A. Os diferentes tipos de tráfego do ExpressRoute são descritos no [documentação do ExpressRoute](../expressroute/expressroute-faqs.md#supported-services).

Tráfego ao Log Analytics utiliza o circuito de ExpressRoute público de peering.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>Q. Existe uma forma simple e fácil de mover uma área de trabalho de análise de registos existente para outra subscrição do Azure/área de trabalho de análise de registos?

A. O `Move-AzureRmResource` cmdlet permite-lhe mover uma área de trabalho de análise de registos bem como uma conta de automatização de uma subscrição do Azure para outro. Para obter mais informações, consulte [mover AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Esta alteração também pode ser efetuada no portal do Azure.

Não é possível mover os dados de uma área de trabalho de análise de registos para outro ou alterar a região de dados de análise de registos são armazenados no.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>P: como adicionar o registo de análise para o System Center Operations Manager?

R: atualização para o update rollup mais recente e importar pacotes de gestão permite-lhe ligar o Operations Manager para análise de registos.

>[!NOTE]
>A ligação do Operations Manager ao Log Analytics só está disponível para o System Center Operations Manager 2012 SP1 e posterior.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>P: como confirmar que um agente é capaz de comunicar com a análise de registos?

R: para garantir que o agente consegue comunicar com o OMS, aceda a: painel de controlo, segurança e as definições, **Microsoft Monitoring Agent**.

Sob o **análise de registos do Azure (OMS)** separador, procure uma marca de verificação verde. Um ícone de marca de verificação verde confirma que o agente é capaz de comunicar com o serviço do Azure.

Um ícone de aviso amarelo significa que o agente está a ter problemas de comunicação com a análise de registos. Um motivo comum é que parou o serviço Microsoft Monitoring Agent. Utilize o Gestor de controlo de serviço para reiniciar o serviço.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>P: como parar a um agente de comunicar com a análise de registos?

R: no System Center Operations Manager, remova o computador da lista de computadores geridos OMS. Já não relatório ao Log Analytics atualizações a configuração do agente do Operations Manager. Para agentes ligados diretamente ao Log Analytics, pode impedi-las a comunicação através de: painel de controlo, segurança e as definições, **Microsoft Monitoring Agent**.
Em **análise de registos do Azure (OMS)**, remova todas as áreas de trabalho listadas.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>P: por que razão estou posso obter um erro ao tentar mover a minha área de trabalho de uma subscrição do Azure para outro?

R: Se estiver a utilizar o portal do Azure, certifique-se que apenas a área de trabalho está selecionada para a movimentação. Não selecione as soluções - será automaticamente mudada após move a área de trabalho. 

Certifique-se de que tem permissão em ambas as subscrições do Azure.

## <a name="agent-data"></a>Dados de agente
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>Q. Quantidade de dados posso enviar através do agente à análise de registos? Existe uma quantidade máxima de dados por cliente?
A. O plano gratuito define um limite diário de 500 MB por área de trabalho. Os planos standard e premium têm sem limite a quantidade de dados que são carregados. Como um serviço em nuvem, análise de registos foi concebido para automaticamente a escala até o identificador do volume proveniente de um cliente –, mesmo se for terabytes por dia.

O agente de análise de registos foi concebido para garantir que tem um ocupa pouco espaço. Um dos nossos clientes escrito um blogue sobre os testes que são executados nos nosso agente e como impressed o fizeram. O volume de dados varia consoante as soluções que ativar. Pode encontrar informações detalhadas sobre o volume de dados e ver o breakup pela solução de [utilização](log-analytics-usage.md) página.

Para obter mais informações, pode ler um [cliente blogue](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) sobre reduzidos do agente do OMS.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>Q. Quantidade largura de banda de rede é utilizada pelo agente de gestão do Microsoft (MMA) ao enviar dados para análise de registos?

A. Largura de banda é uma função a quantidade de dados enviados. Dados são comprimidos como é enviado através da rede.

### <a name="q-how-much-data-is-sent-per-agent"></a>Q. Quantidade de dados é enviado por agente?

A. A quantidade de dados enviados por agente depende de:

* As soluções que tiver ativado
* O número de registos e os contadores de desempenho recolhidos
* O volume de dados nos registos

O escalão de preço gratuito é uma boa forma de integrar vários servidores e o volume de típico de dados do medidor. Global utilização é apresentada no [utilização](log-analytics-usage.md) página.

Para computadores que são capazes de executar o agente de WireData, utilize a seguinte consulta para ver a quantidade de dados está a ser enviada:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Passos seguintes
* [Introdução à análise de registos](log-analytics-get-started.md) para saber mais sobre a análise de registos e começar a trabalhar em minutos.
