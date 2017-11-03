---
title: "Tarefas de arranque comuns para serviços em nuvem | Microsoft Docs"
description: "Fornece alguns exemplos de tarefas comuns de arranque que pretende executar na sua função de web de serviços de nuvem ou a função de trabalho."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: a7095dad-1ee7-4141-bc6a-ef19a6e570f1
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: cee23da5b089b02bfc0ef10afd60f0f2272585b1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="common-cloud-service-startup-tasks"></a>Tarefas comuns de arranque do serviço em nuvem
Este artigo fornece alguns exemplos de tarefas comuns de arranque que pretende executar no seu serviço em nuvem. Pode utilizar tarefas de arranque para executar operações antes de começa a uma função. As operações que poderá querer efetuar incluem instalar um componente, registar componentes COM, definir as chaves de registo ou iniciar um processo de execução longa. 

Consulte [neste artigo](cloud-services-startup-tasks.md) para compreender como funcionam as tarefas de arranque e, especificamente como criar as entradas que definem uma tarefa de arranque.

> [!NOTE]
> Tarefas de arranque não são aplicáveis para máquinas virtuais, apenas a Web do serviço de nuvem e as funções de trabalho.
> 

## <a name="define-environment-variables-before-a-role-starts"></a>Definir variáveis de ambiente antes de começa a uma função
Se precisar de variáveis de ambiente definidas para uma tarefa específica, utilize o [ambiente] elemento dentro do [tarefas] elemento.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
                <Environment>
                    <Variable name="MyEnvironmentVariable" value="MyVariableValue" />
                </Environment>
            </Task>
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Também podem utilizar variáveis um [valor válido de Azure XPath](cloud-services-role-config-xpath.md) para fazer referência a algo sobre a implementação. Em vez de utilizar o `value` atributo, definir uma [RoleInstanceValue] elemento subordinado.

```xml
<Variable name="PathToStartupStorage">
    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
</Variable>
```


## <a name="configure-iis-startup-with-appcmdexe"></a>Configurar o arranque do IIS com AppCmd.exe
O [AppCmd.exe](https://technet.microsoft.com/library/jj635852.aspx) ferramenta da linha de comandos pode ser utilizada para gerir as definições do IIS durante o arranque no Azure. *AppCmd.exe* fornece acesso conveniente, da linha de comandos para as definições de configuração para utilização em tarefas de arranque no Azure. Utilizar *AppCmd.exe*, definições de Web site podem ser adicionadas, modificadas ou removidas de sites e aplicações.

No entanto, existem alguns aspetos a observar na utilização do *AppCmd.exe* como uma tarefa de arranque:

* Tarefas de arranque podem ser executadas mais do que uma vez entre reinícios. Por exemplo, quando uma função recicla.
* Se um *AppCmd.exe* ação é realizada mais do que uma vez, poderá gerar um erro. Por exemplo, a tentar adicionar uma secção para *Web. config* duas vezes, pode gerar um erro.
* Tarefas de arranque falharem se devolvem um código de saída diferente de zero ou **errorlevel**. Por exemplo, quando *AppCmd.exe* gera um erro.

É uma boa prática para verificar o **errorlevel** após chamar *AppCmd.exe*, que é fácil fazer se encapsular a chamada para *AppCmd.exe* com um *. cmd* ficheiro. Se detetar um conhecido **errorlevel** resposta, pode ignorá-lo ou transmiti-lo novamente.

Errorlevel devolvido pelo *AppCmd.exe* estão listados no ficheiro winerror.h e também podem ser vistos no [MSDN](https://msdn.microsoft.com/library/windows/desktop/ms681382.aspx).

### <a name="example-of-managing-the-error-level"></a>Exemplo de gerir o nível de erro
Este exemplo adiciona uma secção de compressão e uma entrada de compressão de JSON para o *Web. config* ficheiros, com o erro de processamento e o registo.

As secções relevantes do [servicedefinition. Csdef] ficheiro são mostradas aqui, que incluem a definição de [executionContext](https://msdn.microsoft.com/library/azure/gg557552.aspx#Task) atributo para `elevated` para dar *AppCmd.exe* permissões suficientes para alterar as definições no *Web. config* ficheiro:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="Startup.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

O *Startup.cmd* utiliza ficheiros de lote *AppCmd.exe* para adicionar uma secção de compressão e uma entrada de compressão para JSON para o *Web. config* ficheiro. Esperados **errorlevel** de 183 está definido como zero utilizando a verificar. Programa de linha de comandos. EXE. São registados StartupErrorLog.txt errorlevels inesperados.

```cmd
REM   *** Add a compression section to the Web.config file. ***
%windir%\system32\inetsrv\appcmd set config /section:urlCompression /doDynamicCompression:True /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1

REM   ERRORLEVEL 183 occurs when trying to add a section that already exists. This error is expected if this
REM   batch file were executed twice. This can occur and must be accounted for in a Azure startup
REM   task. To handle this situation, set the ERRORLEVEL to zero by using the Verify command. The Verify
REM   command will safely set the ERRORLEVEL to zero.
IF %ERRORLEVEL% EQU 183 DO VERIFY > NUL

REM   If the ERRORLEVEL is not zero at this point, some other error occurred.
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding a compression section to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Add compression for json. ***
%windir%\system32\inetsrv\appcmd set config  -section:system.webServer/httpCompression /+"dynamicTypes.[mimeType='application/json; charset=utf-8',enabled='True']" /commit:apphost >> "%TEMP%\StartupLog.txt" 2>&1
IF %ERRORLEVEL% EQU 183 VERIFY > NUL
IF %ERRORLEVEL% NEQ 0 (
    ECHO Error adding the JSON compression type to the Web.config file. >> "%TEMP%\StartupLog.txt" 2>&1
    GOTO ErrorExit
)

REM   *** Exit batch file. ***
EXIT /b 0

REM   *** Log error and exit ***
:ErrorExit
REM   Report the date, time, and ERRORLEVEL of the error.
DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
ECHO An error occurred during startup. ERRORLEVEL = %ERRORLEVEL% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT %ERRORLEVEL%
```

## <a name="add-firewall-rules"></a>Adicionar regras de firewall
No Azure, existem duas firewalls de forma eficaz. A firewall do primeiro controla ligações entre a máquina virtual e mundo externo. Este firewall é controlado pelo [pontos finais] elemento o [servicedefinition. Csdef] ficheiro.

A firewall do segundo controla ligações entre a máquina virtual e os processos de que a máquina virtual. Este firewall pode ser controlada através de `netsh advfirewall firewall` ferramenta da linha de comandos.

O Azure cria regras de firewall para os processos iniciadas as funções. Por exemplo, ao iniciar um serviço ou programa, o Azure cria automaticamente as regras de firewall necessárias para permitir que o serviço comunicar com a Internet. No entanto, se criar um serviço que é iniciado por um processo fora da sua função (como um serviço COM+ ou uma tarefa agendada do Windows), terá de criar manualmente uma regra de firewall para permitir o acesso a esse serviço. Estas regras de firewall podem ser criadas utilizando uma tarefa de arranque.

Uma tarefa de arranque que cria uma regra de firewall tem de ter um [executionContext][tarefas] de **elevada**. Adicione a seguinte tarefa de arranque para o [servicedefinition. Csdef] ficheiro.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WorkerRole name="WorkerRole1">
        ...
        <Startup>
            <Task commandLine="AddFirewallRules.cmd" executionContext="elevated" taskType="simple" />
        </Startup>
    </WorkerRole>
</ServiceDefinition>
```

Para adicionar a regra de firewall, tem de utilizar o adequado `netsh advfirewall firewall` comandos no seu ficheiro de batch de arranque. Neste exemplo, a tarefa de arranque necessita de encriptação e de segurança para a porta TCP 80.

```cmd
REM   Add a firewall rule in a startup task.

REM   Add an inbound rule requiring security and encryption for TCP port 80 traffic.
netsh advfirewall firewall add rule name="Require Encryption for Inbound TCP/80" protocol=TCP dir=in localport=80 security=authdynenc action=allow >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="block-a-specific-ip-address"></a>Bloquear um endereço IP específico
Pode restringir o acesso de função da web do Azure para um conjunto de endereços IP especificados modificando o IIS **Web. config** ficheiro. Terá também de utilizar um ficheiro de comandos que desbloqueia o **ipSecurity** secção o **applicationHost. config** ficheiro.

Para desbloquear o **ipSecurity** secção o **applicationHost. config** de ficheiros, crie um ficheiro de comandos que é executado no início da função. Crie uma pasta no nível de raiz da sua função web chamado **arranque** e, nesta pasta, crie um ficheiro batch chamado **startup.cmd**. Adicione este ficheiro para o projeto do Visual Studio e defina as propriedades **cópia sempre** para se certificar de que está incluído no seu pacote.

Adicione a seguinte tarefa de arranque para o [servicedefinition. Csdef] ficheiro.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
    <WebRole name="WebRole1">
        ...
        <Startup>
            <Task commandLine="startup.cmd" executionContext="elevated" />
        </Startup>
    </WebRole>
</ServiceDefinition>
```

Adicionar este comando para o **startup.cmd** ficheiro:

```cmd
@echo off
@echo Installing "IPv4 Address and Domain Restrictions" feature 
powershell -ExecutionPolicy Unrestricted -command "Install-WindowsFeature Web-IP-Security"
@echo Unlocking configuration for "IPv4 Address and Domain Restrictions" feature 
%windir%\system32\inetsrv\AppCmd.exe unlock config -section:system.webServer/security/ipSecurity
```

Esta tarefa faz com que o **startup.cmd** ficheiro batch para ser executado sempre que a função da web foi inicializada, garantindo que o necessário **ipSecurity** secção está desbloqueada.

Por fim, modifique o [secção System. webServer](http://www.iis.net/configreference/system.webserver/security/ipsecurity#005) a função da web **Web. config** ficheiro para adicionar uma lista de endereços IP que é concedido acesso, conforme mostrado no exemplo seguinte:

Esta configuração de exemplo **permite** todos os IPs para aceder ao servidor, exceto as duas definidos

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are granted access-->
    <ipSecurity>
        <!--The following IP addresses are denied access-->
        <add allowed="false" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="false" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

Esta configuração de exemplo **nega** todos os IPs de aceder ao servidor, exceto para os dois definidos.

```xml
<system.webServer>
    <security>
    <!--Unlisted IP addresses are denied access-->
    <ipSecurity allowUnlisted="false">
        <!--The following IP addresses are granted access-->
        <add allowed="true" ipAddress="192.168.100.1" subnetMask="255.255.0.0" />
        <add allowed="true" ipAddress="192.168.100.2" subnetMask="255.255.0.0" />
    </ipSecurity>
    </security>
</system.webServer>
```

## <a name="create-a-powershell-startup-task"></a>Criar uma tarefa de arranque do PowerShell
Scripts do Windows PowerShell não podem ser chamados diretamente a partir de [servicedefinition. Csdef] ficheiro, mas pode ser invocada a partir de dentro de um ficheiro de batch de arranque.

PowerShell (por predefinição) executar scripts não assinados. A menos que o se inscrever o seu script, terá de configurar o PowerShell para executar scripts não assinados. Para executar scripts não assinados, o **ExecutionPolicy** deve ser definido como **Unrestricted**. O **ExecutionPolicy** definição utilize baseia-se na versão do Windows PowerShell.

```cmd
REM   Run an unsigned PowerShell script and log the output
PowerShell -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

Se estiver a utilizar um SO convidado que é executado o PowerShell 2.0 ou 1.0 que pode forçar versão 2 para executar e, se disponível, utilize a versão 1.

```cmd
REM   Attempt to set the execution policy by using PowerShell version 2.0 syntax.
PowerShell -Version 2.0 -ExecutionPolicy Unrestricted .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   If PowerShell version 2.0 isn't available. Set the execution policy by using the PowerShell
IF %ERRORLEVEL% EQU -393216 (
   PowerShell -Command "Set-ExecutionPolicy Unrestricted" >> "%TEMP%\StartupLog.txt" 2>&1
   PowerShell .\startup.ps1 >> "%TEMP%\StartupLog.txt" 2>&1
)

REM   If an error occurred, return the errorlevel.
EXIT /B %errorlevel%
```

## <a name="create-files-in-local-storage-from-a-startup-task"></a>Criar ficheiros no armazenamento local a partir de uma tarefa de arranque
Pode utilizar um recurso de armazenamento local para armazenar ficheiros criados pela tarefa de arranque que é acedida mais tarde pela sua aplicação.

Para criar o recurso de armazenamento local, adicione um [LocalResources] secção para o [servicedefinition. Csdef] de ficheiros e, em seguida, adicione o [LocalStorage] elemento subordinado. Conceda o recurso de armazenamento local para um nome único e um tamanho adequado para a tarefa de arranque.

Para utilizar um recurso de armazenamento local na sua tarefa de arranque, terá de criar uma variável de ambiente para fazer referência a localização de recursos de armazenamento local. Em seguida, a tarefa de arranque e a aplicação conseguem ler e escrever em ficheiros para o recurso de armazenamento local.

As secções relevantes do **servicedefinition. Csdef** ficheiro são mostradas aqui:

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">
    ...

    <LocalResources>
      <LocalStorage name="StartupLocalStorage" sizeInMB="5"/>
    </LocalResources>

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="PathToStartupStorage">
            <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='StartupLocalStorage']/@path" />
          </Variable>
        </Environment>
      </Task>
    </Startup>
  </WorkerRole>
</ServiceDefinition>
```

Por exemplo, isto **Startup.cmd** batch ficheiro utiliza o **PathToStartupStorage** variável de ambiente para criar o ficheiro **MyTest.txt** na localização de armazenamento local.

```cmd
REM   Create a simple text file.

ECHO This text will go into the MyTest.txt file which will be in the    >  "%PathToStartupStorage%\MyTest.txt"
ECHO path pointed to by the PathToStartupStorage environment variable.  >> "%PathToStartupStorage%\MyTest.txt"
ECHO The contents of the PathToStartupStorage environment variable is   >> "%PathToStartupStorage%\MyTest.txt"
ECHO "%PathToStartupStorage%".                                          >> "%PathToStartupStorage%\MyTest.txt"

REM   Exit the batch file with ERRORLEVEL 0.

EXIT /b 0
```

Pode aceder a pasta de armazenamento local do SDK do Azure utilizando o [GetLocalResource](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) método.

```csharp
string localStoragePath = Microsoft.WindowsAzure.ServiceRuntime.RoleEnvironment.GetLocalResource("StartupLocalStorage").RootPath;

string fileContent = System.IO.File.ReadAllText(System.IO.Path.Combine(localStoragePath, "MyTestFile.txt"));
```

## <a name="run-in-the-emulator-or-cloud"></a>Executar o emulador ou a nuvem
Pode ter a tarefa de arranque efetuar outros passos quando a operação é efetuada na nuvem em comparação com quando estiver a ser o emulador de computação. Por exemplo, poderá utilizar uma cópia nova dos seus dados do SQL Server apenas quando executado no emulador. Em alternativa, pode efetuar algumas otimizações de desempenho para a nuvem que não precisa de fazer quando em execução no emulador.

Esta capacidade para efetuar ações diferentes no emulador de computação e a nuvem, pode ser conseguida através da criação de uma variável de ambiente no [servicedefinition. Csdef] ficheiro. Em seguida, testar essa variável de ambiente para um valor na sua tarefa de arranque.

Para criar a variável de ambiente, adicione o [variável]/[RoleInstanceValue] elemento e criar um valor XPath `/RoleEnvironment/Deployment/@emulated`. O valor da **% ComputeEmulatorRunning %** variável de ambiente é `true` quando em execução no emulador de computação, e `false` quando em execução na nuvem.

```xml
<ServiceDefinition name="MyService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WorkerRole name="WorkerRole1">

    ...

    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>
          <Variable name="ComputeEmulatorRunning">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
          </Variable>
        </Environment>
      </Task>
    </Startup>

  </WorkerRole>
</ServiceDefinition>
```

A tarefa agora pode verificar o **% ComputeEmulatorRunning %** variável de ambiente para executar ações diferentes com base em se a função está em execução na nuvem ou o emulador. Eis um script de shell. cmd que verifica a existência essa variável de ambiente.

```cmd
REM   Check if this task is running on the compute emulator.

IF "%ComputeEmulatorRunning%" == "true" (
    REM   This task is running on the compute emulator. Perform tasks that must be run only in the compute emulator.
) ELSE (
    REM   This task is running on the cloud. Perform tasks that must be run only in the cloud.
)
```


## <a name="detect-that-your-task-has-already-run"></a>Detetar se a sua tarefa já foi executada
A função pode reciclar sem reinício fazendo com que as tarefas de arranque ser executada novamente. Não há nenhum sinalizador que indica se uma tarefa já foi executada na VM de alojamento. Pode ter algumas tarefas onde é irrelevante que executam várias vezes. No entanto, poderá ter uma situação em que precisa de impedir que uma tarefa em execução mais do que uma vez.

É a forma mais simples para detetar se uma tarefa já foi executada para criar um ficheiro no **% TEMP %** pasta quando a tarefa é concluída com êxito e procure-lo no início da tarefa. Eis um script de shell cmd de exemplo que faz que por si.

```cmd
REM   If Task1_Success.txt exists, then Application 1 is already installed.
IF EXIST "%RoleRoot%\Task1_Success.txt" (
  ECHO Application 1 is already installed. Exiting. >> "%TEMP%\StartupLog.txt" 2>&1
  GOTO Finish
)

REM   Run your real exe task
ECHO Running XYZ >> "%TEMP%\StartupLog.txt" 2>&1
"%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (
  REM   The application installed without error. Create a file to indicate that the task
  REM   does not need to be run again.

  ECHO This line will create a file to indicate that Application 1 installed correctly. > "%RoleRoot%\Task1_Success.txt"

) ELSE (
  REM   An error occurred. Log the error and exit with the error code.

  DATE /T >> "%TEMP%\StartupLog.txt" 2>&1
  TIME /T >> "%TEMP%\StartupLog.txt" 2>&1
  ECHO  An error occurred running task 1. Errorlevel = %ERRORLEVEL%. >> "%TEMP%\StartupLog.txt" 2>&1

  EXIT %ERRORLEVEL%
)

:Finish

REM   Exit normally.
EXIT /B 0
```

## <a name="task-best-practices"></a>Melhores práticas de tarefas
Seguem-se algumas melhores práticas que deve seguir quando configurar tarefas para a função web ou de trabalho.

### <a name="always-log-startup-activities"></a>Sempre atividades de arranque do registo
Visual Studio não fornece um depurador para seguir ficheiros batch, pelo que é boa obter o mesmo dados sobre a operação de ficheiros batch quanto possível. Registo de saída do ficheiros batch, ambos **stdout** e **stderr**, pode fornecer informações importantes ao tentar a depuração e corrigir ficheiros batch. Para iniciar sessão ambos **stdout** e **stderr** para o StartupLog.txt ficheiro no diretório indicada pelo **% TEMP %** variável de ambiente, adicione o texto `>>  "%TEMP%\\StartupLog.txt" 2>&1` ao fim de linhas específicas que pretende iniciar sessão. Por exemplo, para executar o setup.exe no **% PathToApp1Install %** diretório:

    "%PathToApp1Install%\setup.exe" >> "%TEMP%\StartupLog.txt" 2>&1

Para simplificar o xml, pode criar um wrapper *cmd* tarefas juntamente com o registo de ficheiros que chama todas o arranque e assegura a cada tarefa subordinada partilha as mesmo variáveis de ambiente.

Pode considerar um pouco aborrecidos para utilizar `>> "%TEMP%\StartupLog.txt" 2>&1` no final de cada tarefa de arranque. Pode impor o registo de tarefas através da criação de um dispositivo de moldagem que processa o registo para si. Este invólucro chama o ficheiro real batch que pretende executar. Qualquer saída do ficheiro de batch de destino será redirecionada para o *Startuplog.txt* ficheiro.

O exemplo seguinte mostra como redirecionar todos os resultados de um ficheiro de batch de arranque. Neste exemplo, o ficheiro ServerDefinition.csdef cria uma tarefa de arranque que chama *logwrap.cmd*. *logwrap.cmd* chamadas *Startup2.cmd*, redirecionar todas as saídas para **% TEMP %\\StartupLog.txt**.

ServiceDefinition.cmd:

```xml
<Startup>
    <Task commandLine="logwrap.cmd startup2.cmd" executionContext="limited" taskType="simple" />
</Startup>
```

**logwrap.cmd:**

```cmd
@ECHO OFF

REM   logwrap.cmd calls passed in batch file, redirecting all output to the StartupLog.txt log file.

ECHO [%date% %time%] == START logwrap.cmd ============================================== >> "%TEMP%\StartupLog.txt" 2>&1
ECHO [%date% %time%] Running %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Call the child command batch file, redirecting all output to the StartupLog.txt log file.
START /B /WAIT %1 >> "%TEMP%\StartupLog.txt" 2>&1

REM   Log the completion of child command.
ECHO [%date% %time%] Done >> "%TEMP%\StartupLog.txt" 2>&1

IF %ERRORLEVEL% EQU 0 (

   REM   No errors occurred. Exit logwrap.cmd normally.
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B 0

) ELSE (

   REM   Log the error.
   ECHO [%date% %time%] An error occurred. The ERRORLEVEL = %ERRORLEVEL%.  >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO [%date% %time%] == END logwrap.cmd ================================================ >> "%TEMP%\StartupLog.txt" 2>&1
   ECHO.  >> "%TEMP%\StartupLog.txt" 2>&1
   EXIT /B %ERRORLEVEL%

)
```

**Startup2.cmd:**

```cmd
@ECHO OFF

REM   This is the batch file where the startup steps should be performed. Because of the
REM   way Startup2.cmd was called, all commands and their outputs will be stored in the
REM   StartupLog.txt file in the directory pointed to by the TEMP environment variable.

REM   If an error occurs, the following command will pass the ERRORLEVEL back to the
REM   calling batch file.

ECHO [%date% %time%] Some log information about this task
ECHO [%date% %time%] Some more log information about this task

EXIT %ERRORLEVEL%
```

Exemplo de saída no **StartupLog.txt** ficheiro:

```txt
[Mon 10/17/2016 20:24:46.75] == START logwrap.cmd ============================================== 
[Mon 10/17/2016 20:24:46.75] Running command1.cmd 
[Mon 10/17/2016 20:24:46.77] Some log information about this task
[Mon 10/17/2016 20:24:46.77] Some more log information about this task
[Mon 10/17/2016 20:24:46.77] Done 
[Mon 10/17/2016 20:24:46.77] == END logwrap.cmd ================================================ 
```

> [!TIP]
> O **StartupLog.txt** ficheiro está localizado no *C:\Resources\temp\\{identificador de função} \RoleTemp* pasta.
> 
> 

### <a name="set-executioncontext-appropriately-for-startup-tasks"></a>Definir executionContext adequadamente para tarefas de arranque
Conjunto de privilégios adequadamente para a tarefa de arranque. Por vezes, tarefas de arranque tem de executar com privilégios elevados, apesar da função é executado com privilégios normais.

O [executionContext][tarefas] atributo define o nível de privilégios da tarefa de arranque. Utilizar `executionContext="limited"` significa que a tarefa de arranque tem o mesmo nível de privilégios, como a função. Utilizar `executionContext="elevated"` significa que a tarefa de arranque tem privilégios de administrador, que permite que a tarefa de arranque efetuar tarefas de administrador sem conceder privilégios de administrador à sua função.

Um exemplo de uma tarefa de arranque que necessita de privilégios elevados é uma tarefa de arranque que utiliza **AppCmd.exe** para configurar o IIS. **AppCmd.exe** requer `executionContext="elevated"`.

### <a name="use-the-appropriate-tasktype"></a>Utilize o taskType adequado
O [taskType][tarefas] atributo determina a forma como a tarefa de arranque é executado. Existem três valores: **simples**, **em segundo plano**, e **em primeiro plano**. As tarefas em segundo plano e em primeiro plano são iniciadas de forma assíncrona e, em seguida, as tarefas simples são executadas de forma síncrona um de cada vez.

Com **simples** tarefas de arranque, pode definir a ordem pela qual as tarefas executadas pela ordem em que as tarefas estão listadas no ficheiro servicedefinition. Csdef. Se um **simples** tarefas termina com um código de saída diferente de zero, em seguida, a paragem do procedimento de arranque e a função não iniciar.

A diferença entre **em segundo plano** tarefas de arranque e **em primeiro plano** tarefas de arranque é que **em primeiro plano** tarefas manter a função em execução até o **em primeiro plano** termina de tarefas. Isto também significa que, se o **em primeiro plano** tarefas bloqueia ou falhas, a função não reciclará até o **em primeiro plano** é forçada a tarefa fechado. Por este motivo, **em segundo plano** tarefas são recomendadas para tarefas de arranque assíncrona, a menos que tem essa funcionalidade do **em primeiro plano** tarefas.

### <a name="end-batch-files-with-exit-b-0"></a>Ficheiros de batch de fim com saída/b 0
A função apenas será iniciado se o **errorlevel** de cada um dos seus arranque simple tarefas é zero. Nem todos os programas definido o **errorlevel** (código de saída) corretamente, por isso, o ficheiro batch deve terminar com um `EXIT /B 0` se tudo executado corretamente.

Um em falta `EXIT /B 0` no final de um lote de arranque o ficheiro é uma causa comum das funções que não são iniciados.

> [!NOTE]
> Posso ter reparado que batch aninhada ficheiros bloquear, por vezes, ao utilizar o `/B` parâmetro. Poderá pretender certificar-se de que este problema hang não acontecer se o outro ficheiro batch chama o ficheiro atual do batch, como se utilizar o [wrapper do registo](#always-log-startup-activities). Pode omitir o `/B` parâmetro neste caso.
> 
> 

### <a name="expect-startup-tasks-to-run-more-than-once"></a>Tarefas de arranque para execução mais do que uma vez de esperar
Nem todas as funções recicla incluem um reinício, mas todas as funções recicla incluir todas as tarefas de arranque em execução. Isto significa que as tarefas de arranque tem de ser capazes de executar várias vezes entre reinícios sem quaisquer problemas. Este aspeto será abordado o [anterior a secção](#detect-that-your-task-has-already-run).

### <a name="use-local-storage-to-store-files-that-must-be-accessed-in-the-role"></a>Utilize o armazenamento local para armazenar os ficheiros que tem de ser acedidos na função
Se pretender copiar ou crie um ficheiro durante a tarefa de arranque, em seguida, é acessível à sua função, em seguida, se o ficheiro tem de ser colocado no armazenamento local. Consulte o [anterior a secção](#create-files-in-local-storage-from-a-startup-task).

## <a name="next-steps"></a>Passos seguintes
Reveja a nuvem [modelo e o pacote de serviço](cloud-services-model-and-package.md)

Saiba mais sobre como [tarefas](cloud-services-startup-tasks.md) funcione.

[Criar e implementar](cloud-services-how-to-create-deploy-portal.md) seu pacote de serviço em nuvem.

[servicedefinition. Csdef]: cloud-services-model-and-package.md#csdef
[tarefas]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[Startup]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
[Pontos finais]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Endpoints
[LocalStorage]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalStorage
[LocalResources]: https://msdn.microsoft.com/library/azure/gg557552.aspx#LocalResources
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
