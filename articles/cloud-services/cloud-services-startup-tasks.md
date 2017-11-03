---
title: "Executar tarefas de arranque nos serviços em nuvem do Azure | Microsoft Docs"
description: "Tarefas de arranque ajudam a preparar o ambiente de serviço em nuvem para a sua aplicação. Isto informa como funcionam as tarefas de arranque e como para torná-las"
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 886939be-4b5b-49cc-9a6e-2172e3c133e9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: adegeo
ms.openlocfilehash: 1c1b3aa86dc8211de0c07c9fb68da5685c86f551
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-and-run-startup-tasks-for-a-cloud-service"></a>Como configurar e executar tarefas de arranque para um serviço em nuvem
Pode utilizar tarefas de arranque para executar operações antes de começa a uma função. As operações que poderá querer efetuar incluem instalar um componente, registar componentes COM, definir as chaves de registo ou iniciar um processo de execução longa.

> [!NOTE]
> Tarefas de arranque não são aplicáveis para máquinas virtuais, apenas a Web do serviço de nuvem e as funções de trabalho.
> 
> 

## <a name="how-startup-tasks-work"></a>Como funcionam as tarefas de arranque
Tarefas de arranque são ações efetuadas antes das funções de começarem e estão definidas no [servicedefinition. Csdef] ficheiro utilizando o [tarefas] elemento o [arranque] elemento. Frequentemente tarefas de arranque são ficheiros batch, mas também podem ser aplicações de consola ou ficheiros de batch que começam scripts do PowerShell.

As variáveis de ambiente são transmitidas informações para uma tarefa de arranque e armazenamento local pode ser utilizado para transmitir informações fora de uma tarefa de arranque. Por exemplo, uma variável de ambiente pode especificar o caminho para um programa que pretende instalar e os ficheiros podem ser gravados no armazenamento local que, em seguida, pode ser lidos mais tarde pelas suas funções.

A tarefa de arranque pode iniciar sessão e erros de informações para o diretório especificado pelo **TEMP** variável de ambiente. Durante a tarefa de arranque, o **TEMP** variável de ambiente é resolvido para o *c:\\recursos\\temp\\[guid]. [ rolename]\\RoleTemp* diretório quando executado na nuvem.

Tarefas de arranque podem também ser executadas várias vezes entre reinícios. Por exemplo, a tarefa de arranque será executada sempre que a função recicla e recicla de função não pode incluir sempre um reinício. Tarefas de arranque devem ser escritas uma forma que permite-lhes para executarem várias vezes sem problemas.

Tarefas de arranque tem de terminar com um **errorlevel** (ou código de saída) de zero para a conclusão do processo de arranque. Se uma tarefa de arranque termina com um diferente de zero **errorlevel**, a função não será iniciada.

## <a name="role-startup-order"></a>Sequência de arranque de função
Segue-se o procedimento de arranque de função no Azure:

1. A instância está marcada como **inicial** e não receber o tráfego.
2. Todas as tarefas de arranque são executadas de acordo com os respetivos **taskType** atributo.
   
   * O **simples** tarefas são executadas de forma síncrona, um de cada vez.
   * O **em segundo plano** e **em primeiro plano** tarefas são iniciadas no modo assíncrono, efetuada em paralelo para a tarefa de arranque.  
     
     > [!WARNING]
     > IIS podem não estar totalmente configuradas durante a fase de tarefa de arranque no processo de arranque, pelo que os dados específicos da função poderão não estar disponíveis. Devem utilizar tarefas de arranque que necessitam de dados específicos da função [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).
     > 
     > 
3. Iniciar o processo de anfitrião de função e o site é criado no IIS.
4. O [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) método é chamado.
5. A instância está marcada como **pronto** e o tráfego é encaminhado para a instância.
6. O [Microsoft.WindowsAzure.ServiceRuntime.RoleEntryPoint.Run](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx) método é chamado.

## <a name="example-of-a-startup-task"></a>Exemplo de uma tarefa de arranque
Tarefas de arranque são definidas no [servicedefinition. Csdef] no ficheiro de **tarefas** elemento. O **commandLine** atributo especifica o nome e os parâmetros do arranque batch ficheiro ou a consola de comando, o **executionContext** atributo especifica o nível de privilégios da tarefa de arranque e o **taskType** atributo especifica como a tarefa será executada.

Neste exemplo, uma variável de ambiente, **MyVersionNumber**, é criado para a tarefa de arranque e definido para o valor "**1.0.0.0**".

**Servicedefinition. Csdef**:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
        <Environment>
            <Variable name="MyVersionNumber" value="1.0.0.0" />
        </Environment>
    </Task>
</Startup>
```

No exemplo seguinte, o **Startup.cmd** ficheiro batch escreve na linha "a versão atual é 1.0.0.0" para o ficheiro StartupLog.txt no diretório especificado pela variável de ambiente TEMP. O `EXIT /B 0` linha garante que a tarefa de arranque termina com um **errorlevel** igual a zero.

```cmd
ECHO The current version is %MyVersionNumber% >> "%TEMP%\StartupLog.txt" 2>&1
EXIT /B 0
```

> [!NOTE]
> No Visual Studio, o **copiar para o diretório de saída** propriedade para o ficheiro de batch de arranque deve ser definida como **cópia sempre** para Certifique-se de que o ficheiro de batch de arranque está corretamente implementado o projeto no Azure (**approot\\bin** para funções da Web, e **approot** para as funções de trabalho).
> 
> 

## <a name="description-of-task-attributes"></a>Descrição de atributos de tarefas
O seguinte descreve os atributos do **tarefas** elemento o [servicedefinition. Csdef] ficheiro:

**commandLine** -Especifica a linha de comandos para a tarefa de arranque:

* O comando, com parâmetros de linha de comandos opcionais, que começa a tarefa de arranque.
* Frequentemente este é o nome de ficheiro de um ficheiro de batch. cmd ou. bat.
* A tarefa é relativo à AppRoot\\pasta Bin para a implementação. As variáveis de ambiente não são expandidas determinar o caminho e o ficheiro da tarefa. Se for necessária a expansão de ambiente, pode criar um script de pequenas. cmd que chama a tarefa de arranque.
* Pode ser uma aplicação de consola ou um ficheiro de batch que inicia uma [script do PowerShell](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task).

**executionContext** -Especifica o nível de privilégio para a tarefa de arranque. O nível de privilégio pode ser limitado ou elevado:

* **limitado**  
  A tarefa de arranque é executado com privilégios mesmos que a função. Quando o **executionContext** atributo para o [Runtime] elemento também é **limitado**, e privilégios de utilizador são utilizados.
* **elevada**  
  A tarefa de arranque é executado com privilégios de administrador. Isto permite que as tarefas de arranque para instalar os programas, efetuar alterações de configuração do IIS, efetuar as alterações ao registo e outras tarefas de nível de administrador, sem aumentar o nível de privilégio da função de si próprio.  

> [!NOTE]
> O nível de privilégios de uma tarefa de arranque não precisa de ser o mesmo que a função de si próprio.
> 
> 

**taskType** -Especifica a forma como é executada uma tarefa de arranque.

* **simples**  
  As tarefas são executadas de forma síncrona, um de cada vez, pela ordem especificada no [servicedefinition. Csdef] ficheiro. Quando um **simples** tarefa de arranque termina com um **errorlevel** de zero, a próxima **simples** é executada a tarefa de arranque. Se existirem mais **simples** tarefas de arranque para executar, em seguida, a função de si próprio será iniciada.   
  
  > [!NOTE]
  > Se o **simples** tarefas termina com um diferente de zero **errorlevel**, a instância será bloqueada. Subsequentes **simples** tarefas de arranque e a função de si próprio, não serão iniciada.
  > 
  > 
  
    Para se certificar de que o ficheiro batch termina com um **errorlevel** de zero, execute o comando `EXIT /B 0` no final do seu processo de ficheiro batch.
* **em segundo plano**  
  As tarefas são executadas no modo assíncrono, em paralelo com o arranque da função.
* **em primeiro plano**  
  As tarefas são executadas no modo assíncrono, em paralelo com o arranque da função. A principal diferença entre um **em primeiro plano** e um **em segundo plano** tarefa é que um **em primeiro plano** tarefas impede a função de reciclagem ou encerrar até que a tarefa foi terminado. O **em segundo plano** tarefas não têm esta restrição.

## <a name="environment-variables"></a>Variáveis de ambiente
As variáveis de ambiente são uma forma para passar informações para uma tarefa de arranque. Por exemplo, pode colocar o caminho para um blob que contém um programa para instalar, ou números de porta que irá utilizar a função ou as definições para controlar as funcionalidades da tarefa de arranque.

Existem dois tipos de variáveis de ambiente para tarefas de arranque variáveis de ambiente estático e variáveis de ambiente com base em membros do [ RoleEnvironment] classe. Ambos estão no [ambiente] secção o [servicedefinition. Csdef] ficheiro e ambas as utilize o [variável] elemento e **nome** atributo.

As variáveis de ambiente estático utiliza o **valor** atributo do [variável] elemento. O exemplo anterior cria a variável de ambiente **MyVersionNumber** que tem o valor estático "**1.0.0.0**". Outro exemplo seria possível criar um **StagingOrProduction** variável de ambiente que pode configurar manualmente os valores de "**transição**"ou"**produção**" para executar ações de arranque diferentes com base no valor da **StagingOrProduction** variável de ambiente.

Variáveis de ambiente com base em membros da classe RoleEnvironment não utilizem o **valor** atributo o [variável] elemento. Em vez disso, o [RoleInstanceValue] elemento subordinado, com as adequadas **XPath** valor de atributo, são utilizados para criar uma variável de ambiente com base no membro específico do [ RoleEnvironment] classe. Os valores para o **XPath** atributo para aceder a várias [ RoleEnvironment] valores podem ser encontrados [aqui](cloud-services-role-config-xpath.md).

Por exemplo, para criar uma variável de ambiente que é "**verdadeiro**" quando a instância está a executar o emulador de computação, e "**falso**" quando em execução na nuvem, utilize o seguinte [variável] e [RoleInstanceValue] elementos:

```xml
<Startup>
    <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple">
        <Environment>

            <!-- Create the environment variable that informs the startup task whether it is running
                in the Compute Emulator or in the cloud. "%ComputeEmulatorRunning%"=="true" when
                running in the Compute Emulator, "%ComputeEmulatorRunning%"=="false" when running
                in the cloud. -->

            <Variable name="ComputeEmulatorRunning">
                <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated" />
            </Variable>

        </Environment>
    </Task>
</Startup>
```

## <a name="next-steps"></a>Passos seguintes
Saiba como efetuar algumas [tarefas comuns de arranque](cloud-services-startup-tasks-common.md) com o serviço de nuvem.

[Pacote](cloud-services-model-and-package.md) seu serviço em nuvem.  

[servicedefinition. Csdef]: cloud-services-model-and-package.md#csdef
[tarefas]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Task
[arranque]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Startup
[Runtime]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Runtime
[ambiente]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Environment
[variável]: https://msdn.microsoft.com/library/azure/gg557552.aspx#Variable
[RoleInstanceValue]: https://msdn.microsoft.com/library/azure/gg557552.aspx#RoleInstanceValue
[ RoleEnvironment]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.aspx
