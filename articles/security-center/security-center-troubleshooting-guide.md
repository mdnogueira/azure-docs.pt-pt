---
title: "Guia de Resolução de Problemas do Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda a resolver problemas no Centro de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: yurid
ms.openlocfilehash: 0e0a0ce5c0795cec0e47cd5f729099f4762381a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-security-center-troubleshooting-guide"></a>Guia de Resolução de Problemas do Centro de Segurança do Azure
Este guia destina-se a profissionais de tecnologias da informação (TI), analistas de segurança de informações e administradores da cloud cujas organizações utilizam o Centro de Segurança do Azure e que precisam de resolver problemas relacionados com o Centro de Segurança.

>[!NOTE] 
>A partir do início de junho de 2017, o Centro de Segurança utiliza o Microsoft Monitoring Agent para recolher e armazenar dados. Veja [Azure Security Center Platform Migration](security-center-platform-migration.md) (Migração da Plataforma do Centro de Segurança do Azure) para saber mais. As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>

## <a name="troubleshooting-guide"></a>Guia de resolução de problemas
Este guia explica como resolver problemas relacionados com o Centro de Segurança. A maior parte da resolução de problemas feita no Centro de Segurança será realizada ao consultar primeiro os [Registos de Auditoria](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/) quanto ao componente que falhou. Os registos de auditoria permitem-lhe determinar:

* As operações que foram executadas
* Quem iniciou a operação
* Quando ocorreu a operação
* O estado da operação
* Os valores de outras propriedades que possam ajudá-lo a procurar a operação

O registo de auditoria contém todas as operações de escrita (PUT, POST, DELETE) efetuadas nos seus recursos, no entanto, não inclui as operações de leitura (GET).

## <a name="microsoft-monitoring-agent"></a>Agente de Monitorização da Microsoft
O Centro de Segurança utiliza o Agente de Monitorização da Microsoft – é o mesmo agente utilizado pelo Operations Management Suite e o serviço Log Analytics – para recolher dados de segurança das máquinas virtuais do Azure. Assim que a recolha de dados estiver ativa e o agente estiver corretamente instalado no computador de destino, o processo abaixo deve estar em execução:

* HealthService.exe

Se abrir a consola de gestão de serviços (services.msc), também verá o serviço Agente de Monitorização da Microsoft em execução, conforme mostrado abaixo:

![Serviços](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Para ver a versão do agente que tem, abra o **Gestor de Tarefas**, no separador **Processos**, localize o serviço **Agente de Monitorização da Microsoft**, clique no mesmo com o botão direito do rato e clique em **Propriedades**. No separador **Detalhes**, procure a versão do ficheiro, conforme mostrado abaixo:

![Ficheiro](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)
   

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Cenários de instalação do Agente de Monitorização da Microsoft
Existem dois cenários de instalação que podem produzir resultados diferentes ao instalar o Agente de Monitorização da Microsoft no seu computador. Os cenários suportados são:

* **Agente instalado automaticamente pelo Centro de Segurança**: neste cenário poderá ver os alertas em ambos os locais, Centro de Segurança e pesquisa de registos. Receberá notificações por e-mail no endereço de e-mail configurado na política de segurança para a subscrição a que pertence o recurso.
.
* **Agente instalado manualmente numa VM localizada no Azure**: neste cenário, se estiver a utilizar agentes transferidos e instalados manualmente antes de fevereiro de 2017, poderá ver os alertas no portal do Centro de Segurança apenas se filtrar pela subscrição a que pertence a área de trabalho. No caso de filtrar pela subscrição a que pertence o recurso, não poderá ver os alertas. Receberá notificações por e-mail no endereço de e-mail configurado na política de segurança para a subscrição a que pertence a área de trabalho.

>[!NOTE]
> Para evitar o comportamento explicado acima, certifique-se de que transfere a versão mais recente do agente.
> 

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Requisitos de resolução de problemas de rede do agente de monitorização
Para os agentes se ligarem e registarem no Centro de Segurança, precisam de ter acesso aos recursos de rede, incluindo os números de porta e os URLs de domínio.

- Para servidores proxy, tem de assegurar que os recursos do servidor proxy apropriados estão configurados nas definições do agente. Leia este artigo para obter mais informações sobre [como alterar as definições de proxy](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings).
- Para as firewalls que restringem o acesso à Internet, tem de configurar a firewall para permitir o acesso ao OMS. Não é necessária nenhuma ação nas definições do agente.

A tabela seguinte mostra os recursos necessários para a comunicação.

| Recursos do Agente | Portas | Inspeção de HTTPS direto |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Sim |
| *.oms.opinsights.azure.com | 443 | Sim |
| *.blob.core.windows.net | 443 | Sim |
| *.azure-automation.net | 443 | Sim |

Se encontrar problemas de integração com o agente, certifique-se de que lê o artigo [How to troubleshoot Operations Management Suite onboarding issues](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues) (Como resolver problemas de integração do Operations Management Suite).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>A resolução de problemas do endpoint protection não está a funcionar corretamente

O agente convidado é o processo principal de tudo o que a extensão do [Microsoft Antimalware](../security/azure-security-antimalware.md) faz. Quando o processo do agente convidado falha, o Microsoft Antimalware que é executado como um processo subordinado do agente convidado também poderá falhar.  Em cenários como os que são recomendados para verificar as seguintes opções:

- Se a VM de destino for uma imagem personalizada e o criador da VM nunca tiver instalado o agente convidado.
- Se o destino for uma VM do Linux em vez de uma VM do Windows, a instalação da versão do Windows da extensão de antimalware numa VM do Linux irá falhar. O agente convidado do Linux tem requisitos específicos em termos da versão do SO e pacotes necessários, e se esses requisitos não forem cumpridos, o agente da VM também não irá funcionar. 
- Se a VM foi criada com uma versão antiga do agente convidado. Caso tenha sido, deve ter em atenção que alguns agentes antigos não conseguirão atualizar de forma automática para a versão mais recente e isto pode levar a este problema. Utilize sempre a versão mais recente do agente convidado se criar as suas próprias imagens.
- Algum software de administração de terceiros pode desativar o agente convidado ou bloquear o acesso a determinadas localizações de ficheiros. Se tiver instalado software de terceiros na VM, certifique-se de que o agente está na lista de exclusão.
- Determinadas definições da firewall ou do Grupo de Segurança de Rede (NSG) podem bloquear o tráfego de rede para e a partir do agente convidado.
- Determinada Lista de Controlo de Acesso (ACL) poderá impedir o acesso ao disco.
- A falta de espaço em disco pode impedir o agente convidado de funcionar corretamente. 

A Interface de Utilizador de Antimalware da Microsoft está desativada por predefinição, leia [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment (Ativar a Interface de Utilizador de Antimalware da Microsoft nas VMs do Azure Resource Manager Depois da Implementação)](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) para obter mais informações sobre como ativá-lo se for necessário.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Resolução de problemas ao carregar o dashboard

Se ocorrerem problemas ao carregar o dashboard do Centro de Segurança, certifique-se de que o utilizador que regista a subscrição no Centro de Segurança (ou seja, o primeiro utilizador que abrir o Centro de Segurança com a subscrição) e o utilizador que gostaria de ativar a recolha de dados são o *Proprietário* ou o *Contribuinte* na subscrição. A partir desse momento os utilizadores com *Leitor* na subscrição também podem ver o dashboard/alertas/recomendação/política.

## <a name="contacting-microsoft-support"></a>Contactar o Suporte da Microsoft
Alguns problemas podem ser identificados através das diretrizes fornecidas neste artigo, ao passo que outros estão também documentados no [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter) público do Centro de Segurança. Contudo, se tiver de resolver mais problemas, pode abrir um novo pedido de suporte através do **portal do Azure**, conforme mostrado abaixo: 

![Suporte da Microsoft](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)


## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure

