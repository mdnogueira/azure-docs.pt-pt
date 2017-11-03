---
title: "Migração de plataforma do Centro de segurança FAQ | Microsoft Docs"
description: "Estas perguntas mais frequentes respondem a dúvidas sobre a migração de plataforma Centre de segurança do Azure."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 4d1364cd-7847-425a-bb3a-722cb0779f78
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/30/2017
ms.author: terrylan
ms.openlocfilehash: 69d0c368eb11953d1a6e954990a3be10df7044f0
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="security-center-platform-migration-faq"></a>Migração de plataforma do Centro de segurança FAQ
No precoce de Junho de 2017, o Centro de segurança do Azure começou a utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Para obter mais informações, consulte [migração de plataforma de centro de segurança do Azure](security-center-platform-migration.md). Estas perguntas mais frequentes respondem a dúvidas sobre a migração de plataforma.

## <a name="data-collection-agents-and-workspaces"></a>Recolha de dados, os agentes e áreas de trabalho

### <a name="how-is-data-collected"></a>Como os dados são recolhidos?
Centro de segurança utiliza o Microsoft Monitoring Agent para recolher dados de segurança das suas VMs. Os dados de segurança incluem informações sobre:

- configurações de segurança - utilizadas para identificar vulnerabilidades
- eventos de segurança - utilizados para detetar ameaças

Dados recolhidos pelo agente são armazenados numa área de trabalho do Log Analytics existente ligado à VM ou uma nova área de trabalho criados pelo centro de segurança. Quando o Centro de segurança cria uma nova área de trabalho, a geolocalização da VM é contemplada.

> [!NOTE]
> O Microsoft Monitoring Agent é o mesmo agente utilizado pelo Operations Management Suite (OMS), serviço de análise de registos e o System Center Operations Manager (SCOM).
>
>

Quando o aprovisionamento automático (anteriormente denominado registo de coleção) está ativado ou quando as subscrições são migradas, o Centro de segurança verifica se o Microsoft Monitoring Agent já está instalado como uma extensão do Azure em cada uma das suas VMs. Se o Microsoft Monitoring Agent não está instalado, em seguida, por predefinição o Centro de segurança irá:

- Instale a extensão do Microsoft Monitoring Agent na VM.

   - Se uma área de trabalho criada pelo centro de segurança já existe a mesma geolocalização da VM, o agente está ligado a esta área de trabalho.
   - Se não existir uma área de trabalho, o Centro de segurança cria uma novo recurso grupo predefinido área de trabalho e em que geolocalização e liga o agente para essa área de trabalho. A Convenção de nomenclatura para o grupo de recursos e área de trabalho é:

       Área de trabalho: DefaultWorkspace-[ID de subscrição]-[georreplicação]

       Grupo de recursos: DefaultResouceGroup-[georreplicação]

- Ativar uma solução de centro de segurança na área de trabalho por VM associado no Centro de segurança do escalão de preço. Para obter mais informações sobre preços, consulte [preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/).
- Para migrados subscrições, o Centro de segurança removerá também o agente de monitorização do Azure anteriores.

> [!NOTE]
> Pode substituir a instalação automática do Microsoft Monitoring Agent e utilizar a seus próprios área de trabalho.  Consulte [como parar a criação automática do agente de instalação e a área de trabalho](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation) e [como utilizar a sua área de trabalho existente](#how-can-i-use-my-existing-log-analytics-workspace).
>
>

A localização da área de trabalho é com base na localização da VM. Para obter mais informações, consulte [segurança dos dados](security-center-data-security.md). Se uma subscrição contém geolocations várias VMs, em seguida, o Centro de segurança cria várias áreas de trabalho. Várias áreas de trabalho são criadas para manter as regras de privacidade de dados.

> [!NOTE]
> Antes da migração de plataforma, o Centro de segurança recolhidos dados de segurança das VMs com o agente de monitorização do Azure e dados que foram armazenados na sua conta de armazenamento. Após a migração de plataforma, o Centro de segurança utiliza o Microsoft Monitoring Agent e a área de trabalho para recolher e armazenar os mesmos dados. A conta de armazenamento pode ser removida após a migração.  Centro de segurança também remove agentes de monitorização de Azure instaladas anteriormente após a migração de plataforma.
>
>

### <a name="am-i-billed-for-log-analytics-or-oms-on-the-workspaces-created-by-security-center"></a>Estou cobrado para análise de registos ou OMS nas áreas de trabalho criadas pelo centro de segurança?
Não. Áreas de trabalho criadas pelo centro de segurança, embora a configurada para OMS por faturação de nó, não pagar OMS. Faturação do Centro de segurança baseia-se sempre na sua política de segurança do Centro de segurança e as soluções instaladas na área de trabalho:

- **Escalão gratuito** – o Centro de segurança permite que a solução de 'SecurityCenterFree' na área de trabalho predefinida. Não são cobradas para o escalão gratuito.
- **Escalão Standard** – o Centro de segurança permite que a solução de 'Security' na área de trabalho predefinida.

Para obter mais informações sobre preços, consulte [preços do Centro de segurança](https://azure.microsoft.com/pricing/details/security-center/). A página de preços aborda as alterações ao armazenamento de dados de segurança e proporcional faturação a partir de Junho de 2017.

> [!NOTE]
> O OMS escalão de áreas de trabalho criados pelo centro de segurança de preço não afeta a faturação do Centro de segurança.
>
>

### <a name="what-qualifies-a-vm-for-automatic-provisioning-of-the-microsoft-monitoring-agent-installation"></a>Uma VM que qualificam para o aprovisionamento automático da instalação do Microsoft Monitoring Agent?
Windows ou Linux VMs de IaaS qualificam se:

- A extensão do Microsoft Monitoring Agent não está atualmente instalada na VM.
- A VM se encontra em estado de execução.
- O Windows ou Linux agente da VM está instalado.
- A VM não é utilizada como um dispositivo, como a firewall de aplicação web ou firewall da próxima geração.

### <a name="can-i-delete-the-default-workspaces-created-by-security-center"></a>Pode eliminar as áreas de trabalho predefinida criadas pelo centro de segurança?
**Não é recomendada a eliminar a área de trabalho predefinida.** Centro de segurança utiliza as áreas de trabalho predefinida para armazenar dados de segurança das suas VMs.  Se eliminar uma área de trabalho, o Centro de segurança não consegue recolher estes dados e algumas recomendações de segurança e alertas não estão disponíveis.

Para recuperar, remova o Microsoft Monitoring Agent em VMs ligadas à área de trabalho foi eliminada. Centro de segurança reinstala o agente e cria novas áreas de trabalho predefinido.

### <a name="how-can-i-use-my-existing-log-analytics-workspace"></a>Como posso utilizar a minha área de trabalho de análise de registos existente?

Pode selecionar uma área de trabalho de análise de registos existente para armazenar os dados recolhidos pelo centro de segurança. Para utilizar a sua área de trabalho de análise de registos existente:

- A área de trabalho tem de estar associada com a sua subscrição do Azure selecionada.
- No mínimo, é necessário ter leitura permissões para aceder à área de trabalho.

Para selecionar uma área de trabalho de análise de registos existente:

1. Em **política de segurança – recolha de dados**, selecione **utilizar outra área de trabalho**.

   ![Utilizar outra área de trabalho][5]

2. No menu pendente, selecione uma área de trabalho para armazenar os dados recolhidos.

   > [!NOTE]
   > Na solicitação menu pendente, são apresentadas apenas áreas de trabalho que tenha acesso e estão na sua subscrição do Azure.
   >
   >

3. Selecione **Guardar**.
4. Depois de selecionar **guardar**, será pedido se gostaria de VMs de reconfigure monitorizado.

   - Selecione **não** se pretender que as novas definições de área de trabalho para **aplicar novas VMs apenas**. As novas definições de área de trabalho só se aplicam a novas instalações de agente; VMs detetadas recentemente que não têm o Microsoft Monitoring Agent instalada.
   - Selecione **Sim** se pretender que as novas definições de área de trabalho para **aplicar todas as VMs**. Além disso, cada VM ligado a uma área de trabalho de criar o Centro de segurança é novamente ligado para a nova área de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, não terá de eliminar o workspace(s) criados pelo centro de segurança até que todas as VMs tiverem sido sido religadas para a nova área de trabalho de destino. Esta operação irá falhar se uma área de trabalho é eliminada demasiado antigo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

      ![Reconfigurar VMs monitorizadas][6]

### <a name="what-if-the-microsoft-monitoring-agent-was-already-installed-as-an-extension-on-the-vm"></a>E se o Microsoft Monitoring Agent já foi instalado como uma extensão da VM?
Centro de segurança não substitui as ligações existentes a áreas de trabalho do utilizador. Centro de segurança armazena os dados de segurança da VM na área de trabalho que já estabeleceu ligada. A versão da extensão para incluir o ID de recurso do Azure da VM para suportar a utilização do Centro de segurança de atualizações do Centro de segurança.

### <a name="what-if-i-had-a-microsoft-monitoring-agent-installed-on-the-machine-but-not-as-an-extension"></a>E se posso tinha um Microsoft Monitoring Agent instalada no computador, mas não como uma extensão?
Se o Microsoft Monitoring Agent é instalado diretamente na VM (não como uma extensão do Azure), o Centro de segurança não instala o Microsoft Monitoring Agent e monitorização de segurança é limitado.

Para obter mais informações, consulte a secção seguinte [o que acontece se um SCOM ou OMS direcionar o agente já está instalado na minha VM?](security-center-platform-migration-faq.md#what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm)

### <a name="what-happens-if-a-scom-or-oms-direct-agent-is-already-installed-on-my-vm"></a>O que acontece se um SCOM ou OMS direcionar o agente já se encontra instalada na minha VM?
Centro de segurança não consegue identificar seguinte com antecedência que um agente está instalado.  O Centro de segurança tenta instalar a extensão do Microsoft Monitoring Agent e falhar devido ao agente instalado existente.  Esta falha impede a substituir as definições de ligação do agente para a área de trabalho e evita multi homing a criar.

> [!NOTE]
> A versão do agente é atualizada para a versão mais recente do agente OMS.  Isto aplica-se aos utilizadores SCOM também.
>
>

### <a name="what-is-the-impact-of-removing-these-extensions"></a>O que é o impacto da remoção estas extensões?
Se remover a extensão de monitorização da Microsoft, o Centro de segurança não consegue recolher dados de segurança a VM e algumas recomendações de segurança e alertas não estão disponíveis. Dentro de 24 horas, o Centro de segurança determina que a VM está em falta a extensão e reinstalará a extensão.

### <a name="how-do-i-stop-the-automatic-agent-installation-and-workspace-creation"></a>Como parar a criação automática do agente de instalação e a área de trabalho?
Pode desativar aprovisionamento automático para as suas subscrições na política de segurança, mas isto não é recomendado. Desativar alertas e os limites de aprovisionamento automáticos as recomendações do Centro de segurança. Aprovisionamento Automático é necessário para subscrições no escalão de preço padrão. Para desativar aprovisionamento automático:

1. Se a sua subscrição está configurada para o escalão Standard, abra a política de segurança para essa subscrição e selecione o **livres** camada.

   ![Escalão de preço][1]

2. Em seguida, desativar aprovisionamento automático selecionando **desativar** no **política de segurança – recolha de dados** painel.
   ![Recolha de dados][2]

### <a name="should-i-opt-out-of-the-automatic-agent-installation-and-workspace-creation"></a>Deve incluir fora de instalação automática do agente e a criação da área de trabalho?

> [!NOTE]
> Lembre-se de que reveja secções [quais são as implicações de aceitar?](#what-are-the-implications-of-opting-out-of-automatic-provisioning) e [recomendado passos quando desativar](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning) se optar ativamente por aprovisionamento automático.
>
>

Poderá pretender ativamente aprovisionamento automático, se o seguinte aplica-se a si:

- Instalação automática do agente pelo centro de segurança aplica-se para a subscrição completa.  Não é possível aplicar a instalação automática para um subconjunto de VMs. Se forem críticas VMs que não podem ser instaladas com o Microsoft Monitoring Agent, deve ativamente por aprovisionamento automático.
- Versão do agente de atualizações de instalação da extensão do Microsoft Monitoring Agent. Isto aplica-se um agente direto e o agente do SCOM. Se o agente do SCOM instalado uma versão 2012 e é atualizado, as capacidades de capacidade de gestão podem ser perdidas quando o servidor do SCOM é também versão 2012. Deve considerar a aceitar fora do aprovisionamento automático, se o agente do SCOM instalado versão 2012.
- Se tiver uma área de trabalho personalizada externa para a subscrição (uma área de trabalho centralizada) deve ativamente por aprovisionamento automático. Pode instalar a extensão do Microsoft Monitoring Agent manualmente e ligá-lo a área de trabalho sem Centro de segurança substituir a ligação.
- Se quiser evitar a criação de várias áreas de trabalho por subscrição e tem a suas próprias área de trabalho personalizada dentro da subscrição, em seguida, tem duas opções:

   1. Pode optar ativamente por participar fora do aprovisionamento automático. Após a migração, predefinir as definições da área de trabalho conforme descrito em [como pode utilizar a minha área de trabalho de análise de registos existente?](#how-can-i-use-my-existing-log-analytics-workspace)
   2. Em alternativa, pode permitir a migração concluir, o Microsoft Monitoring Agent para ser instalada nas VMs, e as VMs ligado à área de trabalho criada. Em seguida, selecione a suas próprias área de trabalho personalizada ao definir a definição predefinida da área de trabalho com aceitar para reconfigurar agentes já instalados. Para obter mais informações, consulte [como pode utilizar a minha área de trabalho de análise de registos existente?](#how-can-i-use-my-existing-log-analytics-workspace)

### <a name="what-are-the-implications-of-opting-out-of-automatic-provisioning"></a>Quais são as implicações de desativar aprovisionamento automático fora?
Após a conclusão da migração, o Centro de segurança não consegue recolher dados de segurança a VM e algumas recomendações de segurança e alertas não estão disponíveis. Se optar por, deve instalar manualmente o Microsoft Monitoring Agent. Consulte [recomendado passos quando desativar](#what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning).

### <a name="what-are-the-recommended-steps-when-opting-out-of-automatic-provisioning"></a>Quais são os passos recomendados quando desativar aprovisionamento automático fora?
Deve instalar manualmente o Microsoft Monitoring Agent para que o Centro de segurança pode recolher dados de segurança das suas VMs e fornecer recomendações e alertas. Consulte [computadores Windows ligar para o serviço de análise de registos do Azure](../log-analytics/log-analytics-windows-agents.md) para obter orientações sobre a instalação.

Pode ligar o agente para qualquer área de trabalho personalizada existente ou centro de segurança criado área de trabalho. Se uma área de trabalho personalizada não tem as soluções de 'Security' ou 'SecurityCenterFree' ativadas, terá de aplicar uma solução. Para aplicar, selecione a área de trabalho personalizada ou de subscrição e aplicar um escalão de preço através de **política de segurança – o escalão de preço** painel.

   ![Escalão de preço][1]

Centro de segurança irá ativar a solução correta na área de trabalho com base no escalão de preço selecionado.

### <a name="how-do-i-remove-oms-extensions-installed-by-security-center"></a>Como posso remover extensões OMS instaladas pelo centro de segurança?
Pode remover manualmente o Microsoft Monitoring Agent. Não é recomendado como limita as recomendações do Centro de segurança e alertas.

> [!NOTE]
> Se a recolha de dados estiver ativada, Centro de segurança irá reinstalar o agente após removê-la.  Terá de desativar a recolha de dados antes de remover manualmente o agente. Consulte [como parar a criação automática do agente de instalação e a área de trabalho?](#how-do-i-stop-the-automatic-agent-installation-and-workspace-creation?) para obter instruções sobre como desativar a recolha de dados.
>
>

Para remover manualmente o agente:

1.  No portal, abra **Log Analytics**.
2.  No painel de análise de registos, selecione uma área de trabalho:
3.  Selecione cada VM que não pretende monitorizar e selecione **desligar**.

   ![Remova o agente][3]

> [!NOTE]
> Se uma VM com Linux já tem um agente do OMS de extensão de nome, remover a extensão remove também o agente e o cliente tem de reinstalá-la.
>
>

## <a name="existing-oms-customers"></a>Clientes existentes do OMS

### <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>O Centro de segurança substituir todas as ligações existentes entre as VMs e áreas de trabalho?
Se uma VM já tem o Microsoft Monitoring Agent instalada como uma extensão do Azure, o Centro de segurança não substitui a ligação de área de trabalho existente. Em vez disso, o Centro de segurança utiliza a área de trabalho existente.

Uma solução de centro de segurança está instalado na área de trabalho se não estiver já presente, e a solução é aplicada apenas às VMs relevantes. Quando adiciona uma solução, é automaticamente implementada por predefinição para todos os agentes Windows e Linux ligado à sua área de trabalho de análise de registos. [Filtragem de solução](../operations-management-suite/operations-management-suite-solution-targeting.md), que é uma funcionalidade do OMS, permite-lhe aplicar um âmbito para as suas soluções.

Se o Microsoft Monitoring Agent é instalado diretamente na VM (não como uma extensão do Azure), o Centro de segurança não instala o Microsoft Monitoring Agent e monitorização de segurança é limitado.

### <a name="what-should-i-do-if-i-suspect-that-the-data-platform-migration-broke-the-connection-between-one-of-my-vms-and-my-workspace"></a>O que posso fazer se posso suspeita que a migração de plataforma de dados quebrou a ligação entre uma das minhas VMs e a minha área de trabalho?
Isto não deve ocorrer. Se esta situação ocorrer, em seguida, [para criar um pedido de suporte do Azure](../azure-supportability/how-to-create-azure-support-request.md) e incluir os seguintes detalhes:

- O ID de recurso do Azure da VM afectado
- O ID de recurso do Azure da área de trabalho configurado na extensão antes da ligação foi interrompida
- O agente e a versão que foi anteriormente instalado

### <a name="does-security-center-install-solutions-on-my-existing-oms-workspaces-what-are-the-billing-implications"></a>Centro de segurança instalar soluções no meu áreas de trabalho do OMS existentes? Quais são as implicações de faturação?
Quando o Centro de segurança identifica que um VM já está ligada a uma área de trabalho que criou, o Centro de segurança permitem soluções nesta área de trabalho, de acordo com o escalão de preço. As soluções são aplicadas apenas para as VMs do Azure relevantes, através de [solução filtragem](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting), por isso, a faturação permanece igual.

- **Escalão gratuito** – Centro de segurança instala a solução de 'SecurityCenterFree', na área de trabalho. Não são cobradas para o escalão gratuito.
- **Escalão Standard** – Centro de segurança instala a solução de 'Security', na área de trabalho.

   ![Soluções na área de trabalho predefinido][4]

> [!NOTE]
> O 'Security' na análise de registos é a solução de segurança e de auditoria no OMS.
>
>

### <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Já tenho áreas de trabalho no meu ambiente, posso utilizá-los para recolher dados de segurança?
Se uma VM já tem o Microsoft Monitoring Agent instalada como uma extensão do Azure, o Centro de segurança utiliza a área de trabalho ligada existente. Uma solução de centro de segurança está instalado na área de trabalho se não estiver já presente, e a solução é aplicada apenas às VMs relevantes através de [solução filtragem](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-solution-targeting).

Quando o Centro de segurança instala o Microsoft Monitoring Agent em VMs, utiliza o workspace(s) predefinida criada pelo centro de segurança. Logo que os clientes poderá configurar qual workspace(s) é utilizados.

### <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Já tenho solução de segurança no meu áreas de trabalho. Quais são as implicações de faturação?
A solução de segurança e de auditoria é utilizada para ativar funcionalidades do escalão padrão de centro de segurança para as VMs do Azure. Se a solução de segurança & auditoria já está instalada na área de trabalho, o Centro de segurança utiliza a solução existente. Não há nenhuma alteração no faturação.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a migração de plataforma do Centro de segurança, consulte

- [Migração de plataforma do Centro de segurança do Azure](security-center-platform-migration.md)
- [Guia de resolução de problemas do Centro de segurança do Azure](security-center-troubleshooting-guide.md)

<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/pricing-tier.png
[2]: ./media/security-center-platform-migration-faq/data-collection.png
[3]: ./media/security-center-platform-migration-faq/remove-the-agent.png
[4]: ./media/security-center-platform-migration-faq/solutions.png
[5]: ./media/security-center-platform-migration-faq/use-another-workspace.png
[6]: ./media/security-center-platform-migration-faq/reconfigure-monitored-vm.png
