---
title: "Adicionar Mobile Services utilizando os serviços ligados no Visual Studio | Microsoft Docs"
description: "Adicionar Mobile Services utilizando a caixa de diálogo do Visual Studio adicionar ligado serviços"
services: visual-studio-online
documentationcenter: na
author: mlhoop
manager: douge
editor: 
ms.assetid: 75c3cb93-88e1-476d-a416-f34caa3608e3
ms.service: visual-studio-online
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: mobile
ms.date: 12/16/2015
ms.author: mlearned
ms.openlocfilehash: d185fdafebad56f8970e390b2a0672c3fb84df8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="adding-mobile-services-by-using-visual-studio-connected-services"></a>Adicionar Mobile Services com o Visual Studio ligado Services
Com o Visual Studio 2015, pode ligar ao Mobile Services do Azure utilizando o **adicionar o serviço ligado** caixa de diálogo. Pode ligar a partir de qualquer aplicação de cliente do c#, qualquer aplicação JavaScript ou aplicação Cordova de plataforma. Depois de estabelecer a ligação, pode criar e aceder aos dados, criar APIs personalizadas e tarefas agendadas ou adicionar suporte para notificações push.  A operação de serviços ligados adiciona todas as referências adequadas e o código de ligação. Também pode tirar partido de suporte incorporado para a autenticação com uma variedade de esquemas de identidade popular, como o Azure AD, Facebook, Twitter e Accounts Microsoft.

## <a name="supported-project-types"></a>Tipos de projeto suportados
> [!NOTE]
> No Visual Studio 2015, adicionar Mobile Services do Azure para projetos de Windows Universal (Windows 10) utilizando a caixa de diálogo Adicionar serviços ligados não é suportada. Pode adicionar Mobile Services do Azure, instalando os pacotes adequados utilizando o Gestor de pacotes NuGet para o seu projeto.
> 
> 

Pode utilizar a caixa de diálogo Serviços ligados para ligar ao Mobile Services do Azure nos seguintes tipos de projeto.

* Projetos do arquivo do .NET Windows 8.1, telefone e aplicação Universal
* Projetos do arquivo de JavaScript Windows 8.1, telefone e aplicação Universal
* Projetos criados com o Visual Studio Tools para Apache Cordova

## <a name="connect-to-azure-mobile-services-using-the-add-connected-services-dialog"></a>Ligar ao Azure Mobile Services utilizando a caixa de diálogo Adicionar serviços ligados
1. Certifique-se de que tem uma conta do Azure. Se não tiver uma conta do Azure, pode inscrever-se para obter uma [avaliação gratuita](http://go.microsoft.com/fwlink/?LinkId=518146).
2. Abra o **adicionar serviços ligados** caixa de diálogo.
   
   * Para aplicações de .NET, abra o projeto no Visual Studio, abra o menu de contexto para a **referências** no Explorador de soluções, nó e, em seguida, escolha **adicionar o serviço ligado**
     
        ![A ligar ao serviço móvel do Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)
   * Para projetos de aplicação Apache Cordova, abra o projeto no Visual Studio, abra o menu de contexto para o nó do projeto no Explorador de soluções e, em seguida, escolha **adicionar o serviço ligado**.
3. No **adicionar o serviço ligado** diálogo caixa, escolha **Mobile Services do Azure**e, em seguida, escolha o **configurar** botão. Poderá ser-lhe pedido para iniciar sessão no Azure se ainda não o tiver feito.
   
    ![Adicionar um serviço móvel do Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)
4. No **Mobile Services do Azure** diálogo caixa, escolha um serviço móvel, se tiver uma. Se precisar de criar um novo serviço móvel do Azure, siga o procedimento abaixo para fazê-lo. Caso contrário, avance para o passo seguinte.
   
    Para criar uma nova conta de serviço móvel:
   
   1. Escolha o * * criar serviço * * ligação na parte inferior da caixa de diálogo.
       ![Adicionar novo serviço ligado móvel](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)
   2. No **criar o serviço móvel** caixa de diálogo, pode escolher um serviço móvel de back-end de JavaScript ou um serviço móvel de back-end de .NET do **Runtime** na lista pendente. 
      
       ![Criar um serviço móvel](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)
      
       Um serviço de back-end de JavaScript é simples e eficiente. Se criar um serviço móvel de back-end de JavaScript, o código JavaScript do lado do servidor é armazenado na nuvem, mas pode editar os scripts de servidor utilizando o Explorador de servidores ou o portal de gestão do Azure. 
      
       Um serviço móvel de back-end do .NET dá-lhe a capacidade total e a flexibilidade de Web API e do Entity Framework. Se criar um serviço móvel de back-end de .NET, um projeto é criado e adicionado à sua solução. 
   3. Escolha o **região** onde pretende que o serviço móvel e, em seguida, introduza um nome de utilizador e palavra-passe para o servidor.
   4. Após introduzir as informações necessárias, escolha o **criar** botão para criar o serviço móvel.
   5. O novo serviço móvel deve aparecer na lista de serviço no **Mobile Services do Azure** caixa de diálogo. Escolha o novo serviço móvel na lista e, em seguida, escolha o **adicionar** botão para adicionar o serviço ao seu projeto.
5. Reveja a página de introdução ao obter que aparece e saber como o projeto foi modificado. É apresentada uma página de introdução no seu browser sempre que adicionar um serviço ligado. Pode consultar os passos sugeridos e exemplos de código, ou mudar para a página que aconteceu para ver as referências adicionadas ao seu projeto e, como os ficheiros de configuração e de código foram modificados.
6. Utilizar os exemplos de código como um guia, começar a escrever código para aceder ao seu serviço móvel!

## <a name="how-your-project-is-modified"></a>Como o projeto é modificado
Como o Visual Studio modifica o projeto depende do tipo de projeto. Para c# aplicações de cliente, consulte [o que aconteceu – c# projetos](http://go.microsoft.com/fwlink/p/?LinkId=513119). Para aplicações de cliente JavaScript, consulte [o que aconteceu – projetos de JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Para aplicações Cordova, consulte [o que aconteceu – projetos Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).

## <a name="next-steps"></a>Passos seguintes
Colocar perguntas e obter ajuda: 

* [Fórum MSDN: Mobile Services do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)
* [Mobile Services do Azure no blogue de equipa do Microsoft Azure](https://azure.microsoft.com/blog/topics/mobile/)
* [Mobile Services do Azure em azure.microsoft.com](https://azure.microsoft.com/services/mobile-services/)
* [Documentação de Mobile Services do Azure em azure.microsoft.com](https://azure.microsoft.com/documentation/services/mobile-services/)

