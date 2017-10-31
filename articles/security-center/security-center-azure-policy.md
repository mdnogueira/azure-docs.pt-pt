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
ms.openlocfilehash: 5e07cd6891a5ab04012f819b5f6b9379312e530d
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center-powered-by-azure-policy"></a>Definir políticas de segurança do Centro de Segurança, com tecnologia do Azure Policy
Este documento ajuda-o a configurar as políticas de segurança do Centro de Segurança, com tecnologia do Azure Policy, ao servir de orientação pelos passos necessários para realizar esta tarefa. 


## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança?
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editar a política no Centro de Segurança ou utilizar o [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) para criar novas definições de políticas, atribuir políticas a vários Grupos de Gestão (que podem representar toda a organização, uma unidade de negócio, etc.) e monitorizar a conformidade das políticas.

> [!NOTE]
> O Azure Policy está em pré-visualização limitada. Clique [aqui](https://aka.ms/getpolicy) para aderir. Para obter mais informações sobre as Políticas do Azure, leia [Criar e gerir políticas para impor a conformidade](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy).

## <a name="edit-security-policies"></a>Editar as políticas de segurança
Pode editar a política de segurança predefinida para cada uma das suas subscrições do Azure no Centro de Segurança. Para modificar uma política de segurança, tem de ser proprietário, contribuidor ou Administrador de Segurança dessa subscrição ou do Grupo de Gestão que a contém. Inicie sessão no portal do Azure e siga os passos subsequentes para ver as suas políticas de segurança no Centro de Segurança:

1. No dashboard **Centro de Segurança**, em **Geral**, clique em **Política de Segurança**.
2. Selecione a subscrição na qual quer ativar a política de segurança.

    ![Gestão de Políticas](./media/security-center-policies/security-center-policies-fig10.png)

3. Na secção **COMPONENTES DA POLÍTICA**, clique em **Política de segurança**.

    ![Componentes da política](./media/security-center-policies/security-center-policies-fig12.png)

4. Esta é a política predefinida atribuída ao Centro de Segurança através do Azure Policy. Pode eliminar os itens que estão em **POLÍTICAS E PARÂMETROS** ou adicionar outras definições de política que estão em **OPÇÕES DISPONÍVEIS**. Para tal, basta clicar no sinal de adição junto ao nome da definição.

    ![Definições de política](./media/security-center-policies/security-center-policies-fig11.png)

5. Se quiser uma explicação mais detalhada sobre a política, clique na mesma e será aberta outra página com os detalhes e o código JSON com a estrutura [definição de política](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy/#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig14.png)

6. Quando concluir a edição, clique em **Guardar**.


## <a name="available-security-policy-definitions"></a>Definições de política de segurança disponíveis

Utilize a tabela seguinte como uma referência para compreender as definições de política disponíveis na política de segurança predefinida: 

| Política | Quando o estado está ativado |
| --- | --- |
| Atualizações do sistema |Obtém uma lista diária de atualizações críticas e de segurança disponíveis a partir do Windows Update ou o Windows Server Update Services. A lista obtida depende do serviço que está configurado para essa máquina virtual e recomenda que as atualizações em falta sejam aplicadas. Para sistemas Linux, a política utiliza o sistema de gestão de pacotes fornecido pelo distro para determinar os pacotes que têm atualizações disponíveis. Também verifica se existem atualizações críticas e de segurança de máquinas virtuais dos [Serviços em Nuvem do Azure](../cloud-services/cloud-services-how-to-configure.md). |
| Vulnerabilidades do SO |Analisa configurações do sistema operativo diariamente para determinar os problemas que podem tornar a máquina virtual vulnerável a ataques. A política também recomenda alterações de configuração para resolver estas vulnerabilidades. Consulte a [lista de linhas de base recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para obter mais informações sobre as configurações específicas a serem monitorizadas. (Neste momento, o Windows Server 2016 não é totalmente suportado.) |
| Endpoint protection |Recomenda que a Endpoint Protection seja aprovisionada para todas as máquinas virtuais do Windows de modo a ajudar a identificar e remover vírus, spyware e outro software malicioso. |
| Encriptação de disco |Recomenda a ativação da encriptação de disco em todas as máquinas virtuais para melhorar a proteção de dados inativos. |
| Grupos de segurança de rede |Recomenda que sejam configurados [grupos de segurança de rede](../virtual-network/virtual-networks-nsg.md) para controlar o tráfego de entrada e de saída para VMs que têm pontos finais públicos. Os grupos de segurança de rede configurados para uma sub-rede são herdados por todas as interfaces de rede de máquina virtual, exceto se for especificado em contrário. Para além de verificar que um grupo de segurança de rede foi configurado, esta política avalia as regras de segurança de entrada para identificar regras que permitam o tráfego de entrada. |
| Firewall de aplicação Web |Recomenda-se que uma firewall de aplicação Web seja aprovisionada em máquinas virtuais quando uma das seguintes opções for verdadeira: </br></br>O [IP público de nível de instância](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) é utilizado e as regras de segurança de entrada para o grupo de segurança de rede associado estão configuradas para permitir o acesso à porta 80/443.</br></br>É utilizado o IP com balanceamento de carga e o balanceamento de carga associado e as regras de tradução (NAT) de endereço de rede de entrada estão configuradas para permitir o acesso à porta 80/443. (Para obter mais informações, veja [Suporte do Azure Resource Manager para o Load Balancer](../load-balancer/load-balancer-arm.md). |
| Firewall da próxima geração |Expande as proteções de rede para além dos grupos de segurança de rede, que estão incorporados no Azure. O Centro de Segurança irá detetar as implementações para as quais se recomenda uma firewall da próxima geração e permite-lhe aprovisionar uma aplicação virtual. |
| Auditoria do SQL e Deteção de Ameaças |Recomenda que a auditoria de acesso à Base de Dados do Azure esteja ativada para fins de conformidade e também para deteção de ameaças avançadas, para propósitos de investigação. |
| Encriptação SQL |Recomenda que a encriptação inativa seja ativada para a suas bases de dados SQL do Azure, cópias de segurança associadas e ficheiros de registo de transação. Mesmo se existir uma falha dos seus dados, esta poderá não ser legível. |
| Avaliação de vulnerabilidades |Recomenda-se de que instala uma solução de avaliação de vulnerabilidades na sua VM. |
| Encriptação de Armazenamento |Atualmente esta funcionalidade está disponível para Blobs do Azure e Ficheiros. Depois de ativar a Encriptação do Serviço de Armazenamento, apenas os novos dados serão encriptados e quaisquer ficheiros existentes nesta conta de armazenamento irão permanecer desencriptados. |
| Acesso de Rede JIT |Quando o just in time estiver ativado, o Centro de Segurança bloqueia o tráfego de entrada para as suas VMs do Azure através da criação de uma regra NSG. Selecione as portas na VM em que o tráfego de entrada deve ser bloqueado. Para obter mais informações, veja [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Gerir o acesso da máquina virtual através do just in time). |


## <a name="next-step"></a>Passo seguinte
Neste documento, aprendeu a configurar as políticas de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Guia de operações e planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md). Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md). Saiba como monitorizar o estado de funcionamento dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md). Saiba como gerir e responder a alertas de segurança.
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md). Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Centro de Segurança do Azure FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md). Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](http://blogs.msdn.com/b/azuresecurity/). Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
