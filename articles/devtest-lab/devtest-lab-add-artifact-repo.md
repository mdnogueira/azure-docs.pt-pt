---
title: "Adicione um repositório de Git para um laboratório no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como adicionar um repositório do GitHub ou Visual Studio o equipa dos serviços de Git para a origem de artefactos personalizados no Azure DevTest Labs."
services: devtest-lab,virtual-machines,visual-studio-online
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 01b459f7-eaf2-45a8-b4b5-2c0a821b33c8
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: tarcher
ms.openlocfilehash: e0fb8b3c991d3f95acef77b37929fb5f6dc06242
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-git-repository-to-store-custom-artifacts-and-resource-manager-templates"></a>Adicione um repositório de Git para armazenar artefactos personalizados e modelos do Resource Manager

Pode [criar artefactos personalizados](devtest-lab-artifact-author.md) para as VMs no laboratório, ou [utilizar modelos Azure Resource Manager para criar um ambiente de teste personalizado](devtest-lab-create-environment-from-arm.md). Tem de adicionar um repositório de Git privado para os artefactos ou modelos do Resource Manager, que cria a sua equipa. O repositório pode ser alojado num [GitHub](https://github.com) ou no [Visual Studio Team Services](https://visualstudio.com).

Que oferecemos uma [repositório do GitHub artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) que pode implementar como-é, ou pode personalizá-las para o seu laboratórios. Quando personalizar ou criar um artefacto, não é possível armazenar os artefactos no repositório de público. Tem de criar o seus próprios privado repositório de artefactos personalizados e de artefactos que criar. 

Quando cria uma VM, pode guardar o modelo do Resource Manager, personalize-se de que pretende e, em seguida, utilizá-lo mais tarde para criar mais VMs. Tem de criar o seus próprios repositório privado para armazenar os modelos de Gestor de recursos personalizados.  

* Para saber como criar um repositório do GitHub, consulte [GitHub Bootcamp](https://help.github.com/categories/bootcamp/).
* Para saber como criar um projeto de equipa de serviços que tenha um repositório de Git, consulte [ligar ao Visual Studio Team Services](https://www.visualstudio.com/get-started/setup/connect-to-visual-studio-online).

A figura seguinte é um exemplo de como um repositório de que tem os artefactos poderá ter o aspeto no GitHub:  

![Repositório do GitHub artefactos de exemplo](./media/devtest-lab-add-repo/devtestlab-github-artifact-repo-home.png)

## <a name="get-the-repository-information-and-credentials"></a>Obter as informações de repositório e credenciais
Adicione um repositório para o laboratório, em primeiro lugar, obter as informações da chave do seu repositório. As secções seguintes descrevem como obter as informações necessárias para repositórios que estão alojados no GitHub ou Visual Studio Team Services.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Obter o URL de clone de repositório do GitHub e o acesso pessoal token

1. Vá para a home page do repositório GitHub que contém o artefacto ou definições de modelo do Resource Manager.
2. Selecione **clonar ou transferir**.
3. Para copiar o URL para a área de transferência, selecione o **url de clone de HTTPS** botão. Guarde o URL para utilização posterior.
4. No canto superior direito do GitHub, selecione a imagem de perfil e, em seguida, selecione **definições**.
5. No **definições pessoais** menu à esquerda, selecione **tokens de acesso pessoal**.
6. Selecione **gerar novo token**.
7. No **novo token de acesso pessoal** página **Token Descrição**, introduza uma descrição. Aceitar os itens de predefinição em **selecione âmbitos**e, em seguida, selecione **gerar Token**.
8. Guarde o token gerado. Irá utilizar o token mais tarde.
9. Feche o GitHub.   
10. Avance para o [ligar o seu laboratório para o repositório](#connect-your-lab-to-the-repository) secção.

### <a name="get-the-visual-studio-team-services-repository-clone-url-and-personal-access-token"></a>Obter o URL de clone de repositório do Visual Studio Team Services e o acesso pessoal token

1. Vá para a home page da sua coleção de equipa (por exemplo, https://contoso-web-team.visualstudio.com) e, em seguida, selecione o seu projeto.
2. Na home page do projeto, selecione **código**.
3. Para ver o URL do clone, no projeto **código** página, selecione **Clone**.
4. Guarde o URL. Irá utilizar o URL mais tarde.
5. Para criar um token de acesso pessoal, no menu de lista pendente de conta de utilizador, selecione **meu perfil**.
6. Na página de informações de perfil, selecione **segurança**.
7. No **segurança** separador, selecione **adicionar**.
8. No **criar um token de acesso pessoal** página:
   1. Introduza um **Descrição** para o token.
   2. No **expira em** lista, selecione **180 dias**.
   3. No **contas** lista, selecione **todas as contas acessíveis**.
   4. Selecione o **todos os âmbitos** opção.
   5. Selecione **criar Token**.
9. O novo token aparece no **Tokens de acesso pessoal** lista. Selecione **cópia Token**e, em seguida, guarde o valor de token para utilização posterior.
10. Avance para o [ligar o seu laboratório para o repositório](#connect-your-lab-to-the-repository) secção.

## <a name="connect-your-lab-to-the-repository"></a>Ligar o seu laboratório para o repositório
1. Inicie sessão no [Portal do Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040).
2. Selecione **mais serviços**e, em seguida, selecione **DevTest Labs** da lista de serviços.
3. Na lista de laboratórios, selecione o laboratório. 
4. Selecione **políticas de configuração e** > **repositórios** > **+ adicionar**.

    ![O botão do repositório de adição](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na segunda **repositórios** página, especifique as seguintes informações:
  1. **Nome**. Introduza um nome para o repositório.
  2. **Url do Clone de Git**. Introduza o URL de clone de HTTPS de Git que copiou anteriormente do GitHub ou Visual Studio Team Services.
  3. **Ramo**. Para obter as definições, introduza o ramo.
  4. **Token de acesso pessoal**. Introduza o token de acesso pessoal que obteve anteriormente do GitHub ou Visual Studio Team Services.
  5. **Caminhos de pastas**. Introduza pelo menos um caminho de pasta relativo para o URL do clone que contém o artefacto ou definições de modelo do Resource Manager. Quando especificar um subdiretório, certifique-se de que inclui a barra no caminho da pasta.

     ![Área de repositórios](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Selecione **Guardar**.

### <a name="related-blog-posts"></a>Mensagens de blogue relacionados
* [Resolver problemas com falhas artefactos no DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Associar uma VM a um domínio do Active Directory existente, utilizando um modelo do Resource Manager no DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
Depois de ter criado o seu repositório de Git privado, pode utilizar um ou ambos os procedimentos seguintes, consoante as suas necessidades:
* Arquivo sua [artefactos personalizados](devtest-lab-artifact-author.md). Pode utilizá-los mais tarde para criar novas VMs.
* [Criar ambientes de várias VMS e PaaS recursos com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md). Em seguida, pode armazenar os modelos no seu repositório privado.

Quando cria uma VM, pode verificar que os modelos ou artefactos são adicionados para o repositório de Git. Estão imediatamente disponíveis na lista de artefactos ou modelos. O nome do seu repositório privado é apresentado na coluna que especifica a origem. 
