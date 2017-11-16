---
title: "Integração de políticas de segurança do Centro de Segurança do Azure com o Azure Policy | Microsoft Docs"
description: "Este documento ajuda-o a configurar as políticas de segurança do Centro de Segurança do Azure com o Azure Policy."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: f0cb50c5ad991073b83dc5e87fbfb0300f40fc03
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integrar políticas de segurança do Centro de Segurança no Azure Policy
Este artigo ajuda-o a configurar políticas de segurança do Centro de Segurança do Azure, que têm tecnologia Azure Policy. 

## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editar as políticas no Centro de Segurança ou utilizar o [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para fazer o seguinte:
* Criar novas definições de política.
* Atribuir políticas em grupos de gestão, que podem representar uma organização completa ou uma unidade de negócio dentro da organização.
* Monitorizar a conformidade com a política.

> [!NOTE]
> O Azure Policy está em pré-visualização limitada. Para aderir, aceda a [Sign up for Azure Policy (Inscrever-se no Azure Policy)](https://aka.ms/getpolicy). Para obter mais informações sobre o Azure Policy, veja [Criar e gerir políticas para impor a conformidade](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Editar as políticas de segurança
Pode editar a política de segurança predefinida para cada uma das suas subscrições do Azure no Centro de Segurança. Para modificar uma política de segurança, tem de ser proprietário, contribuidor ou administrador de segurança dessa subscrição ou do grupo de gestão que a contém. Para ver as suas políticas de segurança no Centro de Segurança, faça o seguinte:

1. Inicie sessão no Portal do Azure.

2. No dashboard **Centro de Segurança**, em **Geral**, selecione **Política de segurança**.

    ![O painel Gestão de Políticas](./media/security-center-policies/security-center-policies-fig10.png)

3. Selecione a subscrição para a qual pretende ativar uma política de segurança.  

4. Na secção **Componentes da Política**, selecione **Política de segurança**.  
    A janela **Noções Básicas** abre.

    ![Componentes da política](./media/security-center-policies/security-center-policies-fig12.png)

5. Para eliminar uma definição de política, em **Políticas e Parâmetros**, junto à definição que quer eliminar, selecione **Eliminar**.

6. Clique em **Guardar**.  
    A janela **Definições Disponíveis** abre, apresentando a política predefinida atribuída ao Centro de Segurança através do Azure Policy. 

7. (Opcional) Na janela **Definições Disponíveis**, efetue um dos seguintes procedimentos:

    * Para adicionar uma definição de política, selecione o sinal de adição (+) junto à definição.

    ![Definições de política disponíveis](./media/security-center-policies/security-center-policies-fig11.png)

    * Para obter uma explicação detalhada de uma política, selecione-a.  
    Uma janela de **Pré-visualização** de definição abre. Apresenta uma descrição da definição e uma hiperligação para o código JSON que fornece a estrutura da [definição de política](../azure-policy/policy-definition.md).

    ![A janela de Pré-visualização de definição](./media/security-center-policies/security-center-policies-fig14.png)

7. Quando concluir a edição, selecione **Guardar**.

## <a name="available-security-policy-definitions"></a>Definições de política de segurança disponíveis

Para compreender as definições de política que estão disponíveis na política de segurança predefinida, consulte a tabela seguinte: 

| Política | O que a política ativada faz |
| --- | --- |
| Atualizações do sistema |Obtém uma lista diária de atualizações críticas e de segurança disponíveis a partir do Windows Update ou o Windows Server Update Services. A lista obtida depende do serviço que está configurado para as suas máquinas virtuais e recomenda a aplicação das atualizações em falta. Para sistemas Linux, a política utiliza o sistema de gestão de pacotes fornecido pelo distro para determinar os pacotes que têm atualizações disponíveis. Também verifica se existem atualizações críticas e de segurança de máquinas virtuais dos [Serviços em Nuvem do Azure](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Vulnerabilidades do SO |Analisa configurações do sistema operativo diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para resolver estas vulnerabilidades. Para obter mais informações sobre as configurações específicas que estão a ser monitorizadas, veja a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). (Neste momento, o Windows Server 2016 não é totalmente suportado.) |
| Endpoint protection |Recomenda que o Endpoint Protection seja configurado para todas as máquinas virtuais (VMs) Windows para ajudar a identificar e remover vírus, spyware e outro software malicioso. |
| Encriptação de disco |Recomenda a ativação da encriptação de disco em todas as máquinas virtuais para melhorar a proteção de dados inativos. |
| Grupos de segurança de rede |Recomenda que sejam configurados [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) para controlar o tráfego de entrada e de saída para VMs que têm pontos finais públicos. Os grupos de segurança de rede configurados para uma sub-rede são herdados por todas as interfaces de rede de máquina virtual, exceto quando especificado em contrário. Para além de verificar para ver se um grupo de segurança de rede foi configurado, esta política avalia as regras de segurança de entrada para identificar regras que permitam o tráfego de entrada. |
| Firewall de aplicação Web |Recomenda a configuração de uma firewall de aplicações Web em máquinas virtuais quando uma das seguintes opções for verdadeira: <ul><li>Um [IP público de nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) é utilizado e as regras de segurança de entrada para o grupo de segurança de rede associado estão configuradas para permitir o acesso à porta 80/443.</li><li>É utilizado um IP com balanceamento de carga e o balanceamento de carga associado e as regras de tradução de endereços de rede (NAT) de entrada estão configuradas para permitir o acesso à porta 80/443. Para obter mais informações, veja [Suporte do Azure Resource Manager para o Load Balancer](../load-balancer/load-balancer-arm.md).</li> |
| Firewall da próxima geração |Expande as proteções de rede para além dos grupos de segurança de rede, que estão incorporados no Azure. O Centro de Segurança deteta as implementações para as quais é recomendada uma firewall da próxima geração e, em seguida, poderá configurar uma aplicação virtual. |
| Auditoria de SQL e deteção de ameaças |Recomenda que a auditoria do acesso à Base de Dados do Azure esteja ativada para fins de conformidade e para deteção de ameaças avançada, para efeitos de investigação. |
| Encriptação SQL |Recomenda que a encriptação inativa seja ativada para a sua base de dados SQL do Azure, cópias de segurança associadas e ficheiros de registo de transações. Mesmo se existir uma falha dos seus dados, esta não é legível. |
| Avaliação de vulnerabilidades |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| Encriptação do armazenamento |Atualmente, esta funcionalidade está disponível para Armazenamento de Blobs do Azure e Ficheiros do Azure. Depois de ativar a Encriptação do Serviço de Armazenamento, apenas os novos dados são encriptados e quaisquer ficheiros existentes nesta conta de armazenamento permanecem desencriptados. |
| Acesso à rede JIT |Quando o acesso à rede just-in-time estiver ativado, o Centro de Segurança bloqueia o tráfego de entrada nas suas VMs do Azure através da criação de uma regra de grupo de segurança de rede. Selecione as portas na VM em que o tráfego de entrada deve ser bloqueado. Para obter mais informações, veja [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Gerir o acesso da máquina virtual através do just in time). |


## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais sobre o Centro de Segurança, veja os artigos seguintes:

* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md): saiba como planear e compreender as considerações de conceção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md): saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md): saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md): saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Perguntas mais frequentes (FAQ) do Centro de Segurança do Azure](security-center-faq.md): obtenha respostas às perguntas mais frequentes sobre como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/): encontre publicações no blogue acerca da segurança e conformidade do Azure.
