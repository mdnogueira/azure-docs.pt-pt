---
title: "Práticas recomendadas de segurança de máquina virtual do Azure | Microsoft Docs"
description: "Este artigo fornece uma variedade de melhores práticas de segurança a ser utilizada nas máquinas virtuais localizadas no Azure."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/19/2017
ms.author: yurid
ms.openlocfilehash: 7122e2f5f7ebc8ef0c6b2083257f26d8d2e5e345
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="best-practices-for-azure-vm-security"></a>Melhores práticas de segurança de VM do Azure

Na maioria dos infraestrutura como cenários de serviço (IaaS), [máquinas de virtuais (VMs) do Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/) é informática da carga de trabalho principal para as organizações que utilizam a nuvem. Este facto é especialmente evidente em [cenários híbridos](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) em organizações pretendem lentamente migrar cargas de trabalho para a nuvem. Tais cenários, siga o [considerações de segurança geral para o IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx)e aplicar procedimentos recomendados de segurança para todas as suas VMs.

Este artigo explica vários VM melhores práticas de segurança, cada derivado os nossos clientes e o nossas própria experiências diretas com as VMs.

As melhores práticas baseiam-se num consenso da opinião e funcionam com capacidades de plataforma do Azure atual e conjuntos de funcionalidade. Porque opinions e tecnologias podem alterar ao longo do tempo, planeamos atualizar este artigo regularmente para refletir essas alterações.

Para cada melhor prática, explica o artigo:

* O que é a melhor prática.
* Por que motivo é aconselhável ativá-la.
* Como pode saber ativá-la.
* O que pode acontecer se falharem ativá-la.
* Possíveis alternativas à melhor prática.

O artigo examina as seguintes VM melhores práticas de segurança:

* VM autenticação e controlo de acesso
* Acesso de disponibilidade e rede VM
* Proteger os dados Inativos no VMs através da imposição de encriptação
* Gerir as atualizações VM
* Gerir a sua postura de segurança VM
* Monitorizar o desempenho de VM

## <a name="vm-authentication-and-access-control"></a>VM autenticação e controlo de acesso

O primeiro passo para proteger a VM é garantir que apenas utilizadores autorizados são capazes de configurar novas VMs. Pode utilizar [políticas do Azure](../azure-policy/azure-policy-introduction.md) para estabelecer as convenções de recursos na sua organização, crie políticas personalizadas e aplicar estas políticas a recursos, tais como [grupos de recursos](../azure-resource-manager/resource-group-overview.md).

As VMs que pertencem a um grupo de recursos naturalmente herdam as respetivas políticas. Embora seja recomendável esta abordagem para gerir VMs, também pode controlar o acesso às políticas de VM individuais utilizando [controlo de acesso baseado em funções (RBAC)](../active-directory/role-based-access-control-configure.md).

Quando ativar políticas de Gestor de recursos e o RBAC para controlar o acesso VM, ajudar a melhorar a segurança VM geral. Recomendamos que consolidar as VMs com o mesmo ciclo de vida no mesmo grupo de recursos. Ao utilizar grupos de recursos, pode implementar, monitorizar e os custos para os seus recursos de faturação de agregação. Para permitir aos utilizadores aceder e definir a segurança das VMs, utilize um [abordagem do menor privilégio](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models). E, quando atribui privilégios aos utilizadores, planeie utilizar as seguintes funções incorporadas do Azure:

- [Contribuinte de máquina virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor): pode gerir VMs, mas não o armazenamento de rede ou conta virtual ao qual estão ligados.
- [Clássico contribuinte de Máquina Virtual](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor): pode gerir VMs criadas utilizando o modelo de implementação clássica, mas não o armazenamento de rede ou conta virtual ao qual as VMs estão ligadas.
- [Gestor de segurança](../active-directory/role-based-access-built-in-roles.md#security-manager): pode gerir os componentes de segurança, as políticas de segurança e as VMs.
- [DevTest Labs utilizador](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user): pode ver tudo ligar, iniciar, reiniciar e encerrar VMs.

Não partilhe as contas e palavras-passe entre administradores e não reutilize palavras-passe em várias contas de utilizador ou serviços, especialmente palavras-passe de redes sociais ou outras atividades não administrativas. Idealmente, deve utilizar [do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) modelos para configurar as suas VMs de forma segura. Ao utilizar esta abordagem, pode reforçar as opções de implementação e impor as definições de segurança em toda a implementação.

As organizações que não a impor controlo de acesso a dados, tirando partido de capacidades, tal como o RBAC podem ser conceder os seus utilizadores mais privilégios que necessário. Acesso de utilizador inadequada para certos dados diretamente pode comprometer a esses dados.

## <a name="vm-availability-and-network-access"></a>Acesso de disponibilidade e rede VM

Se a VM executa aplicações críticas que tem de ter elevada disponibilidade, recomendamos vivamente a utilização de várias VMs. Para uma melhor disponibilidade, criar, pelo menos, duas VMs no [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md).

[Azure Load Balancer](../load-balancer/load-balancer-overview.md) também requer que as VMs com balanceamento de carga pertencem ao mesmo conjunto de disponibilidade. Se estas VMs tem de ser acedidos a partir da Internet, tem de configurar um [Balanceador de carga para a Internet](../load-balancer/load-balancer-internet-overview.md).

Quando as VMs estão expostas à Internet, é importante que [controlar o fluxo de tráfego de rede com grupos de segurança de rede (NSGs)](../virtual-network/virtual-networks-nsg.md). Porque os NSGs podem ser aplicados a sub-redes, pode minimizar o número de NSGs agrupando os recursos por sub-rede e, em seguida, aplicando os NSGs a sub-redes. O objetivo consiste em criar uma camada de isolamento de rede, pode fazê-lo ao configurar corretamente o [segurança de rede](../best-practices-network-security.md) capacidades no Azure.

Também pode utilizar a funcionalidade do just-in-time (JIT) VM acesso de leitura a partir do Centro de segurança do Azure para controlar quem tem acesso remoto para uma VM específica e como período de tempo.

As organizações que não a impor restrições de acesso de rede para acesso à Internet VMs estão expostas a riscos de segurança, tais como um ataque de força bruta de protocolo RDP (Remote Desktop Protocol).

## <a name="protect-data-at-rest-in-your-vms-by-enforcing-encryption"></a>Proteger dados Inativos nas suas VMs através da imposição de encriptação

[Encriptação de dados Inativos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório na direção soberania dos dados, privacidade e conformidade de dados. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permite aos administradores de TI encriptar discos do Windows e a VM do IaaS Linux. Encriptação de disco combina a funcionalidade de BitLocker do Windows de norma da indústria e a funcionalidade de dm-crypt Linux para fornecer a encriptação de volume para o SO e os discos de dados.

Pode aplicar encriptação de disco para o ajudar a salvaguardar os dados para satisfazer os seus requisitos de conformidade e segurança organizacional. Organização deve considerar a utilização de encriptação para ajudar a mitigar os riscos acesso a dados relacionados para não autorizado. Recomendamos também que encriptar as suas unidades antes de escrever os dados confidenciais aos mesmos.

Lembre-se de que encriptar os volumes de dados VM para os proteger, Inativos na sua conta do storage do Azure. Salvaguardar as chaves de encriptação e o segredo utilizando [Cofre de chaves do Azure](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/).

As organizações que não impor a encriptação de dados mais estão expostas a problemas de integridade de dados. Por exemplo, os utilizadores não autorizados ou não autorizados poderão roubo de dados em contas comprometidas ou obter acesso não autorizado aos dados codificados no ClearFormat. Para além de colocar nessas riscos, para estar em conformidade com regulamentos da indústria, as empresas tem de provar que estão exercising diligence e controlos de segurança correta a utilizar para melhorar a segurança de dados.

Para saber mais sobre a encriptação de disco, consulte [encriptação de disco do Azure para Windows e as VMs de IaaS Linux](azure-security-disk-encryption.md).


## <a name="manage-your-vm-updates"></a>Gerir as atualizações VM

Porque as VMs do Azure, como todas as VMs no local, são se destina a ser gerida pelo utilizador, o Azure não push atualizações do Windows aos mesmos. É, no entanto, encouraged para deixar a definição do Windows Update automática ativada. Outra opção consiste em implementar [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) ou outro produto de gestão de atualizações adequado no outro VM ou no local. O WSUS e o Windows Update manter VMs atual. Também recomendamos que utilize um produto de análise para verificar se todas as suas VMs de IaaS são atualizados.

As cotações imagens fornecidas pelo Azure regularmente são atualizadas para incluir o round mais recente de atualizações do Windows. No entanto, não há nenhuma garantia que as imagens serão atuais no momento da implementação. Um atraso ligeiras (de mais do que algumas semanas) seguintes versões públicas pode ser possível. A verificar e instalar todas as atualizações do Windows devem ser o primeiro passo de cada implementação. Esta medida é particularmente importante para aplicar ao implementar imagens do ou as suas próprias bibliotecas. As imagens que são fornecidas como parte do Azure Marketplace são atualizadas automaticamente por predefinição.

As organizações que não impõem políticas de atualização de software mais estão expostas a ameaças que exploram vulnerabilidades conhecidas, anteriormente fixas. Para além de risking estas ameaças, para estar em conformidade com regulamentos da indústria, as empresas tem de provar que estão exercising diligence e controlos de segurança correta a utilizar para ajudar a garantir a segurança da sua carga de trabalho localizada na nuvem.

É importante realçar que as melhores práticas de atualização de software para os centros de dados tradicionais e IaaS do Azure têm diversas semelhanças. Por conseguinte, recomendamos que avaliar as políticas de atualização de software atual para incluir as VMs.

## <a name="manage-your-vm-security-posture"></a>Gerir a sua postura de segurança VM

Estão a evolução das ameaças informático e proteger as suas VMs requer uma capacidade de monitorização de avançada que pode rapidamente detetar ameaças, impedir o acesso não autorizado aos seus recursos, acionar alertas e reduzir os falsos positivos. A postura de segurança para uma carga de trabalho é composto por todos os aspetos de segurança da VM, da gestão de atualização para proteger o acesso de rede.

Para monitorizar a postura de segurança da sua [Windows](../security-center/security-center-virtual-machine.md) e [VMs com Linux](../security-center/security-center-linux-virtual-machine.md), utilize [Centro de segurança do Azure](../security-center/security-center-intro.md). No Centro de segurança do Azure, Salvaguarde as suas VMs, tirando partido das seguintes funcionalidades:

* Aplicar definições de segurança de SO com regras de configuração recomendada
* Identificar e transferir atualizações críticas que podem estar em falta e de segurança do sistema
* Implementar recomendações de proteção antimalware de ponto final
* Validar a encriptação de disco
* Avaliar e remediar as vulnerabilidades
* Detetar ameaças

Centro de segurança pode monitorizar ativamente para as ameaças e potenciais ameaças são expostas em **alertas de segurança**. Ameaças correlacionadas são agregadas de uma única vista chamada **incidente de segurança**.

Para compreender como o Centro de segurança pode ajudar a identificar potenciais ameaças nas suas VMs localizados no Azure, veja o vídeo seguinte:

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

As organizações que não impõem uma postura de segurança forte para as respetivas VMs permanecem não tem conhecimento de tentativas de potenciais por utilizadores não autorizados de contornar controlos de segurança estabelecida.

## <a name="monitor-vm-performance"></a>Monitorizar o desempenho de VM

Abuso de recurso pode ser um problema ao VM processos consumam mais recursos que deveriam. Problemas de desempenho com uma VM podem levar a interrupção do serviço, que viola o princípio de segurança de disponibilidade. Por este motivo, é imperativo para monitorizar o acesso VM não só reativamente, enquanto está a ocorrer um problema, mas também proativamente, contra o desempenho da linha de base medida durante o funcionamento normal.

Analisando [ficheiros de registo de diagnóstico do Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/), pode monitorizar os recursos da VM e identificar potenciais problemas que possam comprometer o desempenho e disponibilidade. A extensão de diagnóstico do Azure fornece capacidades de monitorização e diagnóstico em VMs baseadas no Windows. Pode ativar estas funcionalidades, incluindo a extensão como parte do [modelo Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md).

Também pode utilizar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) ganhar visibilidade em estado de funcionamento do recurso.

As organizações que não a monitorizar o desempenho da VM não são possível determinar se são determinadas alterações nos padrões de desempenho normal ou anormal. Se a VM está a consumir mais recursos que o habitual, essas uma anomalias pode indicar um ataque potencial de um recurso externo ou um processo de comprometido em execução na VM.
