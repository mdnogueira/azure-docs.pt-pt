---
title: "Diagnosticar falhas de artefacto numa máquina virtual do Azure DevTest Labs | Microsoft Docs"
description: Saiba como resolver problemas de falhas de artefacto no Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.openlocfilehash: 9a79e50902e8e99e94148f8ef534e6745e31809a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>Diagnosticar falhas de artefacto no laboratório 
Depois de criar um artefacto, pode verificar para ver se foi concluída com êxito ou falha. Os registos de artefacto no Azure DevTest Labs fornecem informações que pode utilizar para diagnosticar uma falha de artefactos. Tem duas opções para visualizar as informações de registo de artefactos para uma VM do Windows:

* No portal do Azure
* Na VM

> [!NOTE]
> Para garantir que as falhas são corretamente identificadas e explicadas, é importante que o artefacto tem a estrutura adequada. Para obter informações sobre como construir corretamente um artefacto, consulte [criar artefactos personalizados](devtest-lab-artifact-author.md). Para ver um exemplo de um artefacto estruturado corretamente, consulte o [tipos de parâmetro de teste](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) artefactos.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Resolver problemas de falhas de artefacto utilizando o portal do Azure

1. No portal do Azure, na lista de recursos, selecione o laboratório.
2. Escolha a VM do Windows que inclui o artefacto que pretende investigar.
3. No painel esquerdo, em **geral**, selecione **artefactos**. É apresentada uma lista dos artefactos associados a essa VM. O nome do artefacto e o estado de artefactos são indicados.

   ![Estado de artefactos](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. Selecione um artefacto que mostra um **falha** estado. É aberto o artefacto. É apresentada uma mensagem de extensão que inclui detalhes sobre a falha do artefacto.

   ![Mensagem de erro de artefactos](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>Resolver problemas de falhas de artefacto da máquina virtual

1. Inicie sessão VM que contém o artefacto que pretende diagnosticar.
2. Aceda a C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status, onde *1.9* é o número de versão da extensão de Script personalizado do Azure.

   ![O ficheiro de estado](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. Abra o **estado** ficheiro.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Mensagens de blogue relacionados
* [Associar uma VM a um domínio do Active Directory existente, utilizando um modelo do Resource Manager no DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Passos seguintes
* Saiba como [adicione um repositório de Git para um laboratório](devtest-lab-add-artifact-repo.md).

