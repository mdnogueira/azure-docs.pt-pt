---
title: "Testar o desempenho de um serviço em nuvem | Microsoft Docs"
description: "Testar o desempenho de um serviço em nuvem utilizando o gerador de perfis do Visual Studio"
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: ghogen
editor: 
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 483b8b1c7c75c407cb55a1b3b027ae043c506ebb
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testar o desempenho de um serviço em nuvem
## <a name="overview"></a>Descrição geral
Pode testar o desempenho de um serviço em nuvem das seguintes formas:

* Utilize o diagnóstico do Azure para recolher informações sobre pedidos e ligações e para rever as estatísticas de site que mostram como o serviço efetua uma perspetiva do cliente. Para obter uma introdução, consulte o artigo [configurar diagnósticos para máquinas virtuais e serviços Cloud do Azure](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Utilize o gerador de perfis do Visual Studio para obter uma análise aprofundada dos aspetos computacionais da forma como o serviço seja executado. Como este tópico descreve, pode utilizar o gerador de perfis para medir o desempenho como um serviço é executado no Azure. Para obter informações sobre como utilizar o gerador de perfis para medir o desempenho como um serviço é executada localmente no emulador de computação, consulte [testar o desempenho de um serviço de nuvem do Azure localmente no emulador de computação utilizando o Visual Studio do gerador de perfis](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Escolher um método de teste de desempenho
### <a name="use-azure-diagnostics-to-collect"></a>Utilize o diagnóstico do Azure para recolher:
* Estatísticas em páginas web ou de serviços, como pedidos e ligações.
* Estatísticas de funções, tais como a frequência uma função é reiniciada.
* Em geral sobre a utilização de memória, tais como a percentagem de tempo que demora o recoletor de lixo ou a memória definido de uma função em execução.

### <a name="use-the-visual-studio-profiler-to"></a>Utilize o gerador de perfis do Visual Studio para:
* Determine as funções que demorar mais tempo.
* Medir quanto tempo demora de cada parte de um programa viáveis intensiva.
* Comparação detalhada do desempenho de relatórios para duas versões de um serviço.
* Analise a atribuição de memória em mais detalhe que o nível de alocações de memória individuais.
* Analise problemas de concorrência no código multithread.

Quando utiliza o gerador de perfis, é possível recolher dados quando um serviço em nuvem é executada localmente ou no Azure.

### <a name="collect-profiling-data-locally-to"></a>Recolha a criação de perfis dados localmente para:
* Testar o desempenho de uma parte de um serviço em nuvem, tais como a execução da função de trabalho específico, que não necessita de uma carga simulada realista.
* Teste o desempenho de um serviço em nuvem no isolamento, em condições controladas.
* Teste o desempenho de um serviço em nuvem antes de implementar no Azure.
* Teste o desempenho de um serviço em nuvem privada, sem preventiva as implementações existentes.
* Teste o desempenho do serviço sem incorrer em custos de em execução no Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Recolha dados de criação de perfis no Azure para:
* Teste o desempenho de um serviço em nuvem com uma carga real ou simulado.
* Utilize o método de instrumentação de recolha de dados de criação de perfis, tal como este tópico descreve mais tarde.
* Teste o desempenho do serviço no mesmo ambiente, como quando o serviço é executado na produção.

Normalmente, simule uma carga para serviços de cloud de teste em normal ou condições de esforço.

## <a name="profiling-a-cloud-service-in-azure"></a>Criação de perfis de um serviço em nuvem no Azure
Quando publicar o seu serviço em nuvem do Visual Studio, pode o serviço de perfil e especificar as definições de criação de perfis que dão-lhe as informações que pretende. Uma sessão de criação de perfis é iniciada para cada instância de uma função. Para obter mais informações sobre como publicar o serviço do Visual Studio, consulte [publicar num serviço em nuvem do Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Para saber mais sobre a criação de perfis de desempenho no Visual Studio, consulte [manual de principiantes para a criação de perfis de desempenho](https://msdn.microsoft.com/library/azure/ms182372.aspx) e [analisar o desempenho de aplicações através de ferramentas de criação de perfis](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Pode ativar o IntelliTrace ou criação de perfis quando publicar o seu serviço em nuvem. Não é possível ativar a ambos.
> 
> 

### <a name="profiler-collection-methods"></a>Métodos de coleção do gerador de perfis
Pode utilizar métodos de coleção diferente para a criação de perfis, com base na sua problemas de desempenho:

* **A amostragem da CPU** -este método recolhe estatísticas de aplicações que são úteis para análise inicial dos problemas de utilização da CPU. A amostragem da CPU é o método sugerido para iniciar a maioria das investigações de desempenho. Há um impacto baixo na aplicação que lhe está a criação de perfis quando recolher os dados da amostragem de CPU.
* **Instrumentação** -este método recolhe dados de temporização detalhadas que é útil para análise focada e para analisar problemas de desempenho de entrada/saída. O método de instrumentação regista cada entrada, saída e chamada de função das funções de um módulo durante uma criação de perfis de execução. Este método é útil para reunir informações de temporização detalhadas sobre uma secção do seu código e para compreender o impacto das operações de entrada e de saída no desempenho da aplicação. Este método está desativado para um computador a executar um sistema operativo de 32 bits. Esta opção só está disponível quando executa o serviço em nuvem no Azure, não localmente no emulador de computação.
* **Atribuição de memória de .NET** -este método recolhe dados de alocação de memória de .NET Framework, utilizando a método de criação de perfis de amostragem. Os dados recolhidos incluem o número e tamanho dos objetos alocados.
* **Simultaneidade** -este método recolhe dados de contenção de recursos e o processo e o thread de dados de execução que são útil analisar vários threads e processos várias aplicações. O método de concorrência recolhe dados para cada evento que a execução de blocos de código, tal como quando um thread aguarda bloqueada acesso a um recurso de aplicação a ser libertado. Este método é útil para analisar aplicações com vários threads.
* Também pode ativar **criação de perfis de interação do escalão**, que fornece informações adicionais sobre os tempos de execução do ADO.NET síncrona chama nas funções de aplicações de várias camadas de mensagens em fila que comunicam com um ou mais bases de dados. Pode recolher dados de interação do escalão com qualquer um dos métodos de criação de perfis. Para obter mais informações sobre a criação de perfis de interação do escalão, consulte [camada interações vista](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Configurar definições de criação de perfis
A ilustração seguinte mostra como configurar as definições de criação de perfis da caixa de diálogo Publicar aplicação Azure.

![Configurar definições de criação de perfis](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Para ativar o **ativar a criação de perfis** caixa de verificação, tem de ter o gerador de perfis instalado no computador local que está a utilizar para publicar o seu serviço em nuvem. Por predefinição, o gerador de perfis é instalado quando instalar o Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Para configurar as definições de criação de perfis
1. No Explorador de soluções, abra o menu de atalho para o projeto do Azure e, em seguida, escolha **publicar**. Para obter passos detalhados sobre como publicar um serviço em nuvem, consulte [publicação de um serviço em nuvem com as ferramentas do Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. No **publicar aplicação Azure** caixa de diálogo, escolher o **definições avançadas** separador.
3. Para ativar a criação de perfis, selecione o **ativar a criação de perfis** caixa de verificação.
4. Para configurar as definições de criação de perfis, escolha o **definições** hyperlink. É apresentada a caixa de diálogo Definições de criação de perfis.
5. Do **que método de criação de perfis de que pretende utilizar** botões de opção, selecione o tipo de criação de perfis que precisa.
6. Para recolher a interação de camada data de criação de perfis, selecione o **ativar camada interação de criação de perfis** caixa de verificação.
7. Para guardar as definições, escolha o **OK** botão.
   
    Quando publicar esta aplicação, estas definições são utilizadas para criar a sessão de criação de perfis para cada função.

## <a name="viewing-profiling-reports"></a>Visualizar relatórios de criação de perfis
É criada uma sessão de criação de perfis para cada instância de uma função no seu serviço em nuvem. Para ver os relatórios de criação de perfis de cada sessão a partir do Visual Studio, pode ver a janela do Explorador de servidores e, em seguida, escolha o nó de computação do Azure para selecionar uma instância de uma função. Em seguida, pode ver o relatório de criação de perfis, conforme mostrado na ilustração seguinte.

![Vista de criação de perfis de relatório a partir do Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Para ver relatórios de criação de perfis
1. Para ver a janela do Explorador de servidores no Visual Studio, na barra de menus escolha vista, Explorador de servidores.
2. Escolha o nó de computação do Azure e, em seguida, escolha o nó de implementação do Azure para o serviço em nuvem que selecionou para o perfil quando publicado do Visual Studio.
3. Para ver relatórios de criação de perfis para uma instância, escolha a função no serviço, abra o menu de atalho para uma instância específica e, em seguida, escolha **Ver relatório de criação de perfis**.
   
    O relatório, um ficheiro de .vsp, agora é transferido a partir do Azure e o estado da transferência é apresentada no registo de atividade do Azure. Quando a transferência estiver concluída, o relatório de criação de perfis aparece um separador no editor do Visual Studio com o nome <Role name>  *<Instance Number>*  <identifier>.vsp. Dados de resumo para o relatório é apresentado.
4. Para apresentar vistas diferentes do relatório, na lista de vista atual, escolha o tipo de vista que pretende. Para obter mais informações, consulte [vistas de relatório de ferramentas de criação de perfis](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Passos seguintes
[Depuração serviços Cloud](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicação de um serviço em nuvem do Azure a partir do Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

