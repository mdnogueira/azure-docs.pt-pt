---
title: "Criar funções web e de trabalho do Azure para PHP | Microsoft Docs"
description: "Um guia para criar funções web e de trabalho do PHP num serviço em nuvem do Azure e configurar o runtime PHP."
services: 
documentationcenter: php
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: 214fdcfe20f3fa4ebcbe41308404f8b7e7d15310
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-php-web-and-worker-roles"></a>Como criar funções web e de trabalho do PHP
## <a name="overview"></a>Descrição geral
Este guia irá mostrar como criar funções web ou de trabalho do PHP num ambiente de desenvolvimento do Windows, escolher uma versão específica do PHP das versões "incorporadas" disponíveis, alterar a configuração do PHP, ativar extensões e por fim, implementar no Azure. Também descreve como configurar uma função web ou de trabalho para utilizar um runtime PHP (com as extensões e configuração personalizada) que fornecer.

## <a name="what-are-php-web-and-worker-roles"></a>Quais são as funções web e de trabalho do PHP?
O Azure oferece três modelos de computação para aplicações em execução: App Service do Azure, Azure Virtual Machines e Cloud Services do Azure. Todos os três modelos suportam o PHP. Fornece serviços em nuvem, incluindo funções web e de trabalho, *plataforma como serviço (PaaS)*. Dentro de um serviço em nuvem, uma função da web fornece um servidor de web de serviços de informação Internet (IIS) dedicado aplicações web de front-end. Uma função de trabalho pode executar tarefas assíncronas, execução longa ou perpétuas, independentes da interação do utilizador ou de uma entrada.

Para obter mais informações sobre estas opções, consulte [computação que alojam as opções fornecidas pelo Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Transfira o Azure SDK para PHP
O [Azure SDK para PHP] é composta por vários componentes. Este artigo irá utilizar dois deles: Azure PowerShell e os emuladores do Azure. Estes dois componentes podem ser instalados através do instalador de plataforma Web Microsoft. Para obter mais informações, veja [How to install and configure Azure PowerShell (Como instalar e configurar o Azure PowerShell)](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Criar um projeto de serviços Cloud
É o primeiro passo na criação de uma função web ou de trabalho do PHP para criar um projeto de serviço do Azure. um projeto de serviço do Azure funciona como um contentor lógico para funções web e de trabalho e contém o projeto [(. csdef) de definição de serviço] e [a configuração do serviço (. cscfg)] ficheiros.

Para criar um novo projeto de serviço do Azure, execute o Azure PowerShell como administrador e execute o seguinte comando:

    PS C:\>New-AzureServiceProject myProject

Este comando irá criar um novo diretório (`myProject`) ao qual pode adicionar funções web e de trabalho.

## <a name="add-php-web-or-worker-roles"></a>Adicionar funções web ou de trabalho do PHP
Para adicionar uma função da web PHP a um projeto, execute o comando seguinte a partir de diretório de raiz do projeto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para uma função de trabalho, utilize este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> O `roleName` parâmetro é opcional. Se for omitido, o nome da função será gerado automaticamente. A função da web primeiro criada será `WebRole1`, a segunda estará `WebRole2`, e assim sucessivamente. A primeira função de trabalho criada será `WorkerRole1`, a segunda estará `WorkerRole2`, e assim sucessivamente.
>
>

## <a name="specify-the-built-in-php-version"></a>Especifique a versão do PHP incorporada
Quando adiciona uma função web ou de trabalho do PHP para um projeto, ficheiros de configuração do projeto são modificados para que o PHP será instalado em cada instância de web ou de trabalho da sua aplicação quando é implementada. Para ver a versão do PHP que será instalada por predefinição, execute o seguinte comando:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

O resultado do comando acima terá um aspeto semelhante ao que é mostrado abaixo. Neste exemplo, o `IsDefault` sinalizador está definido para `true` para PHP 5.3.17, que indica que será a versão do PHP predefinida instalada.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Pode definir a versão de runtime do PHP para qualquer uma das versões do PHP que são listadas. Por exemplo, para definir a versão do PHP (para uma função com o nome `roleName`) para 5.4.0, utilize o seguinte comando:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Versões do PHP disponíveis podem ser alterados no futuro.
>
>

## <a name="customize-the-built-in-php-runtime"></a>Personalizar o runtime PHP incorporado
Ter controlo total sobre a configuração do runtime PHP instalado ao seguir os passos acima, incluindo a modificação do `php.ini` definições e a ativação das extensões.

Para personalizar o tempo de execução incorporado do PHP, siga estes passos:

1. Adicionar uma nova pasta com o nome `php`, como o `bin` diretório da sua função web. Para uma função de trabalho, adicione-o ao diretório de raiz da função.
2. No `php` pasta, crie outra pasta designada `ext`. Colocar qualquer `.dll` ficheiros de extensão (por exemplo, `php_mongo.dll`) que pretende ativar nesta pasta.
3. Adicionar um `php.ini` do ficheiro para o `php` pasta. Ativar as extensões personalizadas e defina as diretivas PHP neste ficheiro. Por exemplo, se pretendesse ativar `display_errors` no e ativar o `php_mongo.dll` extensão, o conteúdo do seu `php.ini` ficheiro seria o seguinte:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> As definições que não definir explicitamente no `php.ini` ficheiros que fornecem automaticamente será definida para os respetivos valores predefinidos. No entanto, tenha em atenção que pode adicionar um concluída `php.ini` ficheiro.
>
>

## <a name="use-your-own-php-runtime"></a>Utilizar o seu próprio tempo de execução do PHP
Em alguns casos, em vez de selecionar um tempo de execução do PHP incorporado e configurá-lo, tal como descrito acima, poderá pretender fornecer o suas próprias runtime PHP. Por exemplo, pode utilizar o runtime PHP mesmo numa função web ou de trabalho que utiliza no seu ambiente de desenvolvimento. Isto torna mais fácil para se certificar de que a aplicação não irá alterar o comportamento no seu ambiente de produção.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configurar uma função da web para utilizar o seu próprio tempo de execução do PHP
Para configurar uma função da web para utilizar um tempo de execução do PHP que fornece, siga estes passos:

1. Criar um projeto de serviço do Azure e adicione uma função da web PHP, conforme descrito anteriormente neste tópico.
2. Criar um `php` pasta o `bin` pasta que está no diretório de raiz a função da web e, em seguida, adicione o runtime PHP (todos os binários, ficheiros de configuração, subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se o tempo de execução do PHP utiliza o [Drivers Microsoft para PHP para SQL Server][sqlsrv drivers], terá de configurar a função da web para instalar [SQL Server Native Client 2012] [ sql native client] quando está aprovisionado. Para tal, adicione o [sqlncli.msi x64 instalador] para o `bin` pasta no diretório de raiz da sua função web. O script de arranque descrito no próximo passo silenciosamente executará o instalador quando a função está aprovisionada. Se o tempo de execução do PHP não utilizar o Drivers Microsoft para PHP para SQL Server, pode remover a linha seguinte do script mostrado no próximo passo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definir uma tarefa de arranque que configura [serviços de informação Internet (IIS)] [ iis.net] para utilizar o runtime do PHP para processar pedidos para `.php` páginas. Para tal, abra o `setup_web.cmd` ficheiro (no `bin` ficheiros do diretório de raiz a função da web) num editor de texto e substitua o conteúdo com o seguinte script:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Adicione os ficheiros da aplicação para o diretório de raiz da sua função web. Este será o diretório de raiz do servidor web.
6. Publicar a aplicação conforme descrito no [publicar a aplicação](#publish-your-application) secção abaixo.

> [!NOTE]
> O `download.ps1` script (no `bin` pasta de diretório de raiz a função da web) pode ser eliminada depois de seguir os passos descritos acima para utilizar o seu próprio tempo de execução do PHP.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configurar uma função de trabalho para utilizar o seu próprio tempo de execução do PHP
Para configurar uma função de trabalho para utilizar um tempo de execução do PHP que fornece, siga estes passos:

1. Criar um projeto de serviço do Azure e adicione uma função de trabalho do PHP, conforme descrito anteriormente neste tópico.
2. Criar um `php` pasta no diretório de raiz a função de trabalho e, em seguida, adicione o runtime PHP (todos os binários, ficheiros de configuração, subpastas, etc.) para o `php` pasta.
3. (OPCIONAL) Se utilizar o runtime PHP [Drivers Microsoft para PHP para SQL Server][sqlsrv drivers], terá de configurar a função de trabalho para instalar [SQL Server Native Client 2012] [ sql native client] quando está aprovisionado. Para tal, adicione o [sqlncli.msi x64 instalador] ao diretório de raiz a função de trabalho. O script de arranque descrito no próximo passo silenciosamente executará o instalador quando a função está aprovisionada. Se o tempo de execução do PHP não utilizar o Drivers Microsoft para PHP para SQL Server, pode remover a linha seguinte do script mostrado no próximo passo:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Definir uma tarefa de arranque que adiciona o `php.exe` executável à variável de ambiente de caminho a função de trabalho quando a função está aprovisionada. Para tal, abra o `setup_worker.cmd` (no diretório de raiz da função de trabalho) de ficheiro num editor de texto e substitua os respetivos conteúdos com o seguinte script:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Adicione os ficheiros da aplicação para o diretório de raiz da sua função de trabalho.
6. Publicar a aplicação conforme descrito no [publicar a aplicação](#publish-your-application) secção abaixo.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Execute a sua aplicação no emuladores de computação e armazenamento
Os Azure emuladores fornecem um ambiente local na qual pode testar a aplicação do Azure antes de implementá-la para a nuvem. Existem algumas diferenças entre os emuladores e o ambiente do Azure. Para melhor compreender isto, consulte [utilizar o emulador de armazenamento do Azure para desenvolvimento e testes](storage/common/storage-use-emulator.md).

Tenha em atenção que tem de ter o PHP instalado localmente ao utilizar o emulador de computação. O emulador de computação irá utilizar a instalação do PHP local para executar a sua aplicação.

Para executar o projeto de emuladores, execute o seguinte comando do diretório de raiz do projeto:

    PS C:\MyProject> Start-AzureEmulator

Irá ver o resultado semelhante a isto:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Pode ver a sua aplicação em execução no emulador, ao abrir um browser e navegar para o endereço local apresentado no resultado (`http://127.0.0.1:81` na saída de exemplo acima).

Para parar os emuladores, execute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicar a aplicação
Para publicar a aplicação, terá de importar primeiro as definições de publicação utilizando o [importação AzurePublishSettingsFile](https://msdn.microsoft.com/library/azure/dn790370.aspx) cmdlet. Em seguida, pode publicar a aplicação utilizando o [Publish-AzureServiceProject](https://msdn.microsoft.com/library/azure/dn495166.aspx) cmdlet. Para obter informações sobre o início de sessão, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte o [Centro para programadores do PHP](/develop/php/).

[Azure SDK para PHP]: /develop/php/common-tasks/download-php-sdk/ (Azure SDK para PHP)
[install ps and emulators]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[(. csdef) de definição de serviço]: http://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[a configuração do serviço (. cscfg)]: http://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: http://www.iis.net/
[sql native client]: http://msdn.microsoft.com/sqlserver/aa937733.aspx
[sqlsrv drivers]: http://php.net/sqlsrv
[sqlncli.msi x64 instalador]: http://go.microsoft.com/fwlink/?LinkID=239648
