---
title: "Instale o .NET em funções de Cloud Services do Azure | Microsoft Docs"
description: "Este artigo descreve como instalar manualmente o .NET Framework no seu funções serviço na nuvem web e de trabalho"
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 8d1243dc-879c-4d1f-9ed0-eecd1f6a6653
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: adegeo
ms.openlocfilehash: cc4b62bc554757e6e394b78334f52f45aa08efe8
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="install-net-on-azure-cloud-services-roles"></a>Instale o .NET em funções de Cloud Services do Azure
Este artigo descreve como instalar versões do .NET Framework que não são fornecidos com o SO convidado do Azure. Pode utilizar .NET no SO convidado para configurar o serviço de nuvem funções para web e de trabalho.

Por exemplo, pode instalar o .NET 4.6.1 na família de SO convidado 4, que não são fornecidos com qualquer versão do .NET 4.6. (A família de SO convidado 5 vêm com .NET 4.6.) Para informações mais recentes sobre as versões de SO convidado do Azure, consulte o [notícias de versão de SO convidado do Azure](cloud-services-guestos-update-matrix.md). 

>[!IMPORTANT]
>O Azure SDK 2.9 contém uma restrição para implementar o .NET 4.6 a família de SO convidado 4 ou anterior. Uma correção para a restrição está disponível na [Microsoft Docs](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Azure%20Targets%20SDK%202.9) site.

Para instalar o .NET as funções da web e de trabalho, inclua o instalador da web de .NET como parte do seu projeto de serviço em nuvem. Inicie o instalador como parte das tarefas de arranque da função. 

## <a name="add-the-net-installer-to-your-project"></a>Adicionar o programa de instalação do .NET ao seu projeto
Para transferir o instalador da web para o .NET Framework, escolha a versão que pretende instalar:

* [.NET 4.7.1 web instalador](http://go.microsoft.com/fwlink/?LinkId=852095)
* [.NET 4.6.1 web instalador](http://go.microsoft.com/fwlink/?LinkId=671729)

Para adicionar o programa de instalação para um *web* função:
  1. No **Explorador de soluções**, em **funções** no seu projeto de serviço em nuvem, clique no seu *web* função e selecione **adicionar** > **nova pasta**. Crie uma pasta denominada **bin**.
  2. Clique com o botão direito na pasta bin e selecione **adicionar** > **Item existente**. Selecione o programa de instalação do .NET e adicione-o para a pasta Reciclagem.
  
Para adicionar o programa de instalação para um *trabalho* função:
* Clique no seu *trabalho* função e selecione **adicionar** > **Item existente**. Selecione o programa de instalação do .NET e adicione-à função. 

Quando são adicionados ficheiros desta forma para a pasta de conteúdo de função, está a adicionados automaticamente ao seu pacote de serviço em nuvem. Os ficheiros, em seguida, são implementados para uma localização consistente da máquina virtual. Repita este processo para cada função web e de trabalho no seu serviço de nuvem de modo a que todas as funções de uma cópia do programa de instalação.

> [!NOTE]
> Deve instalar o .NET 4.6.1 na sua função de serviço em nuvem mesmo que a aplicação está direcionada para .NET 4.6. O SO convidado inclui a Base de dados de conhecimento [atualizar 3098779](https://support.microsoft.com/kb/3098779) e [atualizar 3097997](https://support.microsoft.com/kb/3097997). Podem ocorrer problemas ao executar as aplicações de .NET se o .NET 4.6 é instalado por cima as atualizações da Base de dados de conhecimento. Para evitar estes problemas, instale o .NET 4.6.1 em vez de versão 4.6. Para obter mais informações, consulte o [artigo da Base de dados de conhecimento 3118750](https://support.microsoft.com/kb/3118750).
> 
> 

![Conteúdo de função com ficheiros de instalador][1]

## <a name="define-startup-tasks-for-your-roles"></a>Definir as tarefas de arranque para as funções
Pode utilizar tarefas de arranque para executar operações antes de começa a uma função. Instalar o .NET Framework como parte da tarefa de arranque assegura que o framework está instalado antes de qualquer código de aplicação é executado. Para obter mais informações sobre tarefas de arranque, consulte [executar tarefas de arranque no Azure](cloud-services-startup-tasks.md). 

1. Adicione o seguinte conteúdo para o ficheiro de servicedefinition. Csdef sob o **WebRole** ou **WorkerRole** nós para todas as funções:
   
    ```xml
    <LocalResources>
      <LocalStorage name="NETFXInstall" sizeInMB="1024" cleanOnRoleRecycle="false" />
    </LocalResources>    
    <Startup>
      <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
        <Environment>
          <Variable name="PathToNETFXInstall">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='NETFXInstall']/@path" />
          </Variable>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
    ```
   
    A configuração anterior executa o comando de consola `install.cmd` com privilégios de administrador para instalar o .NET Framework. A configuração também cria um **LocalStorage** elemento com o nome **NETFXInstall**. O script de arranque define a pasta temporária para utilizar este recurso de armazenamento local. 
    
    > [!IMPORTANT]
    > Para assegurar a instalação correta do framework, defina o tamanho deste recurso para, pelo menos, 1.024 MB.
    
    Para obter mais informações sobre tarefas de arranque, consulte [tarefas de arranque do Cloud Services do Azure comuns](cloud-services-startup-tasks-common.md).

2. Crie um ficheiro denominado **install.cmd** e adicione o seguinte instalar o script para o ficheiro.

    O script verifica se a versão especificada do .NET Framework já está instalada na máquina consultando o registo. Se a versão do .NET não está instalada, o instalador da web de .NET é aberto. Para ajudar a resolver quaisquer problemas, o script regista todas as atividades para o ficheiro startuptasklog-(data e hora atuais). txt que esteja armazenado em **InstallLogs** armazenamento local.
  
    > [!IMPORTANT]
    > Utilize um editor de texto básicas, como o bloco de notas do Windows para criar o ficheiro install.cmd. Se utilizar o Visual Studio para criar um ficheiro de texto e altere a extensão para. cmd, o ficheiro ainda poderá conter uma marca de ordem de bytes UTF-8. Esta marca pode originar um erro quando a primeira linha do script é executada. Para evitar este erro, se a primeira linha do script de uma instrução de alerta de REM que pode ser ignorada pelo processamento de ordem de bytes. 
    > 
    >
   
    ```cmd
    REM Set the value of netfx to install appropriate .NET Framework. 
    REM ***** To install .NET 4.5.2 set the variable netfx to "NDP452" *****
    REM ***** To install .NET 4.6 set the variable netfx to "NDP46" *****
    REM ***** To install .NET 4.6.1 set the variable netfx to "NDP461" *****
    REM ***** To install .NET 4.6.2 set the variable netfx to "NDP462" *****
    REM ***** To install .NET 4.7 set the variable netfx to "NDP47" *****
    REM ***** To install .NET 4.7.1 set the variable netfx to "NDP47" *****
    set netfx="NDP471"
    
    REM ***** Set script start timestamp *****
    set timehour=%time:~0,2%
    set timestamp=%date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2%
    set "log=install.cmd started %timestamp%."
    
    REM ***** Exit script if running in Emulator *****
    if %ComputeEmulatorRunning%=="true" goto exit
    
    REM ***** Needed to correctly install .NET 4.6.1, otherwise you may see an out of disk space error *****
    set TMP=%PathToNETFXInstall%
    set TEMP=%PathToNETFXInstall%
    
    REM ***** Setup .NET filenames and registry keys *****
    if %netfx%=="NDP471" goto NDP471
    if %netfx%=="NDP47" goto NDP47
    if %netfx%=="NDP462" goto NDP462
    if %netfx%=="NDP461" goto NDP461
    if %netfx%=="NDP46" goto NDP46
        set "netfxinstallfile=NDP452-KB2901954-Web.exe"
        set netfxregkey="0x5cbf5"
        goto logtimestamp
    
    :NDP46
    set "netfxinstallfile=NDP46-KB3045560-Web.exe"
    set netfxregkey="0x6004f"
    goto logtimestamp
    
    :NDP461
    set "netfxinstallfile=NDP461-KB3102438-Web.exe"
    set netfxregkey="0x6040e"
    goto logtimestamp
    
    :NDP462
    set "netfxinstallfile=NDP462-KB3151802-Web.exe"
    set netfxregkey="0x60632"
    goto logtimestamp
    
    :NPD47
    set "netfxinstallfile=NDP47-KB3186500-Web.exe"
    set netfxregkey="0x707FE"
    goto logtimestamp
    
    :NDP471
    set "netfxinstallfile=NDP471-KB4033344-Web.exe"
    set netfxregkey="0x709fc"
    goto logtimestamp
    
    :logtimestamp
    REM ***** Setup LogFile with timestamp *****
    md "%PathToNETFXInstall%\log"
    set startuptasklog="%PathToNETFXInstall%log\startuptasklog-%timestamp%.txt"
    set netfxinstallerlog="%PathToNETFXInstall%log\NetFXInstallerLog-%timestamp%"
    echo %log% >> %startuptasklog%
    echo Logfile generated at: %startuptasklog% >> %startuptasklog%
    echo TMP set to: %TMP% >> %startuptasklog%
    echo TEMP set to: %TEMP% >> %startuptasklog%
    
    REM ***** Check if .NET is installed *****
    echo Checking if .NET (%netfx%) is installed >> %startuptasklog%
    set /A netfxregkeydecimal=%netfxregkey%
    set foundkey=0
    FOR /F "usebackq skip=2 tokens=1,2*" %%A in (`reg query "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\NET Framework Setup\NDP\v4\Full" /v Release 2^>nul`) do @set /A foundkey=%%C
    echo Minimum required key: %netfxregkeydecimal% -- found key: %foundkey% >> %startuptasklog%
    if %foundkey% GEQ %netfxregkeydecimal% goto installed
    
    REM ***** Installing .NET *****
    echo Installing .NET with commandline: start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog%  /chainingpackage "CloudService Startup Task" >> %startuptasklog%
    start /wait %~dp0%netfxinstallfile% /q /serialdownload /log %netfxinstallerlog% /chainingpackage "CloudService Startup Task" >> %startuptasklog% 2>>&1
    if %ERRORLEVEL%== 0 goto installed
        echo .NET installer exited with code %ERRORLEVEL% >> %startuptasklog%    
        if %ERRORLEVEL%== 3010 goto restart
        if %ERRORLEVEL%== 1641 goto restart
        echo .NET (%netfx%) install failed with Error Code %ERRORLEVEL%. Further logs can be found in %netfxinstallerlog% >> %startuptasklog%
    
    :restart
    echo Restarting to complete .NET (%netfx%) installation >> %startuptasklog%
    EXIT /B %ERRORLEVEL%
    
    :installed
    echo .NET (%netfx%) is installed >> %startuptasklog%
    
    :end
    echo install.cmd completed: %date:~-4,4%%date:~-10,2%%date:~-7,2%-%timehour: =0%%time:~3,2% >> %startuptasklog%
    
    :exit
    EXIT /B 0
    ```

3. Adicione o ficheiro de install.cmd para cada função utilizando **adicionar** > **Item existente** no **Explorador de soluções** como descrito anteriormente neste tópico. 

    Após a conclusão deste passo, todas as funções devem ter o ficheiro de instalador de .NET e o ficheiro install.cmd.

   ![Conteúdo de função com todos os ficheiros][2]

## <a name="configure-diagnostics-to-transfer-startup-logs-to-blob-storage"></a>Configurar diagnósticos para transferir os registos de arranque para o Blob storage
Para simplificar a resolução de problemas de instalação, pode configurar diagnósticos do Azure para transferir os ficheiros de registo gerados pelo script de arranque ou o programa de instalação do .NET para o armazenamento de Blobs do Azure. Ao utilizar esta abordagem, pode ver os registos ao transferir os ficheiros de registo a partir do Blob storage, em vez de ter para ambiente de trabalho remoto para a função.

Para configurar diagnósticos, abra o ficheiro de diagnostics.wadcfgx e adicione o seguinte conteúdo sob o **diretórios** nó: 

```xml 
<DataSources>
 <DirectoryConfiguration containerName="netfx-install">
  <LocalResource name="NETFXInstall" relativePath="log"/>
 </DirectoryConfiguration>
</DataSources>
```

Este XML configura diagnósticos para transferir os ficheiros no diretório de registo no **NETFXInstall** recursos para a conta de armazenamento do Diagnostics no **netfx instalação** contentor do blob.

## <a name="deploy-your-cloud-service"></a>Implementar o serviço em nuvem
Quando implementar o serviço de nuvem, as tarefas de arranque instalar o .NET Framework, se ainda não estiver instalado. As funções do serviço de nuvem estão no *ocupado* enquanto está a ser instalado o framework de estado. Se a instalação do framework requer um reinício, as funções do serviço também poderão reiniciar. 

## <a name="additional-resources"></a>Recursos adicionais
* [Instalar o .NET Framework][Installing the .NET Framework]
* [Determinar que versões do .NET Framework estão instaladas][How to: Determine Which .NET Framework Versions Are Installed]
* [Resolução de problemas de instalações do .NET Framework][Troubleshooting .NET Framework Installations]

[How to: Determine Which .NET Framework Versions Are Installed]: /dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed
[Installing the .NET Framework]: /dotnet/framework/install/guide-for-developers
[Troubleshooting .NET Framework Installations]: /dotnet/framework/install/troubleshoot-blocked-installations-and-uninstallations

<!--Image references-->
[1]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithinstallerfiles.png
[2]: ./media/cloud-services-dotnet-install-dotnet/rolecontentwithallfiles.png
