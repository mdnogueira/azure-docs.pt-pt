---
title: "Proteger aplicações PaaS, utilizando o armazenamento do Azure | Microsoft Docs"
description: " Saiba mais sobre a segurança de armazenamento do Azure melhores práticas para proteger o seu web de PaaS e as aplicações móveis. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: 
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomShinder
ms.openlocfilehash: 16ee6d9d2f02c758d7682626a8b71a3ff17f841c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Proteger o PaaS web e aplicações móveis com o Storage do Azure
Neste artigo, discutimos a uma coleção de segurança de armazenamento do Azure melhores práticas para proteger o seu web de PaaS e as aplicações móveis. Estas melhores práticas são derivadas da nossa experiência com o Azure e as experiências dos clientes, como por si.

O [manual de segurança de armazenamento do Azure](../storage/common/storage-security-guide.md) é uma excelente origem para obter informações detalhadas sobre o Storage do Azure e segurança.  Este artigo aborda a um nível elevado alguns dos conceitos encontrados no guia de segurança e ligações para o guia de segurança, bem como outras origens, para obter mais informações.

## <a name="azure-storage"></a>Storage do Azure
Azure torna possível implementar e utilizar o armazenamento de formas não facilmente alcançável no local. Com o storage do Azure, pode aceder aos níveis elevados de escalabilidade e disponibilidade com relativamente poucas esforço. Não só é o alicerce de armazenamento do Azure para o Windows e máquinas virtuais do Linux do Azure, também pode suportar grande aplicações distribuídas.

O Storage do Azure fornece os seguintes quatro serviços: Blob Storage, Table Storage, Armazenamento de filas e File Storage. Para obter mais informações, consulte [introdução ao Storage do Microsoft Azure](../storage/storage-introduction.md).

## <a name="best-practices"></a>Melhores práticas
Este artigo aborda os seguintes procedimentos recomendados:

- Proteção de acesso:
   - Assinaturas de Acesso Partilhado (SAS)
   - Disco gerido
   - Controlo de Acesso Baseado em Funções (RBAC)

- Encriptação de armazenamento:
   - Encriptação do lado do cliente para os dados de valor elevado
   - Encriptação de disco do Azure para máquinas virtuais (VMs)
   - Encriptação do Serviço de Armazenamento

## <a name="access-protection"></a>Proteção de acesso
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Utilize a assinatura de acesso partilhado em vez de uma chave de conta de armazenamento

Uma solução de IaaS, normalmente com máquinas virtuais do Windows Server ou Linux, os ficheiros estão protegidos contra divulgação e ameaças de adulteração de mensagens em fila utilizando os mecanismos de controlo de acesso. No Windows que utilizaria [acesso (ACL) de listas de controlo](../virtual-network/virtual-networks-acl.md) e em Linux, provavelmente pretende utilizar [chmod](https://en.wikipedia.org/wiki/Chmod). Essencialmente, esta é exatamente o que deverá fazer se foram proteger ficheiros num servidor no seu próprio Centro de dados hoje.

PaaS é diferente. Uma das formas mais comuns para armazenar os ficheiros no Microsoft Azure está a utilizar [Blob storage do Azure](../storage/storage-dotnet-how-to-use-blobs.md). É uma diferença entre o armazenamento de BLOBs e outros ficheiros de e/s de ficheiro e os métodos de proteção que vêm com e/s de ficheiro.

Controlo de acesso é fundamental. Para o ajudar a controlar o acesso ao armazenamento do Azure, o sistema irá gerar duas chaves de conta de armazenamento de 512 bits (SAKs) quando é [criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md). O nível de redundância chave torna possível para que possa evitar a interrupção do serviço durante a rotação de chave de rotina.

Chaves de acesso de armazenamento são segredos de alta prioridade e só devem estar acessíveis para esses responsável pelo controlo de acesso de armazenamento. Se as pessoas erradas obtém acesso a estas chaves, estes serão tem controlo total do armazenamento e foi substituir, eliminar ou adicionar ficheiros ao armazenamento. Isto inclui o software maligno e outros tipos de conteúdo que potencialmente pode comprometer a sua organização ou os seus clientes.

Ainda precisa de uma forma de fornecer acesso a objetos no armazenamento. Para fornecer acesso mais granular, pode tirar partido de [assinatura de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). O SAS possibilita a partilha de objetos específicos no armazenamento para um intervalo de tempo predefinido e com permissões específicas. Uma assinatura de acesso partilhado permite-lhe definir:

- O intervalo durante o qual a SAS é válida, incluindo a hora de início e a hora de expiração.
- As permissões concedidas à SAS. Por exemplo, um SAS num blob poderá conceder uma leitura de utilizador e permissões de escrita a esse blob, mas não eliminar as permissões.
- Opcional endereço IP ou intervalo de endereços IP do que o armazenamento do Azure aceita a SAS. Por exemplo, poderá especificar um intervalo de endereços IP que pertençam a sua organização. Isto proporciona outra medida de segurança para a SAS.
- O protocolo através do qual o Storage do Azure aceita a SAS. Pode utilizar este parâmetro opcional para restringir o acesso aos clientes que utilizam o HTTPS.

SAS permite-lhe partilhar conteúdo a forma como pretende partilhar sem ausente fornecer as chaves de conta de armazenamento. Sempre através da SAS na sua aplicação é uma forma segura para partilhar os seus recursos de armazenamento sem comprometer as chaves de conta de armazenamento.

Para obter mais informações, consulte [utilizar assinaturas de acesso partilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Para saber mais sobre potenciais riscos e as recomendações para mitigar os riscos, consulte o artigo [de melhores práticas quando através da SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Utilize discos geridos para VMs

Quando escolhe [Azure geridos discos](../storage/storage-managed-disks-overview.md), Azure gere as contas de armazenamento que utilizar para os discos VM. Tudo o que precisa de fazer é escolher o tipo de disco (Premium ou Standard) e o tamanho do disco; Armazenamento do Azure irá efetuar o resto. Não tem de preocupar com limites de escalabilidade que poderão ter caso contrário, necessárias para a várias contas de armazenamento.

Para obter mais informações, consulte [perguntas mais frequentes sobre geridos e não geridas discos premium](../storage/storage-faq-for-disks.md).

### <a name="use-role-based-access-control"></a>Utilizar o controlo de acesso baseado em funções

Anteriormente discutimos utilizando a assinatura de acesso partilhado (SAS) para conceder acesso limitado a objetos da sua conta do storage para outros clientes, sem a chave de conta de armazenamento de conta a exposição. Por vezes, os riscos associados uma operação específica em relação a sua conta do storage suplantam as vantagens de SAS. Por vezes, é mais simples gerir o acesso de outras formas.

Outra forma de gerir o acesso é utilizar [controlo de acesso em funções do Azure](../active-directory/role-based-access-control-what-is.md) (RBAC). Utilizar o RBAC, foco no dar aos funcionários as permissões exatas que necessitam, com base na necessidade de conhecer e princípios de segurança do menor privilégio. Demasiados permissões podem expor uma conta para os atacantes. Permissões insuficientes significa que os funcionários não é possível obter o trabalho feito de forma eficiente. RBAC Ajuda a resolver este problema, oferecendo gestão de acesso detalhada para o Azure. Este é imperativo para as organizações que pretendem aplicar políticas de segurança para acesso a dados.

Pode tirar partido das funções incorporadas do RBAC do Azure para atribuir os privilégios aos utilizadores. Considere a utilização de contribuinte de conta de armazenamento para os operadores da nuvem que necessitam de gerir contas de armazenamento e a função de contribuinte de conta de armazenamento clássico para gerir contas de armazenamento clássicas. Para os operadores da nuvem que necessitam de gerir VMs, mas não o armazenamento de rede ou conta virtual ao qual estão ligados, considere a adicioná-los para a função de contribuinte de Máquina Virtual.

As organizações que não a impor controlo de acesso de dados ao tirar partido das capacidades, tal como o RBAC podem ser dá ao mais privilégios que necessários para os respetivos utilizadores. Isto pode levar ao comprometimento de dados, permitindo que alguns utilizadores acesso a dados que não devem ter em primeiro lugar.

Para saber mais sobre RBAC, consulte:

- [Controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-control-configure.md)
- [Funções incorporadas para controlo de acesso baseado em funções do Azure](../active-directory/role-based-access-built-in-roles.md)
- [Guia de segurança de armazenamento do Azure](../storage/common/storage-security-guide.md) para detalhes sobre como proteger a sua conta de armazenamento com o RBAC

## <a name="storage-encryption"></a>Encriptação do armazenamento
### <a name="use-client-side-encryption-for-high-value-data"></a>Utilizar encriptação do lado do cliente para os dados de valor elevado

Encriptação do lado do cliente permite-lhe programaticamente encriptar dados em trânsito antes de carregar para o armazenamento do Azure e através de programação desencriptar dados ao obtê-lo a partir do armazenamento.  Isto fornece encriptação de dados em trânsito, mas também fornece encriptação de dados inativos.  A encriptação do lado do cliente é o método mais seguro de encriptar os dados, mas que é necessário efetuar alterações programáticas à sua aplicação e colocar os processos de gestão de chaves no local.

Encriptação do lado do cliente também lhe permite ter controlo único as suas chaves de encriptação.  Pode gerar e gerir as suas próprias chaves de encriptação.  A encriptação do lado do cliente utiliza uma técnica de envelope em que a biblioteca de clientes do storage do Azure gera uma conteúdo chave de encriptação (CEK) que, em seguida, é moldada (encriptada) utilizando a chave de encriptação de chaves (KEK). A KEK é identificada por um identificador de chave e pode ser um par de chaves assimétricas ou uma chave simétrica e pode ser gerida localmente ou armazenada no [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md).

Encriptação do lado do cliente está incorporada no Java e as bibliotecas de cliente do armazenamento de .NET.  Consulte [encriptação do lado do cliente e o Cofre de chaves do Azure para armazenamento do Microsoft Azure](../storage/storage-client-side-encryption.md) para obter informações sobre a encriptação de dados dentro de aplicações de cliente e gerar e gerir as suas próprias chaves de encriptação.

### <a name="azure-disk-encryption-for-vms"></a>Encriptação de disco do Azure para as VMs
Encriptação de disco do Azure é uma funcionalidade que ajuda-o a encriptar os discos da máquina virtual do Windows e Linux IaaS. Tira partido do Azure Disk Encryption a funcionalidade do BitLocker de padrão da indústria do Windows e a funcionalidade de DM-Crypt do Linux para fornecer a encriptação de volume para o SO e os discos de dados. A solução é integrada com o Cofre de chaves do Azure para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves. A solução também garante que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure.

Consulte [encriptação de disco do Azure para o Windows e as VMs de Linux IaaS](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Encriptação do Serviço de Armazenamento
Quando [encriptação do serviço de armazenamento](../storage/storage-service-encryption.md) para armazenamento de ficheiros está ativado, os dados são encriptados automaticamente utilizando a encriptação AES 256. A Microsoft lida com todos os encriptação, desencriptação e gestão de chaves. Esta funcionalidade está disponível para tipos de redundância do LRS e GRS.

## <a name="next-steps"></a>Passos seguintes
Este artigo introduzidos uma coleção de segurança de armazenamento do Azure melhores práticas para proteger o seu web de PaaS e as aplicações móveis. Para obter mais informações sobre como proteger as suas implementações PaaS, consulte:

- [Proteger implementações PaaS](security-paas-deployments.md)
- [Proteger o PaaS web e aplicações móveis com o serviços de aplicações do Azure](security-paas-applications-using-app-services.md)
- [Proteger bases de dados PaaS no Azure](security-paas-applications-using-sql.md)
