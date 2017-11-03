---
title: "Configurar o serviço de gestão de API utilizando o Git - Azure | Microsoft Docs"
description: "Saiba como guardar e configurar a configuração do serviço de API Management utilizando o Git."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: mattfarm
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 87d4e3fc4f30d5c7b147fb460fb43367aef19118
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Para guardar e configurar a configuração do serviço de API Management utilizando o Git
> 
> 

Cada instância de serviço de API Management mantém uma base de dados de configuração que contém informações sobre a configuração e metadados para a instância de serviço. É possível efetuar alterações à instância de serviço por alterando no portal do publicador, utilizando um cmdlet do PowerShell ou efetuar uma chamada de REST API. Para além destes métodos, também pode gerir a configuração de instância de serviço utilizando o Git, tais como a ativar cenários de gestão do serviço:

* Controlo de versões de configuração - transferir e armazenar versões diferentes da sua configuração de serviço
* Em massa alterações de configuração - efetuar alterações a várias partes da sua configuração de serviço no seu repositório local e integrar as alterações novamente para o servidor com uma única operação
* Toolchain familiar do Git e fluxo de trabalho - utilize as ferramentas do Git e fluxos de trabalho que já estiver familiarizado com o

O diagrama seguinte mostra uma descrição geral das diferentes formas para configurar a sua instância do serviço de API Management.

![Configurar o Git][api-management-git-configure]

Quando efetuar alterações ao seu serviço utilizando o portal do publicador, os cmdlets do PowerShell ou a API REST, que está a gerir a configuração de serviço da base de dados através de `https://{name}.management.azure-api.net` ponto final, conforme mostrado no lado direito do diagrama. O lado esquerdo do diagrama ilustra a forma como pode gerir a configuração de serviço utilizando o Git e repositório de Git para o seu serviço localizado em `https://{name}.scm.azure-api.net`.

Os passos seguintes fornecem uma descrição geral de gerir a sua instância de serviço de API Management utilizando o Git.

1. Aceder à configuração do Git no seu serviço
2. Guardar a sua base de dados de configuração de serviço para o seu repositório de Git
3. Clone o repositório de Git para o computador local
4. Extraia o repositório mais recente para baixo para o seu computador local e push e consolidar alterações de volta para o repositório
5. Implementar as alterações do seu repositório para a base de dados de configuração de serviço

Este artigo descreve como ativar e utilizar o Git para gerir a sua configuração de serviço e fornece uma referência para os ficheiros e pastas no repositório de Git.

## <a name="access-git-configuration-in-your-service"></a>Aceder à configuração do Git no seu serviço
Pode ver rapidamente o estado da sua configuração de Git visualizando o ícone do Git no canto superior direito do portal do publicador. Neste exemplo, a mensagem de estado indica que existem não guardadas as alterações ao repositório. Isto acontece porque a base de dados de configuração de serviço de API Management ainda não foram guardada no repositório.

![Estado de Git][api-management-git-icon-enable]

Para ver e configurar as definições de configuração do Git, pode clicar no ícone de Git, ou clique em de **segurança** menu e navegue para o **repositório de configuração** separador.

![Ativar o GIT][api-management-enable-git]

> [!IMPORTANT]
> Os segredos que não estão definidos como propriedades serão armazenadas no repositório e permanecerão no respetivo histórico até desativarem e reativar o acesso de Git. Propriedades de fornecem um local seguro para gerir os valores de cadeia constante, incluindo segredos, em todos os configuração da API e as políticas de, pelo que não tem armazená-los diretamente no seu declarações de política. Para obter mais informações, consulte [como utilizar propriedades nas políticas de API Management do Azure](api-management-howto-properties.md).
> 
> 

Para informações sobre como ativar ou desativar o acesso de Git utilizando a API REST, consulte [ativar ou desativar o acesso de Git utilizando a API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para guardar a configuração de serviço para o repositório de Git
É o primeiro passo antes da clonagem o repositório para guardar o estado atual da configuração do serviço para o repositório. Clique em **Guardar configuração para o repositório**.

![Guardar configuração][api-management-save-configuration]

Efetue as alterações pretendidas no ecrã de confirmação e clique em **Ok** para guardar.

![Guardar configuração][api-management-save-configuration-confirm]

Após alguns instantes a configuração é guardada e o estado de configuração do repositório é apresentado, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração do serviço e o repositório.

![Estado da configuração][api-management-configuration-status]

Depois da configuração é guardada no repositório, pode ser clonado.

Para informações sobre como efetuar esta operação utilizando a API REST, consulte [utilizando a API REST de instantâneos de configuração de consolidação](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Para clonar o repositório no seu computador local
Para clonar um repositório, terá do URL do seu repositório, um nome de utilizador e uma palavra-passe. O nome de utilizador e o URL serão apresentados junto ao topo do **repositório de configuração** separador.

![Clone de Git][api-management-configuration-git-clone]

A palavra-passe é gerada na parte inferior do **repositório de configuração** separador.

![Gerar palavra-passe][api-management-generate-password]

Para gerar uma palavra-passe, primeiro certifique-se de que o **expiração** está definido para a data de expiração pretendida e a hora e, em seguida, clique em **gerar Token**.

![Palavra-passe][api-management-password]

> [!IMPORTANT]
> Tome nota desta palavra-passe. Depois de sair desta página a palavra-passe não será apresentada novamente.
> 
> 

Os exemplos seguintes utilizam a ferramenta de Git Bash do [Git para Windows](http://www.git-scm.com/downloads) mas pode utilizar qualquer ferramenta de Git que está familiarizado com.

Abra a ferramenta de Git na pasta pretendida e execute o seguinte comando para clonar o repositório de git para o computador local, utilizando o comando fornecido pelo portal do publicador.

```
git clone https://bugbashdev4.scm.azure-api.net/
```

Forneça o nome de utilizador e palavra-passe quando lhe for pedido.

Se receber erros, tente modificar o `git clone` comando para incluir o nome de utilizador e palavra-passe, conforme mostrado no exemplo seguinte.

```
git clone https://username:password@bugbashdev4.scm.azure-api.net/
```

Se esta opção fornece um erro, tente a parte de palavra-passe do comando de codificação do URL. Uma forma rápida para efetuar este procedimento é abra o Visual Studio e emita o comando seguinte no **janela Immediate**. Para abrir o **janela Immediate**, abra qualquer solução ou o projeto no Visual Studio (ou crie uma nova aplicação de consola vazia) e escolha **Windows**, **Immediate** do **depurar** menu.

```
?System.NetWebUtility.UrlEncode("password from publisher portal")
```

Utilize a palavra-passe codificada juntamente com a localização de nome e o repositório de utilizador para construir os comandos de git.

```
git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/
```

Depois do repositório foi clonado podem visualizar e trabalhar com o mesmo no seu sistema de ficheiros local. Para obter mais informações, consulte [referência do repositório de Git local de estrutura de ficheiros e pastas](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar o seu repositório local com a configuração de instância mais recente do serviço
Se efetuar alterações à sua instância do serviço de gestão de API no portal do publicador ou utilizando a API REST, tem de guardar estas alterações para o repositório antes de poder atualizar o seu repositório local com as alterações mais recentes. Para tal, clique em **Guardar configuração para o repositório** no **repositório de configuração** separador no portal do publicador e, em seguida, emita o comando seguinte no seu repositório local.

```
git pull
```

Antes de executar `git pull` Certifique-se de que estão na pasta para o repositório local. Se acabou de concluir o `git clone` comando, em seguida, tem de alterar o diretório para o repositório ao executar um comando semelhante ao seguinte.

```
cd bugbashdev4.scm.azure-api.net/
```

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para enviar alterações do seu repositório local para o repositório de servidor
Para enviar alterações do seu repositório local para o repositório de servidor, tem de consolidar as alterações e, em seguida, emiti-las para o repositório de servidor. Ao consolidar as alterações, abra a ferramenta do comando de Git, mudar para o diretório do seu repositório local e emitir os seguintes comandos.

```
git add --all
git commit -m "Description of your changes"
```

Para enviar todas as consolidações para o servidor, execute o seguinte comando.

```
git push
```

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implementar alterações de configuração de serviço para a instância do serviço de API Management
Depois das suas alterações locais são aplicadas e enviadas para o repositório de servidor, pode implementá-las à sua instância do serviço de API Management.

![Implementação][api-management-configuration-deploy]

Para informações sobre como efetuar esta operação utilizando a API REST, consulte [Git implementar alterações à base de dados de configuração utilizando a API REST](https://docs.microsoft.com/en-us/rest/api/apimanagement/tenantconfiguration).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência de estrutura de ficheiros e pastas do repositório de Git local
Os ficheiros e pastas no repositório de local git contém as informações de configuração sobre a instância de serviço.

| Item | Descrição |
| --- | --- |
| Pasta raiz de gestão de api |Contém a configuração de nível superior para a instância de serviço |
| pasta de APIs |Contém a configuração para as apis numa instância de serviço |
| pasta de grupos |Contém a configuração para os grupos de instância de serviço |
| pasta de políticas |Contém as políticas a instância de serviço |
| pasta portalStyles |Contém a configuração para as personalizações de portal do programador na instância de serviço |
| pasta de produtos |Contém a configuração para os produtos na instância de serviço |
| pasta de modelos |Contém a configuração para os modelos de e-mail a instância de serviço |

Cada pasta pode conter um ou mais ficheiros e, em alguns casos uma ou mais pastas, por exemplo, uma pasta para cada API, um produto ou um grupo. Os ficheiros dentro de cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de ficheiro | Objetivo |
| --- | --- |
| JSON |Informações de configuração sobre a respetiva entidade |
| HTML |Descrições sobre a entidade, muitas vezes, é apresentada no portal do Programador |
| xml |Instruções de política |
| CSS |Folhas de estilos para personalização do portal de programador |

Estes ficheiros podem ser criados, eliminados, editados e geridos no seu sistema de ficheiros local e as alterações implementadas novamente para a sua instância de serviço de API Management.

> [!NOTE]
> As entidades seguintes não estão contidas no repositório de Git e não podem ser configuradas utilizando o Git.
> 
> * Utilizadores
> * Subscrições
> * Propriedades
> * Entidades de portais para programadores que não sejam estilos
> 
> 

### <a name="root-api-management-folder"></a>Pasta raiz de gestão de api
A raiz `api-management` pasta contém uma `configuration.json` ficheiro que contém informações sobre a instância de serviço no seguinte formato de nível superior.

```json
{
  "settings": {
    "RegistrationEnabled": "True",
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": "False",
    "UserRegistrationTermsConsentRequired": "False",
    "DelegationEnabled": "False",
    "DelegationUrl": "",
    "DelegatedSubscriptionEnabled": "False",
    "DelegationValidationKey": ""
  },
  "$ref-policy": "api-management/policies/global.xml"
}
```

As quatro primeiras definições (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, e `UserRegistrationTermsConsentRequired`) mapear para as seguintes definições no **identidades** separador o **segurança** secção.

| Definição de identidade | É mapeado para |
| --- | --- |
| RegistrationEnabled |**Redirecionar utilizadores anónimos para a página de início de sessão** caixa de verificação |
| UserRegistrationTerms |**Termos de utilização na inscrição de utilizador** caixa de texto |
| UserRegistrationTermsEnabled |**Mostrar os termos de utilização na página de inscrição** caixa de verificação |
| UserRegistrationTermsConsentRequired |**Exigir consentimento** caixa de verificação |

![Definições de identidade][api-management-identity-settings]

As definições junto quatro (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, e `DelegationValidationKey`) mapear para as seguintes definições no **delegação** separador o **segurança** secção.

| Definição de delegação | É mapeado para |
| --- | --- |
| DelegationEnabled |**Delegado início de sessão e inscrição** caixa de verificação |
| DelegationUrl |**URL de ponto final de delegação** caixa de texto |
| DelegatedSubscriptionEnabled |**Delegar a subscrição de produto** caixa de verificação |
| DelegationValidationKey |**Delegar a chave de validação** caixa de texto |

![Definições de delegação][api-management-delegation-settings]

A definição final, `$ref-policy`, mapeia para o ficheiro de instruções de política global para a instância de serviço.

### <a name="apis-folder"></a>pasta de APIs
O `apis` pasta contém uma pasta para cada API numa instância de serviço que contém os seguintes itens.

* `apis\<api name>\configuration.json`-Esta é a configuração para a API e contém informações sobre o URL do serviço de back-end e as operações. Esta é a mesma informação que teria de ser devolvida se chamar [obter uma API específica](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) com `export=true` no `application/json` formato.
* `apis\<api name>\api.description.html`-Esta é a descrição da API e corresponde ao `description` propriedade o [entidade API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
* `apis\<api name>\operations\`-Esta pasta contém `<operation name>.description.html` ficheiros que mapeiam para as operações na API. Cada ficheiro contém a descrição de uma operação única na API que mapeia para o `description` propriedade o [entidade operação](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) na REST API.

### <a name="groups-folder"></a>pasta de grupos
O `groups` pasta contém uma pasta para cada grupo definido na instância do serviço.

* `groups\<group name>\configuration.json`-Esta é a configuração para o grupo. Esta é a mesma informação que teria de ser devolvida se foram chamar o [obter um grupo específico](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) operação.
* `groups\<group name>\description.html`-Esta é a descrição do grupo e corresponde ao `description` propriedade o [grupo entidade](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>pasta de políticas
O `policies` pasta contém as declarações de política para a instância de serviço.

* `policies\global.xml`-contém as políticas definidas no âmbito global para a instância de serviço.
* `policies\apis\<api name>\`-Se tiver quaisquer políticas definidas no âmbito de API, estão contidos nesta pasta.
* `policies\apis\<api name>\<operation name>\`pasta - se tiver quaisquer políticas definidas no âmbito da operação, estão contidos nesta pasta no `<operation name>.xml` ficheiros que mapeiam para as declarações de política para cada operação.
* `policies\products\`-Se tiver quaisquer políticas definidas no âmbito do produto, estão contidos nesta pasta, que contém `<product name>.xml` ficheiros que mapeiam para as declarações de política para cada produto.

### <a name="portalstyles-folder"></a>pasta portalStyles
O `portalStyles` pasta contém folhas de estilo de configuração e para personalizações de portal de programador para a instância de serviço.

* `portalStyles\configuration.json`-contém os nomes das folhas de estilo utilizadas pelo portal do Programador
* `portalStyles\<style name>.css`-cada `<style name>.css` ficheiro contém estilos para o portal do programador (`Preview.css` e `Production.css` por predefinição).

### <a name="products-folder"></a>pasta de produtos
O `products` pasta contém uma pasta para cada produto definido na instância do serviço.

* `products\<product name>\configuration.json`-Esta é a configuração para o produto. Esta é a mesma informação que teria de ser devolvida se foram chamar o [obter um produto específico](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) operação.
* `products\<product name>\product.description.html`-Esta é a descrição do produto e que corresponde à `description` propriedade o [entidade produto](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) na REST API.

### <a name="templates"></a>modelos
O `templates` contém a pasta de configuração para o [modelos de e-mail](api-management-howto-configure-notifications.md) da instância do serviço.

* `<template name>\configuration.json`-Esta é a configuração para o modelo de correio eletrónico.
* `<template name>\body.html`-Este é o corpo do modelo de correio eletrónico.

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre outras formas de gerir a sua instância de serviço, consulte:

* Gerir a sua instância de serviço a utilizar os seguintes cmdlets do PowerShell
  * [Referência do cmdlet do Powershell de implementação do serviço](https://msdn.microsoft.com/library/azure/mt619282.aspx)
  * [Gestão de serviço referência de cmdlets do PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
* Gerir a sua instância de serviço no portal do publicador
  * [Gerir a primeira API](api-management-get-started.md)
* Gerir a sua instância de serviço utilizando a API REST
  * [Referência da API de REST de gestão de API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Veja uma descrição geral do vídeo
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Configuration-over-Git/player]
> 
> 

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




