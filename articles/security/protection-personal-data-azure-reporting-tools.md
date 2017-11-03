---
title: "O documento de proteção de dados pessoais com o Azure, as ferramentas de relatórios | Microsoft Docs"
description: "como utilizar os serviços de relatórios do Azure e as tecnologias para ajudar a proteger a privacidade dos dados pessoais."
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.openlocfilehash: 0ec9ceb63c3e1872e9815a7895b624276fc46123
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="document-protection-of-personal-data-with-azure-reporting-tools"></a>Proteção de documentos dos dados pessoais com o Azure, as ferramentas de relatórios

Este artigo irá discutir como utilizar os serviços de relatórios do Azure e tecnologias para ajudar a proteger a privacidade dos dados pessoais.

## <a name="scenario"></a>Cenário

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries no Mediterranean, Adriatic e Baltic seas, bem como o Isles território. Para ajudar a estes esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K.

A empresa utiliza o Microsoft Azure de processamento e de armazenamento dos dados empresariais. Isto inclui informações pessoais tais como nomes, endereços, números de telefone e informações de cartão de crédito da respetiva base de cliente global. Também inclui informações de recursos humanos tradicionais, como endereços, números de telefone, os números de identificação de dedução dos impostos e outras informações sobre os funcionários da empresa em todas as localizações. A linha cruise mantém também uma grande base de dados de membros de programa reward e loyalty que inclui informações pessoais, para controlar as relações com clientes atuais e anteriores.

Os funcionários empresarias acedam à rede de escritórios remotos da empresa e agentes levar localizados em todo o mundo tem acesso a alguns recursos da empresa.

## <a name="problem-statement"></a>Declaração do problema

A empresa tem de proteger a privacidade dos clientes e dos empregados e dados pessoais através de uma estratégia de segurança por várias camadas que utiliza a gestão do Azure e segurança impõe strict controlos no processamento dos dados pessoais e de acesso a funcionalidades e devem ser capazes de demonstrar as protective medidas para auditores internos e externos.

## <a name="company-goal"></a>Objetivo da empresa

Como parte da sua estratégia de segurança de defesa em profundidade, é um objetivo da empresa para controlar o acesso a todos os e do processamento dos dados pessoais e certifique-se de que a documentação de proteção de privacidade adequado para os dados pessoais estão no local e em funcionamento.

## <a name="solutions"></a>Soluções

Microsoft Azure fornece abrangentes de monitorização, registo e ferramentas de diagnóstico para ajudar a monitorizar e atividades de registos e eventos associados a aceder e processar dados pessoais, geográfico fluxo de dados e acesso de terceiros para dados pessoais. Porque a segurança dos dados pessoais na nuvem é uma responsabilidade partilhada, a Microsoft fornece também clientes com:

- Informações detalhadas sobre a suas próprias processamento dos dados dos clientes

- Medidas de segurança administradas pela Microsoft

- Onde e como envia dados de clientes

- Detalhes do processo de revisões de privacidade da Microsoft

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/services/active-directory/) é baseado na nuvem, o multi-inquilino diretório e identidade do serviço de gestão da Microsoft. O serviço início de sessão e auditar as capacidades de relatórios fornecem-lhe o início de sessão detalhado e informações de atividade de utilização de aplicação para o ajudar a monitorizar e certifique-se de um acesso adequado aos dados pessoais dos clientes e dos funcionários.

Existem dois tipos de relatórios de atividade:

- O [relatórios de atividade/registos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) fornecer um registo de tarefas e actividades sistema detalhado

- O [relatório/registo de atividade de inícios de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) mostra-lhe que efetuou a cada atividade listada no relatório de auditoria

Utilizar as duas em conjunto, pode controlar o histórico de cada tarefa executada e de quem efetuou cada. Ambos os tipos de relatórios são personalizáveis e podem ser filtrados.

#### <a name="how-do-i-access-the-audit-and-security-logs"></a>Como aceder a registos de auditoria e segurança?

Os registos de auditoria e segurança podem ser acedidos a partir do portal do Active Directory de três formas diferentes: através de **atividade** secção (selecione **registos de auditoria** ou **inícios de sessão**), ou a partir de **utilizadores e grupos** ou **aplicações empresariais** em **gerir** no Active Directory. Relatórios também podem ser acedidos através do Azure Active Directory API do relatório. 

1. No portal do Azure, selecione **do Azure Active Directory.**

2. No **atividade** secção, selecione **registos de auditoria.**

    ![](media/protection-personal-data-azure-reporting-tools/image001.png)

3. Personalizar a vista de lista, clicando em **colunas** na barra de ferramentas.

4.  Selecione um item na vista de lista para ver todos os detalhes disponíveis sobre o assunto.

    ![](media/protection-personal-data-azure-reporting-tools/image003.png)

Relatórios do Azure Active Directory também incluem dois tipos de relatórios de segurança, **utilizadores sinalizados para risco** e **inícios de sessão arriscados**, que pode ajudar a monitorizar potenciais riscos no seu ambiente do Azure.

Para obter mais informações sobre o serviço de relatórios, consulte [relatórios do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)

Visite [relatórios de atividade do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal#activity-reports) para informações mais específicas sobre os relatórios disponíveis no Azure Active Directory. Este site inclui mais informações sobre como aceder e utilizar [relatórios de atividade os registos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) e [relatórios de atividade de início de sessão](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) no portal. Também inclui informações sobre [utilizadores sinalizados para risco](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-user-at-risk) e [o início de sessão arriscado](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-security-risky-sign-ins) relatórios de segurança.

Visite o [auditoria do Azure Active Directory referência da API](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-audit-reference) site para obter mais informações sobre como ligar ao Azure Directory reporting programaticamente.

### <a name="log-analytics"></a>Log Analytics

[Análise de registo](https://azure.microsoft.com/services/log-analytics/) pode [recolher dados a partir do Azure Monitor](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage) para correlacioná-la com outros dados e fornecer análise adicional. Monitor do Azure recolhe e analisa os dados de monitorização para o seu ambiente do Azure. 

Ferramentas de análise na análise de registos, tais como a procura de registo, vistas e soluções de funcionam com todos os dados recolhidos, fornecendo uma análise centralizada de todo o seu ambiente. Análise de registos pode agregar e analisar registos de eventos do Windows, registos de IIS e auditáveis, que podem ajudar a detetar potenciais falhas de dados pessoais pode expor os dados pessoais para utilizadores não autorizados.

#### <a name="how-do-i-use-log-analytics"></a>Como posso utilizar a análise de registos?

Pode aceder a análise de registos através do portal do OMS ou o portal do Azure, a partir de qualquer browser web. O Log Analytics inclui uma linguagem de consulta para obter e consolidar rapidamente dados no repositório. Pode criar e guardar pesquisas de registo para analisar diretamente dados no portal.

Para criar uma área de trabalho de análise de registos no portal do Azure, efetue o seguinte:

1. Selecione **Log Analytics** da lista de serviços no Marketplace.

2. Selecione **criar,** , em seguida, especifique o nome da sua área de trabalho do OMS, selecione a sua subscrição, o grupo de recursos, a localização e escalão de preço.

3. Clique em **OK** para apresentar uma lista das suas áreas de trabalho.

4. Selecione uma área de trabalho para ver os respetivos detalhes.

    ![](media/protection-personal-data-azure-reporting-tools/image004.png)

Visite o [documentação de análise de registos](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) para saber mais sobre o serviço.

Visite o [começar com uma área de trabalho de análise de registos](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started) tutorial para criar uma área de trabalho de avaliação e aprender as noções básicas sobre como utilizar o serviço.

Visite as seguintes páginas web para obter informações mais específicas sobre como ligar ao utilizar a análise de registos com os registos descritos acima:

[Origens de dados do Windows registos de eventos no registo de análise](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events)

[Registo do IIS na análise de registos](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs)

[Origens de dados de syslog no Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-syslog)

### <a name="azure-monitorazure-activity-log"></a>Registo de atividade do Monitor/Azure do Azure 

[Monitor do Azure](https://azure.microsoft.com/services/monitor/) fornece registos e as métricas de infraestrutura de nível de base para a maioria dos serviços no Microsoft Azure.
Monitorização pode ajudá-lo a obter conhecimentos aprofundados sobre aplicações do Azure. Monitor do Azure baseia-se a extensão de diagnóstico do Azure (Windows ou Linux) para recolher a maioria dos registos e as métricas de nível de aplicação. [O registo de atividade do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) é um dos recursos que pode ver com a monitorização do Azure. Controla a cada chamada à API e fornece uma variedade de informações sobre as atividades que ocorrem no [do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
Pode pesquisar o registo de atividade (anteriormente denominada registos de auditoria ou operacional) para obter informações sobre o seu recurso visto pela infraestrutura do Azure. 

Embora a maioria das informações registadas no registo de atividade diz respeito ao desempenho e estado de funcionamento do serviço, há também informações relacionadas com a proteção de dados. Utilizar o registo de atividade, poderá determinar o "o que, quem e quando" para escrever qualquer operações (PUT, POST, DELETE) efetuadas nos recursos na sua subscrição do Azure.

Por exemplo, fornece um registo quando um administrador elimina um grupo de segurança de rede, o que pode afetar a proteção dos dados pessoais. Entradas de registo de atividade são armazenadas no Azure Monitor 90 dias.

#### <a name="how-do-i-use-the-data-collected-by-azure-monitor"></a>Forma como utiliza os dados recolhidos pelo Monitor do Azure?

Existem várias formas de utilizar os dados no registo de atividade e outros recursos de Monitor do Azure.

- Pode transmitir os dados para outras localizações na linha real.

- Pode armazenar os dados para períodos de tempo maiores do que as predefinições, utilizando um [conta do storage do Azure](https://docs.microsoft.com/azure/storage/common/storage-introduction) e definir uma política de retenção.

- Pode visual os dados nos gráficos e gráficos, utilizando o [portal do Azure](https://azure.microsoft.com/features/azure-portal/), [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), [Microsoft PowerBI](https://powerbi.microsoft.com/), ou ferramentas de visualização de terceiros.

- Pode consultar os dados utilizando a API de REST de Monitor do Azure, comandos da CLI, [PowerShell](https://docs.microsoft.com/powershell/) cmdlets, ou o SDK .NET.

Para começar a utilizar com a monitorização do Azure, selecione **mais serviços** no portal do Azure.

1. Desloque para baixo até **Monitor** no **monitorizar e gerir** secção.

    ![](media/protection-personal-data-azure-reporting-tools/image005.png)

2.  Monitor será aberta a **registo de atividade** vista.

    ![](media/protection-personal-data-azure-reporting-tools/image007.png)

Pode criar e guardar consultas para filtros comuns, e afixar as consultas mais importantes num dashboard do portal para saber sempre se ocorreram eventos que correspondem os critérios.

1. Pode filtrar a vista por grupo de recursos, timespan e categoria de evento.

    ![](media/protection-personal-data-azure-reporting-tools/image008.png)

2. Pode afixar consultas a um dashboard do portal, em seguida, clicando a **Pin** botão. Isto ajuda-o a criar uma única origem de informações de dados operacionais no seus serviços. O nome da consulta e o número de resultados serão apresentados no dashboard.

Também pode utilizar o Monitor para ver métricas para todos os recursos do Azure, configure as definições de diagnóstico e de alertas e o registo de pesquisa. Para obter mais informações sobre como utilizar o Monitor do Azure e o registo de atividade, consulte [introdução ao Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure 

A capacidade de diagnóstico do Azure permite a recolha de dados de várias origens. Os registos de eventos do Windows, que incluem o registo de segurança, podem ser especialmente útil para controlar e documentar a proteção dos dados pessoais. O registo de segurança controla os eventos de êxito e falha de início de sessão, bem como as alterações de permissões, deteção de padrões que indiquem determinados tipos de ataques, as alterações nas políticas relacionadas com segurança, as alterações de associação de grupo de segurança e muito mais.

Por exemplo, 4695 de ID de evento de alerta para o unprotection tentativa de syslogs dados protegidos. Isto diz respeito para a API Data Protection (DPAPI), que ajuda a proteger dados, tais como as chaves privadas, credenciais armazenadas e outras informações confidenciais.

#### <a name="how-do-i-enable-the-diagnostics-extension-for-windows-vms"></a>Como ativar a extensão de diagnóstico para VMs do Windows

Pode utilizar o PowerShell para ativar a extensão de diagnóstico para uma VM do Windows, de modo a recolher dados de registo. Os passos para se o fizer, dependem de no modelo de implementação que utilizar (Gestor de recursos ou clássico). Para ativar a extensão de diagnóstico numa VM existente que tenha sido criada através do modelo de implementação Resource Manager, pode utilizar o [cmdlet do PowerShell Set-AzureRMVMDiagnosticsExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdiagnosticsextension?view=azurermps-4.3.1).

   ![](media/protection-personal-data-azure-reporting-tools/image009.png)

*\$diagnosticsconfig_path* é o caminho para o ficheiro que contém a configuração de diagnósticos no XML. Para obter mais instruções sobre como ativar o diagnóstico do Azure numa VM, consulte [utilize o PowerShell para ativar o diagnóstico do Azure numa máquina virtual com o Windows.](https://docs.microsoft.com/azure/virtual-machines/windows/ps-extensions-diagnostics)

A extensão de diagnóstico do Azure pode transferir os dados recolhidos para uma conta de armazenamento do Azure ou enviá-lo para serviços como o Application Insights. Em seguida, pode utilizar os dados de auditoria.

#### <a name="how-do-i-store-and-view-diagnostic-data"></a>Como armazenar e ver os dados de diagnóstico?

É importante lembrar-se de que dados de diagnóstico não é permanentemente armazenados, a menos que a transferência para o emulador de armazenamento do Microsoft Azure ou para armazenamento do Azure. Para armazenar e visualizar dados de diagnóstico no armazenamento do Azure, siga estes passos:

1. Especifique uma conta do storage no ficheiro serviceconfiguration. Cscfg. Diagnóstico do Azure pode utilizar o serviço Blob ou o serviço tabela, dependendo do tipo de dados. Registos de eventos do Windows são armazenados em formato de tabela.

2. Transferência de dados. Pode pedir para transferir os dados de diagnóstico através do ficheiro de configuração. SDK 2.4 e anterior, pode também efetuar a pedido através de programação.

3. Ver os dados, utilizando [Explorador de armazenamento do Azure](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer), [Explorador de servidores](https://docs.microsoft.com/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) no Visual Studio, ou [Azure Diagnostics Manager](https://www.cerebrata.com/products/azure-diagnostics-manager) no Azure Management Studio.

Para obter mais informações sobre como efetuar cada um destes passos, consulte [arquivo e a vista de dados de diagnóstico no armazenamento do Azure.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)

### <a name="azure-storage-analytics"></a>Análise do Armazenamento do Azure 

Análise de armazenamento regista informações detalhadas sobre pedidos com êxito e a um serviço de armazenamento. Estas informações podem ser utilizadas para monitorizar os pedidos individuais que podem ajudar na documentação de acesso a dados pessoais armazenados no serviço. No entanto, o registo de análise de armazenamento não está ativado por predefinição para a sua conta de armazenamento. Pode ativá-la no portal do Azure.

#### <a name="how-do-i-configure-monitoring-for-a-storage-account"></a>Como configurar a monitorização de uma conta de armazenamento?

Para configurar a monitorização de uma conta do storage, efetue o seguinte:

1. Selecione **contas do Storage** no portal do Azure, em seguida, selecione o nome da conta que pretende monitorizar.

    ![](media/protection-personal-data-azure-reporting-tools/image011.png)

2. No **monitorização** secção, selecione **diagnóstico.**

3.  Selecione o **tipo** dos dados de métricas que pretende monitorizar para cada serviço (ficheiro de Blob, tabela,). Ao instruir o Storage do Azure para guardar os registos de diagnóstico de leitura, escrita e pedidos de eliminação para o blob, tabela e serviços da fila, selecione **registos do Blob, tabela registos** e **fila registos.**

    ![](media/protection-personal-data-azure-reporting-tools/image013.png)

4. Com o controlo de deslize na parte inferior, defina o **retenção** política em dias (valor de 1 – 365). A predefinição de é de sete dias.

5. Selecione **guardar** para aplicar as definições de configuração.

Entradas de registo de registo de armazenamento contém as seguintes informações sobre pedidos individuais:

- Informações de temporização, tais como a hora de início, a latência de ponto a ponto e a latência de servidor.

- Introduza os detalhes da operação de armazenamento, tais como a operação, a chave do objeto de armazenamento do cliente está a aceder, êxito ou falha e o código de estado HTTP devolvido para o cliente.

- Detalhes de autenticação, tais como o tipo de autenticação de cliente utilizou.

- Informações de concorrência como o valor ETag e a última timestamp modificada.

- Os tamanhos das mensagens de pedido e resposta.

Para obter mais instruções sobre como ativar o registo de análise de armazenamento, consulte [monitorizar uma conta de armazenamento no portal do Azure.](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account)

### <a name="azure-security-center"></a>Centro de Segurança do Azure 

[Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/) monitoriza o estado de segurança dos seus recursos do Azure para evitar, detetar ameaças e fornecer recomendações para a responder. Fornece várias formas para ajudar o documento as medidas de segurança que proteger a privacidade dos dados pessoais.

Monitorização de estado de funcionamento de segurança ajuda-o a garantir a conformidade com as políticas de segurança. Monitorização de segurança é uma estratégia proativa que Audita os seus recursos para identificar sistemas que não cumprem organizacionais normas ou melhores práticas. Pode monitorizar o estado de segurança dos seguintes recursos:

- Computação (máquinas virtuais e serviços em nuvem)

- Funcionamento em rede (redes virtuais)

- Dados (servidor e base de dados de auditoria e deteção de ameaças, TDE, encriptação de armazenamento) e armazenamento

- Aplicações (potenciais problemas de segurança)

Problemas de segurança em qualquer uma destas categorias foi representam uma ameaça à privacidade dos dados pessoais.

#### <a name="how-do-i-view-the-security-state-of-my-azure-resources"></a>Como posso ver o estado de segurança dos meus recursos do Azure?

O Centro de Segurança analisa periodicamente o estado de segurança dos seus recursos do Azure. Pode ver quaisquer potenciais vulnerabilidades de segurança que identifica no **prevenção** secção do dashboard.

   ![](media/protection-personal-data-azure-reporting-tools/image014.png)

1. No **prevenção** secção, selecione o **computação** mosaico. Verá aqui uma **descrição geral,** juntamente com o **máquinas virtuais** lista de todas as VMs e os respetivos Estados de segurança e o **serviços em nuvem** lista de funções web e de trabalho monitorizados pelo centro de segurança.

2. No **descrição geral** separador, segundo uma recomendação para visualizar mais informações.

3. No **máquinas virtuais** separador, selecione uma VM para ver detalhes adicionais.

Quando a recolha de dados estiver ativada no Centro de segurança do Azure, o Microsoft Monitoring Agent é automaticamente aprovisionado em todos os existentes e quaisquer novas suportadas máquinas virtuais que estão implementadas. Dados recolhidos a partir deste agente estão armazenados em qualquer um dos existente [Log Analytics](https://azure.microsoft.com/services/log-analytics/) área de trabalho associada à sua subscrição ou uma nova área de trabalho.

[Os relatórios Intelligence da ameaça](https://docs.microsoft.com/azure/security-center/security-center-threat-report) são fornecidos pelo centro de segurança. Estes dão-lhe informações úteis para ajudar a discernir o atacante identidade, os objetivos, ataque atual e histórica campanhas e táticas de TI, procedimentos e as ferramentas utilizados. Informações de mitigação e remediação também estão incluídas.

O objetivo principal destes relatórios incluem ameaça é ajudá-lo a responder efetivamente a ameaça imediata e ajudar a tomar medidas posteriormente para mitigar o problema. As informações nos relatórios também podem ser útil quando o documento a resposta a incidentes de relatório e auditoria fins.

Os relatórios de Intelligence ameaça são apresentados. Formato PDF, acedido através de uma ligação no **relatórios** campo a **suspeito processo executado** painel para cada alerta de segurança no Centro de segurança do Azure.

Para obter mais informações sobre como ver e utilizar o relatório de Intelligence ameaças, consulte [relatório de Intelligence de ameaças de centro de segurança do Azure.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

## <a name="next-steps"></a>Passos Seguintes:

[Introdução ao Azure Active Directory API do relatório](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

[O que é o Log Analytics?](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

[Descrição Geral de Monitorização no Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

[Introdução ao registo de atividade do Azure (vídeo)](https://azure.microsoft.com/resources/videos/intro-activity-log/)
