---
title: "Configurar a integração contínua para micro-serviços do Azure | Microsoft Docs"
description: "Obter uma descrição geral de como configurar a integração contínua e a implementação para uma aplicação de Service Fabric, utilizando os serviços de equipa do Visual Studio (VSTS)."
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 3e8c2290-9e7a-456a-9b2c-db44d1b3988d
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2016
ms.author: ryanwi
ms.openlocfilehash: d73120018fa02a64c2a895a93b43661fccd4e5aa
ms.sourcegitcommit: 659cc0ace5d3b996e7e8608cfa4991dcac3ea129
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="set-up-service-fabric-continuous-integration-and-deployment-with-visual-studio-team-services"></a>Configurar a integração contínua de recursos de infraestrutura de serviço e a implementação com o Visual Studio Team Services
Este artigo descreve os passos para configurar a integração contínua e a implementação de uma aplicação de Service Fabric do Azure, utilizando os serviços de equipa do Visual Studio (VSTS).

Este documento reflete o procedimento atual e esperado para alterar o ao longo do tempo.

## <a name="prerequisites"></a>Pré-requisitos
Para começar, siga estes passos:

1. Certifique-se de que tem acesso a uma conta de serviços da equipa ou [criar um](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) por si.
2. Certifique-se de que tem acesso a um projeto de equipa de serviços da equipa ou [criar um](https://www.visualstudio.com/docs/setup-admin/create-team-project) por si.
3. Certifique-se de que tem um cluster do Service Fabric para a qual pode implementar a aplicação ou criar utilizando um o [portal do Azure](service-fabric-cluster-creation-via-portal.md), um [modelo Azure Resource Manager](service-fabric-cluster-creation-via-arm.md), ou [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).
4. Certifique-se de que já criou um projeto de aplicação de recursos de infraestrutura de serviço (.sfproj). Tem de ter um projeto que foi criado ou atualizado com o Service Fabric SDK 2.1 ou superior (o ficheiro .sfproj deve conter um valor de propriedade ProjectVersion de 1.1 ou superior).

> [!NOTE]
> Agentes de compilação personalizado já não são necessários. Equipa de serviços alojados agentes agora vêm pré-instaladas com software de gestão de cluster do Service Fabric, permitindo a implementação das suas aplicações diretamente a partir desses agentes.
> 
> 

## <a name="configure-and-share-your-source-files"></a>Configurar e partilhe os ficheiros de origem
A primeira coisa que pretende efetuar é preparar um perfil de publicação para utilização pelo processo de implementação que é executada num Team Services.  O perfil de publicação deve ser configurado para o cluster que já preparou anteriormente de destino:

1. Escolha um perfil de publicação dentro do seu projeto de aplicação que pretende utilizar para o fluxo de trabalho de integração contínua. Siga o [publicar instruções](service-fabric-publish-app-remote-cluster.md) sobre como publicar uma aplicação para um cluster remoto. Não, na verdade, terá de publicar a aplicação apesar. Pode clicar no **guardar** hyperlink na caixa de diálogo Publicar depois de configurar devidamente coisas.
2. Se pretender que a aplicação a ser atualizado para cada implementação que ocorre dentro do Team Services, em que pretende configurar o perfil de publicação para ativar a atualização. No mesmo publicar caixa de diálogo utilizada no passo 1, certifique-se de que o **atualizar a aplicação** caixa de verificação está selecionada.  Saiba mais sobre [configurar definições de atualização adicionais](service-fabric-visualstudio-configure-upgrade.md). Clique em de **guardar** hiperligação para guardar as definições para o perfil de publicação.
3. Certifique-se de que tenha as alterações foram guardadas para o perfil de publicação e cancelar a caixa de diálogo Publicar.
4. Agora está na altura de [partilhar os ficheiros de origem do projeto de aplicação](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services#vs) com a equipa de serviços. Depois dos ficheiros de origem estão acessíveis no Team Services, agora pode mover para o passo seguinte de gerar compilações. 

## <a name="create-a-build-definition"></a>Criar uma definição de compilação
Uma definição de compilação Team Services descreve um fluxo de trabalho é composto por um conjunto de passos de compilação que são executados sequencialmente. O objetivo da definição de compilação que está a criar é produzir um pacote de aplicação de Service Fabric e outros objetos, que podem ser utilizados para implementar a aplicação. Saiba mais sobre os serviços da equipa [definições de compilação](https://www.visualstudio.com/docs/build/define/create).

### <a name="create-a-definition-from-the-build-template"></a>Criar uma definição do modelo de compilação
1. Abra o projeto de equipa no Visual Studio Team Services.
2. Selecione o **criar** separador.
3. Selecione a verde  **+**  iniciar sessão para criar uma nova definição de compilação.
4. Na caixa de diálogo que se abre, selecione **aplicação do Azure Service Fabric** dentro de **criar** categoria de modelo.
5. Selecione **seguinte**.
6. Selecione o repositório e ramo associada com a sua aplicação de Service Fabric.
7. Seleccione a fila de agente que pretende utilizar. Agentes alojados são suportados.
8. Selecione **Criar**.
9. Guardar a definição de compilação e forneça um nome.
10. O parágrafo seguinte é uma descrição dos passos de compilação gerado pelo modelo:

| Passo de compilação | Descrição |
| --- | --- |
| Restauro do NuGet |Restaura os pacotes de NuGet da solução. |
| Compilar solução \*. sln |Baseia-se a solução completa. |
| Compilar solução \*.sfproj |Gera o pacote de aplicação de Service Fabric que é utilizado para implementar a aplicação. A localização do pacote de aplicação é especificada para ser dentro do diretório de artefacto a compilação. |
| Atualizar versões de aplicação do Service Fabric |Atualiza os valores de versão contidos nos ficheiros de manifesto do pacote de aplicação para permitir o suporte de atualização. Consulte o [página de documentação de tarefas](https://go.microsoft.com/fwlink/?LinkId=820529) para obter mais informações. |
| Copiar ficheiros |Copia os ficheiros de parâmetros de perfil e a aplicação da publicação para artefactos a compilação para ser utilizada para a implementação. |
| Publicar artefactos |Publica artefactos a compilação. Permite que uma definição de versão consumir artefactos a compilação. |

### <a name="verify-the-default-set-of-tasks"></a>Verifique se o conjunto predefinido de tarefas
1. Certifique-se a **solução** campo de entrada a **NuGet restauro** e **construir solução** criar passos.  Por predefinição, estes passos de compilação executar após todos os ficheiros de solução que estão contidos no repositório de associado.  Se pretender apenas a definição de compilação para funcionar desses ficheiros de solução, tem de atualizar explicitamente o caminho para esse ficheiro.
2. Certifique-se a **solução** campo de entrada a **empacotar a aplicação** passo de compilação.  Por predefinição, este passo de compilação assume que apenas um projeto de aplicação de recursos de infraestrutura de serviço (.sfproj) existe no repositório.  Se tiver vários esses ficheiros no seu repositório e destino apenas um para esta definição de compilação, terá de atualizar explicitamente o caminho para esse ficheiro.  Se pretender vários projetos de aplicação no seu repositório do pacote, terá de criar adicionais **compilação do Visual Studio** os passos na definição de compilação que cada um projeto de aplicação de destino.  Em seguida, também terá de atualizar o **argumentos de MSBuild** campo para cada um desses passos de compilação, para que a localização do pacote é exclusiva para cada um deles.
3. Verifique se o comportamento de controlo de versões definido no **versões de aplicação de recursos de infraestrutura de serviço de atualização** passo de compilação.  Por predefinição, este passo de compilação acrescenta o número de compilação para todos os valores da versão nos ficheiros de manifesto do pacote de aplicação. Consulte o [página de documentação de tarefas](https://go.microsoft.com/fwlink/?LinkId=820529) para obter mais informações. Isto é útil para suportar a atualização da sua aplicação, uma vez que cada implementação de atualização necessita de valores de versão diferente da implementação anterior. Se não estiver a pretender utilizar a atualização da aplicação no seu fluxo de trabalho, pode considerar desativar este passo de compilação. Tem de ser desativada se a sua intenção é para produzir uma compilação que pode ser utilizada para substituir uma aplicação de Service Fabric existente. A implementação falha se a versão da aplicação produzida pela compilação do não corresponde à versão da aplicação no cluster.
4. Se a solução contiver um projeto de .NET Core, certifique-se de que a definição da compilação contiver um passo de compilação que restaura as dependências:
   1. Selecione **passo de compilação de adicionar...** .
   2. Localize o **da linha de comandos** no separador do utilitário de tarefas e clique o botão Adicionar.
   3. Para campos de entrada da tarefa, utilize os seguintes valores:
   4. Ferramenta: dotnet
   5. Argumentos: restaurar
   6. Arraste a tarefa para que seja imediatamente após o **NuGet restauro** passo.
5. Guarde as alterações efetuadas a definição de compilação.

### <a name="try-it"></a>Experimente
Selecione **criar fila** para iniciar manualmente a compilação. Baseia-se também acionadores na emissão ou entrada. Depois de verificar que a compilação está a executar com êxito, pode agora passar para definir uma definição de versão que implementa a aplicação para um cluster.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Uma definição de versão Team Services descreve um fluxo de trabalho é composto por um conjunto de tarefas que são executados sequencialmente. O objetivo da definição de versão que está a criar é a utilizar um pacote de aplicação e implementá-la para um cluster. Quando utilizado em conjunto, a definição de compilação e a definição de versão podem executar o fluxo de trabalho completo de iniciar com os ficheiros de origem que termina com uma aplicação em execução no seu cluster. Saiba mais sobre os serviços da equipa [versão definições](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-definition-from-the-release-template"></a>Criar uma definição a partir do modelo da versão
1. Abra o projeto no Visual Studio Team Services.
2. Selecione o **versão** separador.
3. Selecione a verde  **+**  iniciar sessão para criar uma nova definição de versão e selecione **criar versão definição** no menu.
4. Na caixa de diálogo que se abre, selecione **implementação de recursos de infraestrutura do serviço de Azure** dentro de **implementação** categoria de modelo.
5. Selecione **seguinte**.
6. Selecione a definição de compilação que pretende utilizar como origem desta definição de versão.  A definição de versão referencia os artefactos foram produzidos pela definição do compilação selecionado.
7. Verifique o **a implementação contínua** caixa de verificação se pretender ter Team Services automaticamente criar uma nova versão e implementar a aplicação de Service Fabric sempre que uma compilação for concluída.
8. Seleccione a fila de agente que pretende utilizar. Agentes alojados são suportados.
9. Selecione **Criar**.
10. Edite o nome da definição clicando no ícone de lápis no topo da página.
11. Selecione o cluster para o qual a aplicação deve ser implementada do **ligação de Cluster** campo entrada da tarefa. A ligação de cluster fornece as informações necessárias, que permite que a tarefa de implementação ligar ao cluster. Se ainda não tiver uma ligação de cluster para o cluster, selecione o **gerir** hiperligação junto ao campo para adicionar um. Na página que abre, execute os seguintes passos:
    1. Selecione **ponto final de serviço novo** e, em seguida, selecione **Azure Service Fabric** no menu.
    2. Selecione o tipo de autenticação a ser utilizado pelo cluster visado por este ponto final.
    3. Defina um nome para a sua ligação no **nome da ligação** campo.  Normalmente, utilizará o nome do cluster.
    4. Definir o URL de ponto final de ligação de cliente no **ponto final de Cluster** campo.  Exemplo: tcp://contoso.westus.cloudapp.azure.com:19000.
    5. As credenciais do Azure Active Directory, defina as credenciais que pretende utilizar para ligar ao cluster o **Username** e **palavra-passe** campos.
    6. Para a autenticação baseada em certificado, defina a codificação Base64 do ficheiro de certificado de cliente no **certificado de cliente** campo.  Consulte a ajuda de pop-up sobre esse campo para informações sobre como obter esse valor.  Se o certificado é protegido por palavra-passe, defina a palavra-passe a **palavra-passe** campo.
    7. Confirmar as alterações ao clicar em **OK**. Depois de navegar para a definição da versão, clique no ícone de atualização no **ligação de Cluster** campo para ver o ponto final que acabou de adicionar.
12. Guarde a definição de versão.

> [!NOTE]
> Accounts da Microsoft (por exemplo, @hotmail.com ou @outlook.com) não são suportadas com a autenticação do Azure Active Directory.
> 
> 

A definição que é criada é composta por uma tarefa do tipo **implementação de aplicação do serviço de recursos de infraestrutura**. Consulte o [página de documentação de tarefas](https://go.microsoft.com/fwlink/?LinkId=820528) para obter mais informações sobre esta tarefa.

### <a name="verify-the-template-defaults"></a>Certifique-se as predefinições de modelo
1. Certifique-se a **publicar perfil** campo de entrada a **implementar aplicações de recursos de infraestrutura de serviço** tarefas. Por predefinição, este campo faz referência a um perfil de publicação com o nome Cloud.xml contidas no artefactos a compilação. Se pretender fazer referência a um perfil de publicação diferente ou se a compilação contém vários pacotes de aplicações os artefactos, terá de atualizar o caminho corretamente.
2. Certifique-se a **pacote de aplicação** campo de entrada a **implementar aplicações de recursos de infraestrutura de serviço** tarefas. Por predefinição, este campo referencia o caminho de pacote de aplicação predefinido utilizado no modelo de definição de compilação.  Se tiver modificado o caminho do pacote de aplicação predefinido na definição de compilação, terá de atualizar, bem como o caminho corretamente aqui.

### <a name="try-it"></a>Experimente
Selecione **criar versão** do **versão** menu do botão para criar manualmente uma versão. Na caixa de diálogo que se abre, selecione a compilação que pretende base a versão em e, em seguida, clique em **criar**. Se ativou a implementação contínua, versões serão criadas automaticamente quando a definição de compilação associado concluir uma compilação.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre a integração contínua com aplicações de Service Fabric, leia os artigos seguintes:

* [Equipa de documentação dos serviços de raiz](https://www.visualstudio.com/docs/overview)
* [Criar gestão nos serviços de equipa](https://www.visualstudio.com/docs/build/overview)
* [Gestão de versões nos serviços de equipa](https://www.visualstudio.com/docs/release/overview)

