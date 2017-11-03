---
title: "Iniciar criação de soluções do Batch com modelos de projeto do Visual Studio - Azure | Microsoft Docs"
description: "Saiba como modelos de projeto do Visual Studio podem ajudá-lo de implementar e executar as cargas de trabalho intensivas de computação do Azure batch."
services: batch
documentationcenter: .net
author: fayora
manager: timlt
editor: 
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: da77ce827c65deb18d9d84ce5cf768d89788e205
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Utilizar modelos de projeto do Visual Studio para iniciar os soluções do Batch

O **Gestor de tarefas** e **modelos de tarefas processador Visual Studio** lote fornecer o código para o ajudar a implementar e executar as cargas de trabalho intensivas de computação no Batch com o mínimo de esforço. Este documento descreve estes modelos e fornece orientações para como utilizá-los.

> [!IMPORTANT]
> Este artigo aborda apenas aplicáveis a estes dois modelos de informações e parte do princípio de que está familiarizado com o serviço Batch e conceitos chave relacionadas com a mesma: conjuntos, nós de computação, trabalhos e tarefas, tarefas do Gestor, variáveis de ambiente e outros relevantes informações. Pode encontrar mais informações em [Noções básicas do Azure Batch](batch-technical-overview.md), [descrição geral da funcionalidade Batch para programadores](batch-api-basics.md), e [introdução à biblioteca do Azure Batch para .NET](batch-dotnet-get-started.md).
> 
> 

## <a name="high-level-overview"></a>Descrição geral de alto nível
Os modelos de Gestor de tarefas e tarefas processador podem ser utilizados para criar dois componentes úteis:

* Uma tarefa do Gestor de tarefas que implementa o divisor uma tarefa que pode dividir uma tarefa em várias tarefas que podem ser executadas de forma independente, em paralelo.
* Um processador de tarefas que pode ser utilizado para efetuar pré-processadas e processamento pós-cópia em torno de uma linha de comandos de aplicação.

Por exemplo, num cenário de composição de filmes, o divisor tarefa seria ativar uma tarefa de filmes único em centenas ou milhares de tarefas separadas que seriam processam frames individuais separadamente. Proporcionalmente, o processador de tarefa invocará a aplicação de composição e todos os processos dependentes que são necessárias para compor cada moldura, bem como realizar quaisquer ações adicionais (por exemplo, a cópia da moldura composta para uma localização de armazenamento).

> [!NOTE]
> Os modelos de Gestor de tarefas e o processador de tarefas são independentes entre si, pelo que pode optar por utilizar ambos ou apenas um deles, consoante os requisitos do seu trabalho de computação e nas suas preferências.
> 
> 

Como é mostrado no diagrama abaixo, uma tarefa de computação que utiliza estes modelos passarão três fases:

1. O código de cliente (por exemplo, aplicações, serviço web, etc.) submete uma tarefa para o serviço Batch no Azure, especificando como a tarefa do Gestor de tarefas o programa de Gestor de tarefas.
2. O serviço Batch executa a tarefa do Gestor de tarefas num nó de computação e o divisor tarefa inicia o número de tarefas de processador de tarefas, especificado no como muitas nós de computação conforme necessário, com base nos parâmetros e as especificações no código do divisor da tarefa.
3. As tarefas de processador de tarefas de forma independente, executam em paralelo, para processar os dados de entrada e gerar os dados de saída.

![Diagrama que mostra como o código de cliente interage com o serviço Batch][diagram01]

## <a name="prerequisites"></a>Pré-requisitos
Para utilizar os modelos do Batch, irá precisar do seguinte:

* Um computador com o Visual Studio 2015, instalado. Os modelos de lote são atualmente suportados apenas para o Visual Studio 2015.
* Os modelos do Batch, que estão disponíveis a partir de [galeria do Visual Studio] [ vs_gallery] como extensões de Visual Studio. Existem duas formas de obter os modelos:
  
  * Instalar os modelos utilizando o **extensões e atualizações** caixa de diálogo no Visual Studio (para obter mais informações, consulte [a localizar e utilizar extensões de Visual Studio][vs_find_use_ext]). No **extensões e atualizações** diálogo caixa, procurar e transferir as extensões de dois seguintes:
    
    * Gestor de tarefas de lote do Azure com o divisor da tarefa
    * Processador de tarefa de lote do Azure
  * Transferir os modelos da galeria do online para o Visual Studio: [modelos de projeto do Microsoft Azure Batch][vs_gallery_templates]
* Se planeia utilizar o [pacotes de aplicações](batch-application-packages.md) funcionalidade para implementar o Gestor de tarefas e nós de computação de processador de tarefas para o Batch, precisa de associar uma conta de armazenamento à sua conta do Batch.

## <a name="preparation"></a>Preparação
Recomendamos a criação de uma solução que pode conter o seu Gestor de tarefas, bem como o processador de tarefa, porque esta pode tornar mais fácil partilhar código entre o Gestor de tarefas e programas de processador de tarefas. Para criar esta solução, siga estes passos:

1. Abra o Visual Studio e selecione **ficheiro** > **novo** > **projeto**.
2. Em **modelos**, expanda **outros tipos de projeto**, clique em **soluções do Visual Studio**e, em seguida, selecione **solução em branco**.
3. Escreva um nome que descreve a aplicação e o objetivo desta solução (por exemplo, "LitwareBatchTaskPrograms").
4. Para criar a nova solução, clique em **OK**.

## <a name="job-manager-template"></a>Modelo de Gestor de tarefas
O modelo de Gestor de tarefas ajuda-o a implementar uma tarefa do Gestor de tarefas que pode realizar as seguintes ações:

* Divida uma tarefa em várias tarefas.
* Submeta as tarefas necessárias para executar no Batch.

> [!NOTE]
> Para mais informações sobre o Gestor de tarefas, consulte [descrição geral da funcionalidade Batch para programadores](batch-api-basics.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Criar um Gestor de tarefas utilizando o modelo
Para adicionar um Gestor de tarefas para a solução que criou anteriormente, siga estes passos:

1. Abra a solução existente no Visual Studio.
2. No Explorador de soluções, clique com botão direito na solução, clique em **adicionar** > **novo projeto**.
3. Em **Visual c#**, clique em **nuvem**e, em seguida, clique em **Gestor de tarefas de lote do Azure com a tarefa divisor**.
4. Escreva um nome que descreve a aplicação e identifica este projeto como o Gestor de tarefas (por exemplo "LitwareJobManager").
5. Para criar o projeto, clique em **OK**.
6. Por fim, crie o projeto para forçar o Visual Studio para carregar todos os pacotes de NuGet referenciados e certifique-se de que o projeto é válido antes de começar a modificá-lo.

### <a name="job-manager-template-files-and-their-purpose"></a>Ficheiros de modelo de Gestor de tarefas e respetivo objetivo
Quando cria um projeto com o modelo de Gestor de tarefas, gera três grupos de ficheiros de código:

* O ficheiro de programa principal (Program.cs). Contém o ponto de entrada do programa e processamento de exceções de nível superior. Normalmente, não deve necessitar de modificar esta.
* O diretório Framework. Contém os ficheiros responsáveis para o trabalho de 'automático' efetuado pelo programa de Gestor de tarefas – unpacking parâmetros, adicionar tarefas para a tarefa do Batch, etc. Normalmente, não deve necessitar de modificar estes ficheiros.
* O ficheiro de divisor de tarefa (JobSplitter.cs). Esta é a localização onde irá colocar a lógica de aplicação específicos para dividir uma tarefa para tarefas.

Obviamente pode adicionar ficheiros adicionais conforme necessário para suportar o código de divisor de tarefa, dependendo da complexidade da tarefa dividir lógica.

O modelo gera também padrão ficheiros de projeto de .NET como um ficheiro. csproj, App. config, Packages, etc.

As restantes desta secção descreve os diferentes ficheiros e a respetiva estrutura de código e explica o que faz cada classe.

![Visual Studio Solution Explorer com o Gestor de tarefas a solução de modelo][solution_explorer01]

**Ficheiros de Framework**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de tarefas, tais como os detalhes da conta de Batch, as credenciais da conta de armazenamento ligado, o trabalho e informações de tarefas e parâmetros da tarefa. Também fornece acesso a variáveis de ambiente definidas pelo Batch (consulte as definições de ambiente para tarefas, na documentação do Batch) através da classe de Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Deduz a implementação da classe de configuração, para que pode o teste da unidade do divisor da tarefa com um objeto de configuração falsificados ou mock.
* `JobManager.cs`: Orquestra os componentes do programa de Gestor de tarefas. É responsável por a inicializar o divisor de tarefa, invocar o divisor de tarefa e emitir as tarefas devolvidas pelo divisor da tarefa para o submissor da tarefa.
* `JobManagerException.cs`: Representa um erro que exige o Gestor de tarefas terminar. JobManagerException é utilizado para encapsular erros 'esperados' onde as informações de diagnóstico específicas podem ser fornecidas como parte de terminação.
* `TaskSubmitter.cs`: Esta classe é da responsabilidade adicionar tarefas devolvidas pelo divisor da tarefa para a tarefa de lote. As agregações de classe JobManager a sequência de tarefas em lotes para eficiente mas atempada adição da tarefa, em seguida, chama TaskSubmitter.SubmitTasks no thread de segundo plano para cada lote.

**Tarefa divisor**

`JobSplitter.cs`: Esta classe contém específicas da aplicação lógica para dividir a tarefa para tarefas. A estrutura invoca o método JobSplitter.Split para obter uma sequência de tarefas, que adiciona à tarefa como o método devolve-os. Esta é a classe onde irá inserir a lógica do seu trabalho. Implemente o método de divisão para devolver uma sequência de instâncias de CloudTask que representa as tarefas para o qual pretende a tarefa de partição.

**Ficheiros de projeto de linha de comandos do padrão .NET**

* `App.config`: Ficheiro de configuração da aplicação .NET padrão.
* `Packages.config`: Ficheiro de dependência de pacote NuGet padrão.
* `Program.cs`: Contém o ponto de entrada do programa e processamento de exceções de nível superior.

### <a name="implementing-the-job-splitter"></a>Implementar o divisor da tarefa
Quando abrir o projeto de modelo de Gestor de tarefas, o projeto terá o ficheiro de JobSplitter.cs abrir por predefinição. Pode implementar a lógica de divisão para as tarefas na sua carga de trabalho utilizando a mostrar de método Split() abaixo:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> A secção anotada o `Split()` método é a secção apenas o código de modelo de Gestor de tarefas que pretende modificar adicionando a lógica de dividir as tarefas para tarefas diferentes. Se pretender modificar uma secção diferente do modelo, certifique-se são familiarized com como funciona o Batch e experimente algumas do [exemplos de código do Batch][github_samples].
> 
> 

A implementação de Split() tem acesso ao:

* Os parâmetros da tarefa, através de `_parameters` campo.
* O objeto de CloudJob que representa a tarefa, através de `_job` campo.
* O objeto de CloudTask que representa a tarefa do Gestor de tarefas, através de `_jobManagerTask` campo.

O `Split()` implementação não tem de adicionar tarefas ao trabalho diretamente. Em vez disso, o código deverá devolver uma sequência de objetos de CloudTask e estes serão adicionados à tarefa automaticamente pelas classes de estrutura que invocam o divisor da tarefa. É comum para iterator do utilizar # (`yield return`) funcionalidade de implementar splitters tarefa Isto permite que as tarefas iniciar a ser executado logo que possível, em vez de aguardar que todas as tarefas deve ser calculado.

**Falha da tarefa divisor**

Se o divisor da tarefa encontra um erro, quer devia:

* Terminar a sequência utilizando o c# `yield break` declaração, na qual caso o Gestor de tarefas será tratado como êxito; ou
* Gerar uma exceção, caso que o Gestor de tarefas será tratado como falha e pode ser repetido, dependendo de como o cliente tenha configurado).

Em ambos os casos, todas as tarefas já devolvidos pelo divisor de tarefa e adicionadas ao trabalho Batch será elegíveis para ser executada. Se não quiser isto acontecer, em seguida, pode:

* Terminar a tarefa antes de regressar do divisor da tarefa
* Formular a coleção de tarefas completo antes de o devolver (ou seja, devolver um `ICollection<CloudTask>` ou `IList<CloudTask>` em vez de implementar o divisor tarefa utilizando um iterator c#)
* Utilize as dependências de tarefas para efetuar todas as tarefas que dependem a conclusão com êxito do Gestor de tarefas

**Tentativas de Gestor de tarefas**

Se o Gestor de tarefas falhar, pode ser repetida pelo serviço Batch, consoante as definições de repetição do cliente. Em geral, isto é seguro, porque quando o framework adiciona tarefas ao trabalho, ignora todas as tarefas que já existem. No entanto, esteja calcular tarefas dispendiosas, poderá não quiser assumir o custo de recalcular as tarefas que já foram adicionadas ao trabalho; por outro lado, se a voltar a executar não é assegurada para gerar a mesma tarefa IDs, em seguida, o comportamento 'Ignorar duplicados' será não iniciar. Nestes casos deve conceber a sua divisor da tarefa para detetar o trabalho que já foi executado e repita-lo, por exemplo, efetuando uma CloudJob.ListTasks antes de começar a produzir tarefas.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Códigos de saída e exceções no modelo de Gestor de tarefas
Códigos de saída e exceções de fornecem um mecanismo para determinar o resultado da execução de um programa e podem ajudar a identificar quaisquer problemas com a execução do programa. O modelo de Gestor de tarefas implementa os códigos de saída e exceções descritas nesta secção.

Uma tarefa do Gestor de tarefas que é implementado com o modelo de Gestor de tarefas pode devolver as três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| 0 |O Gestor de tarefas foi concluído com êxito. Código do divisor da tarefa foi executada para conclusão e todas as tarefas foram adicionadas ao trabalho. |
| 1 |A tarefa de gestão falhou com uma exceção numa parte 'esperada' do programa. A exceção foi convertida para um JobManagerException com informações de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |A tarefa de gestão falhou com uma exceção 'inesperada'. A exceção foi registada para a saída padrão, mas o Gestor de tarefas não conseguiu adicionar qualquer informação de diagnóstico ou remediação adicional. |

No caso de falha de tarefa de Gestor de tarefas, algumas tarefas poderão ainda ter sido adicionadas ao serviço antes do erro ocorreu. Estas tarefas serão executadas como normais. Consulte "Falha de divisor da tarefa" acima para debate deste caminho de código.

Todas as informações devolvidas pelo exceções são escritas na stdout.txt e stderr.txt ficheiros. Para obter mais informações, consulte [erro processamento](batch-api-basics.md#error-handling).

### <a name="client-considerations"></a>Considerações de cliente
Esta secção descreve alguns requisitos de implementação do cliente ao invocar um Gestor de tarefas com base neste modelo. Consulte [como passar os parâmetros e variáveis de ambiente do código de cliente](#pass-environment-settings) para obter detalhes sobre a transmitir parâmetros e definições de ambiente.

**Credenciais obrigatórias**

Para poder adicionar tarefas à tarefa do Azure Batch, a tarefa de gestão requer o URL da conta do Azure Batch e a chave. Tem de passar estes nas variáveis de ambiente com o nome YOUR_BATCH_URL e YOUR_BATCH_KEY. Pode configurar estes no Gestor de tarefas definições de ambiente de tarefas. Por exemplo, num c# cliente:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Credenciais do armazenamento**

Normalmente, o cliente não tem de fornecer as credenciais da conta de armazenamento ligada para a tarefa de gestão porque não necessita de (a) a maioria dos gestores de tarefa explicitamente aceder à conta de armazenamento ligada e (b) a conta do storage ligadas, muitas vezes, é fornecida para todas as tarefas como um definição de ambiente comuns para a tarefa. Se não está a fornecer a conta do storage ligadas através das definições do ambiente comuns e o Gestor de tarefas necessita de acesso a armazenamento ligado, deve fornecer as credenciais de armazenamento ligada da seguinte forma:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Definições de tarefa do Gestor de tarefas**

O cliente deve definir o Gestor de tarefas *killJobOnCompletion* sinalizador para **falso**.

É normalmente seguro para o cliente definir *runExclusive* para **falso**.

O cliente deve utilizar o *resourceFiles* ou *applicationPackageReferences* coleção para que a tarefa manager executável (e o respetivos DLLs necessários) implementadas para o nó de computação.

Por predefinição, o Gestor de tarefas não será repetido se falhar. Dependendo da lógica de Gestor de tarefa, o cliente poderá querer permitir repetição de tentativas através de *restrições*/*maxTaskRetryCount*.

**Definições da tarefa**

Se o divisor tarefa emite tarefas com as dependências, o cliente tem de definir usesTaskDependencies da tarefa como verdadeiro.

No modelo de divisor tarefa, é pouco usual que os clientes pretendem adicionar tarefas a tarefas over and above que cria o divisor da tarefa. O cliente, por conseguinte, deve definir normalmente a tarefa *onAllTasksComplete* para **terminatejob**.

## <a name="task-processor-template"></a>Modelo de processador de tarefas
Um modelo de processador de tarefas ajuda-o a implementar um processador de tarefas que pode realizar as seguintes ações:

* Configure as informações necessárias para cada tarefa do Batch para executar.
* Execute todas as ações de cada tarefa do Batch.
* Guarde saídas de tarefas para o armazenamento persistente.

Embora um processador de tarefas não é necessário para executar tarefas do batch, a vantagem de utilizar um processador de tarefas é que fornece um wrapper para implementar todas as ações de execução de tarefas numa única localização. Por exemplo, se tiver de executar várias aplicações no contexto de cada tarefa, ou se tem de copiar dados para o armazenamento persistente depois de concluir cada tarefa.

As ações executadas pelo processador tarefas podem ser como simples ou complexas e como muitas ou poucos, conforme necessário, a carga de trabalho. Além disso, ao implementar todas as ações de tarefas para um processador de tarefas, pode prontamente atualizar ou adicionar ações com base nas alterações a requisitos de carga de trabalho ou aplicações. No entanto, em alguns casos um processador de tarefas pode não ser a solução ideal para a implementação que pode adicionar complexidade desnecessária, por exemplo quando tarefas em execução que podem ser rapidamente iniciadas a partir de uma linha de comandos simple.

### <a name="create-a-task-processor-using-the-template"></a>Criar um processador de tarefas utilizando o modelo
Para adicionar um processador de tarefas para a solução que criou anteriormente, siga estes passos:

1. Abra a solução existente no Visual Studio.
2. No Explorador de soluções, clique com botão direito na solução, clique em **adicionar**e, em seguida, clique em **novo projeto**.
3. Em **Visual c#**, clique em **nuvem**e, em seguida, clique em **processador de tarefa de lote do Azure**.
4. Escreva um nome que descreve a aplicação e identifica este projeto como o processador de tarefas (por exemplo "LitwareTaskProcessor").
5. Para criar o projeto, clique em **OK**.
6. Por fim, crie o projeto para forçar o Visual Studio para carregar todos os pacotes de NuGet referenciados e certifique-se de que o projeto é válido antes de começar a modificá-lo.

### <a name="task-processor-template-files-and-their-purpose"></a>Ficheiros de modelo de processador de tarefa e respetivo objetivo
Quando cria um projeto com o modelo de processador de tarefas, gera três grupos de ficheiros de código:

* O ficheiro de programa principal (Program.cs). Contém o ponto de entrada do programa e processamento de exceções de nível superior. Normalmente, não deve necessitar de modificar esta.
* O diretório Framework. Contém os ficheiros responsáveis para o trabalho de 'automático' efetuado pelo programa de Gestor de tarefas – unpacking parâmetros, adicionar tarefas para a tarefa do Batch, etc. Normalmente, não deve necessitar de modificar estes ficheiros.
* O ficheiro de processador de tarefas (TaskProcessor.cs). Esta é a localização onde irá colocar a lógica de aplicação específicos para executar uma tarefa (normalmente, chamando para um ficheiro executável existente). Código de pré e pós-processamento, tal como a transferência de dados adicionais ou carregar ficheiros de resultado, também aqui.

Obviamente pode adicionar ficheiros adicionais conforme necessário para suportar o código de processador de tarefas, dependendo da complexidade da tarefa dividir lógica.

O modelo gera também padrão ficheiros de projeto de .NET como um ficheiro. csproj, App. config, Packages, etc.

As restantes desta secção descreve os diferentes ficheiros e a respetiva estrutura de código e explica o que faz cada classe.

![Visual Studio Solution Explorer que mostra a solução de modelo de processador de tarefas][solution_explorer02]

**Ficheiros de Framework**

* `Configuration.cs`: Encapsula o carregamento de dados de configuração de tarefas, tais como os detalhes da conta de Batch, as credenciais da conta de armazenamento ligado, o trabalho e informações de tarefas e parâmetros da tarefa. Também fornece acesso a variáveis de ambiente definidas pelo Batch (consulte as definições de ambiente para tarefas, na documentação do Batch) através da classe de Configuration.EnvironmentVariable.
* `IConfiguration.cs`: Deduz a implementação da classe de configuração, para que pode o teste da unidade do divisor da tarefa com um objeto de configuração falsificados ou mock.
* `TaskProcessorException.cs`: Representa um erro que exige o Gestor de tarefas terminar. TaskProcessorException é utilizado para encapsular erros 'esperados' onde as informações de diagnóstico específicas podem ser fornecidas como parte de terminação.

**Processador de tarefas**

* `TaskProcessor.cs`: Executa a tarefa. A estrutura invoca o método TaskProcessor.Run. Esta é a classe onde irá inserir a lógica de específicas da aplicação da sua tarefa. Implemente o método executar:
  * Analisar e validar os parâmetros da tarefa
  * Componha a linha de comandos para qualquer programa externo que pretende invocar
  * Inicie sessão quaisquer informações de diagnóstico que precisa para fins de depuração
  * Iniciar um processo com essa linha de comandos
  * Aguarde que a saída do processo
  * O código de saída do processo para determinar se foi concluída com êxito ou falha de captura
  * Guardar os ficheiros de saída que pretende manter para o armazenamento persistente

**Ficheiros de projeto de linha de comandos do padrão .NET**

* `App.config`: Ficheiro de configuração da aplicação .NET padrão.
* `Packages.config`: Ficheiro de dependência de pacote NuGet padrão.
* `Program.cs`: Contém o ponto de entrada do programa e processamento de exceções de nível superior.

## <a name="implementing-the-task-processor"></a>Implementar o processador de tarefas
Quando abrir o projeto de modelo de processador de tarefas, o projeto terá o ficheiro de TaskProcessor.cs abrir por predefinição. Pode implementar a lógica de execução para as tarefas na sua carga de trabalho utilizando o método de Run() mostrado abaixo:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> A secção anotada no método Run() é a secção apenas o código de modelo de processador de tarefas que pretende modificar adicionando a lógica de execução para as tarefas na sua carga de trabalho. Se pretender modificar uma secção diferente do modelo,. primeiro familiarizar-se com como funciona o Batch, revendo a documentação do Batch e experimentar alguns dos exemplos de código do Batch.
> 
> 

O método Run() é responsável por iniciar a linha de comandos a partir de um ou mais processos, a aguardar que todos os processos concluir, guardar os resultados e, finalmente, devolvendo com um código de saída. O método de Run() é onde implementar a lógica de processamento para as suas tarefas. A arquitetura de processador de tarefa invoca o método Run() para si; Não é necessário chamá-la.

A implementação de Run() tem acesso ao:

* Os parâmetros da tarefa, através de `_parameters` campo.
* Os ids de tarefas e, através de `_jobId` e `_taskId` campos.
* A configuração da tarefa, através de `_configuration` campo.

**Falha de tarefa**

Em caso de falha, pode sair do método Run() ao gerar uma exceção, mas esta opção deixa o processador de exceções de nível superior no controlo de código de saída de tarefas. Se precisar de controlar o código de saída, de modo a que poder diferenciar os diferentes tipos de falha, por exemplo para fins de diagnóstico ou porque algumas modos de falha devem terminar a tarefa e outros não devem, em seguida, deve sair o método Run() devolvendo um diferente de zero código de saída. Isto torna-se o código de saída da tarefa.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Códigos de saída e exceções no modelo de processador de tarefas
Códigos de saída e exceções de fornecem um mecanismo para determinar o resultado da execução de um programa e pode ajudar a identificar quaisquer problemas com a execução do programa. O modelo de processador de tarefa implementa os códigos de saída e exceções descritas nesta secção.

Uma tarefa do processador de tarefas que é implementada com o modelo de processador de tarefas pode devolver as três códigos de saída possíveis:

| Código | Descrição |
| --- | --- |
| [Process.ExitCode][process_exitcode] |O processador de tarefa foi executada para conclusão. Tenha em atenção que isto não implica que o programa é invocado foi bem sucedido – apenas que o processador de tarefas-invocado com sucesso e efetuar qualquer processamento posterior sem exceções. O significado do código de saída depende do programa invocado – normalmente, o código de saída 0 significa que o programa foi concluída com êxito e outro código de saída significa que o programa com falhas. |
| 1 |O processador de tarefa falhou com uma exceção numa parte 'esperada' do programa. A exceção foi convertida para um `TaskProcessorException` com informações de diagnóstico e, sempre que possível, sugestões para resolver a falha. |
| 2 |O processador de tarefa falhou com uma exceção 'inesperada'. A exceção foi registada para a saída padrão, mas o processador de tarefas não conseguiu adicionar qualquer informação de diagnóstico ou remediação adicional. |

> [!NOTE]
> Se o programa que é a invocar utiliza códigos de saída a 1 e 2 para indicar os modos de falha específica, em seguida, utilizar códigos de saída a 1 e 2 para erros de processador de tarefas é ambígua. Pode alterar estes códigos de erro do processador de tarefas para códigos de saída distinctive ao editar os casos de exceção no ficheiro Program.cs.
> 
> 

Todas as informações devolvidas pelo exceções são escritas na stdout.txt e stderr.txt ficheiros. Para obter mais informações, consulte o erro de processamento, na documentação do Batch.

### <a name="client-considerations"></a>Considerações de cliente
**Credenciais do armazenamento**

Se o processador de tarefas utiliza armazenamento de Blobs do Azure para manter saídas, por exemplo, utilizando a biblioteca de programa auxiliar de convenções de ficheiro, em seguida, precisa de acesso à *ou* as credenciais de conta de armazenamento de nuvem *ou* um blob URL do contentor que inclui uma assinatura de acesso partilhado (SAS). O modelo inclui suporte para fornecer credenciais através de variáveis de ambiente comuns. O cliente pode passar as credenciais do armazenamento da seguinte forma:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

A conta de armazenamento, em seguida, está disponível na classe TaskProcessor através de `_configuration.StorageAccount` propriedade.

Se preferir utilizar um URL de contentor com SAS, pode também passar esta através de uma definição de ambiente comuns de tarefa, mas o modelo de processador de tarefas atualmente não inclui suporte incorporado para este.

**Configuração de armazenamento**

Recomenda-se que o cliente ou a tarefa do Gestor de tarefas criar quaisquer contentores necessários pelas tarefas antes de adicionar tarefas ao trabalho. Isto é obrigatório que se utilizar um URL de contentor com SAS, tal como um URL não inclui permissão para criar o contentor. Recomenda-se, mesmo que se passa credenciais da conta de armazenamento, como ela guarda cada tarefa ter chamar CloudBlobContainer.CreateIfNotExistsAsync no contentor.

## <a name="pass-parameters-and-environment-variables"></a>Transmita os parâmetros e variáveis de ambiente
### <a name="pass-environment-settings"></a>Transmita as definições de ambiente
Um cliente pode transmitir informações para a tarefa do Gestor de tarefas sob a forma de definições de ambiente. Esta informação, em seguida, pode ser utilizada pela tarefa de gestão ao gerar as tarefas de processador de tarefas que serão executada como parte do trabalho de computação. Exemplos de informações que podem passar como definições de ambiente são:

* Chaves de conta e o nome de conta de armazenamento
* URL da conta de batch
* Chave de conta do batch

O serviço Batch tem um mecanismo de simple para passar definições de ambiente para uma tarefa de gestão utilizando o `EnvironmentSettings` propriedade no [Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask].

Por exemplo, para obter o `BatchClient` instância para uma conta do Batch, pode passar como código de variáveis de ambiente do cliente, o URL e as credenciais da chave partilhadas para a conta de Batch. Da mesma forma, para aceder à conta de armazenamento que está ligada à conta do Batch, pode passar o nome da conta de armazenamento e a chave de conta de armazenamento como variáveis de ambiente.

### <a name="pass-parameters-to-the-job-manager-template"></a>Transmita os parâmetros para o modelo de Gestor de tarefas
Em muitos casos, isto é útil passar os parâmetros-job para a tarefa do Gestor de tarefas, para controlar a tarefa de divisão de processo ou para configurar as tarefas da tarefa. Pode fazê-lo através do carregamento de um ficheiro JSON com o nome Parameters. JSON como um ficheiro de recursos para a tarefa de gestão. Os parâmetros, em seguida, podem ficar disponíveis no `JobSplitter._parameters` campo no modelo de Gestor de tarefas.

> [!NOTE]
> O processador de parâmetro incorporada suporta apenas dicionários de cadeia-cadeia. Se pretender passar valores JSON complexos como valores de parâmetros, terá de passar como cadeias e analisá-los no divisor tarefa ou modificar a estrutura `Configuration.GetJobParameters` método.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Transmita os parâmetros para o modelo de processador de tarefas
Também pode passar os parâmetros para tarefas individuais implementadas utilizando o modelo de processador de tarefas. Tal como com o modelo de Gestor de tarefas, o modelo de processador de tarefas procura um ficheiro de recursos com o nome

Parameters. JSON e se encontrar esta carrega-o como o dicionário de parâmetros. Existem duas opções passar os parâmetros para as tarefas de processador de tarefas:

* Reutilize os parâmetros da tarefa JSON. Isto funciona bem se os parâmetros apenas são ao nível da tarefa (por exemplo, uma composição altura e largura). Para tal, quando criar um CloudTask no divisor da tarefa, adicione uma referência para o objeto de ficheiro de recursos de Parameters. JSON de ResourceFiles a tarefa do Gestor (`JobSplitter._jobManagerTask.ResourceFiles`) para a coleção de ResourceFiles o CloudTask.
* Gerar e carregar um documento de Parameters. JSON de específicas de tarefas como parte da execução da tarefa divisor para fazer referência a esse blob na coleção de ficheiros de recursos da tarefa. Isto é necessário se diferentes tarefas possuem parâmetros diferentes. Um exemplo pode ser um cenário de composição 3D onde o índice de moldura é passado para a tarefa como parâmetro.

> [!NOTE]
> O processador de parâmetro incorporada suporta apenas dicionários de cadeia-cadeia. Se pretender passar valores JSON complexos como valores de parâmetros, terá de passar como cadeias e analisá-los no processador de tarefa de ou modificar a estrutura `Configuration.GetTaskParameters` método.
> 
> 

## <a name="next-steps"></a>Passos seguintes
### <a name="persist-job-and-task-output-to-azure-storage"></a>Manter a saída de tarefas e ao armazenamento do Azure
Outra ferramenta útil no desenvolvimento de solução do Batch é [convenções de ficheiro do Azure Batch][nuget_package]. Utilize esta biblioteca de classe de .NET (atualmente em pré-visualização) nas suas aplicações de .NET do Batch para facilmente armazenam e obtêm saídas de tarefas para e do armazenamento do Azure. [Manter a saída de tarefas e do Azure Batch](batch-task-output.md) contém um debate completo de biblioteca e respetiva utilização.

### <a name="batch-forum"></a>Fórum do batch
O [fórum do Azure Batch] [ forum] no MSDN é um excelente local para discutir Batch e de fazer perguntas sobre o serviço. HEAD na ativação pós-falha para mensagens "temporária" úteis e publique as suas perguntas que possam surgir ao criar as soluções do Batch.

[forum]: https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azurebatch
[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
