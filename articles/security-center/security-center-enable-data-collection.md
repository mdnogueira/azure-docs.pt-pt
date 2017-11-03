---
title: "Recolha de dados no Centro de segurança do Azure | Microsoft Docs"
description: " Saiba como ativar a recolha de dados no Centro de segurança do Azure. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: terrylan
ms.openlocfilehash: 226fc82abf7aa24a0aa1bd3c21279158e1ce8e95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="data-collection-in-azure-security-center"></a>Recolha de dados no Centro de segurança do Azure
Centro de segurança recolhe dados de máquinas de virtuais (VMs) do Azure e computadores não do Azure para monitorizar vulnerabilidades de segurança e ameaças. Os dados são recolhidos através do Microsoft Monitoring Agent, que lê várias configurações relacionadas com segurança e registos de eventos a partir da máquina e copia os dados para a sua área de trabalho para análise. Os exemplos destes dados incluem: tipo e versão do sistema operativo, registos de sistema operativo (registos de eventos do Windows), processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino. O Microsoft Monitoring Agent também copia os ficheiros de informação de falha para a área de trabalho.

## <a name="enable-automatic-provisioning-of-microsoft-monitoring-agent"></a>Ativar aprovisionamento automático do Microsoft Monitoring Agent     
Quando o aprovisionamento automático está ativado, Aprovisiona o Centro de segurança do Microsoft Monitoring Agent em todos os suportadas VMs do Azure e quaisquer novos que são criados. Aprovisionamento Automático é vivamente recomendado, não sendo necessário para subscrições na camada padrão do Centro de segurança.

> [!NOTE]
> Desativar automática aprovisionamento limites monitorização de segurança para os seus recursos. Para obter mais informações, consulte [desativar aprovisionamento automático](security-center-enable-data-collection.md#disable-automatic-provisioning) neste artigo. Coleção de artefacto e instantâneos de discos VM estão ativadas, mesmo se o aprovisionamento automático está desativado.
>
>

Para ativar o aprovisionamento automático do Microsoft Monitoring Agent:
1. No menu principal do Centro de segurança, selecione **política de segurança**.
2. Selecione uma subscrição.
3. Em **política de segurança**, selecione **recolha de dados**.
4. Em **integração**, selecione **no** para ativar o aprovisionamento automático.
5. Selecione **Guardar**.

![Ativar aprovisionamento automático][1]

## <a name="default-workspace-configuration"></a>Configuração de área de trabalho predefinida
Dados recolhidos pelo centro de segurança são armazenados no workspace(s) de análise de registos.  Pode optar por ter dados recolhidos a partir de VMs do Azure armazenados no áreas de trabalho criadas pelo centro de segurança nem de uma área de trabalho existente que criou.

Para utilizar a sua área de trabalho de análise de registos existente:
- A área de trabalho tem de estar associada com a sua subscrição do Azure selecionada.
- No mínimo, é necessário ter leitura permissões para aceder à área de trabalho.

Para selecionar uma área de trabalho de análise de registos existente:

1. Em **política de segurança – recolha de dados**, selecione **utilizar outra área de trabalho**.

   ![Selecione a área de trabalho existente][2]

2. No menu pendente, selecione uma área de trabalho para armazenar os dados recolhidos.

> [!NOTE]
> Na solicitação menu pendente, são apresentadas apenas áreas de trabalho que tenha acesso e estão na sua subscrição do Azure.
>
>

3. Selecione **Guardar**.
4. Depois de selecionar **guardar**, será pedido se gostaria de VMs de reconfigure monitorizado.

   - Selecione **não** se pretender que as novas definições de área de trabalho para aplicar novas VMs apenas. As novas definições de área de trabalho só se aplicam a novas instalações de agente; VMs detetadas recentemente que não têm o Microsoft Monitoring Agent instalada.
   - Selecione **Sim** se pretender que as novas definições de área de trabalho para aplicar a todas as VMs. Além disso, cada VM ligado a uma área de trabalho de criar o Centro de segurança é novamente ligado para a nova área de trabalho de destino.

   > [!NOTE]
   > Se selecionar Sim, não terá de eliminar o workspace(s) criados pelo centro de segurança até que todas as VMs tiverem sido sido religadas para a nova área de trabalho de destino. Esta operação irá falhar se uma área de trabalho é eliminada demasiado antigo.
   >
   >

   - Selecione **Cancelar** para cancelar a operação.

   ![Selecione a área de trabalho existente][3]

## <a name="data-collection-tier"></a>Camada de recolha de dados
Centro de segurança pode reduzir o volume de eventos, mantendo em simultâneo suficiente eventos para investigação, auditoria e deteção de ameaças. Pode escolher o direito de filtragem de política para as suas subscrições e áreas de trabalho de quatro conjuntos de eventos recolhidos pelo agente.

- **Todos os eventos** – para os clientes que deve certificar-se de que todos os eventos são recolhidos. Esta é a predefinição.
- **Comuns** – este é um conjunto de eventos que satisfaça a maioria dos clientes e permite-lhes uma avaliação completa de auditoria.
- **Mínimo** – um conjunto mais pequeno de eventos para os clientes que pretendem minimizar o volume de eventos.
- **Nenhum** – desativar a recolha de eventos de segurança de segurança e do App Locker registos. Para os clientes que escolha esta opção, os seus dashboards de segurança tem apenas registos de Firewall do Windows e avaliações proativa como antimalware, a linha de base e a atualização.

> [!NOTE]
> Estes conjuntos foram concebidos para abordar cenários típicos. Certifique-se ao avaliar quais um necessidades da sua antes de a implementar.
>
>

Para determinar os eventos que irão pertencer ao **comuns** e **mínima** conjuntos de evento, iremos trabalhou com os clientes e normas da indústria para saber mais sobre a frequência e não filtrada da respetiva utilização e cada evento. Utilizámos as seguintes diretrizes neste processo:

- **Mínimo** -Certifique-se de que este conjunto abrange apenas os eventos que possam indicar uma violação com êxito e eventos importantes que têm um volume muito baixo. Por exemplo, este conjunto contém utilizador com êxito e início de sessão (evento 4624 de IDs, 4625), mas não contém o fim de sessão que é importante para auditoria mas não significativo para deteção e tem relativamente elevado volume. A maioria do volume de dados deste conjunto é os eventos de início de sessão e os eventos de criação do processo (evento ID 4688).
- **Comuns** -forneça um registo de auditoria de utilizador completo deste conjunto. Por exemplo, este conjunto contém inícios de sessão do utilizador e de fim de sessão do utilizador (evento ID 4634). Incluímos auditoria ações como alterações ao grupo de segurança, operações de Kerberos do controlador de domínio de chave e outros eventos que são recomendados por organizações da indústria.

Eventos com um volume muito baixa foram incluídos no comuns definido como a motivação principal para escolher o ao longo de todos os eventos é para reduzir o volume e não para filtrar os eventos específicos.

Eis uma divisão de segurança e do App Locker de IDs de evento para cada conjunto concluída:

   ![IDs de evento][4]

Para escolher a política de filtragem:
1. No **definições de política de segurança &** painel, selecione a política de filtragem em **eventos de segurança**.
2. Selecione **Guardar**.

   ![Escolha a política de filtragem][5]

## <a name="disable-automatic-provisioning"></a>Desativar aprovisionamento automático
Pode desativar aprovisionamento automático de recursos em qualquer altura desativar esta definição na política de segurança. Aprovisionamento Automático é altamente recomendável para obter os alertas de segurança e recomendações sobre as atualizações do sistema, endpoint protection e vulnerabilidades do SO.

> [!NOTE]
> Desativar aprovisionamento automático não remover o Microsoft Monitoring Agent VMs do Azure onde o agente tiver sido aprovisionado.
>
>

1. Regressar ao menu principal do Centro de segurança e selecione a política de segurança.

   ![Desativar aprovisionamento automático][6]

2. Selecione a subscrição que pretende desativar aprovisionamento automático.
3. No **política de segurança – recolha de dados** painel, em **integração** selecione **desativar** para desativar aprovisionamento automático.
4. Selecione **Guardar**.  

## <a name="next-steps"></a>Passos seguintes
Este artigo como mostrado a recolha de dados e o aprovisionamento automático no Centro de segurança funciona. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-policies.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como recomendações ajudam a proteger os seus recursos do Azure.
* [Monitorização do estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
- [Segurança de dados do Centro de segurança do Azure](security-center-data-security.md) -Saiba como os dados são geridos e salvaguardados no Centro de segurança.
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Obtenha as notícias e informações mais recentes de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[4]: ./media/security-center-enable-data-collection/event-id.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-automatic-provisioning.png
