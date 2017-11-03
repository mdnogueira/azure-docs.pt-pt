---
title: "Resolução de problemas de erros de cliente de Docker no Windows utilizando o Visual Studio | Microsoft Docs"
description: "Resolva problemas que encontrar ao utilizar o Visual Studio para criar e implementar aplicações web para Docker no Windows utilizando o Visual Studio."
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 89fa04a1107b6abb49aefd68066443717ac9b731
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-visual-studio-docker-development"></a>Resolver problemas de desenvolvimento de Visual Studio Docker

Quando estiver a trabalhar com o Visual Studio Tools para pré-visualização do Docker, pode encontrar alguns problemas devido a natureza a pré-visualização.
Seguem-se alguns problemas e resoluções comuns.  

## <a name="visual-studio-2017-rc"></a>RC de 2017 do Visual Studio

### <a name="linux-containers"></a>**Contentores do Linux**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>Criar ocorrer erros ao depurar uma aplicação de web ou a consola do .NET Core  

Isto pode estar relacionado com a partilha não a unidade onde reside o projeto com o Docker para Windows.  Poderá receber um erro, como o seguinte:

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
Para resolver este problema:

1. Clique com botão direito **Docker para Windows** na área de notificação e, em seguida, selecione **definições**.  
2. Selecione **partilhado unidades** e partilhar a unidade onde reside o projeto.

### <a name="windows-containers"></a>**Contentores do Windows**

Os seguintes problemas são específicos de depuração de aplicações web e a consola do .NET Framework nos contentores do Windows.

#### <a name="prerequisites"></a>Pré-requisitos

1. RC de 2017 do Visual Studio (ou posterior) com o .NET Core e a pré-visualização do Docker carga de trabalho tem de estar instalada.
2. Patches do Windows 10 aniversário da atualização com essa atualização mais recente do Windows. Especificamente [KB3194798](https://support.microsoft.com/en-us/help/3194798/cumulative-update-for-windows-10-version-1607-and-windows-server-2016-october-11,-2016) tem de estar instalado. 
3. [Docker para Windows](https://docs.docker.com/docker-for-windows/) (criar 1.13.0 ou posterior) tem de estar instalado.
4. **Mudar para contentores de Windows** tem de ser selecionada. Na área de notificação, clique em **Docker para Windows**e, em seguida, selecione **mudar para contentores de Windows**. Depois do computador ser reiniciado, certifique-se de que esta definição é mantida.

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>Resultado da consola não são apresentados na janela de saída do Visual Studio durante a depuração de uma aplicação de consola

Este é um problema conhecido com o depurador do Visual Studio (msvsmon.exe), que atualmente não foi concebido para este cenário. Suporte para este cenário poderá ser incluído numa versão futura. Para ver os resultados da aplicação de consola no Visual Studio, utilize **Docker: iniciar o projeto**, que é equivalente a **iniciar sem a depuração**.

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>A depuração de aplicações web com a falha de configuração de versão com o erro de proibido (403)

Para contornar este problema, abra web.release.config na solução e comente ou eliminar as seguintes linhas:

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Contentores do Linux**

#### <a name="unable-to-validate-volume-mapping"></a>Não foi possível validar o mapeamento do volume
Mapeamento do volume é necessária para partilhar o código de origem e os binários da sua aplicação com a pasta de aplicação no contentor.  Mapeamentos de volume específicas são contidos no docker compose.dev.debug.yml e docker compose.dev.release.yml. Como os ficheiros são alterados no seu computador anfitrião, os contentores refletem estas alterações numa estrutura de pasta semelhantes.

Para ativar o mapeamento do volume:

1. Clique em **Moby** na área de notificação e selecione **definições**.
2. Selecione **partilhado unidades**.
3. Selecione a unidade que aloja o seu projeto e a unidade onde reside % USERPROFILE %.
4. Clique em **Aplicar**.

Para testar se o mapeamento do volume está a funcionar, reconstruir e selecione F5 do Visual Studio, depois de uma ou mais unidades foram partilhadas, ou executadas o código seguinte numa linha de comandos.

> [!NOTE]
> Este exemplo assume que a pasta de utilizadores está localizada na unidade C e que foi partilhado.
> Rever conforme necessário se partilhou uma unidade diferente.

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

Execute o seguinte código no contentor do Linux.

```
/ # ls
```

Deverá ver uma listagem da pasta utilizadores/público de diretórios. Se não existem ficheiros são apresentados e a pasta de /c/Users/Public não está vazia, mapeamento do volume não está configurado corretamente.

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

Mude para o diretório de wormhole para ver o conteúdo a `/c/Users/Public` diretório:

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> Quando estiver a trabalhar com as VMs do Linux, o sistema de ficheiros de contentor diferencia maiúsculas de minúsculas.

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>Compilação: "PrepareForBuild" tarefa falhou inesperadamente

Microsoft.DotNet.Docker.CommandLine.ClientException: Ocorreu um erro ao tentar ligar.

Certifique-se de que o anfitrião de Docker predefinido está em execução. Abra uma linha de comandos e execute:

```
docker info
```

Se esta ação devolve um erro, em seguida, tentar iniciar o **Docker para Windows** aplicações de ambiente de trabalho. Se a aplicação de ambiente de trabalho está em execução, em seguida, **Moby** devem estar visíveis na área de notificação. Clique com botão direito **Moby** e abra **definições**. Clique em **repor**e, em seguida, reinicie Docker.

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>Uma caixa de diálogo de erro ocorre quando tentar adicionar suporte de Docker ou depurar uma aplicação ASP.NET Core num contentor de (F5)

Depois de desinstalar e instalar as extensões, a cache de estrutura de extensibilidade geridos (MEF) no Visual Studio pode ficar danificada. Se isto ocorre, pode causar várias mensagens de erro quando que está a adição de suporte de Docker e/ou tentar executar ou de depuração (F5) a aplicação ASP.NET Core. Como solução temporária, utilize os seguintes passos para eliminar e voltar a gerar a cache MEF.

1. Feche todas as instâncias do Visual Studio.
1. Abra % USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\.
1. Elimine as seguintes pastas:
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. Abra o Visual Studio.
1. Tentativa do cenário.
