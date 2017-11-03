---
title: "Implementação de Git Local no Serviço de Aplicações do Azure"
description: "Saiba como ativar a implementação de Git local para o App Service do Azure."
services: app-service
documentationcenter: 
author: dariagrigoriu
manager: erikre
editor: mollybos
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2016
ms.author: dariagrigoriu
ms.openlocfilehash: ed0239df7bf1e4d37987aaa929d0c67bec595b30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="local-git-deployment-to-azure-app-service"></a>Implementação de Git Local no Serviço de Aplicações do Azure
Este tutorial mostra como implementar a aplicação [Web Apps do Azure](app-service-web-overview.md) um repositório de Git no seu computador local. Serviço de aplicações suporta esta abordagem com a **Local Git** a opção de implementação no [Portal do Azure].  
Muitos dos comandos Git descritos neste artigo são efetuados automaticamente quando criar uma aplicação de serviço de aplicações utilizando o [Interface de linha de comandos do Azure] conforme descrito [aqui](app-service-web-get-started-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos
Para concluir este tutorial, precisa de:

* Git. Pode transferir a instalação binária [aqui](http://www.git-scm.com/downloads).  
* Conhecimento básico de Git.
* Uma conta do Microsoft Azure. Se não tiver uma conta, pode [inscrever-se numa avaliação gratuita](https://azure.microsoft.com/pricing/free-trial) ou [ativar os benefícios de subscritor do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

> [!NOTE]
> Se pretender começar a utilizar o App Service do Azure antes de inscrever-se numa conta do Azure, aceda a [experimentar o App Service](https://azure.microsoft.com/try/app-service/), onde, pode criar imediatamente uma aplicação de arranque de curta duração no App Service. Sem cartões de crédito; sem compromissos.  
> 
> 

## <a name="Step1"></a>Passo 1: Criar um repositório local
Execute as tarefas seguintes para criar um novo repositório de Git.

1. Iniciar uma ferramenta de linha de comandos, tal como **GitBash** (Windows) ou **Bash** (Shell de Unix). Em sistemas OS X podem aceder a linha de comandos através de **Terminal** aplicação.
2. Navegue para o diretório onde o conteúdo para implementar seria localizado.
3. Utilize o seguinte comando para inicializar um novo repositório de Git:

```bash  
git init
```

## <a name="Step2"></a>Passo 2: Confirmar o seu conteúdo
Serviço de aplicações suporta aplicações criadas numa variedade de linguagens de programação. 

1. Se o seu repositório já inclui conteúdo ignorar esta ponto e mover para apontar 2 abaixo. Se o repositório não já incluir conteúdo basta preencha com um ficheiro estático *.HTML da seguinte forma: 
   
   * Com um editor de texto, crie um novo ficheiro designado **index.html** na raiz do repositório de Git
   * Adicione o seguinte texto como o conteúdo para o index.html do ficheiro e guarde-o: *Hello Git!*
2. Na linha de comandos, certifique-se de que estão na raiz do repositório de Git. Em seguida, utilize o seguinte comando para adicionar ficheiros ao repositório:

```bash  
git add -A
```
3. Em seguida, confirme as alterações para o repositório utilizando o seguinte comando:

```bash  
git commit -m "Hello Azure App Service"
```  

## <a name="Step3"></a>Passo 3: Ativar o repositório da aplicação de serviço de aplicações
Execute os seguintes passos para ativar um repositório de Git para a sua aplicação de serviço de aplicações.

1. Inicie sessão no [Portal do Azure].
2. No painel da sua aplicação de serviço de aplicações, clique em **definições > origem de implementação**. Clique em **Escolher origem**, em seguida, clique em **repositório de Git Local**e, em seguida, clique em **OK**.  
   
    ![Repositório de Git local](./media/app-service-deploy-local-git/local_git_selection.png)
3. Se esta for a primeira hora configurar um repositório no Azure, terá de criar as credenciais de início de sessão para o mesmo. Irá utilizá-los para iniciar sessão nas alterações de push e de repositório do Azure do seu repositório de Git local. No painel da sua aplicação, clique em **definições > credenciais de implementação**, em seguida, configure o seu nome de utilizador de implementação e a palavra-passe. Quando tiver terminado, clique em **guardar**.
   
    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Passo 4: Implementar o projeto
Utilize os seguintes passos para publicar a aplicação no App Service utilizando o Local Git.

1. No painel da sua aplicação no Portal do Azure, clique em **definições > propriedades** para o **URL do Git**.
   
    ![](./media/app-service-deploy-local-git/git_url.png)
   
    **URL do Git** é a referência para implementar a partir do seu repositório local remota. Irá utilizar este URL nos passos seguintes.
2. Utilizando a linha de comandos, certifique-se de que estão na raiz do repositório de Git local.
3. Utilize `git remote` para adicionar a referência remota listada no **URL do Git** do passo 1. O comando será semelhante ao seguinte:
   
        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
   > [!NOTE]
   > O **remoto** comando adiciona uma referência nomeada para um repositório remoto. Neste exemplo, cria uma referência com o nome 'azure' para o repositório da sua aplicação web.
   > 
   > 
4. Push o conteúdo para o App Service utilizando a nova **azure** remoto que acabou de criar.

```bash  
git push azure master
```
    You will be prompted for the password you created earlier when you reset your deployment credentials in the Azure Portal. Enter the password (note that Gitbash does not echo asterisks to the console as you type your password). 
5. Volte para a sua aplicação no Portal do Azure. Uma entrada de registo do seu push mais recente deverá ser apresentada no **implementações** painel. 
   
    ![](./media/app-service-deploy-local-git/deployment_history.png)
6. Clique em de **procurar** botão na parte superior do painel da aplicação para verificar o conteúdo tenha sido implementado. 

## <a name="Step5"></a>Resolução de Problemas
Seguem-se erros ou problemas comuns encontrados quando utilizar o Git para publicar uma aplicação de serviço de aplicações no Azure:

- - -
**Sintoma**: não é possível acesso [siteURL]: Falha ao ligar ao [scmAddress]

**Causa**: Este erro pode ocorrer se a aplicação não se encontra em execução.

**Resolução**: iniciar a aplicação no Portal do Azure. Implementação de Git não funcionará, a menos que a aplicação está em execução. 

- - -
**Sintoma**: não foi possível resolver o anfitrião 'hostname'

**Causa**: Este erro pode ocorrer se as informações de endereço que introduziu durante a criação do azure remoto estavam incorretas.

**Resolução**: Utilize o `git remote -v` comando para listar todos os remotes, juntamente com o URL associado. Certifique-se de que o URL para 'azure' remoto está correto. Se for necessário, remova e recrie esta remoto utilizando o URL correto.

- - -
**Sintoma**: nenhum refs no comuns e nenhum especificado; fazer nada. Talvez deve especificar um ramo, tais como 'master'.

**Causa**: Este erro pode ocorrer se não especificar um ramo quando efetuar um git push operação e não definiu o valor de push.default utilizado pelo Git.

**Resolução**: executar a operação de push novamente, especificando o ramo principal. Por exemplo:

```bash  
git push azure master
```
- - -
**Sintoma**: src refspec [branchname] não corresponde a nenhum.

**Causa**: Este erro pode ocorrer se tentar ativar para um ramo diferente mestre do azure remoto.

**Resolução**: executar a operação de push novamente, especificando o ramo principal. Por exemplo:

```bash  
git push azure master
```
- - -
**Sintoma**: falha de RPC; resultar = 22, código de HTTP = 502.

**Causa**: Este erro pode ocorrer se tentar push um repositório de git grande através de HTTPS.

**Resolução**: alterar a configuração do git no computador local para tornar o postBuffer superior

```bash  
git config --global http.postBuffer 524288000
```
- - -
**Sintoma**: erro - alterações consolidadas para o repositório remoto, mas a aplicação web não atualizada.

**Causa**: Este erro pode ocorrer se estiver a implementar uma aplicação Node.js que contém um ficheiro de Package. JSON que especifica os módulos necessários adicionais.

**Resolução**: mensagens adicionais que contém 'npm ERR'! deve ser iniciado antes deste erro e pode fornecer contexto adicional sobre a falha. Os seguintes são conhecidos causas deste erro e a correspondente 'npm ERR!' mensagem:

* **Ficheiro Package. JSON com formato incorreto**: npm ERR! Não foi possível ler as dependências.
* **Módulo nativo que não tenha uma distribuição binária para o Windows**:
  
  * npm ERR! \`cmd "/ c" "nó gyp reconstrução"\` falhou, com 1
    
      OU
  * npm ERR! [modulename@version] pré-instalar: \`tornar | | gmake\`

## <a name="additional-resources"></a>Recursos Adicionais
* [Documentação do Git](http://git-scm.com/documentation)
* [Documentação do Kudu projeto](https://github.com/projectkudu/kudu/wiki)
* [Implementação contínua do serviço de aplicações do Azure](app-service-continuous-deployment.md)
* [Como utilizar o PowerShell para o Azure](/powershell/azure/overview)
* [Como utilizar a Interface de linha de comandos do Azure](../cli-install-nodejs.md)

[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Portal do Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Interface de linha de comandos do Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
