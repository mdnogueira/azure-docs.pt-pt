---
title: "Guia de Operações e Planeamento do Centro de Segurança | Microsoft Docs"
description: "Este documento ajuda-o a planear antes de adotar o Centro de Segurança do Azure e as considerações relativas à operações diárias."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f984e4a2-ac97-40bf-b281-2f7f473494c4
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: c502e4363dbaa37455d1aad90d1e9fa855fd09b0
ms.contentlocale: pt-pt
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-planning-and-operations-guide"></a>Guia de operações e planeamento do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), arquitetos de TI, analistas de segurança de informações e administradores de nuvem cujas organizações estejam a planear utilizar o Centro de Segurança do Azure.

>[!NOTE] 
>A partir do início de junho de 2017, o Centro de Segurança irá utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Veja [Azure Security Center Platform Migration](security-center-platform-migration.md) (Migração da Plataforma do Centro de Segurança do Azure) para saber mais. As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>

## <a name="planning-guide"></a>Guia de planeamento
Este guia aborda um conjunto de passos e tarefas que pode seguir para otimizar a utilização do Centro de Segurança do Azure com base nos requisitos de segurança e o modelo de gestão de nuvem da sua organização. Para tirar o máximo partido do Centro de Segurança, é importante compreender de que forma diferentes pessoas ou equipas na sua organização utilizam o serviço para dar resposta às necessidades de desenvolvimento e operações seguras, monitorização, governação e resposta a incidentes. As principais áreas a considerar quando planear utilizar o Centro de Segurança são:

* Funções de Segurança e Controlos de Acesso
* Recomendações e Políticas de Segurança
* Armazenamento e Recolha de Dados
* Monitorização de Segurança em Curso
* Resposta a Incidentes

Na secção seguinte irá aprender a planear cada uma dessas áreas e aplicar essas recomendações com base nos seus requisitos.

> [!NOTE]
> Leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](security-center-faq.md) para obter uma lista de perguntas comuns que também podem ser úteis durante a fase de conceção e planeamento.
> 

## <a name="security-roles-and-access-controls"></a>Funções de segurança e controlos de acesso
Dependendo do tamanho e da estrutura da sua organização, várias pessoas e equipas podem utilizar o Centro de Segurança para executar diversas tarefas relacionadas com a segurança. No diagrama abaixo segue-se um exemplo de pessoas fictícias e as respetivas funções e responsabilidades de segurança:

![Funções](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig01-new.png)

O Centro de Segurança permite que estes indivíduos cumpram estas variadas responsabilidades. Por exemplo:

**Jorge (Proprietário da Carga de Trabalho de Nuvem)**

* Gerir uma carga de trabalho de nuvem e os respetivos recursos relacionados
* Responsável pela implementação e manutenção de proteções de acordo com a política de segurança da empresa

**Helena (CISO/CIO)**

* Responsável por todos os aspetos de segurança na empresa
* Pretende compreender a posição sobre segurança da empresa em cargas de trabalho de nuvem
* Tem de ser informado sobre principais ataques e riscos

**Diogo (Segurança de TI)**

* Define as políticas de segurança da empresa para assegurar que estão implementadas as proteções adequadas
* Monitoriza a conformidade com as políticas
* Gera relatórios para liderança ou auditores

**Júlia (Operações de Segurança)**

* Monitoriza e responde a alertas de segurança 24/7
* Escala para o proprietário de carga de trabalho da nuvem ou Analista de Segurança de TI

**Samuel (Analista de Segurança)**

* Investigar ataques
* Funciona com o Proprietário de Carga de Trabalho da Cloud para aplicar a remediação 

O Centro de Segurança utiliza o [Controlo de Acesso Baseado em Funções (RBAC)](../active-directory/role-based-access-control-configure.md), que fornece [funções incorporadas](../active-directory/role-based-access-built-in-roles.md) que podem ser atribuídas a utilizadores, grupos e serviços no Azure. Quando um utilizador abre o Centro de Segurança, vê apenas informações relacionadas com os recursos a que tem acesso. Isso significa que é atribuído ao utilizador a função de Proprietário, Contribuinte ou Leitor para a subscrição ou grupo de recursos a que pertence um recurso. Além destas funções, há duas funções específicas do Centro de Segurança:

- **Leitor de segurança**: o utilizador que pertence a esta função consegue ver os direitos do Centro de Segurança, incluindo as recomendações, os alertas, a política e o estado de funcionamento, mas não consegue fazer alterações.
- **Administrador de segurança**: igual ao leitor de segurança, mas também pode atualizar a política de segurança, bem como ignorar recomendações e alertas.

As funções do Centro de Segurança descritas acima não têm acesso a outras áreas de serviço do Azure como o Armazenamento, Web e Móvel ou a Internet das Coisas.  

> [!NOTE]
> Um utilizador tem de ser, pelo menos, um contribuidor ou proprietário de uma subscrição ou grupo de recursos para conseguir ver o Centro de Segurança no Azure. 
> 
> 

Utilizando as pessoas fidedignas explicadas no diagrama anterior, é necessário o seguinte RBAC:

**Jorge (Proprietário da Carga de Trabalho de Nuvem)**

* Proprietário/Colaborador de Grupo de Recursos

**Diogo (Segurança de TI)**

* Proprietário/Colaborador de Subscrição ou Administrador de Segurança

**Júlia (Operações de Segurança)**

* Leitor de Subscrição ou Leitor de Segurança para visualizar Alertas
* Proprietário/Colaborador de Subscrição ou Administrador de Sistema necessário para dispensar Alertas

**Samuel (Analista de Segurança)**

* Leitor de Subscrição para visualizar Alertas
* Proprietário/Colaborador de Subscrição necessário para dispensar Alertas
* O acesso à área de trabalho poderá ser necessário

Mais algumas informações importantes a considerar:

* Apenas os Proprietários/Contribuintes de subscrição e Administradores de Segurança podem editar uma política de segurança
* Apenas os Proprietários e os Contribuintes do grupo de recursos e de subscrição podem aplicar recomendações de segurança a um recurso

Quando planear o controlo de acesso através do RBAC para o Centro de Segurança, certifique-se de que compreende quem na sua organização vai utilizar o Centro de Segurança. Além disso, os tipos de tarefas que vão executar e, em seguida, configura o RBAC em conformidade.

> [!NOTE]
> Recomendamos que atribua a função menos permissiva necessária para que os utilizadores concluam as respetivas tarefas. Por exemplo, os utilizadores que apenas necessitam de ver informações acerca do estado de segurança dos recursos, mas não têm de tomar medidas, como aplicar as recomendações ou editar políticas, também devem ter atribuída a função de Leitor.
> 
> 

## <a name="security-policies-and-recommendations"></a>Recomendações e políticas de segurança
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada. No Centro de Segurança, é possível definir políticas de acordo com os requisitos de segurança da sua empresa e com o tipo de aplicações ou sensibilidade dos dados.

As políticas ativadas ao nível da subscrição são propagadas automaticamente para todos os grupos de recursos na subscrição, como indicado no seguinte diagrama:

![Políticas de Segurança](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig2-newUI.png)

> [!NOTE]
> Se precisar de rever as políticas que foram alteradas, pode utilizar os [Registos de Auditoria do Azure](https://blogs.msdn.microsoft.com/cloud_solution_architect/2015/03/10/audit-logs-for-azure-events/). As alterações de políticas são sempre registadas nos Registos de Auditoria do Azure.
> 
> 

### <a name="security-recommendations"></a>Recomendações de segurança
Antes de configurar as políticas de segurança, reveja cada uma das [recomendações de segurança](security-center-recommendations.md) e determine se estas são adequadas para as suas várias subscrições e grupos de recursos. Também é importante entender qual ação deve ser realizada para abordar as [recomendações de segurança](https://docs.microsoft.com/en-us/azure/security-center/security-center-recommendations) e quem na sua organização será responsável por monitorizar novas recomendações e tomar as medidas necessárias.

O Centro de Segurança irá recomendar que forneça os detalhes de contacto de segurança para a sua subscrição do Azure. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. Leia [Provide security contact details in Azure Security Center (Fornecer detalhes de contacto de segurança no Centro de Segurança do Azure)](security-center-provide-security-contact-details.md) para obter mais informações sobre como ativar esta recomendação.

## <a name="data-collection-and-storage"></a>Armazenamento e recolha de dados
O Centro de Segurança do Azure do Azure utiliza o Agente de Monitorização Microsoft – é o mesmo agente utilizado pelo serviço Operations Management Suite e do serviço Log Analytics – recolher dados de segurança das máquinas virtuais. Os dados recolhidos deste agente serão armazenados nas suas áreas de trabalho do Log Analytics.

### <a name="agent"></a>Agente

Após a recolha de dados estar ativada na política de segurança, o Agente de Monitorização Microsoft (para [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) ou [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents)) é instalado em todas as VMs do Azure e nas novas que forem criadas.  Se a VM já tiver instalado o Agente de Monitorização Microsoft, o Centro de Segurança do Azure irá tirar partido do agente atual instalado. O processo do agente foi concebido para ser não invasivo e ter um impacto muito reduzido no desempenho da VM.

O Agente de Monitorização Microsoft para Windows requer a utilização da Porta TCP 443. Veja o [artigo Resolução de Problemas](security-center-troubleshooting-guide.md) para mais detalhes.

Se a determinada altura pretender desativar a Recolha de Dados, pode desativá-la na política de segurança. No entanto, como o Agente de Monitorização Microsoft pode ser utilizado por outros serviços de gestão e monitorização do Azure, o agente não será desinstalado automaticamente quando desligar a recolha de dados no Centro de Segurança. Pode desinstalar o agente manualmente, se necessário.

> [!NOTE]
> Para encontrar uma lista de VMs suportadas, leia as [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure ](security-center-faq.md).
> 

### <a name="workspace"></a>Área de trabalho

Os dados recolhidos pelo Agente de Monitorização Microsoft (em nome do Centro de Segurança do Azure) serão armazenados nas áreas de trabalho do Log Analytics existentes, associadas à sua subscrição do Azure ou a novas áreas de trabalho, tendo em consideração a Geo da VM. 

No portal do Azure, pode procurar uma lista das áreas de trabalho do Log Analytics, incluindo as que foram criadas pelo Centro de Segurança do Azure. Um grupo de recursos relacionado será criado para as novas áreas de trabalho. Ambos seguirão esta convenção de nomenclatura: 

* Área de trabalho: *DefaultWorkspace-[subscription-ID]-[geo]*
* Grupo de Recursos: *DefaultResouceGroup-[geo]*

Para áreas de trabalho criadas pelo Centro de Segurança do Azure, os dados são retidos durante 30 dias. Para sair de áreas de trabalho, a retenção baseia-se no escalão de preço da área de trabalho.

> [!NOTE]
> A Microsoft está extremamente empenhada em proteger a privacidade e segurança destes dados. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço. Para obter mais informações sobre o processamento de dados e privacidade, leia [Segurança de Dados do Centro de Segurança do Azure](security-center-data-security.md).
> 

## <a name="ongoing-security-monitoring"></a>Monitorização de segurança em curso
Após a configuração inicial e a aplicação das recomendações do Centro de Segurança, o passo seguinte é considerar os processos operacionais do Centro de Segurança.

Para aceder ao Centro de Segurança a partir do portal do Azure, pode clicar em **Procurar** e escrever **Centro de Segurança** no campo **Filtro**. As vistas obtidas pelo utilizador estão em conformidade com os filtros aplicados, o exemplo abaixo mostra um ambiente com vários problemas a abordar:

![dashboard](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig6.png)

> [!NOTE]
> O Centro de Segurança não irá interferir com os seus procedimentos operacionais normais, irá monitorizar passivamente as implementações e facultar recomendações com base nas políticas de segurança que ativou.

Quando optar pela primeira vez por utilizar o Centro de Segurança no atual ambiente do Azure, certifique-se de que revê todas as recomendações, o que pode ser feito no mosaico **Recomendações** ou por recurso (**Computação**, **Rede**, **Armazenamento e dados**, **Aplicação**).

Assim que abordar todas as recomendações, a secção **Prevenção** deve estar verde em todos os recursos que foram abordados. A monitorização contínua neste momento torna-se mais fácil, uma vez que apenas realizará ações com base em alterações nos mosaicos de estado de funcionamento de segurança de recursos e de recomendações.

A secção **Deteção** é mais reativa, pois trata-se de alertas relativos a problemas que estão a ocorrer ou que ocorreram anteriormente e foram detetados pelos controlos do Centro de Segurança e por sistemas de terceiros. O mosaico Alertas de Segurança mostrará gráficos de barras que representam o número de alertas de deteção de ameaças encontradas em cada dia e a respetiva distribuição entre as diferentes categorias de gravidade (baixa, média, alta). Para obter mais informações acerca dos Alertas de Segurança, leia [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).

> [!NOTE]
> Também pode tirar partido do Microsoft Power BI para visualizar os seus dados do Centro de Segurança. Leia [Obter informações a partir de dados do Centro de Segurança do Azure com Power BI](security-center-powerbi.md).
> 
> 

### <a name="monitoring-for-new-or-changed-resources"></a>Monitorização de recursos novos ou alterados
A maior parte dos ambientes do Azure são dinâmicos, com recursos novos que são regularmente acelerados ou desacelerados, configurações ou alterações, etc. O Centro de Segurança ajuda-o a garantir que tem visibilidade em relação ao estado de segurança destes novos recursos.

Quando adicionar novos recursos (VMs, BDs SQL) ao seu Ambiente do Azure, o Centro de Segurança irá detetar automaticamente estes recursos e começar a monitorizar a segurança dos mesmos. Isto também inclui funções da Web e funções de trabalho PaaS. Se a Recolha de Dados estiver ativada na [Política de Segurança](security-center-policies.md), serão ativadas automaticamente capacidades de monitorização adicionais para as suas máquinas virtuais.

![Áreas principais](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig3-newUI.png)

1. Para máquinas virtuais, clique em **Computação**, na secção **Prevenção**. Quaisquer problemas com a ativação de dados ou recomendações relacionadas aparecerão no separador **Descrição Geral** e na secção **Recomendações de Monitorização**.
2. Visualize as **Recomendações** para ver que riscos de segurança foram identificados para o novo recurso, se for o caso.
3. É muito comum que, quando novas VMs são adicionadas ao seu ambiente, apenas o sistema operativo é inicialmente instalado. O proprietário do recurso pode necessitar de algum tempo para implementar outras aplicações que serão utilizadas por estas VMs.  Idealmente, deve ter conhecimento da intenção final desta carga de trabalho. Vai ser um Servidor de Aplicações? Com base no que esta nova carga de trabalho vai ser, pode ativar a **Política de Segurança** adequada, que é o terceiro passo deste fluxo de trabalho.
4. À medida que são adicionados novos recursos ao seu ambiente do Azure, é possível que apareçam novos alertas no mosaico **Alertas de Segurança**. Verifique sempre se existem novos alertas neste mosaico e tome medidas de acordo com as recomendações do Centro de Segurança.

Também é aconselhável que monitorize regularmente o estado dos recursos existentes para identificar as alterações de configuração que criaram riscos de segurança, que se desviam das linhas de base recomendadas e dos alertas de segurança. Comece no dashboard do Centro de Segurança. A partir daí tem três áreas principais para rever de forma consistente.

![Operações](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig4-newUI.png)

1. O painel da secção **Prevenção** fornece-lhe acesso rápido aos seus recursos principais. Utilize esta opção para monitorizar a Computação, a Rede, o Armazenamento e dados e as Aplicações.
2. O painel **Recomendações** permite-lhe rever as recomendações do Centro de Segurança. Durante a monitorização contínua, pode constatar que não tem recomendações diariamente. Tal é normal, uma vez que abordou todas as recomendações na configuração inicial do Centro de Segurança. Por este motivo, é possível que não tenha novas informações nesta secção todos os dias e que apenas tenha de aceder à mesma conforme seja necessário.
3. A secção **Deteção** pode ser alterada de forma bastante frequente ou pouco frequente. Reveja sempre os alertas de segurança e tome medidas com base nas recomendações do Centro de Segurança.

## <a name="incident-response"></a>Resposta a incidentes
O Centro de Segurança deteta e alerta-o relativamente a ameaças à medida que ocorrem. As organizações devem monitorizar a existência de novos alertas de segurança e tomar medidas conforme seja necessário para uma investigação mais aprofundada ou para remediar o ataque. Para obter mais informações sobre como funciona a deteção de ameaças do Centro de Segurança, leia [Azure Security Center detection capabilities (Capacidades de deteção do Centro de Segurança do Azure)](security-center-detection-capabilities.md).

Embora este artigo não pretenda ajudá-lo a criar o seu próprio plano de Resposta a Incidentes, iremos utilizar o Microsoft Azure Security Response no ciclo de vida da Nuvem como a base para as fases de resposta a incidentes. As fases são apresentadas no diagrama seguinte:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-1.png)

> [!NOTE]
> Pode utilizar o [Computer Security Incident Handling Guide](http://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf) (Guia de Processamento de Incidentes de Segurança Informática) do National Institute of Standards and Technology (NIST) (Instituto Nacional de Normas e Tecnologia dos Estados Unidos) como referência para o ajudar a criar o seu próprio.
> 

Pode utilizar Alertas do Centro de Segurança durante as seguintes fases:

* **Detetar**: identifique uma atividade suspeita num ou mais recursos. 
* **Avaliar**: efetue a avaliação inicial para obter mais informações sobre a atividade suspeita.
* **Diagnosticar**: utilize os passos de remediação para realizar o procedimento técnico para resolver o problema.

Cada Alerta de Segurança faculta informações que podem ser utilizadas para compreender melhor a natureza do ataque e sugerir possíveis mitigações. Alguns alertas também fornecem ligações para mais informações ou para outras fontes de informação no Azure. Pode utilizar as informações fornecidas para uma pesquisa aprofundada e começar a mitigação, e também pode pesquisar os dados relacionados com a segurança que estejam armazenados na área de trabalho.

O exemplo seguinte mostra uma atividade suspeita de RDP em curso:

![Atividade suspeita](./media/security-center-planning-and-operations-guide/security-center-planning-and-operations-guide-fig5-ga.png)

Como pode ver, este painel mostra informações detalhadas acerca da hora em que ocorreu o ataque, o nome do anfitrião de origem, a VM visada e também lhe disponibiliza passos de recomendação. Em determinadas circunstâncias, as informações da origem do ataque podem estar vazias. Leia [Informações da Origem em Falta nos Alertas do Centro de Segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/25/missing-source-information-in-azure-security-center-alerts/) para obter mais informações acerca deste tipo de comportamento.

No vídeo [Como tirar partido do Centro de segurança do Azure e do Microsoft Operations Management Suite para uma resposta a incidentes](https://channel9.msdn.com/Blogs/Taste-of-Premier/ToP1703) pode ver algumas demonstrações que podem ajudá-lo a compreender como o Centro de segurança pode ser utilizado em cada uma destas fases.

> [!NOTE]
> Leia [Tirar Partido do Centro de Segurança do Azure para Resposta a Incidentes](security-center-incident-response.md) para obter mais informações sobre como utilizar as capacidades do Centro de Segurança para o ajudar durante os processos de Resposta a Incidentes. 
> 
> 

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a planear a adoção do Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.


