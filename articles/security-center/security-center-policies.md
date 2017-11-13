---
title: "Definir políticas de segurança no Centro de Segurança do Azure | Microsoft Docs"
description: "Este artigo ajuda-o a configurar as políticas de segurança no Centro de Segurança do Azure."
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
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: b4b33143ad6e24fc40acafbe4ad37b7152d2b8d4
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="set-security-policies-in-azure-security-center"></a>Definir políticas de segurança no Centro de Segurança do Azure
Este artigo ajuda-o a configurar as políticas de segurança no Centro de Segurança. 

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. No Centro de Segurança, pode editar as políticas e monitorizar a conformidade das mesmas. 

> [!NOTE]
> Pode agora expandir as políticas do Centro de Segurança com o Azure Policy, que está em pré-visualização limitada. Para aderir à pré-visualização, aceda a [Sign up for Azure Policy](https://aka.ms/getpolicy) (Inscrever-se no Azure Policy). Para obter mais informações, veja [Integrar políticas de segurança do Centro de Segurança no Azure Policy](security-center-azure-policy.md).

Os requisitos de segurança dos recursos que são utilizados para desenvolvimento ou teste podem ser diferentes dos requisitos dos recursos utilizados para aplicações de produção. As aplicações que utilizam dados regulados, como, por exemplo, informações pessoais, podem exigir um nível mais elevado de segurança. As políticas de segurança que são ativadas no Centro de Segurança do Azure aplicam recomendações de segurança e monitorização para o ajudar a identificar potenciais vulnerabilidades e a mitigar ameaças. Para obter mais informações sobre como determinar a opção mais adequada para si, veja [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Editar as políticas de segurança
Pode editar a política de segurança predefinida para cada uma das suas subscrições do Azure no Centro de Segurança. Para modificar uma política de segurança, tem de ser proprietário, contribuidor ou administrador de segurança da subscrição em causa. Para configurar as políticas de segurança no Centro de Segurança, faça o seguinte:

1. Inicie sessão no Portal do Azure.

2. No dashboard **Centro de Segurança**, em **Geral**, selecione **Política de segurança**.

3. Selecione a subscrição para a qual pretende ativar uma política de segurança.

4. Na secção **Componentes da Política**, selecione **Política de segurança**.  
    Esta é a política predefinida que o Centro de Segurança atribui. Pode ativar ou desativar as recomendações de segurança disponíveis.

5. Quando concluir a edição, selecione **Guardar**.

## <a name="available-security-policy-definitions"></a>Definições de política de segurança disponíveis

Para compreender as definições de política que estão disponíveis na política de segurança predefinida, consulte a tabela seguinte:

| Política | O que faz a política |
| --- | --- |
| Atualizações do sistema |Obtém uma lista diária de atualizações críticas e de segurança disponíveis a partir do Windows Update ou o Windows Server Update Services. A lista obtida depende do serviço que está configurado para as suas máquinas virtuais e recomenda a aplicação das atualizações em falta. Para sistemas Linux, a política utiliza o sistema de gestão de pacotes fornecido pelo distro para determinar os pacotes que têm atualizações disponíveis. Também verifica se existem atualizações críticas e de segurança de máquinas virtuais dos [Serviços em Nuvem do Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do SO |Analisa configurações do sistema operativo diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para resolver estas vulnerabilidades. Para obter mais informações sobre as configurações específicas que estão a ser monitorizadas, veja a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Neste momento, o Windows Server 2016 não é totalmente suportado.) |
| Endpoint protection |Recomenda que o Endpoint Protection seja configurado para todas as máquinas virtuais (VMs) Windows para ajudar a identificar e remover vírus, spyware e outro software malicioso. |
| Encriptação de disco |Recomenda a ativação da encriptação de disco em todas as máquinas virtuais para melhorar a proteção de dados inativos. |
| Grupos de segurança de rede |Recomenda que sejam configurados [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) para controlar o tráfego de entrada e de saída para VMs que têm pontos finais públicos. Os grupos de segurança de rede configurados para uma sub-rede são herdados por todas as interfaces de rede de máquina virtual, exceto quando especificado em contrário. Para além de verificar para ver se um grupo de segurança de rede foi configurado, esta política avalia as regras de segurança de entrada para identificar regras que permitam o tráfego de entrada. |
| Firewall de aplicação Web |Recomenda a configuração de uma firewall de aplicações Web em máquinas virtuais quando uma das seguintes opções for verdadeira: <ul><li>Um [IP público de nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) é utilizado e as regras de segurança de entrada para o grupo de segurança de rede associado estão configuradas para permitir o acesso à porta 80/443.</li><li>É utilizado um IP com balanceamento de carga e o balanceamento de carga associado e as regras de tradução de endereços de rede (NAT) de entrada estão configuradas para permitir o acesso à porta 80/443. Para obter mais informações, veja [Suporte do Azure Resource Manager para o Load Balancer](../load-balancer/load-balancer-arm.md).</li> |
| Firewall da próxima geração |Expande as proteções de rede para além dos grupos de segurança de rede, que estão incorporados no Azure. O Centro de Segurança deteta as implementações para as quais é recomendada uma firewall da próxima geração e, em seguida, poderá configurar uma aplicação virtual. |
| Auditoria de SQL e deteção de ameaças |Recomenda que a auditoria do acesso à sua base de dados SQL seja ativada, quer para conformidade, quer para deteção de ameaças avançada, para efeitos de investigação. |
| Encriptação SQL |Recomenda que a encriptação inativa seja ativada na sua base de dados SQL, cópias de segurança associadas e ficheiros de registo de transações. Mesmo se existir uma falha dos seus dados, esta não é legível. |
| Avaliação de vulnerabilidades |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| Encriptação do armazenamento |Atualmente, esta funcionalidade está disponível para blobs e Ficheiros do Azure. Depois de ativar a Encriptação do Serviço de Armazenamento, apenas os novos dados são encriptados e quaisquer ficheiros existentes nesta conta de armazenamento permanecem desencriptados. |
| Acesso à rede JIT |Quando o acesso à rede just-in-time estiver ativado, o Centro de Segurança bloqueia o tráfego de entrada nas suas VMs do Azure através da criação de uma regra de grupo de segurança de rede. Selecione as portas na VM em que o tráfego de entrada deve ser bloqueado. Para obter mais informações, veja [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Gerir o acesso da máquina virtual através do just in time). |


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planear e compreender as considerações de conceção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](security-center-faq.md): obtenha respostas às perguntas mais frequentes sobre como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre publicações no blogue acerca da segurança e conformidade do Azure.
