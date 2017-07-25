---
title: "Definir políticas de segurança no Centro de Segurança do Azure | Microsoft Docs"
description: "Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança do Azure."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 26c07d30f9166e0e52cb396cdd0576530939e442
ms.openlocfilehash: aefec15c72c6cf8389a29b03be70abb4c7f020b9
ms.contentlocale: pt-pt
ms.lasthandoff: 07/19/2017

---
# <a name="set-security-policies-in-azure-security-center"></a>Definir políticas de segurança no Centro de Segurança do Azure
Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança, ao servir de orientação pelos passos necessários para realizar esta tarefa.

>[!NOTE] 
>A partir do início de junho de 2017, o Centro de Segurança irá utilizar o Microsoft Monitoring Agent para recolher e armazenar dados. Veja [Azure Security Center Platform Migration](security-center-platform-migration.md) (Migração da Plataforma do Centro de Segurança do Azure) para saber mais. As informações neste artigo representam a funcionalidade do Centro de Segurança após a transição para o Agente de Monitorização da Microsoft.
>

## <a name="what-are-security-policies"></a>O que são políticas de segurança?
Uma política de segurança define o conjunto de controlos que são recomendados para recursos dentro da subscrição especificada. No Centro de Segurança, pode definir políticas para as suas subscrições do Azure, de acordo com as necessidades de segurança da empresa e do tipo de aplicações ou sensibilidade dos dados em cada subscrição.

Por exemplo, os recursos que são utilizados para programação ou testes podem ter requisitos de segurança diferentes dos que são utilizados para aplicações de produção. Da mesma forma, as aplicações que utilizam dados regulados como informação identificativa podem exigir um nível mais elevado de segurança. As políticas de segurança que são ativadas no Centro de Segurança do Azure aplicam recomendações de segurança e monitorização para o ajudar a identificar potenciais vulnerabilidades e a mitigar ameaças. Leia o [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) para obter mais informações sobre como determinar a opção mais adequada para si.

## <a name="set-security-policies"></a>Definir políticas de segurança
Pode configurar políticas de segurança para cada subscrição. Para modificar uma política de segurança, tem de ser um proprietário ou contribuinte dessa subscrição. Inicie sessão no portal do Azure e siga os passos subsequentes para configurar as políticas de segurança no Centro de Segurança:

1. Clique no mosaico **Política** no dashboard do Centro de Segurança.
2. No painel Política de Segurança que se abre, selecione a subscrição na qual pretende ativar a política de segurança.

    ![Definir política](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. O painel **Política de segurança** da subscrição selecionada abre-se com um conjunto de opções. As opções disponíveis neste painel são:

   * **Política de prevenção**: utilize esta opção para configurar políticas por subscrição.  
   * **Notificação por e-mail**: utilize esta opção para configurar uma notificação por e-mail que é enviada na primeira ocorrência diária de um alerta e relativamente a alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Leia [Provide security contact details in Azure Security Center (Fornecer detalhes de contacto de segurança no Centro de Segurança do Azure)](security-center-provide-security-contact-details.md) para obter mais informações sobre como configurar uma notificação por e-mail.
   * **Escalão de preço**: utilize esta opção para atualizar a seleção do escalão de preço. Consulte os [Preços do Centro de Segurança](security-center-pricing.md) para saber mais sobre as opções de preços.
4. Certifique-se de que a opção **Recolher dados de máquinas virtuais** está **Ativada**. Essa opção permite a coleção de registos automáticos para os recursos novos e existentes com o Microsoft Monitoring Agent – este é o mesmo agente utilizado pelo serviço do Operations Management Suite e do Log Analytics. Os dados recolhidos por este agente serão armazenados na(s) área(s) de trabalho do Log Analytics existente, associada(s) à sua subscrição do Azure ou na(s) nova(s) área(s) de trabalho, tendo em conta a geografia da VM.

5. No painel **Política de Segurança**, clique em **Política de prevenção**, para ver as opções disponíveis. Clique em **Ativar** para ativar as recomendações de segurança que são relevantes para esta subscrição.

    ![Selecionar as políticas de segurança](./media/security-center-policies/security-center-policies-fig4-newUI.png)

Utilize a tabela seguinte como uma referência para compreender cada opção:

| Política | Quando o estado está ativado |
| --- | --- |
| Atualizações do sistema |Obtém uma lista diária de atualizações críticas e de segurança disponíveis a partir do Windows Update ou o Windows Server Update Services. A lista obtida depende do serviço que está configurado para essa máquina virtual e recomenda que as atualizações em falta sejam aplicadas. Para sistemas Linux, a política utiliza o sistema de gestão de pacotes fornecido pelo distro para determinar os pacotes que têm atualizações disponíveis. Também verifica se existem atualizações críticas e de segurança de máquinas virtuais dos [Serviços em Nuvem do Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do SO |Analisa configurações do sistema operativo diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para resolver estas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas a serem monitorizadas. (Neste momento, o Windows Server 2016 não é totalmente suportado.) |
| Endpoint protection |Recomenda que a Endpoint Protection seja aprovisionada para todas as máquinas virtuais do Windows de modo a ajudar a identificar e remover vírus, spyware e outro software malicioso. |
| Encriptação de disco |Recomenda a ativação da encriptação de disco em todas as máquinas virtuais para melhorar a proteção de dados inativos. |
| Grupos de segurança de rede |Recomenda que sejam configurados [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) para controlar o tráfego de entrada e de saída para VMs que têm pontos finais públicos. Os grupos de segurança de rede configurados para uma sub-rede serão herdados por todas as interfaces de rede de máquina virtual, exceto se for especificado em contrário. Para além de verificar que um grupo de segurança de rede foi configurado, esta política avalia as regras de segurança de entrada para identificar regras que permitam o tráfego de entrada. |
| Firewall de aplicação Web |Recomenda-se que uma firewall de aplicação Web seja aprovisionada em máquinas virtuais quando uma das seguintes opções for verdadeira: </br></br>O [IP público de nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) é utilizado e as regras de segurança de entrada para o grupo de segurança de rede associado estão configuradas para permitir o acesso à porta 80/443.</br></br>É utilizado o IP com balanceamento de carga e o balanceamento de carga associado e as regras de tradução (NAT) de endereço de rede de entrada estão configuradas para permitir o acesso à porta 80/443. (Para obter mais informações, veja [Suporte do Azure Resource Manager para o Load Balancer](../load-balancer/load-balancer-arm.md). |
| Firewall da próxima geração |Expande as proteções de rede para além dos grupos de segurança de rede, que estão incorporados no Azure. O Centro de Segurança irá detetar as implementações para as quais se recomenda uma firewall da próxima geração e permite-lhe aprovisionar uma aplicação virtual. |
| Auditoria do SQL e Deteção de Ameaças |Recomenda que a auditoria de acesso à Base de Dados do Azure esteja ativada para fins de conformidade e também para deteção de ameaças avançadas, para propósitos de investigação. |
| Encriptação SQL |Recomenda que a encriptação inativa seja ativada para a suas bases de dados SQL do Azure, cópias de segurança associadas e ficheiros de registo de transação. Mesmo se existir uma falha dos seus dados, esta poderá não ser legível. |
| Avaliação de vulnerabilidades |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| Encriptação de Armazenamento |Atualmente esta funcionalidade está disponível para Blobs do Azure e Ficheiros. Tenha em atenção que depois de ativar a Encriptação do Serviço de Armazenamento, apenas os novos dados serão encriptados e quaisquer ficheiros existentes nesta conta de armazenamento irão permanecer desencriptados. |

Assim que configurar todas as opções, clique em **OK** no painel **Política de Segurança** que tem as recomendações e clique em **Guardar** no painel **Política de Segurança** que tem as definições iniciais.

> [!NOTE]
> O escalão de preço ainda é aplicável ao nível do grupo de recursos. Para obter mais informações, visite a página [Pricing](https://azure.microsoft.com/pricing/details/security-center/) (Preços).
>
>

## <a name="see-also"></a>Consultar também
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.

