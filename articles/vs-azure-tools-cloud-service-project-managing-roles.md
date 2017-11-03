---
title: "Gestão de funções nos serviços em nuvem do Azure com o Visual Studio | Microsoft Docs"
description: "Saiba como adicionar e remover funções no cloud services do Azure com o Visual Studio."
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 5ec9ae2e-8579-4e5d-999e-8ae05b629bd1
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 03/21/2017
ms.author: kraigb
ms.openlocfilehash: 6ed857b857cf8c14506ca39725c214a7fea4fc95
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="managing-roles-in-azure-cloud-services-with-visual-studio"></a>Gestão de funções nos serviços em nuvem do Azure com o Visual Studio
Depois de ter criado o seu serviço de nuvem do Azure, pode adicionar novas funções ao mesmo ou remova funções existentes a partir do mesmo. Também pode importar um projeto existente e convertê-la a uma função. Por exemplo, pode importar uma aplicação web ASP.NET e designá-la como uma função da web.

## <a name="adding-a-role-to-an-azure-cloud-service"></a>Adicionar uma função a um serviço em nuvem do Azure
Os seguintes passos guiá-lo através da adição de uma função web ou de trabalho a um projeto de serviço em nuvem do Azure no Visual Studio.

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, expanda o nó do projeto

1. Clique com botão direito do **funções** nó para apresentar o menu de contexto. No menu de contexto, selecione **adicionar**, em seguida, selecione uma função web existente ou a função de trabalho da solução atual, ou crie um projeto de função web ou de trabalho. Também pode selecionar um projeto adequado, como um projeto de aplicação web do ASP.NET e associá-la a um projeto de função.

    ![Opções de menu para adicionar uma função a um projeto de serviço em nuvem do Azure](media/vs-azure-tools-cloud-service-project-managing-roles/add-role.png)

## <a name="removing-a-role-from-an-azure-cloud-service"></a>Remover uma função de um serviço em nuvem do Azure
Os seguintes passos guiá-lo através da remoção de uma função web ou de trabalho de um projeto de serviço em nuvem do Azure no Visual Studio.

1. Criar ou abrir um projeto de serviço em nuvem do Azure no Visual Studio.

1. No **Explorador de soluções**, expanda o nó do projeto

1. Expanda o **funções** nós.

1. Clique com o botão direito no nó que pretende remover e, no menu de contexto, selecione **remover**. 

    ![Opções de menu para adicionar uma função a um serviço em nuvem do Azure](media/vs-azure-tools-cloud-service-project-managing-roles/remove-role.png)

## <a name="readding-a-role-to-an-azure-cloud-service-project"></a>Readding uma função a um projeto de serviço em nuvem do Azure
Se remover uma função de projeto de serviço em nuvem, mas optar posteriormente voltar a adicionar a função ao projeto, apenas a declaração de função e os atributos básicos, tais como pontos finais e informações de diagnóstico, são adicionados. Não existem recursos adicionais ou referências são adicionadas à `ServiceDefinition.csdef` ficheiro ou o `ServiceConfiguration.cscfg` ficheiro. Se pretender adicionar esta informação, terá de adicioná-lo manualmente nestes ficheiros.

Por exemplo, poderá remover uma função de serviço web e mais tarde decidir adicionar esta função novamente para a sua solução. Se o fizer, ocorre um erro. Para evitar este erro, terá de adicionar o `<LocalResources>` elemento mostrado na seguinte XML com o `ServiceDefinition.csdef` ficheiro. Utilize o nome da função de serviço web que adicionou no projeto como parte do atributo de nome para o  **<LocalStorage>**  elemento. Neste exemplo, o nome da função de serviço web é **WCFServiceWebRole1**.

    <WebRole name="WCFServiceWebRole1">
        <Sites>
          <Site name="Web">
            <Bindings>
              <Binding name="Endpoint1" endpointName="Endpoint1" />
            </Bindings>
          </Site>
        </Sites>
        <Endpoints>
          <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <Imports>
          <Import moduleName="Diagnostics" />
        </Imports>
       <LocalResources>
          <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
       </LocalResources>
    </WebRole>

## <a name="next-steps"></a>Passos seguintes
- [Configurar as funções para um serviço em nuvem do Azure com o Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md)
