---
title: "Funcionalidades de segurança do Azure utilizadas com máquinas virtuais do Azure | Microsoft Docs"
description: " Uma descrição geral das funcionalidades de segurança do Azure de núcleos pode ser utilizado com máquinas virtuais do Azure. As VMs do Azure dão-lhe a flexibilidade de Virtualização sem ter de comprar e manter o hardware físico que executa a VM. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 467b2c83-0352-4e9d-9788-c77fb400fe54
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9d10b9fde99867a218c4368f0be6bee00bc44dc1
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-virtual-machines-security-overview"></a>Descrição geral de segurança de máquinas virtuais do Azure
As Máquinas Virtuais do Azure permitem implementar uma vasta gama de soluções de computação de forma ágil. Com suporte do Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP e Serviços BizTalk do Azure, pode implementar qualquer carga de trabalho, qualquer linguagem, em praticamente qualquer sistema operativo.

Uma máquina virtual do Azure fornece-lhe a flexibilidade de virtualização sem ter de comprar e manter o hardware físico que executa a máquina virtual.  Pode criar e implementar as suas aplicações com a garantia que os seus dados estão protegidos e seguro nos nossos centros de dados altamente seguros.

Com o Azure, pode criar soluções de segurança avançada, compatíveis que:

* Proteja as suas máquinas virtuais contra vírus e malware
* Encripte os seus dados confidenciais
* Proteja o tráfego de rede
* Identifique e detete ameaças
* Cumpra os requisitos de conformidade

O objetivo deste artigo é fornecer uma descrição geral das principais funcionalidades de segurança do Azure que podem ser utilizadas com máquinas virtuais. Podemos também fornecem ligações para artigos que fornecer detalhes de cada funcionalidade, pelo que pode saber mais.  

As capacidades centrais de Máquina Virtual do Azure segurança para ser abordadas neste artigo:

* Antimalware
* Módulo de hardware de segurança
* Encriptação de disco de máquina virtual
* Cópia de segurança da máquina virtual
* Azure Site Recovery
* Redes virtuais
* Gestão de políticas de segurança e de relatórios
* Conformidade

## <a name="antimalware"></a>Antimalware
Com o Azure, pode utilizar o software antimalware dos fornecedores de segurança, tais como a Microsoft, da Symantec, Micro de tendência e Kaspersky para proteger as máquinas virtuais de ficheiros maliciosos, adware e outras ameaças. Consulte a secção Saiba mais abaixo para localizar soluções de artigos no parceiro.

Antimalware da Microsoft para máquinas virtuais e serviços Cloud do Azure é uma funcionalidade de proteção em tempo real que ajuda a identificar e remover vírus, spyware e outro software malicioso.  Antimalware da Microsoft fornece configuráveis alertas ao conhecido software malicioso ou indesejável, tentar instalar-se ou executar nos seus sistemas do Azure.

Antimalware da Microsoft é uma solução de agente único para aplicações e ambientes de inquilino, concebidos para ser executado em segundo plano, sem qualquer intervenção humana. Pode implementar a proteção com base nas necessidades das cargas de trabalho aplicações, com o básico secure-por-predefinição ou avançadas de configuração personalizada, incluindo a monitorização de antimalware.

Quando implementar e ativar o Antimalware da Microsoft, as seguintes funcionalidades de núcleos estão disponíveis:

* Proteção em tempo real – atividade de monitores nos serviços em nuvem e em máquinas virtuais para detetar e bloquear a execução de software maligno.
* A análise agendada - periodicamente efetua a análise de destino para detetar software maligno, incluindo ativamente em execução programas.
* Remediação de software maligno - executa automaticamente ação no software maligno detetado, tais como eliminar ou quarentena ficheiros maliciosos e limpeza de entradas do registo malicioso.
* Atualizações de assinatura - automaticamente instala as assinaturas de proteção mais recentes (definições de vírus) para garantir a proteção é atualizado numa frequência previamente determinada.
* Motor antimalware atualiza – automaticamente atualiza o motor Antimalware da Microsoft.
* Plataforma de antimalware atualiza – automaticamente as atualizações a plataforma de Antimalware da Microsoft.
* A proteção ativa - relatórios aos metadados de telemetria do Azure sobre ameaças detetadas e os recursos suspeitos para se certificar de resposta rápida e permite a entrega de assinatura síncrono em tempo real através do Microsoft Active Protection System (MAPS).
* Amostras do Reporting Services - fornece e relatórios de exemplos para o serviço de Antimalware da Microsoft para ajudar a otimizar o serviço e ative a resolução de problemas.
* Exclusões – permite que a aplicação e administradores de serviço configurar a determinados ficheiros, processos e as unidades para os excluir da proteção e ao analisar o desempenho e a outros motivos.
* Recolha de eventos de antimalware - regista o estado de funcionamento do serviço antimalware, atividades suspeitas e as ações de remediação executadas no registo de eventos de sistema operativo e recolhe-los para a conta de armazenamento do Azure do cliente.

Saiba mais: para saber mais sobre o software antimalware para proteger as máquinas virtuais, consulte:

* [Antimalware da Microsoft para máquinas virtuais e serviços em nuvem do Azure](azure-security-antimalware.md)
* [Implementar Soluções Antimalware em Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
* [Como instalar e configurar a segurança avançada do tendência Micro como um serviço numa VM do Windows](../virtual-machines/windows/classic/install-trend.md)
* [Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows](../virtual-machines/windows/classic/install-symantec.md)
* [Soluções de segurança no Azure Marketplace](https://azure.microsoft.com/marketplace/?term=security)

## <a name="hardware-security-module"></a>Segurança de hardware módulo
As proteções de encriptação e autenticação podem ser melhoradas pela melhorar a segurança de chave. Pode simplificar a gestão e segurança dos seus segredos críticos e chaves armazenando-os no Cofre de chaves do Azure. O Key Vault oferece a opção de armazenar as suas chaves em módulos de hardware de segurança (HSMs) com certificação pela norma FIPS 140-2 de Nível 2. Chaves de encriptação do SQL Server para cópia de segurança ou [encriptação transparente de dados](https://msdn.microsoft.com/library/bb934049.aspx) podem todos ser armazenados no Cofre de chaves com quaisquer chaves ou segredos das suas aplicações. As permissões e acesso a estes itens protegidos são geridos através de [do Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

Saiba mais:

* [O que é o Cofre de chaves do Azure?](../key-vault/key-vault-whatis.md)
* [Introdução ao Cofre de chaves do Azure](../key-vault/key-vault-get-started.md)
* [Blogue do Cofre de chaves do Azure](https://blogs.technet.microsoft.com/kv/)

## <a name="virtual-machine-disk-encryption"></a>Encriptação de disco de máquina virtual
Encriptação de disco do Azure é uma nova capacidade que permite-lhe encriptar os discos do Windows e a Máquina Virtual do Linux do Azure. Encriptação de disco do Azure utiliza o padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funcionalidade do Windows e o [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer a encriptação de volume para o SO e os discos de dados.

A solução é integrada com o Cofre de chaves do Azure para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, garantindo que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure.

Saiba mais:

* [Encriptação de disco do Azure para o Windows e as VMs de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)
* [Encriptação de disco do Azure para Linux e máquinas virtuais do Windows](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/azure-disk-encryption-for-linux-and-windows-virtual-machines-public-preview-now-available/)
* [Encriptar uma máquina virtual](../security-center/security-center-disk-encryption.md)

## <a name="virtual-machine-backup"></a>Cópia de segurança da máquina virtual
O Azure Backup é uma solução dimensionável que protege os dados da sua aplicação com nenhum investimento capital e com custos de funcionamento mínimos. Os erros das aplicações podem danificar os dados e os erros humanos podem introduzir erros nas suas aplicações. Com a cópia de segurança do Azure, as máquinas virtuais que executam o Windows e Linux estão protegidas.

Saiba mais:

* [O que é o Azure Backup?](../backup/backup-introduction-to-azure-backup.md)
* [Percurso de aprendizagem de cópia de segurança do Azure](https://azure.microsoft.com/documentation/learning-paths/backup/)
* [Serviço de cópia de segurança do Azure - FAQ](../backup/backup-azure-backup-faq.md)

## <a name="azure-site-recovery"></a>Azure Site Recovery
Uma parte importante da estratégia da BCDR da sua organização é perceber como manter as aplicações e cargas de trabalho empresariais prontas para funcionar quando ocorrerem falhas planeadas ou imprevistas. O Azure Site Recovery ajuda orquestrar a replicação, ativação pós-falha e recuperação de cargas de trabalho e aplicações para que estão disponíveis a partir de uma localização secundária se a sua localização principal ficar inativo.

Recuperação de sites:

* **Simplifica a sua estratégia BCDR** — recuperação de sites facilita a replicação, ativação pós-falha e recuperação de várias cargas de trabalho de empresas e aplicações a partir de uma única localização. A Recuperação de Sites controla a replicação e ativação pós-falha, mas não interceta os dados da aplicação nem obtém informação sobre a mesma.
* **Proporciona replicação flexível** — com a recuperação de sites pode replicar as cargas de trabalho em execução em máquinas virtuais Hyper-V, máquinas virtuais VMware e servidores físicos Windows/Linux.
* **Suporta a ativação pós-falha e recuperação** — recuperação de sites fornece ativações pós-falha de teste manobras recuperação após desastre sem afetar os ambientes de produção. Também pode executar as ativações pós-falha planeadas sem nenhuma perda de dados para as falhas esperadas ou as ativações pós-falha não planeadas com perda mínima de dados (dependendo da frequência de replicação) perante desastres inesperados. Após a ativação pós-falha pode fazer a reativação nos sites primários. A Recuperação de Sites fornece planos de recuperação que podem incluir scripts e livros de automatização do Azure, para que possa personalizar a ativação pós-falha e a recuperação de aplicações de várias camadas.
* **Elimina o datacenter secundário** — pode replicar para um site secundário no local ou para o Azure. Utilizar o Azure como um destino de recuperação após desastre, elimina o custo e a complexidade de manter um site secundário. Dados replicados são guardados no armazenamento do Azure.
* **Integra-se com tecnologias BCDR existentes** – parceiros da recuperação de sites com outras funcionalidades de aplicação de BCDR. Por exemplo, pode utilizar a recuperação de sites para proteger o SQL Server de back-end de cargas de trabalho empresariais. Isto inclui suporte nativo para o SQL Server AlwaysOn gerir a ativação pós-falha de grupos de disponibilidade.

Saiba mais:

* [O que é o Azure Site Recovery?](../site-recovery/site-recovery-overview.md)
* [Como funciona o Azure Site Recovery?](../site-recovery/site-recovery-components.md)
* [Que cargas de trabalho estão protegidas pelo Azure Site Recovery?](../site-recovery/site-recovery-workload.md)

## <a name="virtual-networking"></a>Redes virtuais
Máquinas virtuais precisa de conectividade de rede. Para suportar este requisito, o Azure requer que as máquinas virtuais ligadas a uma rede Virtual do Azure. Uma rede Virtual do Azure é uma construção lógica desenvolvida com os recursos de infraestrutura de rede do Azure física. Cada rede Virtual do Azure lógica está isolada da todas as outras redes virtuais do Azure. Este isolamento ajuda a assegurar que o tráfego de rede das implementações não está acessível para outros clientes do Microsoft Azure.

Saiba mais:

* [Descrição geral de segurança de rede do Azure](security-network-overview.md)
* [Descrição geral da rede virtual](../virtual-network/virtual-networks-overview.md)
* [Funcionalidades de redes e partnerships para cenários de empresa](https://azure.microsoft.com/blog/networking-enterprise/)

## <a name="security-policy-management-and-reporting"></a>Gestão de políticas de segurança e de relatórios
Centro de segurança do Azure ajuda-o a evitar, detetar e responder a ameaças e fornece que maior visibilidade e controlo sobre, a segurança dos seus recursos Azure. Fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

Centro de segurança do Azure ajuda-o a otimizar e monitorizar a segurança da máquina virtual por:

* Fornecendo a máquina virtual [recomendações de segurança](../security-center/security-center-recommendations.md) , tais como aplicar atualizações do sistema, configure os pontos finais ACLs, ativar antimalware, ative os grupos de segurança de rede e aplicar encriptação de disco.
* Monitorizar o estado das suas máquinas virtuais

Saiba mais:

* [Introdução ao centro de segurança do Azure](../security-center/security-center-intro.md)
* [Perguntas mais frequentes sobre o Centro de segurança do Azure](../security-center/security-center-faq.md)
* [Operações e planeamento do Centro de segurança do Azure](../security-center/security-center-planning-and-operations-guide.md)

## <a name="compliance"></a>Conformidade
Máquinas virtuais do Azure está certificado para FISMA, FedRAMP, HIPAA, PCI DSS nível 1 e outros programas de conformidade de chave. Este certificação torna mais fácil para as suas próprias aplicações do Azure satisfazer os requisitos de conformidade e para a sua empresa resolver uma vasta gama de locais e internacionais requisitos regulamentares.

Saiba mais:

* [Centro de fidedignidade da Microsoft: conformidade](https://www.microsoft.com/TrustCenter/Compliance/default.aspx)
* [Nuvem fidedigna: Segurança do Microsoft Azure, privacidade e conformidade](http://download.microsoft.com/download/1/6/0/160216AA-8445-480B-B60F-5C8EC8067FCA/WindowsAzure-SecurityPrivacyCompliance.pdf)
