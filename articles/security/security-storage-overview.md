---
title: "Funcionalidades de segurança que podem ser utilizadas com o Storage do Azure | Microsoft Docs"
description: " Este artigo fornece uma descrição geral das principais funcionalidades de segurança do Azure que podem ser utilizadas com o Storage do Azure. "
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: a118bde2290e68c9a741e40cda210d47db918047
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="azure-storage-security-overview"></a>Descrição geral de segurança de armazenamento do Azure
O Storage do Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem da durabilidade, da disponibilidade e da escalabilidade para satisfazer as necessidades dos seus clientes. Storage do Azure fornece um conjunto completo de capacidades de segurança:

* A conta de armazenamento pode ser protegida utilizando o controlo de acesso baseado em funções e o Azure Active Directory.
* Dados podem ser protegidos em trânsito entre uma aplicação e o Azure utilizando a encriptação do lado do cliente, HTTPS ou SMB 3.0.
* Dados podem ser definidos para ser encriptados automaticamente quando escrita ao Storage do Azure através da encriptação do serviço de armazenamento.
* SO e discos de dados utilizados por máquinas virtuais podem ser definidos para ser encriptado utilizando o Azure Disk Encryption.
* Pode ser concedido acesso delegado aos objetos de dados no armazenamento do Azure através de assinaturas de acesso partilhado.
* O método de autenticação utilizado por outra quando acedem ao armazenamento pode ser controlado através da análise do armazenamento.

Para ver mais detalhada segurança no armazenamento do Azure, consulte o [manual de segurança de armazenamento do Azure](../storage/common/storage-security-guide.md). Este guia fornece uma descrição detalhada para as funcionalidades de segurança do Storage do Azure, tal como chaves de conta de armazenamento, a encriptação de dados em trânsito e rest e análise de armazenamento.

Este artigo fornece uma descrição geral das funcionalidades de segurança do Azure que pode ser utilizado com o Storage do Azure. As ligações são fornecidos para artigos que fornecer detalhes de cada funcionalidade, pode saber mais.

Seguem-se as funcionalidades de núcleos para ser abordadas neste artigo:

* Controlo de Acesso Baseado em Funções
* Acesso delegado a objetos de armazenamento
* Encriptação em trânsito
* Encriptação rest/encriptação do serviço de armazenamento
* Azure Disk Encryption
* Azure Key Vault

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)
Pode proteger a sua conta de armazenamento com controlo de acesso baseado em funções (RBAC). Restringir o acesso com base no [precisa de saber](https://en.wikipedia.org/wiki/Need_to_know) e [menor privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) princípios de segurança é imperativo para as organizações que pretendem aplicar políticas de segurança para acesso a dados. Estes direitos de acesso são concedidos ao atribuir a função RBAC adequada aos grupos e aplicações num determinado âmbito. Pode utilizar [funções incorporadas do RBAC](../active-directory/role-based-access-built-in-roles.md), tais como contribuinte de conta de armazenamento, para atribuir os privilégios aos utilizadores.

Saiba mais:

* [Controlo de Acesso Baseado em Funções do Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado a objetos de armazenamento
Uma assinatura de acesso partilhado (SAS) concede acesso delegado a recursos na sua conta de armazenamento. O SAS significa que pode conceder que um cliente limitada permissões para objetos na sua conta de armazenamento para um determinado período de tempo e com um conjunto especificado de permissões. Pode conceder estas permissões limitadas sem ter de partilhar as chaves de acesso da conta. O SAS é um URI que abrange nos respetivos parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para aceder aos recursos de armazenamento com a SAS, o cliente só tem de fornecer a SAS para o método ou construtor adequado.

Saiba mais:

* [Compreender o modelo SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Criar e utilizar um SAS com o Blob storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Encriptação em trânsito
A encriptação em trânsito é um mecanismo de proteção de dados quando transmitido através de redes. Com o Storage do Azure pode proteger dados utilizando:

* [Encriptação de nível de transporte](../storage/common/storage-security-guide.md#encryption-in-transit), por exemplo, HTTPS quando a transferência de dados ou a sair do armazenamento do Azure.
* [Ligar a encriptação](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), tais como a encriptação SMB 3.0 para partilhas de ficheiros do Azure.
* [Encriptação do lado do cliente](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para encriptar os dados antes de é transferido para o armazenamento e para desencriptar os dados depois de serem transferidos fora do armazenamento.

Saiba mais sobre a encriptação do lado do cliente:

* [Encriptação do lado do cliente para o armazenamento do Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Série os controlos da segurança de nuvem: encriptação de dados em trânsito](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Encriptação inativa
Para muitas organizações, [encriptação de dados Inativos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório para soberania dos dados, privacidade e conformidade de dados. Existem três funcionalidades do Azure que fornecem encriptação de dados "Inativos":

* [Encriptação do serviço de armazenamento](../storage/common/storage-security-guide.md#encryption-at-rest) permite-lhe solicitar que o serviço de armazenamento encriptar dados automaticamente quando escrita ao Storage do Azure.
* [Encriptação do lado do cliente](../storage/common/storage-security-guide.md#client-side-encryption) também fornece a funcionalidade de encriptação de inativos.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite-lhe encriptar os discos de SO e discos de dados utilizados pela máquina virtual IaaS.

Saiba mais sobre a encriptação do serviço de armazenamento:

* [Encriptação do serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/) está disponível para [Blob Storage do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento do Azure, consulte [ficheiro](https://azure.microsoft.com/services/storage/files/), [disco (armazenamento Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [tabela](https://azure.microsoft.com/services/storage/tables/), e [fila](https://azure.microsoft.com/services/storage/queues/).
* [Encriptação do serviço de armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
Encriptação de disco do Azure para máquinas virtuais (VMs) ajuda-o a segurança organizacional do endereço e requisitos de conformidade ao encriptar os discos da VM (incluindo discos de dados e de arranque) com chaves e as políticas de controlo no [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/).

Encriptação de disco para as VMs funciona para os sistemas operativos Linux e Windows. Também utiliza o Cofre de chaves para o ajudar a salvaguardar, gerir e utilização das suas chaves de encriptação de disco de auditoria. Todos os dados em discos de VM são encriptados em pausa utilizando a tecnologia de encriptação de norma da indústria nas suas contas do Storage do Azure. A solução de encriptação de disco para o Windows é baseada no [a encriptação de unidade BitLocker do Microsoft](https://technet.microsoft.com/library/cc732774.aspx), e a solução de Linux baseia-se no [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba mais:

* [Encriptação de disco do Azure para o Windows e as máquinas virtuais de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Utiliza a Azure Disk Encryption [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/) para ajudar a controlar e gerir chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, enquanto garantir que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure. Deve utilizar o Cofre de chaves para chaves e a utilização da política de auditoria.

Saiba mais:

* [O que é o Cofre de chaves do Azure?](../key-vault/key-vault-whatis.md)
* [Introdução ao Cofre de chaves do Azure](../key-vault/key-vault-get-started.md)
