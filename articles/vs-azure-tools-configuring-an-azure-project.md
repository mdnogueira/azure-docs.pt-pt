---
title: "Configurar um projeto de serviço em nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como configurar um projeto de serviço em nuvem do Azure no Visual Studio, dependendo dos requisitos para esse projeto."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 609d6965-05cc-47b1-82dc-c76a92d4f295
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/06/2017
ms.author: kraigb
ms.openlocfilehash: 799715093bd1a8c8e77e6cdb7168670dc263dfa5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-an-azure-cloud-service-project-with-visual-studio"></a>Configurar um projeto de serviço em nuvem do Azure com o Visual Studio
Pode configurar um projeto de serviço em nuvem do Azure, dependendo dos requisitos para esse projeto. Pode definir propriedades do projeto relativamente às seguintes categorias:

- **Publicar um serviço em nuvem para o Azure** -pode definir uma propriedade para se certificar de que um serviço em nuvem existente implementado no Azure não sejam eliminado acidentalmente.
- **Executar ou de depuração de um serviço em nuvem no computador local** -pode selecionar uma configuração de serviço a utilizar e indique se pretende iniciar o emulador do storage do Azure.
- **Validar um pacote de serviço em nuvem quando for criado** -pode optar por processe quaisquer avisos como erros, para que possa garantir que o pacote de serviço em nuvem implementa sem quaisquer problemas. 

## <a name="steps-to-configure-an-azure-cloud-service-project"></a>Passos para configurar um projeto de serviço em nuvem do Azure
1. Abrir ou criar um projeto de serviço em nuvem no Visual Studio

1. No **Explorador de soluções**, clique com o botão direito no projeto e, no menu de contexto, selecione **propriedades**.
   
1. Na página de propriedades do projeto, selecione o **desenvolvimento** separador.

    ![Menu de propriedades do projeto](./media/vs-azure-tools-configuring-an-azure-project/solution-explorer-project-properties-menu.png)

1. Definir **pedido antes de eliminar uma implementação existente** para **verdadeiro**. Esta definição ajuda a garantir que não elimina acidentalmente uma implementação existente no Azure

1. Selecione o pretendido **a configuração do serviço** para indicar que a configuração do serviço que pretende utilizar ao executar ou depurar o seu serviço em nuvem localmente. Para obter mais informações sobre como modificar uma configuração de serviço para uma função, consulte [como configurar as funções para um serviço em nuvem do Azure com o Visual Studio](./vs-azure-tools-configure-roles-for-cloud-service.md).

1. Definir **emulador do storage do Azure iniciar** para **verdadeiro** para iniciar o emulador do storage do Azure quando executa ou depurar o seu serviço em nuvem localmente.

1. Definir **processe avisos como erros** para **verdadeiro** para se certificar de que não é possível publicar se existem erros de validação do pacote.

1. Definir **utilizar portas de projeto web** para **verdadeiro** para se certificar de que a função da web utiliza a mesma porta de cada vez que inicia localmente no IIS Express.

1. Na barra de ferramentas da Visual Studio, selecione **guardar**.

## <a name="next-steps"></a>Passos seguintes
- [Configurar um projeto do Azure através de várias configurações de serviço](vs-azure-tools-multiple-services-project-configurations.md)

