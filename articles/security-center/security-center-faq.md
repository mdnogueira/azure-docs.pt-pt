---
title: "Perguntas mais frequentes sobre (FAQ) do Centro de segurança do Azure | Microsoft Docs"
description: "Estas perguntas mais frequentes respondem a dúvidas sobre o Centro de segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2017
ms.author: terrylan
ms.openlocfilehash: 35aa45ce09b756dd7413a1df3d3c7b0c428b7a97
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure
Estas perguntas mais frequentes respondem a dúvidas sobre o Centro de segurança do Azure, um serviço que o ajuda a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos do Microsoft Azure.

> [!NOTE]
> A partir do início de junho de 2017, o Centro de Segurança irá utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, consulte [migração de plataforma de centro de segurança do Azure](security-center-platform-migration.md). As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>
>

## <a name="general-questions"></a>Perguntas gerais
### <a name="what-is-azure-security-center"></a>O que é o Centro de Segurança do Azure?
O Centro de Segurança do Azure ajuda-o a evitar, detetar e responder a ameaças com uma maior visibilidade e controlo sobre a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

### <a name="how-do-i-get-azure-security-center"></a>Como posso obter o Centro de segurança do Azure?
Ativado com a sua subscrição do Microsoft Azure e acessível a partir do Centro de segurança do Azure a [portal do Azure](https://azure.microsoft.com/features/azure-portal/). ([Iniciar sessão portal do](https://portal.azure.com), selecione **procurar**e desloque-se para **Centro de segurança**).  

## <a name="billing"></a>Faturação
### <a name="how-does-billing-work-for-azure-security-center"></a>Como funciona o trabalho de faturação para o Centro de segurança do Azure?
Centro de segurança é oferecido na duas camadas:

O **escalão gratuito** -lhe visibilidade para o estado de segurança dos seus recursos do Azure, política de segurança básica, recomendações de segurança e integração com serviços e produtos de segurança de parceiros.

O **escalão Standard** adiciona ameaça avançada as capacidades de deteção, incluindo ameaça intelligence, a análise comportamental, a deteção de anomalias, incidentes de segurança e relatórios de atribuição da ameaça. O escalão Standard é gratuito durante os primeira 60 dias. Deverá optar por continuar a utilizar o serviço para além de 60 dias, vamos iniciar automaticamente a cobram para o serviço.  Para atualizar, selecione [escalão de preço](https://docs.microsoft.com/azure/security-center/security-center-pricing) na política de segurança.

## <a name="permissions"></a>Permissões
O Centro de Segurança do Azure utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../active-directory/role-based-access-control-configure.md), que fornece [funções incorporadas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure.

Centro de segurança avalia a configuração dos seus recursos para identificar problemas de segurança e vulnerabilidades. No Centro de segurança, verá apenas informações relacionadas com a um recurso quando são atribuídas a função de proprietário, Contribuidor ou leitor para a subscrição ou grupo de recursos que um recurso pertence.

Consulte [permissões no Centro de segurança do Azure](security-center-permissions.md) para saber mais sobre as funções e as ações permitidas no Centro de segurança.

## <a name="data-collection"></a>Recolha de dados
Centro de segurança recolhe dados das suas máquinas virtuais para avaliar o respetivo estado de segurança, fornecer recomendações de segurança e alertá-lo a ameaças. Quando aceder primeiro ao centro de segurança, a recolha de dados está ativada em todas as máquinas virtuais na sua subscrição. Também pode ativar a recolha de dados na política de centro de segurança.

### <a name="how-do-i-disable-data-collection"></a>Como desativar a recolha de dados?
Se estiver a utilizar o escalão gratuito de centro de segurança do Azure, pode desativar a recolha de dados provenientes de máquinas virtuais em qualquer altura. Recolha de dados é necessária para subscrições na camada padrão. Pode desativar a recolha de dados para uma subscrição na política de segurança. ([Inicie sessão no portal do Azure](https://portal.azure.com), selecione **procurar**, selecione **Centro de segurança**e selecione **política**.)  Ao selecionar uma subscrição, um novo painel abre-se e fornece-lhe a opção para desativar **recolha de dados**.

### <a name="how-do-i-enable-data-collection"></a>Como ativar a recolha de dados?
Pode ativar a recolha de dados para a sua subscrição do Azure na política de segurança. Para ativar a recolha de dados. [Inicie sessão no portal do Azure](https://portal.azure.com), selecione **procurar**, selecione **Centro de segurança**e selecione **política**. Definir **recolha de dados** para **no**.

### <a name="what-happens-when-data-collection-is-enabled"></a>O que acontece quando a recolha de dados está ativada?
Quando a recolha de dados estiver ativada, o Microsoft Monitoring Agent é automaticamente aprovisionado em todos os existente e máquinas virtuais que são implementadas novas suportadas na subscrição.

### <a name="does-the-monitoring-agent-impact-the-performance-of-my-servers"></a>O agente de monitorização afeta o desempenho dos meus servidores?
O agente consome uma quantidade nominal de recursos do sistema e deve ter e pouco impacto sobre o desempenho. Para obter mais informações sobre o impacto no desempenho e o agente e a extensão, consulte o [guia de operações e planeamento](security-center-planning-and-operations-guide.md#data-collection-and-storage).

### <a name="where-is-my-data-stored"></a>Onde são armazenados os meus dados?
Dados recolhidos a partir deste agente são armazenados numa área de trabalho do Log Analytics existente associados à subscrição ou uma nova área de trabalho. Para obter mais informações, consulte [segurança dos dados](security-center-data-security.md).

## <a name="using-azure-security-center"></a>Utilizando o Centro de segurança do Azure
### <a name="what-is-a-security-policy"></a>O que é uma política de segurança?
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada. No Centro de segurança do Azure, é possível definir políticas para as subscrições do Azure, de acordo com requisitos de segurança da sua empresa e o tipo de aplicações ou sensibilidade dos dados em cada subscrição.

As políticas de segurança ativadas no monitorização e recomendações de segurança de unidade de centro de segurança do Azure. Para saber mais sobre as políticas de segurança, consulte o artigo [monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md).

### <a name="who-can-modify-a-security-policy"></a>Quem pode modificar uma política de segurança?
Para modificar uma política de segurança, tem de ser um administrador de segurança ou um proprietário ou contribuinte dessa subscrição.

Para saber como configurar uma política de segurança, consulte [definir políticas de segurança no Centro de segurança do Azure](security-center-policies.md).

### <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?
Centro de segurança do Azure analisa o estado de segurança dos seus recursos Azure. Quando são identificadas potenciais vulnerabilidades de segurança, recomendações são criadas. As recomendações descrevem o processo de configuração o controlo necessário. Os exemplos são:

* Aprovisionamento de antimalware para ajudar a identificar e remover software malicioso
* Configurar [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) e regras para controlar tráfego para máquinas virtuais
* Aprovisionamento de uma firewall de aplicação web para ajudar a proteger contra ataques de filtragem de aplicações web-
* Implementação de atualizações do sistema em falta
* Abordagem de configurações de SO que não correspondam às linhas de base recomendadas

Apenas as recomendações que estão ativadas nas políticas de segurança são mostradas aqui.

### <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança atual dos meus recursos do Azure?
O **descrição geral do Centro de segurança** painel mostra a postura de segurança global do seu ambiente reduzida por computação, redes, armazenamento e dados e aplicações. Cada tipo de recurso tem uma apresentação do indicador se quaisquer potenciais vulnerabilidades de segurança que tenham sido identificadas. Ao clicar em cada mosaico apresenta uma lista dos problemas de segurança identificadas pelo centro de segurança, juntamente com um inventário dos recursos na sua subscrição.

### <a name="what-triggers-a-security-alert"></a>O que é acionado um alerta de segurança?
Centro de segurança do Azure automaticamente recolhe, analisa e fuses dados de registo dos seus recursos do Azure, rede e soluções de parceiros, como antimalware e firewalls. Quando são detetadas ameaças, é criado um alerta de segurança. Os exemplos incluem a deteção de:

* Virtual Machines comprometidas a comunicar com endereços IP maliciosos conhecidos
* Software maligno avançado detetado utilizando o relatório de erros do Windows
* Ataques de força bruta contra máquinas virtuais
* Alertas de segurança de soluções de segurança de parceiros integradas, tais como antimalware ou Firewalls de aplicação Web

### <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>O que é a diferença entre a ameaças detetadas e alertado em pela Microsoft Security Response Center versus Centro de segurança do Azure?
O Microsoft Security Response Center (MSRC) efetua a segurança selecione monitorização de rede do Azure e a infraestrutura e recebe queixas intelligence e abuso de ameaças de terceiros. Quando MSRC torna-se em atenção que os dados de cliente foram acedidos por uma entidade ilegal ou não autorizada ou que a utilização do Azure não está em conformidade com os termos de licenciamento para utilizar aceitável, o cliente notifica o Gestor de incidente de segurança. Notificação normalmente ocorre enviando um e-mail para os contactos de segurança especificados no Centro de segurança do Azure ou o proprietário da subscrição do Azure, se não for especificado um contacto de segurança.

Centro de segurança é um serviço Azure que continuamente monitoriza o ambiente do Azure do cliente e aplica-se a análise para detetar automaticamente uma vasta gama de atividades potencialmente maliciosas. Estas deteções estão anexadas como alertas de segurança no dashboard do Centro de segurança.

### <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Os recursos do Azure são monitorizados pelo centro de segurança do Azure?
Centro de segurança do Azure monitoriza os seguintes recursos do Azure:

* Máquinas virtuais (VMs) (incluindo [serviços em nuvem](../cloud-services/cloud-services-choose-me.md))
* Redes Virtuais do Azure
* Serviço do SQL do Azure
* Conta de armazenamento do Azure
* Aplicações Web do Azure (no [ambiente de serviço de aplicações](../app-service/environment/intro.md))
* Soluções de parceiros integradas na sua subscrição do Azure como uma firewall de aplicação web em VMs e no ambiente de serviço de aplicações

## <a name="virtual-machines"></a>Virtual Machines
### <a name="what-types-of-virtual-machines-are-supported"></a>Que tipos de máquinas virtuais são suportados?
Monitorização e recomendações estão disponíveis para máquinas virtuais (VMs) criadas utilizando ambos os [clássica e modelos de implementação do Resource Manager](../azure-classic-rm.md).

Consulte [plataformas suportadas no Centro de segurança do Azure](security-center-os-coverage.md) para obter uma lista das plataformas suportadas.

### <a name="why-doesnt-azure-security-center-recognize-the-antimalware-solution-running-on-my-azure-vm"></a>Por que motivo o Centro de segurança do Azure não reconhece a solução antimalware em execução na minha VM do Azure?
Centro de segurança do Azure tem visibilidade antimalware instalada através de extensões do Azure. Por exemplo, o Centro de segurança não é capaz de detetar antimalware que foi previamente instalada numa imagem fornecida ou se tiver instalado antimalware nas máquinas virtuais com os seus processos (por exemplo, sistemas de gestão de configuração).

### <a name="why-do-i-get-the-message-missing-scan-data-for-my-vm"></a>Por que motivo devo proceder para que a mensagem de "Dados de análise em falta" para a minha VM?
Esta mensagem é apresentada quando não existem dados de análise para uma VM. Pode demorar algum tempo (menos de uma hora) de dados de análise para preencher após a recolha de dados está ativada no Centro de segurança do Azure. Depois do número inicial de dados de análise, poderá receber esta mensagem porque existe sem dados de análise de todo ou não recentes dados de análise. Análises não preencher para uma VM no estado parado. Esta mensagem também pode aparecer se os dados de análise não foi preenchido recentemente (em conformidade com a política de retenção para o agente do Windows, que tem um valor predefinido de 30 dias).

### <a name="why-do-i-get-the-message-vm-agent-is-missing"></a>Por que motivo devo proceder para que a mensagem "O agente VM está em falta?"
O agente da VM tem de ser instalado em VMs para ativar a recolha de dados. O Agente de VM é instalado por predefinição em VMs que são implementadas a partir do Azure Marketplace. Para obter informações sobre como instalar o agente da VM em outras VMs, consulte a mensagem de blogue [extensões e agente da VM](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/).
