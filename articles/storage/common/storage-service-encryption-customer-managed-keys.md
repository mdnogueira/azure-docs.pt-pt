---
title: "Encriptação do serviço de armazenamento do Azure utilizando o cliente chaves geridas no Cofre de chaves do Azure | Microsoft Docs"
description: "Utilizar a funcionalidade de encriptação do serviço do Storage do Azure para encriptar o armazenamento de Blobs do Azure no lado do serviço ao armazenar os dados e desencriptá-lo ao obter os dados utilizando o cliente chaves geridas."
services: storage
documentationcenter: .net
author: lakasa
manager: jahogg
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2017
ms.author: lakasa
ms.openlocfilehash: 6d1e6752fb631114f5be06cb27a63e40547bf6ca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Encriptação do serviço de armazenamento utilizando o cliente chaves geridas no Cofre de chaves do Azure

Microsoft Azure está empenhada em ajudar a proteger e a salvaguardar os seus dados para satisfazer os seus compromissos de conformidade e segurança organizacional.  Pode proteger os dados inativos de uma forma consiste em utilizar o armazenamento serviço encriptação (SSE), que encripta os dados ao escrever-a para o armazenamento e automaticamente desencripta os dados ao obtê-lo. A encriptação e desencriptação é completamente transparente, automática e utiliza 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um bloco de maior cifras disponíveis.

Pode utilizar as chaves de encriptação gerida pela Microsoft com SSE ou pode utilizar as suas próprias chaves de encriptação. Este artigo aborda a última opção. Para obter mais informações sobre a utilização de chaves gerida pela Microsoft, ou SSE no geral, consulte [encriptação do serviço de armazenamento para dados Inativos](../storage-service-encryption.md).

Para fornecer a capacidade de utilizar as suas próprias chaves de encriptação, SSE para o Blob storage é integrado com o Cofre de chaves do Azure (AKV). Pode criar as suas próprias chaves de encriptação e armazená-las num AKV, ou pode utilizar os APIs do AKV para gerar chaves de encriptação. Não só AKV permite-lhe gerir e controlar as chaves, também lhe permite efetuar a auditoria da utilização de chave. 

Por que motivo seria que pretende criar as suas próprias chaves? Proporciona uma maior flexibilidade, permitindo-lhe criar, rodar, desativar e definir os controlos de acesso. Também permite que as chaves de encriptação utilizadas para proteger os seus dados de auditoria.

## <a name="sse-with-customer-managed-keys-preview"></a>SSE pré-visualização do cliente chaves geridas

Esta funcionalidade encontra-se em pré-visualização. Para utilizar esta funcionalidade, terá de criar uma nova conta de armazenamento. Pode criar um novo cofre de chaves e chave ou utilizar um cofre de chaves e a chave. A conta de armazenamento e o Cofre de chaves têm de ser na mesma região, mas podem estar em diferentes subscrições.

Para participar na pré-visualização, contacte [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com). Podemos irá fornecer uma ligação especial para participar na pré-visualização.

Para obter mais informações, consulte o [FAQ](#frequently-asked-questions-about-storage-service-encryption-for-data-at-rest).

> [!IMPORTANT]
> Tem de inscrever-se para a pré-visualização antes de seguir os passos neste artigo. Sem acesso de pré-visualização, não poderá ativar esta funcionalidade no portal.

## <a name="getting-started"></a>Introdução
## <a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Passo 1: [criar uma nova conta de armazenamento](../storage-create-storage-account.md)

## <a name="step-2-enable-encryption"></a>Passo 2: Encriptação ativar
Pode ativar SSE para a conta de armazenamento utilizando o [portal do Azure](https://portal.azure.com). No painel de definições para a conta de armazenamento, procure a secção de serviço Blob, conforme mostrado na figura seguinte e clique em encriptação.

![Portal captura de ecrã com a opção de encriptação](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)
<br/>*Ativar SSE para o serviço Blob*

Se pretender programaticamente ativar ou desativar a encriptação do serviço de armazenamento numa conta de armazenamento, pode utilizar o [API de REST do fornecedor de recursos do Azure Storage](https://docs.microsoft.com/en-us/rest/api/storagerp/?redirectedfrom=MSDN), a [biblioteca cliente de fornecedor de recursos do armazenamento para .NET ](https://docs.microsoft.com/en-us/dotnet/api/?redirectedfrom=MSDN), [o azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/overview?view=azurermps-4.0.0), ou o [CLI do Azure](https://docs.microsoft.com/en-us/azure/storage/storage-azure-cli).

Neste ecrã, se não vir a caixa de verificação "utilizar a sua própria chave", não foi aprovou para a pré-visualização. Enviar um e-mail para [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) e solicitar a aprovação.

![Portal captura de ecrã que mostra a pré-visualização de encriptação](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

Por predefinição, SSE utiliza chaves gerida pela Microsoft. Para utilizar as suas próprias chaves, a caixa de verificação. Em seguida, pode especificar a chave de URI, ou selecione um cofre de chaves e uma chave de selecionador de.

## <a name="step-3-select-your-key"></a>Passo 3: Selecione a sua chave

![Utilizam o portal encriptações de captura de ecrã com a suas próprias chave opção](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

![Captura de ecrã Portal, com encriptação com introduzir uri chave opção](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se a conta de armazenamento não tem acesso ao Cofre de chave, pode executar o seguinte comando com o Azure Powershell para conceder acesso às contas de armazenamento para o Cofre de chave necessário.

![Captura de ecrã portal com acesso negado para o Cofre de chaves](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Também pode conceder o acesso através do portal do Azure ao ir para o Cofre de chaves do Azure no portal do Azure e conceder acesso à conta de armazenamento.

## <a name="step-4-copy-data-to-storage-account"></a>Passo 4: Copiar dados para a conta de armazenamento
Se gostaria de transferência de dados na sua nova conta de armazenamento, de modo a que está encriptada, consulte [passo 3 de introdução na encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-3-copy-data-to-storage-account).

## <a name="step-5-query-the-status-of-the-encrypted-data"></a>Passo 5: Consultar o estado dos dados encriptados
Para consultar o estado dos dados encriptados, consulte [passo 4 de introdução na encriptação do serviço de armazenamento para dados Inativos](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption#step-4-query-the-status-of-the-encrypted-data).

## <a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Perguntas mais frequentes sobre a encriptação do serviço de armazenamento para dados Inativos
**P: Posso estou a utilizar o Premium storage; Pode utilizar SSE com chaves cliente gerido?**

R: Sim, SSE com gerida pela Microsoft e cliente chaves geridas é suportado no armazenamento Standard e Premium storage. 

**P: Posso criar novas contas de armazenamento com SSE com chaves de cliente gerido ativadas com o Azure PowerShell e da CLI do Azure?**

R: Sim.

**P: quanto mais efetua um custo de armazenamento do Azure se chaves geridas de SSE com o cliente está ativado?**

R: não há um custo associado para utilizar o Cofre de chaves do Azure. Para obter mais detalhes visitam [preços do Cofre de chave](https://azure.microsoft.com/en-us/pricing/details/key-vault/). Não há sem custos adicionais para a utilização de SSE.

**P: posso revogar o acesso às chaves de encriptação?**

R: Sim, pode revogar o acesso em qualquer altura. Existem várias formas para revogar o acesso às suas chaves. Consulte [PowerShell do Cofre de chaves do Azure](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.0.0) e [CLI do Azure chave de cofre](https://docs.microsoft.com/en-us/cli/azure/keyvault) para obter mais detalhes. Revogar o acesso eficazmente irá bloquear o acesso a todos os blobs na conta de armazenamento como a chave de encriptação da conta não está acessível ao Storage do Azure.

**P: Posso criar uma conta de armazenamento e a chave numa região diferente?**

R: não, a conta de armazenamento e a chave de cofre/chave tem de estar na mesma região. 

**P: posso ativar SSE com cliente gerido chaves ao criar a conta de armazenamento?**

R: um número de Quando ativar SSE ao criar a conta de armazenamento, só pode utilizar as chaves Microsoft gerida. Se gostaria de utilizar chaves de cliente gerido, tem de atualizar as propriedades da conta de armazenamento. Pode utilizar REST ou uma das bibliotecas de cliente de armazenamento para atualizar programaticamente a sua conta de armazenamento ou atualizar as propriedades da conta de armazenamento no portal do Azure depois de criar a conta.

**P: posso desativar a encriptação enquanto utilizar SSE com o cliente chaves geridas?**

R: não, não é possível desativar a encriptação enquanto utilizar SSE com o cliente chaves geridas. Para desativar a encriptação, tem de trocar a utilização de chaves gerida pela Microsoft. Pode fazê-lo utilizando o portal do Azure ou o PowerShell.

**P: é SSE ativada por predefinição, quando criar uma nova conta de armazenamento?**

R: SSE não está ativada por predefinição; Pode utilizar o portal do Azure para ativá-la. Através de programação também pode ativar esta funcionalidade utilizando a API de REST de fornecedor de recursos de armazenamento. 

**P: Posso não é possível ativar a encriptação na minha conta de armazenamento.**

R: é uma conta de armazenamento do Resource Manager? Contas de armazenamento clássicas não são suportadas. SSE com chaves de cliente gerido também pode ser ativado apenas em contas de armazenamento recentemente criada do Gestor de recursos.

**P: É SSE com o cliente gerido chaves só é permitidas em regiões específicas?**

R: SSE está disponível apenas determinados regiões para armazenamento de BLOBs para esta pré-visualização. E-mail [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) para procurar a disponibilidade e detalhes sobre a pré-visualização. 

**P: como posso o contacto certo se posso ter algum problema ou quiser fornecer comentários?**

R: contacto [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) para quaisquer problemas relacionados com a encriptação do serviço de armazenamento. 

## <a name="next-steps"></a>Passos seguintes

*   Para mais informações sobre o conjunto completo de segurança capacidades que ajudam os programadores criem aplicações seguras, consulte o [manual de segurança de armazenamento](https://docs.microsoft.com/en-us/azure/storage/storage-security-guide).
*   Para obter informações gerais sobre o Cofre de chaves do Azure, consulte [que é o Cofre de chaves do Azure](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis)?
*   Para começar a trabalhar no Cofre de chaves do Azure, consulte [introdução ao Cofre de chaves do Azure](../../key-vault/key-vault-get-started.md).
