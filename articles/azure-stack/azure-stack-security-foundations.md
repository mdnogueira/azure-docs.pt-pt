---
title: "Compreender os controlos de segurança de pilha do Azure | Microsoft Docs"
description: "Enquanto administrador de serviços Saiba mais sobre os controlos de segurança aplicados a pilha do Azure"
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 106fcf7b0edc095a52e82d58ad48a73084b65d1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-infrastructure-security-posture"></a>Postura de segurança da infraestrutura de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas*

Considerações de segurança e normas de conformidade são entre os controladores de principais para a utilização de nuvens híbrida. Pilha do Azure foi concebida para estes cenários, e é importante compreender os controlos existentes no local quando que adotar a pilha do Azure.

Na pilha do Azure, existem duas camadas de postura de segurança que coexistir. A primeira camada compreende a infraestrutura de pilha do Azure, que passa dos componentes de hardware até até o Gestor de recursos do Azure e inclui o administrador e os portais de inquilino. A segunda camada é composta por cargas de trabalho inquilinos criar, implementar e gerirem e inclui coisas como máquinas virtuais ou serviços de aplicações web sites.  

## <a name="security-approach"></a>Abordagem de segurança
Pilha do Azure foi concebida com uma postura de segurança para se Defender contra ameaças modernas e foi compilada para cumprir os requisitos das normas de conformidade principais. Como resultado, a postura de segurança da infraestrutura de pilha do Azure baseia-se em dois pillars:

 - **Partem do princípio de violação.** A partir do pressuposto de que o sistema já foi infringido, iremos focar-se no *detetar e limitar o impacto de violações* versus apenas tentar impedir ataques. 
 - **Protegido por predefinição.**  Uma vez que a infraestrutura é executado em hardware bem definido e software, iremos *ativar, configurar e validar as funcionalidades de segurança* que são normalmente, deixado aos clientes implementar.

Porque a pilha do Azure é fornecida como um sistema integrado, a postura de segurança da infraestrutura de pilha do Azure é definida pela Microsoft.  Tal como no Azure, os inquilinos são responsáveis por definir a postura de segurança de cargas de trabalho respetivas inquilino. Este documento fornece dados de conhecimento básico sobre a postura de segurança da infraestrutura de pilha do Azure.

## <a name="data-at-rest-encryption"></a>Dados na encriptação de Inativos
Todos os dados de infraestrutura e de inquilino de pilha do Azure são encriptados em descanso ao utilizar o Bitlocker. Esta encriptação protege contra física perda ou roubo dos componentes de armazenamento de pilha do Azure. 

## <a name="data-in-transit-encryption"></a>Dados de encriptação de tráfego
Os componentes de infra-estrutura de pilha do Azure comunicam através dos canais de encriptado com TLS 1.2. Certificados de encriptação automática são geridos pela infraestrutura. 

Todos os pontos finais de infraestrutura externo, como os pontos finais REST ou o portal de pilha do Azure suportam TLS 1.2 para comunicações seguras. Certificados de encriptação, a partir do terceiros ou a sua empresa autoridade de certificação, tem de ser fornecidos para esses pontos finais. 

Enquanto podem ser utilizados certificados autoassinados para estes pontos finais externos, Microsoft aconselha-se vivamente contra a utilizá-los. 

## <a name="secret-management"></a>Gestão secreta
Infraestrutura de pilha do Azure utiliza um sem-número de segredos, como palavras-passe, a funcionar. A maior parte dos mesmos automaticamente rodam com frequência, porque são as contas de serviço geridas de grupo, que rodar a cada 24 horas.

Os segredos restantes que não são contas de serviço geridas de grupo podem ser rotação manualmente com um script no ponto final com privilégios.

## <a name="code-integrity"></a>Integridade do código
Pilha do Azure torna a utilizar o Windows Server 2016 mais recentes funcionalidades de segurança. Uma delas é o Windows Defender Device Guard, que fornece a listas brancas de aplicação e assegura que apenas autorizados execuções de código dentro da infraestrutura de pilha do Azure. 

Código autorizado está assinado pela Microsoft ou o parceiro OEM e está incluído na lista de permitidos software que é especificado numa política definida pela Microsoft. Por outras palavras, pode ser executado apenas o software que tenha sido aprovado para executar na infraestrutura de pilha do Azure. Qualquer tentativa de executar código não autorizado é bloqueado e é gerada uma auditoria.

A política de Device Guard também impede que os agentes de terceiros ou software em execução na infraestrutura de pilha do Azure.

## <a name="credential-guard"></a>Proteção de credenciais
Outra funcionalidade de segurança do Windows Server 2016 na pilha do Azure é a proteção de credenciais do Windows Defender, que é utilizado para proteger as credenciais de infraestrutura do Azure pilha de passagem do Hash e ataques de passagem da permissão.

## <a name="antimalware"></a>Antimalware
Todos os componentes na pilha do Azure (anfitriões Hyper-V e máquinas virtuais) está protegido com o Windows Defender antivírus.

## <a name="constrained-administration-model"></a>Modelo de administração restrita
Administração na pilha do Azure é controlada através da utilização de três pontos de entrada, cada um com um objetivo específico: 
1. O [Portal de administrador](azure-stack-manage-portals.md) fornece uma experiência de ponto e clique para operações de gestão diárias.
2. O Azure Resource Manager expõe todas as operações de gestão do Portal de administrador através de uma API REST, utilizado pelo PowerShell e a CLI do Azure. 
3. Para operações de baixo nível específicas, por exemplo, dados do Centro de integração ou suportam cenários, pilha do Azure expõe um ponto final de PowerShell chamado [ponto final com privilégios](azure-stack-privileged-endpoint.md). Este ponto final expõe um conjunto de na lista de permissões de cmdlets e descontos elevados está a ser auditada.

## <a name="network-controls"></a>Controlos de rede
Infraestrutura de pilha do Azure vem com várias camadas de List(ACL) de controlo de acesso de rede.  As ACLs impedem o acesso não autorizado para os componentes de infraestrutura e limitam as comunicações de infraestrutura para apenas os caminhos que são necessárias para a funcionar. 

ACLs de rede são impostas em três camadas:
1.  Parte superior do bastidor muda
2.  Rede definidas por software
3.  Firewalls de sistema operativo anfitrião e VM 


