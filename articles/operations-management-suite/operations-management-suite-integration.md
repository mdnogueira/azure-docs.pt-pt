---
title: "Integração com o Operations Management Suite (OMS) | Microsoft Docs"
description: "Além de utilizar as funcionalidades padrão do OMS, pode integrá-lo com outras aplicações de gestão e experiência de serviços para fornecer um ambiente de gestão híbrida, para fornecer os cenários de gestão personalizado exclusivos para o seu ambiente, ou para fornecer um gestão personalizado para os seus clientes.  Este artigo fornece uma descrição geral das suas opções diferentes para integrar com o OMS e ligações para artigos fornecer informações técnicas detalhadas."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: fc5f3a8a-77f7-4103-bd7e-744c15ffcca7
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/11/2017
ms.author: bwren
ms.openlocfilehash: 7a24df6f2c3b2c091d1b66b8b9c0a61035ffde11
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrating-with-operations-management-suite-oms"></a>Integração com o Operations Management Suite (OMS)
Operations Management Suite é baseado na nuvem IT solução de gestão que o ajuda a gerir e proteger no local e a infraestrutura de nuvem. da Microsoft  Além de utilizar as funcionalidades padrão do OMS, pode integrá-lo com outras aplicações de gestão e experiência de serviços para fornecer um ambiente de gestão híbrida, para fornecer os cenários de gestão personalizado exclusivos para o seu ambiente, ou para fornecer um gestão personalizado para os seus clientes.  Este artigo fornece uma descrição geral das suas opções diferentes para integrar com os serviços do OMS e ligações para artigos fornecer informações técnicas detalhadas. 

## <a name="log-analytics"></a>Log Analytics
Dados de gestão recolhidos através da análise de registos são armazenados num repositório de que está alojado no Azure.  Todos os dados armazenados no repositório de está disponível em procuras de registo que fornecem análise rápida em extremamente grandes quantidades de dados.  Os requisitos de integração podem ser para preencher o repositório com novos dados, tornando-a disponível para análise, ou para extrair dados no repositório para fornecer uma visualização de novo ou integrar com outra ferramenta de gestão.

Cada conjunto de dados no repositório é armazenado como um registo.  Quando a preencher o repositório, deve fornecer aos utilizadores com o tipo de registo que utiliza a sua solução e uma descrição das propriedades.  Quando recuperar dados, terá destas informações sobre os dados que está a trabalhar.

![Preencher o repositório do OMS](media/operations-management-suite-integration/repository.png)

### <a name="populate-the-log-analytics-repository"></a>Preencher o repositório de análise de registos
Existem vários métodos para povoar o repositório do OMS.  O método que utilizar irão depender de fatores, tais como onde está localizada a origem de dados, o formato dos dados e de que os clientes que seja necessário suportar.  Depois dos dados são armazenados no repositório, não faz diferença como foi recolhido.

As secções seguintes descrevem as diferentes opções para povoar o repositório do OMS.

#### <a name="connected-sources-and-data-sources"></a>Ligado origens e origens de dados
Ligado origens são as localizações onde os dados podem ser obtidos para o repositório do OMS.  Origens de dados e soluções de executam em origens ligadas e definem os dados específicos que são recolhidos.  Se a sua aplicação escreve dados para uma destas origens de dados, pode recolhê-lo ao configurar a origem de dados.  Por exemplo, se a sua aplicação cria eventos Syslog, em seguida, estes podem ser recolhidos pela origem de dados de Syslog num agente Linux.

* [Origens de dados de análise de registos](../log-analytics/log-analytics-data-sources.md)

#### <a name="solutions"></a>Soluções
Soluções de expandem as capacidades do OMS.  Uma solução pode recolher dados da origem de ligado ou pode executar uma análise em registos já recolhidos no repositório de.  Cada solução fornecida pela Microsoft tem um artigo individuais que fornece os detalhes sobre os dados que recolhe.

* [Soluções de análise de registos](../log-analytics/log-analytics-add-solutions.md)

#### <a name="http-data-collector-api"></a>API de Recoletores de dados de HTTP
A API de Recoletor de dados do registo de análise de HTTP é uma API de REST que lhe permite adicionar dados JSON no repositório de análise de registos.  Pode tirar partido desta API quando tiver uma aplicação que não fornece dados através de uma das outras origens de dados ou soluções.  Pode ser utilizado para preencher o repositório de qualquer cliente que pode chamar a API e não recorra apenas a agenda de coleção de qualquer origem de dados ou uma solução.

* [API de Recoletores de dados de HTTP de análise do registo](../log-analytics/log-analytics-data-collector-api.md)

### <a name="retrieve-data-from-the-log-analytics-repository"></a>Obter dados do repositório de análise de registos
Existem vários métodos para obter dados a partir do repositório do OMS.  Poderá pretender que os utilizadores obtêm dados utilizando a consola do OMS e forneça com diferentes tipos de análise e visualizações.  Também pode obter os dados de um processo externo, como outra solução de gestão.

#### <a name="log-searches"></a>Pesquisas de registo
Todos os dados armazenados no repositório de OMS está disponível através de pesquisas de registo.  Os utilizadores podem efetuar os seus próprios analysis ad-hoc na consola do OMS ou criar um dashboard com uma visualização de uma pesquisa de registo específica.  Soluções podem conter vistas personalizadas com visualizações baseadas nos pesquisas predefinidas.  Pode utilizar a API de pesquisa de registo para aceder a dados no repositório de OMS de uma ferramenta de gestão de aplicações ou externa.  

* [Pesquisas de registo na análise de registos](../log-analytics/log-analytics-log-searches.md)
* [Análise de registos de pesquisa REST API de registo](../log-analytics/log-analytics-log-search-api.md)
* [Cmdlets de análise do registo](https://msdn.microsoft.com/library/mt188224.aspx)

#### <a name="custom-views"></a>Vistas personalizadas
O estruturador de vistas permite-lhe criar vistas personalizadas na consola do OMS que fornecem aos utilizadores a visualização e análise dos dados na sua solução.  Cada vista inclui um mosaico que é apresentado na página principal da consola e qualquer número de partes de visualização, que se baseiam em procuras de registo que definir.

* [Estruturador de vistas de análise do registo](../log-analytics/log-analytics-view-designer.md)

#### <a name="power-bi"></a>Power BI
Análise de registos pode automaticamente exportar dados do repositório OMS no Power BI para pode tirar partido do respetivo visualizações e ferramentas de análise.  Executa esta exportação com base numa agenda, para que os dados são mantidos atualizados. 

* [Exportar dados de análise de registos para o Power BI](../log-analytics/log-analytics-powerbi.md)

## <a name="automation"></a>Automatização
OMS pode automatizar os processos de reagir a dados recolhidos ou efetuar outras funções de gestão.  Pode recolher dados a partir da sua aplicação e insira-o para o repositório do OMS, ou pode automatizar a correção de um problema conhecido em resposta a encontrados no repositório de dados. 

![Automatização](media/operations-management-suite-integration/automate.png)

### <a name="runbooks"></a>Runbooks
Os Runbooks na automatização do Azure executar scripts do PowerShell e fluxos de trabalho na nuvem do Azure.  Pode utilizá-los para gerir recursos no Azure ou quaisquer outros recursos que podem ser acedidos a partir da nuvem.  Os Runbooks também podem ser executados num centro de dados local utilizando o Runbook Worker híbrido.  Pode iniciar um runbook a partir do portal do Azure ou de processos externos, utilizando um número de métodos, tais como o PowerShell ou a API de automatização.

* [Iniciar um runbook na automatização do Azure](../automation/automation-starting-a-runbook.md)
* [Cmdlets de automatização do Azure](https://msdn.microsoft.com/library/dn690262.aspx)
* [API de REST de automatização](https://msdn.microsoft.com/library/mt662285.aspx)
* [Automatização .NET](https://msdn.microsoft.com//library/mt465763.aspx)

### <a name="alerts"></a>Alertas
Regras de alertas automaticamente executam pesquisas de registo, de acordo com uma agenda.  Se os resultados correspondentes aos critérios de determinado o alerta resultante pode iniciar um runbook na automatização do Azure ou chamar um webhook que pode iniciar o processo externo.  Ambos estes respostas podem incluir detalhes do alerta, incluindo os dados devolvidos pela pesquisa de registo.

* [Alertas na análise de registos](../log-analytics/log-analytics-alerts.md)
* [API de alerta de análise do registo](../log-analytics/log-analytics-api-alerts.md)

## <a name="backup-and-site-recovery"></a>Cópia de segurança e recuperação de sites
Cópia de segurança do Azure e de recuperação de sites fornecem serviços para proteger os dados da empresa e garantir a disponibilidade de aplicações e servidores.  Pode tirar partido destes serviços efetuar tais cenários como fornecer serviços de cópia de segurança para a sua aplicação ou iniciar uma ativação pós-falha de uma máquina virtual.

* [Cmdlets de cópia de segurança do Azure](https://msdn.microsoft.com/library/mt619253.aspx)
* [REST API do Azure Site Recovery](https://msdn.microsoft.com/library/azure/mt750497.aspx)
* [Cmdlets de recuperação de sites do Azure](https://msdn.microsoft.com/library/mt637930.aspx)

## <a name="custom-solutions"></a>Soluções personalizadas
Pode encapsular lógica de integração para uma solução personalizada para ser executado na sua área de trabalho ou na área de trabalho do cliente.  A solução pode incluir qualquer um dos métodos integração neste artigo, além dos outros recursos para fornecer um cenário de gestão completa.  Os recursos na solução são reunidos, de forma que quando a solução for removida, todos os recursos que tenha criado são removidos da área de trabalho OMS e subscrição do Azure.

Por exemplo, a sua solução pode incluir um runbook de automatização para recolher e processar os dados e, em seguida, preencher o repositório de análise de registos utilizando a API de Recoletor de dados de HTTP.  Também pode incluir uma vista personalizada que apresenta e analisa os dados recolhidos.  

* Criar soluções personalizadas (brevemente)    

## <a name="next-steps"></a>Passos seguintes
* Referência a [OMS SDK](operations-management-suite-sdk.md) para obter informações técnicas sobre automatizar serviços OMS.  

