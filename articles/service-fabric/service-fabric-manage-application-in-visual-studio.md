---
title: "Gerir as suas aplicações no Visual Studio | Microsoft Docs"
description: "Utilize o Visual Studio para criar, desenvolver, o pacote, implementar e depurar os seus serviços e aplicações de Service Fabric."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: timlt
editor: 
ms.assetid: c317cb7e-7eae-466e-ba41-6aa2518be5cf
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/07/2017
ms.author: mikkelhegn
ms.openlocfilehash: 81814ef07cfab06855b3bfbf0eb6ef51b8dfce4f
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Utilizar o Visual Studio para simplificar a escrever e gestão das suas aplicações de Service Fabric
Pode gerir as suas aplicações do Azure Service Fabric e serviços através do Visual Studio. Assim que tiver [configurar o ambiente de desenvolvimento](service-fabric-get-started.md), pode utilizar o Visual Studio para criar aplicações de Service Fabric, adicionar serviços, ou o pacote, registar e implementar aplicações no seu cluster de desenvolvimento local.

## <a name="deploy-your-service-fabric-application"></a>Implementar a aplicação de Service Fabric
Por predefinição, a implementar uma aplicação combina os seguintes passos para uma operação simple:

1. Criar o pacote de aplicações
2. Carregar o pacote de aplicação para o arquivo de imagens
3. Registar o tipo de aplicação
4. Remover quaisquer instâncias de aplicações em execução
5. Criar uma instância de aplicação

No Visual Studio, premindo **F5** implementa a aplicação e anexe o depurador para todas as instâncias da aplicação. Pode utilizar **Ctrl + F5** para implementar uma aplicação sem depuração, ou pode publicar um cluster local ou remoto, utilizando o perfil de publicação. 

### <a name="application-debug-mode"></a>Modo de depuração de aplicações
Visual Studio fornecem uma propriedade denominada **modo de depuração da aplicação**, que controla a forma como pretende estúdios Visual para lidar com a implementação de aplicações como parte de depuração.

#### <a name="to-set-the-application-debug-mode-property"></a>Para definir a propriedade de modo de depuração de aplicações
1. No Service Fabric aplicação do projeto (*.sfproj) menu de atalho, escolha **propriedades** (ou prima o **F4** chave).
2. No **propriedades** janela, defina o **modo de depuração da aplicação** propriedade.

![Definir a propriedade de modo de depuração de aplicações][debugmodeproperty]

#### <a name="application-debug-modes"></a>Modos de depuração de aplicações

1. **Atualizar aplicação** este modo permite-lhe alterar e depurar o seu código e suporta a edição de ficheiros estáticos web durante a depuração rapidamente. Este modo só funciona se o cluster de desenvolvimento local está a ser [1 nó modo](/service-fabric-get-started-with-a-local-cluster.md#one-node-and-five-node-cluster-mode).
2. **Remover aplicação** faz com que a aplicação a serem removidos quando termina a sessão de depuração.
3. **Auto atualização** a aplicação continua a ser executado quando termina a sessão de depuração. A próxima sessão de depuração irão tratar a implementação como uma atualização. O processo de atualização preserva todos os dados que introduziu numa sessão de depuração anterior.
4. **Manter aplicações** a aplicação mantém em execução no cluster quando termina a sessão de depuração. No início de sessão de depuração seguinte, a aplicação será removida.

Para **automática atualizar** os dados são preservados aplicando as capacidades de atualização da aplicação de Service Fabric. Para obter mais informações sobre como atualizar aplicações e como pode executar uma atualização num ambiente real, consulte [atualização da aplicação de Service Fabric](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Adicionar um serviço a sua aplicação de Service Fabric
Pode adicionar novos serviços à sua aplicação para expandir a sua funcionalidade.  Para garantir que o serviço está incluído no seu pacote de aplicação, adicione o serviço através de **novo serviço de recursos de infraestrutura...**  item de menu.

![Adicionar um novo serviço de Service Fabric][newservice]

Selecione um tipo de projeto de Service Fabric para adicionar à sua aplicação e especifique um nome para o serviço.  Consulte [escolher uma estrutura para o seu serviço](service-fabric-choose-framework.md) para ajudar a decidir o tipo de serviço a utilizar.

![Selecione um tipo de projeto de serviço do Service Fabric para adicionar à sua aplicação][addserviceproject]

O novo serviço é adicionado à sua solução e o pacote de aplicação existente. As referências de serviço e uma instância de serviço predefinido serão adicionados ao manifesto da aplicação, fazendo com que o serviço ser criado e iniciado da próxima vez que a aplicação é implementada.

![O novo serviço é adicionado ao manifesto da aplicação][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Pacote da aplicação de Service Fabric
Para implementar a aplicação e os respetivos serviços para um cluster, terá de criar um pacote de aplicação.  O pacote organiza o manifesto da aplicação, manifestos de serviço e outros ficheiros necessários num esquema específico.  Visual Studio configura e gere o pacote na pasta do projeto de aplicação, no diretório 'pkg'.  Ao clicar em **pacote** do **aplicação** menu de contexto cria ou atualiza o pacote de aplicações.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Remover aplicações e tipos de aplicação utilizando o Explorador de nuvem
Pode efetuar operações de gestão de cluster básico a partir do Visual Studio através do Explorador de nuvem, o que pode iniciar a partir de **vista** menu. Por exemplo, pode eliminar as aplicações e anular o aprovisionamento de tipos de aplicações em clusters locais ou remotos.

![Remover uma aplicação][removeapplication]

> [!TIP]
> Para mais rica funcionalidades de gestão de cluster, consulte [visualizar o cluster com o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Passos seguintes
* [Modelo de aplicação de Service Fabric](service-fabric-application-model.md)
* [Implementação de aplicação de Service Fabric](service-fabric-deploy-remove-applications.md)
* [Gerir aplicações parâmetros para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md)
* [Depurar a aplicação de Service Fabric](service-fabric-debugging-your-application.md)
* [Visualizar o cluster utilizando o Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png