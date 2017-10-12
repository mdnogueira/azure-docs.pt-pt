---
title: "Introdução à Solução de Segurança e Auditoria do Operations Management Suite| Microsoft Docs"
description: "Este documento ajuda-o a começar com as funções da solução de Segurança e Auditoria do Operations Management Suite para monitorizar a nuvem híbrida."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 754796ef-a43e-468a-86c9-04a2eda55b5b
ms.service: operations-management-suite
ms.custom: oms-security
ms.topic: get-started-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.openlocfilehash: eb5283c8f32fddaa8a20a565e4b877821de979a4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-operations-management-suite-security-and-audit-solution"></a>Introdução à Solução de Segurança e Auditoria do Operations Management Suite
Este documento ajuda-o a começar rapidamente com as funções da solução de Segurança e Auditoria do Operations Management Suite (OMS) guiando-o ao longo de cada opção.

## <a name="what-is-oms"></a>O que é o OMS?
O Microsoft Operations Management Suite (OMS) é a solução de gestão de TI baseada na nuvem da Microsoft que o ajuda a gerir e a proteger a sua infraestrutura no local e na nuvem. Para mais informações sobre o OMS, leia o artigo [Operations Management Suite](https://technet.microsoft.com/library/mt484091.aspx).

## <a name="oms-security-and-audit-dashboard"></a>Dashboard de Segurança e Auditoria do OMS
A solução de Segurança e Auditoria do OMS permite uma visão abrangente da postura de segurança de TI da organização com consultas de pesquisa incorporada para problemas relevantes que requerem a sua atenção. O dashboard de **Segurança e Auditoria** é o ecrã principal de tudo o que se relacione com a segurança no OMS. Fornece informações de alto nível sobre o estado de segurança dos computadores. Também inclui a capacidade de ver todos os eventos das últimas 24 horas, 7 dias ou qualquer outro intervalo de tempo personalizado. Para aceder ao dashboard de **Segurança e Auditoria**, siga estes passos:

1. No dashboard principal do **Microsoft Operations Management Suite** clique no mosaico **Definições** à esquerda.
2. No painel **Definições**, em **Soluções** clique na opção **Segurança e Auditoria**.
3. O dashboard de **Segurança e Auditoria** aparece:
   
    ![Dashboard de Segurança e Auditoria do OMS](./media/oms-security-getting-started/oms-getting-started-fig1-ga.png)

Se estiver a aceder a este dashboard pela primeira vez e se não tiver dispositivos monitorizados pelo OMS, os mosaicos não estarão preenchidos com dados obtidos a partir do agente. Depois de instalar o agente, pode demorar algum tempo a preencher, pelo que poderá não ver inicialmente alguns dados que ainda estejam a ser carregados para a nuvem.  Neste caso, é normal ver alguns mosaicos sem informações concretas. Leia [Ligar computadores Windows diretamente ao OMS](https://technet.microsoft.com/library/mt484108.aspx) para obter mais informações sobre como instalar o agente OMS num sistema Windows e [Ligar computadores Linux ao OMS](https://technet.microsoft.com/library/mt622052.aspx) para obter mais informações sobre como efetuar esta tarefa num sistema Linux.

> [!NOTE]
> O agente recolhe as informações com base nos eventos atuais que estão ativados, por exemplo, no nome do computador, no nome de utilizador e no endereço IP. No entanto, nenhum documento/ficheiros, nome de base de dados ou dados privados são recolhidos.   
> 
> 

As soluções são uma coleção de regras de aquisição de dados, de lógica e de visualização para lidar com dificuldades principais do cliente. A Segurança e Auditoria é uma solução, outras podem ser adicionadas separadamente. Leia o artigo [Adicionar soluções](https://technet.microsoft.com/library/mt674635.aspx) para obter mais informações sobre como adicionar uma nova solução.

O dashboard de Segurança e Auditoria do OMS está organizado em quatro categorias principais:

* **Domínios de Segurança**: nesta área poderá explorar mais aprofundadamente os registos de segurança ao longo do tempo, aceder a avaliações de software maligno, avaliação de atualização, segurança de rede, informação de acesso e de identidade, computadores com eventos de segurança, bem como aceder rapidamente ao dashboard do Centro de Segurança do Azure.
* **Problemas Relevantes**: esta opção permite-lhe identificar rapidamente o número de problemas ativos e a gravidade destes problemas.
* **Deteções (Pré-visualização)**: permite-lhe identificar padrões de ataque ao visualizar os alertas de segurança à medida que ocorrem em relação aos recursos.
* **Informações sobre ameaças**: permite-lhe identificar padrões de ataque ao visualizar o número total de servidores com tráfego de IPs maliciosos de saída, o tipo malicioso da ameaça e um mapa que mostra a proveniência desses IPs. 
* **Consultas de segurança comuns**: esta opção fornece-lhe uma lista das consultas de segurança mais comuns que pode utilizar para monitorizar o ambiente. Ao clicar nestas consultas, é aberto o painel **Pesquisa** com os resultados para essa consulta.

> [!NOTE]
> para obter mais informações sobre como o OMS mantém os dados seguros, leia Como o OMS protege os dados.
> 
> 

## <a name="security-domains"></a>Domínios de segurança
Ao monitorizar os recursos, é importante poder aceder rapidamente ao estado atual do ambiente. No entanto, também é importante poder monitorizar eventos passados que permitam uma melhor compreensão do que está a decorrer no ambiente em determinado momento. 

> [!NOTE]
> a retenção de dados é de acordo com o plano de preços do OMS. Para obter mais informações, visite a página de preços do [Microsoft Operations Management Suite](https://www.microsoft.com/server-cloud/operations-management-suite/pricing.aspx).
> 
> 

Os cenários de investigação forense e de resposta a incidentes beneficiarão diretamente dos resultados disponíveis no mosaico **Registos de Segurança ao longo do Tempo**.

![Registos de segurança ao longo do tempo](./media/oms-security-getting-started/oms-getting-started-fig2.JPG)

Ao clicar neste mosaico, o painel **Pesquisa** é aberto, apresentando um resultado de consulta para **Eventos de Segurança** (Tipo = SecurityEvents) com dados baseados nos últimos sete dias, conforme apresentado abaixo:

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Registos de segurança ao longo do tempo](./media/oms-security-getting-started/oms-getting-started-fig3.JPG)

O resultado de pesquisa está dividido em dois painéis: o painel esquerdo apresenta o número de eventos de segurança que foram encontrados, os computadores em que estes eventos foram encontrados, o número de contas detetadas nestes computadores e os tipos de atividades. O painel da direita fornece-lhe os resultados totais e uma vista cronológica dos eventos de segurança com a atividade do evento e o nome do computador. Também pode clicar em **Mostrar Mais** para ver mais detalhes sobre este evento, tais como os dados do evento, o ID do evento e a origem do evento.

> [!NOTE]
> Para obter mais informações sobre a consulta de pesquisa do OMS, leia [Referência de pesquisa do OMS](https://technet.microsoft.com/library/mt450427.aspx).
> 
> 

### <a name="antimalware-assessment"></a>Avaliação de antimalware
Esta opção permite-lhe identificar rapidamente os computadores com proteção insuficiente e os computadores que estejam comprometidos por um software maligno. O estado de avaliação de software maligno e as ameaças detetadas nos servidores monitorizados são lidos e, em seguida, os dados são enviados para o serviço OMS na nuvem para processamento. Os servidores com ameaças detetadas e os servidores com proteção insuficiente são apresentados no dashboard de avaliação do software maligno, que está acessível depois de clicar no mosaico **Avaliação de Antimalware**. 

![avaliação de software maligno](./media/oms-security-getting-started/oms-getting-started-fig4-ga.png)

Tal como qualquer outro mosaico dinâmico disponível no Dashboard OMS, ao clicar no mesmo, o painel **Pesquisa** será aberto com o resultado da consulta. Em relação a esta opção, se clicar na opção **Sem Relatório** em **Estado de Proteção**, terá o resultado da consulta que mostra esta entrada única com o nome do computador e a respetiva classificação, conforme apresentado abaixo:

![resultado da pesquisa](./media/oms-security-getting-started/oms-getting-started-fig5.png)

> [!NOTE]
> A *classificação* é uma classificação atribuída para refletir o estado da proteção (ativada, desativada, atualizada, etc.) e as ameaças que são encontradas. Ter isto como um número ajuda a criar agregações.
> 
> 

Se clicar no nome do computador, terá a vista cronológica do estado de proteção para este computador. Isto é muito útil para cenários em que tem de perceber se o antimalware foi instalado e removido em determinada altura.   

### <a name="update-assessment"></a>Avaliação de atualização
Esta opção permite-lhe determinar rapidamente a exposição geral a potenciais problemas de segurança e até que ponto estas atualizações são críticas para o ambiente. A solução de Segurança e Auditoria do OMS apenas fornece a visualização destas atualizações, os dados reais são provenientes das [Soluções de Gestão de Atualizações](oms-solution-update-management.md), que é um módulo diferentes dentro do OMS. Eis um exemplo das atualizações:

![atualizações do sistema](./media/oms-security-getting-started/oms-getting-started-fig6-new.png)

> [!NOTE]
> Para obter mais informações sobre a solução de Gestão de Atualizações, leia [Update Management solution in OMS (Solução de Gestão de Atualizações no OMS)](oms-solution-update-management.md).
> 
> 

### <a name="identity-and-access"></a>Identidade e Acesso
A identidade deve ser o plano de controlo da sua empresa e proteger a sua identidade deve ser a sua prioridade. Enquanto no passado existiam perímetros à volta das organizações e esses perímetros eram um dos limites de defesa primários, hoje em dia, com mais dados e mais aplicações a moverem-se para a nuvem, a identidade torna-se o novo perímetro. 

> [!NOTE]
> atualmente, os dados baseia-se apenas nos dados de início de sessão de Eventos de Segurança (ID do evento 4624), no futuro, os dados de inícios de sessão do Office 365 e do Azure AD também serão incluídos.
> 
> 

Através da monitorização das atividades de identidade será capaz de levar a cabo ações proativas antes da ocorrência de um incidente ou ações reativas que param uma tentativa de ataque. O dashboard de **Identidade e Acesso** fornece uma descrição geral do estado da identidade, incluindo o número de tentativas falhadas de início de sessão, as contas de utilizador que foram utilizadas durante essas tentativas, as contas que foram bloqueadas, as contas com palavras-passe alteradas ou repostas e o número atual de contas que têm sessão iniciada. 

Ao clicar no mosaico **Identidade e Acesso** verá o dashboard seguinte:

![identidade e acesso](./media/oms-security-getting-started/oms-getting-started-fig7-ga.png)

As informações disponíveis neste dashboard podem ajudá-lo a identificar imediatamente uma potencial atividade suspeita. Por exemplo, existem 338 tentativas de início de sessão como **Administrador** e 100% destas tentativas falharam. Isto pode ser causado por um ataque de força bruta contra esta conta. Se clicar nesta conta irá obter mais informações que podem ajudá-lo a determinar o recurso de destino para este potencial ataque:

![resultados de pesquisa](./media/oms-security-getting-started/oms-getting-started-fig8.JPG)

O relatório detalhado fornece informações importantes sobre este evento, incluindo: o computador de destino, o tipo de início de sessão (neste caso início de sessão de Rede), a atividade (neste caso o evento 4625) e uma linha cronológica abrangente de cada tentativa. 

### <a name="computers"></a>Computadores
Este mosaico pode ser utilizado para aceder a todos os computadores que tenham eventos de segurança ativamente. Ao clicar neste mosaico irá ver a lista de computadores com eventos de segurança e o número de eventos em cada computador:

![Computadores](./media/oms-security-getting-started/oms-getting-started-fig9.JPG)

Pode continuar a investigação clicando em cada computador e rever os eventos de segurança que foram sinalizados.

### <a name="threat-intelligence"></a>Informações sobre Ameaças

Ao utilizar a opção de Análise de Ameaças disponível na Auditoria e Segurança de OMS, os administradores de TI podem identificar ameaças de segurança contra o ambiente, por exemplo, identificar se um determinado computador faz parte de um botnet. Os computadores podem tornar-se nós de um botnet quando os atacantes instalam ilicitamente malware que liga secretamente este computador ao comando e controlo. Também poderá identificar potenciais ameaças provenientes de canais de comunicação underground, como o darknet. Saiba mais sobre a Análise de Ameaças ao ler o artigo [Monitoring and responding to security alerts in Operations Management Suite Security and Audit Solution (Monitorização e resposta aos alertas de segurança na Solução de Segurança e Auditoria do Operations Management Suite)](oms-security-responding-alerts.md).

Em alguns cenários, pode observar um IP potencialmente mal-intencionado que foi acedido a partir de um computador monitorizado:

![mapa de informações sobre ameaças](./media/oms-security-responding-alerts/oms-security-responding-alerts-fig6.png)

Este alerta e outros dentro da mesma categoria, são gerados através da Segurança do OMS, ao tirar partido das [Informações sobre Ameaças da Microsoft](https://youtu.be/O4WtxgUrDc8). Os dados das Informações sobre Ameaças são recolhidos pela Microsoft e são comprados aos principais fornecedores de inteligência de ameaças. Estes dados são atualizados com frequência e adaptados para ameaças rápidas. Devido à sua natureza, devem ser combinados com outras fontes de informações de segurança ao [investigar](https://blogs.technet.microsoft.com/msoms/2016/12/08/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) um alerta de segurança. 

### <a name="baseline-assessment"></a>Avaliação da Linha de Base

A Microsoft, juntamente com o setor e as organizações governamentais em todo o mundo, define uma configuração do Windows que representa implementações de servidor altamente seguras. Esta configuração é um conjunto de chaves de registo, definições de política de auditoria e definições de política de segurança juntamente com os valores recomendados da Microsoft para estas definições. Este conjunto de regras é conhecido como linha de base de Segurança. Leia [Avaliação da Linha de Base em Solução de Segurança e Auditoria do Operations Management Suite](oms-security-baseline.md) para obter mais informações sobre esta opção.

### <a name="azure-security-center"></a>Centro de Segurança do Azure
Este mosaico é basicamente um atalho para aceder ao dashboard do Centro de Segurança do Azure. Leia [Introdução ao Centro de Segurança do Azure](../security-center/security-center-get-started.md) para obter mais informações sobre esta solução.

## <a name="notable-issues"></a>Problemas relevantes
A intenção principal deste grupo de opções consiste em fornecer uma vista rápida dos problemas que existem no ambiente, categorizando-os como Crítico, Aviso e Informativo. O mosaico do tipo de problema Ativo é uma visualização destes problemas, mas não lhe permite explorar mais detalhes sobre os mesmos. Para isso tem de utilizar a parte inferior deste mosaico que inclui o nome do problema (NOME), com quantos objetos ocorreu (CONTAGEM) e quão crítico é (GRAVIDADE).

![Problemas relevantes](./media/oms-security-getting-started/oms-getting-started-fig10.JPG)

Pode ver que estes problemas já foram abordados em diferentes áreas do grupo **Domínios de Segurança**, o que reforça a intenção desta vista: visualizar os problemas mais importantes do ambiente a partir de um único local.

## <a name="detections-preview"></a>Deteções (Pré-visualização)
A intenção principal desta opção é permitir que as TI identifiquem rapidamente potenciais ameaças para o ambiente e a respetiva gravidade.

![Informações sobre Ameaças](./media/oms-security-getting-started/oms-getting-started-fig12.png)

Esta opção também pode ser utilizada durante uma [investigação de resposta a incidentes](https://blogs.msdn.microsoft.com/azuresecurity/2016/11/30/investigating-suspicious-activity-in-a-hybrid-cloud-with-oms-security/) para efetuar a avaliação e obter mais informações sobre o ataque.

> [!NOTE]
> Para obter mais informações sobre como utilizar o OMS para Resposta a Incidentes, veja este vídeo: [How to Leverage the Azure Security Center & Microsoft Operations Management Suite for an Incident Response (Como tirar partido do Centro de Segurança do Azure e do Microsoft Operations Management Suite para uma Resposta a Incidentes)](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703).
> 
> 

## <a name="threat-intelligence"></a>Informações sobre Ameaças
A nova secção de informações sobre ameaças da solução de Segurança e Auditoria visualiza os padrões de ataque possíveis de diversas formas: o número total de servidores com tráfego de IPs maliciosos de saída, o tipo malicioso da ameaça e um mapa que mostra a proveniência desses IPs. Pode interagir com o mapa e clicar nos IPs para obter mais informações.

Os alfinetes amarelos no mapa indicam o tráfego de entrada de IPs maliciosos. Não é incomum que os servidores expostos à Internet recebam tráfego de entrada malicioso, mas é recomendável rever estas tentativas para certificar-se de que nenhuma foi concluída com êxito. Estes indicadores baseiam-se nos registos do IIS, no WireData e nos registos de Firewall do Windows.  

![Informações sobre Ameaças](./media/oms-security-getting-started/oms-getting-started-fig11-ga.png)

## <a name="common-security-queries"></a>Consultas de segurança comuns
A lista de consultas de segurança comuns disponível pode ser útil para aceder rapidamente a informações do recurso e personalizá-lo com base nas necessidades do ambiente. Estas consultas comuns são:

* todas as Atividades de Segurança
* atividades de Segurança no computador "computer01.contoso.com" (substitua com o nome do computador)
* atividades de Segurança no computador "computer01.contoso.com" para a conta "Administrador" (substitua com os nomes do computador e de conta)
* atividade de Início de Sessão por Computador
* contas que terminaram o antimalware da Microsoft em qualquer computador
* computadores nos quais o processo de proteção contra software maligno da Microsoft foi terminado
* computadores nos quais foi executado o "hash.exe" (substitua com um nome de processo diferente)
* todos os nomes de Processo que foram executados
* atividade de Início de Sessão por Conta
* contas que iniciaram sessão remotamente no computador "computer01.contoso.com" (substitua com o nome do computador)

## <a name="see-also"></a>Consultar também
Neste documento, foi-lhe apresentada a solução de Segurança e Auditoria do OMS. Para saber mais sobre a Segurança do OMS, veja os artigos seguintes:

* [Descrição geral do Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Monitorização e Resposta aos Alertas de Segurança na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-responding-alerts.md)
* [Recursos de Monitorização na Solução de Segurança e Auditoria do Operations Management Suite](oms-security-monitoring-resources.md)

