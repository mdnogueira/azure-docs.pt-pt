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
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 1cebb6edecd13c6ab32c6854bfd6fe908c1f71f4
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center"></a>Definir políticas de segurança no Centro de Segurança
Este documento ajuda-o a configurar as políticas de segurança no Centro de Segurança, ao servir de orientação pelos passos necessários para realizar esta tarefa. 


## <a name="how-security-policies-work"></a>Como funcionam as políticas de segurança
O Centro de Segurança cria automaticamente uma política de segurança predefinida para cada uma das suas subscrições do Azure. Pode editar a política no Centro de Segurança e monitorizar a conformidade da mesma. 

> [!NOTE]
> Pode agora expandir as políticas do Centro de Segurança com o Azure Policy, que está em pré-visualização limitada. Clique [aqui](http://aka.ms/getpolicy) para participar na pré-visualização ou veja a documentação [aqui](security-center-azure-policy.md).

Por exemplo, os recursos que são utilizados para programação ou testes podem ter requisitos de segurança diferentes dos que são utilizados para aplicações de produção. Da mesma forma, as aplicações que utilizam dados regulados como informação identificativa podem exigir um nível mais elevado de segurança. As políticas de segurança que são ativadas no Centro de Segurança do Azure aplicam recomendações de segurança e monitorização para o ajudar a identificar potenciais vulnerabilidades e a mitigar ameaças. Leia o [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) para obter mais informações sobre como determinar a opção mais adequada para si.

## <a name="edit-security-policies"></a>Editar as políticas de segurança
Pode editar a política de segurança predefinida para cada uma das suas subscrições do Azure no Centro de Segurança. Para modificar uma política de segurança, tem de ser um proprietário, contribuinte ou Administrador de Segurança dessa subscrição. Inicie sessão no portal do Azure e siga os passos subsequentes para configurar as políticas de segurança no Centro de Segurança: 

1.  No dashboard **Centro de Segurança**, em **Geral**, clique em **Política de Segurança**.
2.  Selecione a subscrição na qual quer ativar a política de segurança.
3.  Na secção **COMPONENTES DA POLÍTICA**, clique em **Política de segurança**.
4.  Esta é a política predefinida atribuída pelo Centro de Segurança. Pode ativar/desativar as recomendações de segurança disponíveis.
5.  Quando concluir a edição, clique em **Guardar**.

## <a name="available-security-policy-options"></a>Opções de política de segurança disponíveis

Utilize a tabela seguinte como uma referência para compreender cada opção:

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
