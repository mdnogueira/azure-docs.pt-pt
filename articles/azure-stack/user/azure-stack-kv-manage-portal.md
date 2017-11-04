---
title: Gerir o Cofre de chaves na pilha do Azure utilizando o portal | Microsoft Docs
description: "Saiba como gerir o Cofre de chaves na pilha do Azure através do portal"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: d263cbcc81be37eaedfdb771436fd13ef25362f8
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="manage-key-vault-in-azure-stack-by-using-the-portal"></a>Gerir o Cofre de chaves na pilha do Azure através do portal

Pode gerir o Cofre de chaves na pilha do Azure utilizando o portal de pilha do Azure. Este artigo ajuda-o a começar a utilizar para criar e gerir um cofre de chaves na pilha do Azure. 

## <a name="prerequisites"></a>Pré-requisitos  

Tem de subscrever uma oferta, que inclui o serviço Cofre de chaves do Azure.
 
## <a name="create-a-key-vault"></a>Criar um cofre de chaves 

1. Iniciar sessão para o [portal de utilizador](https://portal.local.azurestack.external).  

2. A partir do dashboard, selecione **novo** > **segurança + identidade** > **Cofre de chaves**.  

    ![Ecrã de Cofre de chaves](media/azure-stack-kv-manage-portal/image1.png)  

3. No **criar Cofre de chaves** painel, atribuir um **nome** para o cofre. Os nomes de cofre podem conter apenas carateres alfanuméricos e o caráter especial de hífen (-). Estes não devem começar com um número.  

4. Escolha um **subscrição** da lista de subscrições disponíveis. Todas as subscrições que oferecem serviço Cofre de chaves são apresentadas na lista pendente.  

5. Selecione um existente **grupo de recursos** ou crie um novo.  

6. Selecione o **escalão de preço**.  
    >[!NOTE]
    > Chave cofres no suporte do Azure pilha Development Kit **padrão** SKUs apenas.

7. Escolha uma das existente **políticas de acesso** ou crie um novo. Uma política de acesso permite-lhe de conceder permissões para um utilizador, aplicação ou um grupo de segurança executar operações com este cofre.  

8. Opcionalmente, escolha um **política de acesso avançado** para ativar funcionalidades, como o acesso às máquinas virtuais (VMs) para a implementação, aceder ao Gestor de recursos para implementação do modelo e acesso ao Azure Disk Encryption para encriptação de volume. 
  
9.  Depois de configurar as definições, selecione **OK**e, em seguida, selecione **criar**. Esta ação inicia a implementação do Cofre de chaves. 

## <a name="manage-keys-and-secrets"></a>Gerir chaves e segredos

Depois de criar um cofre, utilize os seguintes passos para criar e gerir chaves e segredos no cofre.

### <a name="create-a-key"></a>Criar uma chave

1. Iniciar sessão para o [portal de utilizador](https://portal.local.azurestack.external).  

2. A partir do dashboard, selecione **todos os recursos**, selecione o Cofre de chaves que criou anteriormente e, em seguida, selecione o **chaves** mosaico.  

3. No **chaves** painel, selecione **adicionar**. 

4. No **criar uma chave** painel, na lista de **opções**, escolha o método que pretende utilizar para criar uma chave. Pode **gerar** uma nova chave, **carregar** existente de chaves ou utilizar **restaurar cópia de segurança** para selecionar uma cópia de segurança de uma chave.  

5. Introduza um **nome** para a sua chave. O nome da chave pode conter apenas carateres alfanuméricos e o hífen caráter especial (-).  

6. Opcionalmente, configure o **definir a data de ativação** e **definir a data de expiração** valores para a sua chave.  

7. Selecione **criar** para iniciar a implementação.  

Depois da chave foi criada com êxito, pode selecioná-lo em **chaves** e ver ou modificar as respetivas propriedades. A secção de propriedades contém o **identificador da chave**, que é um Uniform Resource Identifier (URI) através do qual aplicações externas podem aceder esta chave. Para limitar as operações esta chave, configure as definições em **permitido operações**.

![Chave URI](media/azure-stack-kv-manage-portal/image4.png)  

### <a name="create-a-secret"></a>Criar um segredo 

1. Iniciar sessão para o [portal de utilizador](https://portal.local.azurestack.external).  
2. A partir do dashboard, selecione **todos os recursos**, selecione o Cofre de chaves que criou anteriormente e, em seguida, selecione o **segredos** mosaico.  

3. Em **segredos**, selecione **adicionar**.  

4. Em **criar um segredo**, na lista de **carregar opções**, escolha uma opção através do qual pretende criar um segredo. Pode criar um segredo **manualmente** se introduzir um valor para o segredo ou ao carregar um **certificado** do seu computador local.  

5. Introduza um **nome** para o segredo. O nome secreto pode conter apenas carateres alfanuméricos e o hífen caráter especial (-).  

6. Opcionalmente, especifique o **tipo de conteúdo**e configurar os valores para **definir a data de ativação** e **definir a data de expiração** para o segredo.  

7. Selecione **criar** para iniciar a implementação.  

Depois do segredo foi criado com êxito, pode selecioná-lo em **segredos** e ver ou modificar as respetivas propriedades. A secção de propriedades contém um **segredo identificador**, que é um URI que aplicações externas podem aceder a este segredo. 

![Segredo do URI](media/azure-stack-kv-manage-portal/image5.png) 


## <a name="next-steps"></a>Passos seguintes
* [Implementar uma VM ao obter a palavra-passe armazenada no Cofre de chaves](azure-stack-kv-deploy-vm-with-secret.md) 
* [Implementar uma VM com o certificado armazenado no Cofre de chaves](azure-stack-kv-push-secret-into-vm.md)     


