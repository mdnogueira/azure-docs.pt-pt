---
title: "Encriptação de disco do Azure FAQ | Microsoft Docs"
description: "Este artigo fornece respostas às perguntas mais frequentes sobre o Microsoft Azure disco encriptação para o Windows e as VMs de IaaS Linux."
services: security
documentationcenter: na
author: deventiwari
manager: avibm
editor: yuridio
ms.assetid: 7188da52-5540-421d-bf45-d124dee74979
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: devtiw
ms.openlocfilehash: 2ccadfdec0e653264671f5a9a38d4541b0fc4e69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-disk-encryption-faq"></a>Encriptação de disco do Azure FAQ

Este artigo fornece respostas às perguntas mais frequentes (FAQ) sobre a encriptação de disco do Azure para Windows e as VMs de IaaS Linux. Para obter mais informações sobre este serviço, consulte [encriptação de disco do Azure para Windows e as VMs de IaaS Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption).

## <a name="general-questions"></a>Perguntas gerais
**P:** em que é o Azure Disk Encryption na disponibilidade geral (DG)?

**R:** encriptação de disco do Azure para Windows e as VMs de IaaS Linux em geral é disponibilidade em todas as regiões públicas do Azure.

**P:** ocorre com o qual o utilizador que estão disponíveis com a Azure Disk Encryption?

**R:** GA de encriptação de disco do Azure suporta modelos Azure Resource Manager, do Azure PowerShell e CLI do Azure. Isto dá-lhe muita flexibilidade. Tem três opções diferentes para ativar a encriptação de disco para as suas VMs de IaaS. Para obter mais informações sobre a experiência de utilizador e orientações passo a passo disponíveis no Azure Disk Encryption, consulte experiências e cenários de implementação do Azure Disk Encryption.

**P:** quanto does Azure Disk Encryption custo?

**R:** há sem qualquer encargo para encriptar os discos da VM com o Azure Disk Encryption.

**P:** as camadas de máquina virtual do Azure Disk Encryption suporta a?

**R:** Azure Disk Encryption está disponível em VMs do escalão standard, incluindo [A, D, DS, G, GS e F](https://azure.microsoft.com/pricing/details/virtual-machines/) série VMs de IaaS. Também está disponível para VMs com o armazenamento premium. Não está disponível na camada básica VMs.

**P:** as distribuições do Linux que suporte do Azure Disk Encryption?

**R:** Azure Disk Encryption é suportada nos seguintes distribuições de servidor Linux e versões:

| Distribuição de Linux | Versão | Tipo de volume suportado para a encriptação|
| --- | --- |--- |
| Ubuntu | 16.04-DIARIAMENTE-LTS | Disco do SO e dados |
| Ubuntu | 14.04.5-DAILY-LTS | Disco do SO e dados |
| RHEL | 7.3 | Disco do SO e dados |
| RHEL | 7.2 | Disco do SO e dados |
| RHEL | 6.8 | Disco do SO e dados |
| RHEL | 6.7 | Disco de dados |
| CentOS | 7.3 | Disco do SO e dados |
| CentOS | 7.2N | Disco do SO e dados |
| CentOS | 6.8 | Disco do SO e dados |
| CentOS | 7.1 | Disco de dados |
| CentOS | 7.0 | Disco de dados |
| CentOS | 6.7 | Disco de dados |
| CentOS | 6.6 | Disco de dados |
| CentOS | 6.5 | Disco de dados |
| openSUSE | 13.2 | Disco de dados |
| SLES | 12 SP1 | Disco de dados |
| SLES | Prioridade: 12-SP1 | Disco de dados |
| SLES | HPC 12 | Disco de dados |
| SLES | Prioridade: 11-SP4 | Disco de dados |
| SLES | 11 SP4 | Disco de dados |

**P:** como posso começar a utilizar o Azure Disk Encryption?

**R:** para começar, leia o [encriptação de disco do Azure para Windows e as VMs de IaaS Linux](https://docs.microsoft.com/azure/security/azure-security-disk-encryption) documento técnico.

**P:** pode encriptar volumes de arranque e dados com o Azure Disk Encryption?

**R:** Sim, pode encriptar volumes de arranque e de dados para o Windows e as VMs de IaaS Linux. Para VMs do Windows, não é possível encriptar os dados sem primeiro encriptando o volume de SO. Para VMs com Linux, pode encriptar o volume de dados sem ter de encriptar o volume de SO primeiro. Depois de encriptou o volume de SO para Linux, a desativação da encriptação num volume SO para as VMs de IaaS Linux não é suportada.

**P:** Does Azure Disk Encryption permitem-lhe colocar a seus próprios capacidade de chave (BYOK)?

**R:** Sim, pode fornecer as suas próprias chaves de encriptação de chaves. Estas chaves são salvaguardadas no Cofre de chaves do Azure, que é o armazenamento de chaves de encriptação de disco do Azure. Para mais informações sobre as chaves de encriptação de chaves suporta cenários, consulte experiências e cenários de implementação do Azure Disk Encryption.

**P:** posso utilizar uma chave de encriptação de chaves do Azure-criar?

**R:** Sim, pode utilizar o Cofre de chaves do Azure para gerar uma chave de encriptação de chave para utilização de encriptação de disco do Azure. Estas chaves são salvaguardadas no Cofre de chaves do Azure, que é o armazenamento de chaves de encriptação de disco do Azure. Para obter mais informações sobre os cenários de suporte de chave de encriptação de chaves, consulte experiências e cenários de implementação do Azure Disk Encryption.

**P:** posso utilizar um serviço de gestão de chaves no local ou HSM para salvaguardar as chaves de encriptação?

**R:** não é possível utilizar o serviço de gestão de chaves no local ou HSM para salvaguardar as chaves de encriptação com o Azure Disk Encryption. Só pode utilizar o serviço Cofre de chaves do Azure para salvaguardar as chaves de encriptação. Para obter mais informações sobre os cenários de suporte de chave de encriptação de chaves, consulte experiências e cenários de implementação do Azure Disk Encryption.

**P:** quais são os pré-requisitos para configurar a encriptação de disco do Azure?

**R:** há um script do PowerShell de pré-requisitos. Com este script, pode criar uma aplicação do Azure Active Directory, crie um novo cofre de chave ou configurar um cofre de chaves de acesso de encriptação de disco ativar a encriptação e salvaguarda segredos e as chaves. Para obter mais informações sobre os cenários de suporte de chave de encriptação de chaves, consulte pré-requisitos do Azure Disk Encryption e cenários de implementação e experiências.

**P:** onde pode obter mais informações sobre como utilizar o PowerShell para configurar a Azure Disk Encryption?

**R:** temos algumas excelente artigos sobre a forma como pode efetuar tarefas básicas do Azure Disk Encryption, bem como em cenários mais avançados. Para as tarefas básicas, consulte [explorar o Azure Disk Encryption com o Azure PowerShell – parte 1](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/16/explore-azure-disk-encryption-with-azure-powershell/). Para cenários mais avançados, consulte [explorar o Azure Disk Encryption com o Azure PowerShell – parte 2](https://blogs.msdn.microsoft.com/azuresecurity/2015/11/21/explore-azure-disk-encryption-with-azure-powershell-part-2/).

**P:** qual a versão do Azure PowerShell o Azure Disk Encryption suporta?

**R:** utilizar a versão mais recente do SDK do Azure PowerShell para configurar a Azure Disk Encryption. Transfira a versão mais recente do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). A encriptação de disco do Azure é *não* suportada pela versão 1.1.0 do Azure SDK.

> [!NOTE]
> A extensão de pré-visualização de encriptação de disco do Linux Azure foi preterida. Para obter mais informações, consulte [extensão pré-visualização de encriptação de disco do Azure descontinuar para as VMs de IaaS Linux](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/12/deprecating-azure-disk-encryption-preview-extension-for-linux-iaas-vms/).

**P:** pode aplicar Azure Disk Encryption no meu imagem personalizada do Linux?

**R:** não é possível aplicar a Azure Disk Encryption sua imagem personalizada do Linux. Suportamos apenas as imagens de Linux Galeria para as distribuições suportadas que referida anteriormente. Não é suportada atualmente imagens personalizadas do Linux.

**P:** pode aplicar atualizações para a VM com Linux Red Hat que utiliza a atualização yum?

**R:** Sim, pode efetuar uma atualização ou correção uma VM do Red Hat com Linux. Para obter mais informações, consulte [aplicar atualizações às encriptados VM do Azure IaaS Red Hat utilizando a atualização yum](https://blogs.msdn.microsoft.com/azuresecurity/2017/07/13/applying-updates-to-a-encrypted-azure-iaas-red-hat-vm-using-yum-update/).

**P:** o que é o fluxo de trabalho de encriptação de disco do Azure recomendado para Linux?

**R:** seguinte fluxo de trabalho é recomendado ter os melhores resultados no Linux:
* Iniciar na imagem de galeria as cotações inalterado correspondente para o pretendido SO distro e a versão do
* Cópia de segurança de quaisquer unidades montadas que serão encriptadas.  Isto permite a recuperação em caso de falha, por exemplo, se a VM é reiniciada antes da encriptação foi concluída.
* Encriptar (pode demorar várias horas ou até mesmo dias dependendo das características da vm e o tamanho dos discos de dados anexados)
* Personalizar e adicione software à imagem, conforme necessário.

Se este fluxo de trabalho não for possível, depender [encriptação do serviço de armazenamento](https://docs.microsoft.com/en-us/azure/storage/common/storage-service-encryption) (SSE) de armazenamento de plataforma camada de conta pode ser uma alternativa à utilização dm-crypt de encriptação de disco completa.

**P:** o que é o disco "Bek Volume" ou "/ mnt/azure_bek_disk"?

**R:** "Bek volume" para Windows ou "azure_bek_disk/mnt /" para Linux é um volume de dados local que armazena em segurança as chaves de encriptação para encriptados VMs de IaaS do Azure.
> [!NOTE]
> Não elimine ou edite qualquer conteúdo este disco. Não é desmonte o disco, uma vez que a presença da chave de encriptação é necessária para as operações de encriptação na VM do IaaS.

**P:** onde pode devo para colocar questões ou comentários?

**R:** pode colocar questões ou comentários sobre o [fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passos seguintes
Neste documento, aprendeu mais informações sobre as perguntas mais frequentes relacionadas com a Azure Disk Encryption. Para obter mais informações sobre este serviço e as respetivas capacidades, consulte os artigos seguintes:

- [Aplicar a encriptação de disco no Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Encriptar uma máquina virtual do Azure](https://docs.microsoft.com/azure/security-center/security-center-disk-encryption)
- [Encriptação de dados do Azure Inativos](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest)
