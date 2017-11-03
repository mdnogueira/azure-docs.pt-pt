---
title: "Inicie sessão no Azure a partir da CLI do | Microsoft Docs"
description: "Ligar à sua subscrição do Azure da linha de comandos Interface do Azure (CLI do Azure) para Mac, Linux e Windows"
editor: tysonn
manager: timlt
documentationcenter: 
author: squillace
services: virtual-machines-linux,virtual-network,storage,azure-resource-manager
tags: azure-resource-manager,azure-service-management
ms.assetid: ed856527-d75e-4e16-93fb-253dafad209d
ms.service: multiple
ms.workload: multiple
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 10/04/2016
ms.author: rasquill
"\"/": 
ms.openlocfilehash: 31efab60690b54faf7992251fcd01e307c4464f2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-in-to-azure-from-the-azure-cli"></a>Inicie sessão no Azure a partir da CLI do Azure
A CLI do Azure é um conjunto de comandos de open source, várias plataformas para trabalhar com recursos do Azure. Este artigo descreve as diferentes formas de fornecer as credenciais de conta do Azure para ligar a CLI do Azure a sua subscrição do Azure:

* Execute o `azure login` comando da CLI para autenticar através do Azure Active Directory. Este método dá-lhe acesso aos comandos da CLI no [comando modos](#cli-command-modes). Quando executar o comando sem opções adicionais, `azure login` pede-lhe para continuar a iniciar sessão interativamente através de um portal web. Para adicionais `azure login` opções de comando, consulte os cenários neste artigo, tipo ou `azure login --help`.
* Se só precisa de utilizar comandos da CLI de modo de gestão de serviço do Azure (não recomendados para a maioria das implementações novas), pode transferir e instalar um ficheiro de definições de publicar no seu computador.

Se ainda não instalou o CLI, consulte [instalar a CLI do Azure](cli-install-nodejs.md). Se não tiver uma subscrição do Azure, pode criar uma [conta gratuita](http://azure.microsoft.com/free/) em apenas alguns minutos.

Para fundo sobre as identidades de conta diferente e subscrições do Azure, consulte [subscrições do Azure como estão associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md).

## <a name="scenario-1-azure-login-with-interactive-login"></a>Cenário 1: início de sessão do azure com início de sessão interativo
Com determinadas contas, a CLI requer a execução `azure login` e, em seguida, continuar o processo de início de sessão com um browser através de um portal web, um processo denominado *início de sessão interativo*. Um motivo comum é quando tiver uma conta escolar ou profissional (também designado por um *conta institucional*) que está configurado para exigir a autenticação multifator. Utilize também o início de sessão interativo com a sua conta Microsoft, quando pretender utilizar comandos do Gestor de recursos de modo.

Início de sessão interativo é fácil: tipo `azure login` – sem quaisquer opções – conforme mostrado no exemplo seguinte:

```
azure login
```                                                                                             

É apresentado o resultado semelhante ao seguinte o seguinte:

```         
info:    Executing command login
info:    To sign in, use a web browser to open the page http://aka.ms/devicelogin. Enter the code XXXXXXXXX to authenticate.
```
Copie o código oferecido para si no resultado do comando e abra um browser para http://aka.ms/devicelogin ou outra página se for especificado. (Pode abrir um browser no mesmo computador ou num computador diferente ou dispositivo). Introduza o código e, em seguida, é-lhe pedido que introduza o nome de utilizador e palavra-passe para a identidade que pretende utilizar. Quando tiver concluído o processo, a shell de comandos conclui o início de sessão. Poderá ser algo semelhante:

    info:    Added subscription Visual Studio Ultimate with MSDN
    info:    Added subscription Azure Free Trial
    info:    Setting subscription "Visual Studio Ultimate with MSDN" as default
    +
    info:    login command OK

> [!NOTE]
> Com início de sessão interativo, autenticação e autorização são efetuadas utilizando o Azure Active Directory. Se utilizar uma identidade de conta Microsoft, o processo de início de sessão acede ao seu domínio predefinido do Azure Active Directory. (Se tiver sessão numa conta gratuita do Azure, Azure Active Directory criado automaticamente um domínio predefinido para a sua conta.)
>
>

## <a name="scenario-2-azure-login-with-a-username-and-password"></a>Cenário 2: início de sessão do azure com um nome de utilizador e palavra-passe
Utilize o `azure login` comando com o nome de utilizador (`-u`) parâmetro para autenticar quando pretender utilizar um trabalho ou escola conta que não requer a autenticação multifator. Lhe for pedido na linha de comandos para a palavra-passe (ou, opcionalmente, pode passar a palavra-passe como um parâmetro adicional a `azure login` comando). O exemplo seguinte transmite o nome de utilizador de uma conta organizacional:

    azure login -u myUserName@contoso.onmicrosoft.com

Em seguida, é-lhe pedido para introduzir a palavra-passe:

    info:    Executing command login
    Password: *********

Em seguida, concluir o processo de início de sessão.

    info:    Added subscription Visual Studio Ultimate with MSDN
    +
    info:    login command OK

Se este for o primeiro iniciar a tempo com estas credenciais, é-lhe pedido para confirmar que pretende colocar em cache um token de autenticação. Esta linha de comandos também ocorre se tiver utilizado anteriormente a `azure logout` comando (descrito mais à frente do artigo). Para ignorar esta linha de comandos para cenários de automatização, execute `azure login` com o `-q` parâmetro.

## <a name="scenario-3-azure-login-with-a-service-principal"></a>Cenário 3: início de sessão do azure com um principal de serviço
Se criar um principal de serviço para uma aplicação do Active Directory e o principal de serviço tem as permissões na sua subscrição, pode utilizar o `azure login` comando para se autenticar o principal de serviço. Dependendo do seu cenário, pode fornecer as credenciais do principal de serviço como parâmetros explícitos a `azure login` comando. Por exemplo, o seguinte comando transmite o nome principal do serviço e o ID de inquilino do Active Directory:

    azure login -u https://www.contoso.org/example --service-principal --tenant myTenantID

Em seguida, é-lhe pedido para fornecer a palavra-passe. Também pode fornecer as credenciais através de um código de aplicação ou script CLI ou utilizar um certificado para autenticar o principal de serviço, forma não interativa para cenários de automatização. Para obter detalhes e exemplos, consulte [autenticar um principal de serviço com o Azure Resource Manager](resource-group-authenticate-service-principal-cli.md).

## <a name="scenario-4-use-a-publish-settings-file"></a>Cenário 4: Utilizar um ficheiro de definições de publicação
Se só precisa de utilizar os comandos da CLI modo gestão de serviço do Azure (por exemplo, para implementar as VMs do Azure no modelo de implementação clássica), pode ligar através de um ficheiro de definições de publicação. Este método instala um certificado no computador local que permite-lhe efetuar tarefas de gestão, desde que a subscrição e o certificado são válidos.

* **Para transferir o ficheiro de definições de publicação** para a sua conta, certifique-se de que o CLI está no modo de gestão de serviço, escrevendo `azure config mode asm`. Em seguida, execute o seguinte comando:

        azure account download

Isto abre-se o browser predefinido e pede-lhe para iniciar sessão para o [portal clássico do Azure](https://manage.windowsazure.com). Depois de iniciar sessão, um `.publishsettings` transferências de ficheiros. Aponte a localização onde o ficheiro é guardado.

> [!NOTE]
> Se a sua conta está associada a vários inquilinos do Azure Active Directory, poderá ser-lhe pedido para selecionar que do Active Directory que pretende transferir um ficheiro de definições de publicação para.
>
>

Depois de selecionada utilizando a página de transferência ou através do portal clássico do Azure Active Directory selecionado torna-se predefinido utilizado pela página de portal e transferência clássica. Assim que tiver sido estabelecida uma predefinição, consulte o texto '**clique aqui para regressar à página de seleção de**' no topo da página de transferência. Utilize a ligação fornecida para regressar à página de seleção.

* **Para importar o ficheiro de definições de publicação**, execute o seguinte comando:

        azure account import <path to your .publishsettings file>

> [!IMPORTANT]
> Depois de importar as definições de publicação, deve eliminar o `.publishsettings` ficheiro. Este já não é necessária para a CLI do Azure e apresenta um risco de segurança porque foi utilizado para obter acesso à sua subscrição.
>
>

## <a name="cli-command-modes"></a>Modos de comandos da CLI
A CLI do Azure fornece dois modos de comando para trabalhar com recursos do Azure, com conjuntos de comando diferentes:

* **Modo Resource Manager** - para trabalhar com recursos do Azure no modelo de implementação Resource Manager. Para definir neste modo, execute `azure config mode arm`.
* **Modo de gestão de serviço** - para trabalhar com recursos do Azure no modelo de implementação clássica. Para definir neste modo, execute `azure config mode asm`.

Quando instalado pela primeira vez, a versão atual do CLI está no modo Resource Manager.

> [!NOTE]
> O modo Resource Manager e o modo de gestão de serviço são mutuamente exclusivos. Ou seja, recursos criados no modo de um não podem ser geridos a partir de outro modo.
>
>

## <a name="multiple-subscriptions"></a>Várias subscrições
Se tiver várias subscrições do Azure, ligar para o Azure concede acesso a todas as subscrições associadas as suas credenciais. Uma subscrição é selecionada por predefinição e utilizada pela CLI do Azure, quando executar operações. Pode ver as subscrições, incluindo a subscrição predefinida atual, utilizando o `azure account list` comando. Este comando devolve informações semelhante ao seguinte:

    info:    Executing command account list
    data:    Name              Id                                    Current
    data:    ----------------  ------------------------------------  -------
    data:    Azure-sub-1       ####################################  true
    data:    Azure-sub-2       ####################################  false

Na lista anterior, o **atual** coluna indica a subscrição predefinida atual como sub-Azure-1. Para alterar a subscrição predefinida, utilize o `azure account set` de comandos e especificar a subscrição que pretende ser a predefinição. Por exemplo:

    azure account set Azure-sub-2

A subscrição predefinida é alterado para sub-Azure-2.

> [!NOTE]
> Alterar a subscrição predefinida entra em vigor imediatamente e é uma alteração global; novos comandos da CLI do Azure, se executar a mesma instância de linha de comandos ou uma instância diferente, utilizam a nova subscrição de predefinição.
>
>

Se pretender utilizar uma subscrição predefinida não com a CLI do Azure, mas não pretender alterar a predefinição atual, pode utilizar o `--subscription` opção para o comando e forneça o nome da subscrição que pretende utilizar para a operação.

Assim que estiver ligado à sua subscrição do Azure, pode começar a utilizar os comandos da CLI do Azure para trabalhar com recursos do Azure.

## <a name="storage-of-cli-settings"></a>Armazenamento das definições da CLI
Se iniciar sessão com a `azure login` comando ou importar definições de publicação, o perfil do CLI e os registos são armazenados num `.azure` diretório localizado na sua `user` diretório. O `user` diretório é protegido pelo seu sistema operativo. No entanto, recomendamos que efetuar passos adicionais para encriptar o seu `user` diretório. Pode fazê-lo das seguintes formas:

* No Windows, modifique as propriedades de diretório ou utilizar o BitLocker.
* No Mac, ative FileVault para o diretório.
* No Ubuntu, utilize a funcionalidade de diretório Encrypted Home. Outras as distribuições do Linux oferecem funcionalidades semelhantes.

## <a name="logging-out"></a>Registo de
Para terminar sessão, utilize o seguinte comando:

    azure logout -u <username>

Se as subscrições associadas à conta apenas são autenticadas com o Active Directory, a terminar elimina as informações de subscrição do perfil local. No entanto, se um ficheiro de definições de publicação também foi importado para as subscrições, terminada a sessão só eliminações do Active Directory relacionadas com informações do perfil local.

## <a name="next-steps"></a>Passos seguintes
* Para utilizar comandos da CLI do Azure, consulte [comandos da CLI do Azure no modo Resource Manager](virtual-machines/azure-cli-arm-commands.md) e [comandos da CLI do Azure no modo de gestão do serviço](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).
* Para obter mais informações sobre a CLI do Azure, código de origem da transferência, problemas de relatório, ou a contribuir para o projeto, visite o [repositório do GitHub para a CLI do Azure](https://github.com/azure/azure-xplat-cli).
* Se tiver problemas com a CLI do Azure ou o Azure, visite o [fóruns do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurescripting).
