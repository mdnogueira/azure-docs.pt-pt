---
title: "Criação de perfis de um serviço em nuvem localmente no emulador de computação | Microsoft Docs"
services: cloud-services
description: "Investigar problemas de desempenho nos serviços em nuvem com o gerador de perfis do Visual Studio"
documentationcenter: 
author: mikejo
manager: ghogen
editor: 
tags: 
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 5e3c729ce3e75665078d7f33baed943087fbe0ca
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testar o desempenho de um serviço em nuvem localmente no emulador de computação do Azure utilizando o gerador de perfis do Visual Studio
Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho dos serviços em nuvem.
Quando publica um serviço em nuvem do Azure, pode ter o Visual Studio recolher dados de criação de perfis e, em seguida, analisá-lo localmente, conforme descrito em [criação de perfis de uma aplicação do Azure][1].
Pode também utilizar diagnósticos para controlar uma variedade de contadores de desempenho, conforme descrito em [utilizando os contadores de desempenho no Azure][2].
Pode também querer perfil a aplicação localmente no emulador de computação antes de o implementar para a nuvem.

Este artigo aborda o método de criação de perfis da Amostragem de CPU, que pode ser levada a cabo localmente no emulador. A amostragem da CPU é um método de criação de perfis que não é intrusivo muito. Num intervalo de amostragem designado, o gerador de perfis tira um instantâneo da pilha de chamadas. Os dados são recolhidos ao longo de um período de tempo e apresentados num relatório. Este método de criação de perfis tende a indicar onde numa aplicação viáveis intensiva maioria do trabalho CPU está a ser efetuada.  Isto dá-lhe a oportunidade para se focarem em "path frequente" em que a aplicação está a dedicar muito mais tempo.

## <a name="1-configure-visual-studio-for-profiling"></a>1: configurar o Visual Studio para criação de perfis
Em primeiro lugar, existem algumas opções de configuração do Visual Studio que poderão ser úteis quando a criação de perfis. Para fazer sentido dos relatórios de criação de perfis, terá de símbolos (ficheiros .pdb) para a sua aplicação e também símbolos para bibliotecas do sistema. Poderá ser útil para se certificar de que referenciam os servidores de símbolo disponíveis. Para tal, no **ferramentas** menu no Visual Studio, escolha **opções**, em seguida, escolha **a depuração**, em seguida, **símbolos**. Certifique-se de que os servidores do Microsoft símbolo está listado em **localizações de ficheiros (.pdb) do símbolo**.  Também pode referenciar http://referencesource.microsoft.com/symbols, que pode ter ficheiros de símbolo adicionais.

![Opções de símbolo][4]

Se assim o desejar, pode simplificar os relatórios que gera o gerador de perfis, definindo apenas código My. Com apenas os meus código ativada, as pilhas de chamada de função são simplificadas, para que as chamadas inteiramente internas para o .NET Framework e bibliotecas estão ocultas dos relatórios. No **ferramentas** menu, escolha **opções**. Em seguida, expanda o **ferramentas de desempenho** nó e escolha **geral**. Selecione a caixa de verificação **ative apenas os meus código para o gerador de perfis relatórios**.

![Apenas as opções de código My][17]

Pode utilizar estas instruções com um projeto existente ou com um novo projeto.  Se criar um novo projeto para experimentar as técnicas descritas abaixo, escolha um c# **o serviço em nuvem do Azure** projeto e selecione um **função Web** e um **função de trabalho**.

![Funções de projeto de serviço em nuvem do Azure][5]

Por exemplo fins, adicionar algum código ao projeto que demora muito tempo e demonstra a um problema de desempenho óbvios. Por exemplo, adicione o seguinte código para um projeto de função de trabalho:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Este código de chamada do método runasync com na classe derivada de RoleEntryPoint da função de trabalho. (Ignorar o aviso sobre o método executar de forma síncrona.)

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Compilar e executar localmente o serviço em nuvem sem depuração (Ctrl + F5), com a solução definida uma configuração para **versão**. Isto garante que todos os ficheiros e pastas são criados para executar a aplicação localmente e garante que todos os emuladores são iniciados. Inicie a IU do emulador de computação na barra de tarefas para verificar se a sua função de trabalho está em execução.

## <a name="2-attach-to-a-process"></a>2: ligar a um processo
Em vez de criação de perfis de aplicação por iniciá-lo a partir do Visual Studio 2010 IDE, terá de ligar o gerador de perfis para um processo em execução. 

Ligar o gerador de perfis a um processo no **analisar** menu, escolha **gerador de perfis** e **anexar/desanexar**.

![Anexar a opção de perfil][6]

Para uma função de trabalho, localize o processo de WaWorkerHost.exe.

![Processo de WaWorkerHost][7]

Se a pasta do projeto é numa unidade de rede, o gerador de perfis irá pedir-lhe fornecer outra localização para guardar os relatórios de criação de perfis.

 Também pode anexar a uma função web ao anexar o WaIISHost.exe.
Se existirem vários processos de função de trabalho na sua aplicação, terá de utilizar o ID do processo para distingui-los. Pode consultar o ID do processo através de programação, ao aceder ao objeto de processo. Por exemplo, se adicionar este código para o método de execução da classe derivada de RoleEntryPoint numa função, pode examinar o registo na IU do emulador de computação para saber que processo para ligar a.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para ver o registo, inicie a IU do emulador de computação.

![Iniciar o IU do emulador de computação][8]

Abra a janela de consola de registo de função de trabalho na IU do emulador de computação clicando na barra de título da janela da consola. Pode ver o ID de processo no registo.

![ID do processo de vista][9]

Um que tenha ligado, execute os passos na IU da aplicação (se necessário) para reproduzir o cenário.

Quando pretender parar a criação de perfis, escolha o **parar a criação de perfis** ligação.

![Parar a opção de criação de perfis][10]

## <a name="3-view-performance-reports"></a>3: ver relatórios de desempenho
O relatório de desempenho para a sua aplicação é apresentado.

Neste momento, o gerador de perfis deixa de executar, guarda dados num ficheiro .vsp e apresenta um relatório que mostra uma análise de dados.

![Relatório de gerador de perfis][11]

Se vir String.wstrcpy no caminho de acesso frequente, clique em apenas os meus código para alterar a vista para mostrar apenas o código de utilizador.  Se vir String.Concat, tente premir o botão Mostrar todo o código.

Deverá ver o método Concatenate e String.Concat utilizando uma grande parte do tempo de execução.

![Análise de relatório][12]

Se tiver adicionado o código de concatenação de cadeia neste artigo, deverá ver um aviso na lista de tarefas para esta situação. Também poderá ver um aviso que não há uma quantidade excessiva de recolha de lixo, ou seja, devido ao número de cadeias que são criados e eliminado.

![Avisos de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: efetuar alterações e compare o desempenho
Também pode comparar o desempenho antes e após uma alteração de código.  Pare o processo em execução e editar o código para a operação de concatenação de cadeia de substituição com a utilização de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Não execute outra de desempenho e, em seguida, compare o desempenho. No Explorador de desempenho, se o é executado na mesma sessão, pode apenas selecionar ambos os relatórios, abra o menu de atalho e escolha **comparar o desempenho relatórios**. Se de que pretende comparar com uma execução na outra sessão de desempenho, abra o **analisar** menu e escolha **comparar o desempenho relatórios**. Especifique ambos os ficheiros na caixa de diálogo que aparece.

![Comparar a opção de relatórios de desempenho][15]

Os relatórios realce as diferenças entre o duas executa.

![Relatório de comparação][16]

Parabéns! Que tiver iniciado com o gerador de perfis.

## <a name="troubleshooting"></a>Resolução de problemas
* Certifique-se de que é uma versão de lançamento de criação de perfis e iniciar sem depuração.
* Se a opção de anexar/desanexar não estiver ativada no menu do gerador de perfis, execute o Assistente de desempenho.
* Utilize a IU do emulador de computação para ver o estado da aplicação. 
* Se tiver problemas de início de aplicações no emulador, ou encerramento de anexar o gerador de perfis, pendente o emulador de computação e reiniciá-lo. Se que não resolverem o problema, tente reiniciar. Este problema pode ocorrer se utilizar o emulador de computação para suspender e remover implementações em execução.
* Se tiver utilizado a qualquer um dos comandos criação de perfis na linha de comandos, especialmente as definições globais, certifique-se de que VSPerfClrEnv /globaloff EnableNotification for chamada e que VsPerfMon.exe ter sido encerrado.
* Se ao amostragem, verá a mensagem "PRF0025: foram recolhidos quaisquer dados," Verifique se o processo anexado ao tem atividades de CPU. As aplicações que não estão a fazer qualquer trabalho computacional podem não produzir quaisquer dados de amostragem.  Também é possível que o processo foi terminado antes de qualquer amostragem foi efetuada. Certifique-se de que o método de execução de uma função que lhe está a criação de perfis não terminar.

## <a name="next-steps"></a>Passos Seguintes
Instrumentação binários do Azure no emulador não é suportado o gerador de perfis do Visual Studio, mas se pretender testar a atribuição de memória, pode escolher essa opção quando a criação de perfis. Pode também escolher simultaneidade criação de perfis, que ajuda a determinar se os threads são perder tempo competir pela bloqueios ou camada interação de criação de perfis, que ajuda a identificar problemas de desempenho quando interagir entre camadas de uma aplicação, mais frequentemente entre a camada de dados e uma função de trabalho.  Pode ver as consultas de base de dados que gera a sua aplicação e os dados de criação de perfis são utilizadas para melhorar a utilização da base de dados. Para obter informações sobre a criação de perfis de interação do escalão, consulte a mensagem de blogue [explicação passo a passo: utilizar o gerador de perfis de interação de camada no Visual Studio Team System 2010][3].

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
