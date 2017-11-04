---
title: "A depuração de um serviço em nuvem do Azure ou a máquina virtual no Visual Studio | Microsoft Docs"
description: "A depuração de um serviço em nuvem ou Máquina Virtual no Visual Studio"
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: ghogen
editor: 
ms.assetid: 945e06e0-2100-41af-b218-72347367ddab
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: a303e080bc847daf023eed2e9ba1ffc31e340160
ms.sourcegitcommit: b83781292640e82b5c172210c7190cf97fabb704
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="debugging-an-azure-cloud-service-or-virtual-machine-in-visual-studio"></a>A depuração de um serviço em nuvem do Azure ou a máquina virtual no Visual Studio
Visual Studio fornece-lhe diferentes opções para a depuração de máquinas virtuais e serviços em nuvem do Azure.

## <a name="debug-your-cloud-service-on-your-local-computer"></a>Depurar o seu serviço em nuvem no seu computador local
Pode poupar tempo e dinheiro através da utilização do Azure computação emulador para depurar o seu serviço em nuvem num computador local. Por depuração localmente um serviço antes de implementá-lo, pode melhorar a fiabilidade e desempenho sem pagar tempo de computação. No entanto, poderão ocorrer alguns erros apenas quando executa um serviço em nuvem no Azure em si. Pode depurar estes erros se ativar a depuração remota quando publicar o serviço e, em seguida, anexe o depurador a uma instância de função.

O emulador simula o serviço de computação do Azure e é executado no seu ambiente local para que possa testar e depurar o seu serviço em nuvem antes de implementá-lo. O emulador processa o ciclo de vida das suas instâncias de função e fornece acesso a recursos simulados, como o armazenamento local. Quando a depuração ou executar o serviço a partir do Visual Studio, inicia o emulador automaticamente como uma aplicação de segundo plano e, em seguida, implementa o seu serviço para o emulador. Pode utilizar o emulador para ver o seu serviço quando é executada no ambiente local. Pode executar a versão completa ou a versão do emulador rápida. (A partir do Azure 2.3, a versão do emulador rápida é a predefinição.) Consulte [utilizando o emulador rápida para executar e depurar um serviço em nuvem localmente](vs-azure-tools-emulator-express-debug-run.md).

### <a name="to-debug-your-cloud-service-on-your-local-computer"></a>Para depurar o seu serviço em nuvem no seu computador local
1. Na barra de menus, escolha **depurar**, **iniciar depuração** para executar o projeto de serviço em nuvem do Azure. Como alternativa, pode premir F5. Verá uma mensagem que está a iniciar o emulador de computação. Quando o emulador inicia, confirma que o ícone de tabuleiro de sistema-lo.

    ![Emulador do Azure no tabuleiro do sistema](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC783828.png)
2. Apresentar a interface de utilizador para o emulador de computação ao abrir o menu de atalho para o ícone do Azure na área de notificação e, em seguida, selecione **mostrar IU do emulador de computação**.

    O painel esquerdo da IU apresenta os serviços que estão atualmente implementados para o emulador de computação e as instâncias de função que cada serviço está em execução. Pode escolher o serviço ou as funções para apresentar o ciclo de vida, registo e informações de diagnóstico no painel direito. Se o put o foco na margem da parte superior de uma janela incluída, expandir para preencher o painel da direita.
3. Passo através da aplicação, selecionando comandos na **depurar** menu e a definição de pontos de interrupção no seu código. Como passo através da aplicação no depurador, os painéis são atualizados com o estado atual da aplicação. Quando deixa de depuração, a implementação de aplicação é eliminada. Se a aplicação inclui uma função da web e definiu a propriedade de ação de arranque para iniciar o web browser, o Visual Studio inicia a aplicação web no browser. Se alterar o número de instâncias de uma função da configuração de serviço, tem de parar o serviço de nuvem e, em seguida, reinicie a depuração para que pode depurar estas novas instâncias da função.

    **Nota:** quando deixa de executar ou de depuração do seu serviço, o emulador de computação local e o emulador de armazenamento não estão parados. Tem de pará-los explicitamente de área de notificação.

## <a name="debug-a-cloud-service-in-azure"></a>Depurar um serviço em nuvem no Azure
Para depurar um serviço em nuvem a partir de um computador remoto, tem de ativar essa funcionalidade explicitamente quando implementar o serviço de nuvem, para que o necessário serviços (msvsmon.exe, por exemplo) estão instalados nas máquinas virtuais que executam as instâncias da função. Se não ativar a depuração remota quando publicado o serviço, terá de voltar a publicar o serviço com a depuração remota ativada.

Se ativar a depuração remota para um serviço em nuvem, não apresentem um degradação do desempenho ou implicar custos adicionais. Não deve utilizar a depuração remota no serviço de produção, porque os clientes que utilizam o serviço poderão ser afetados negativamente.

> [!NOTE]
> Quando publica um serviço em nuvem do Visual Studio, pode ativar **IntelliTrace** para quaisquer funções em que o serviço direcionadas para o .NET Framework 4 ou .NET Framework 4.5. Ao utilizar **IntelliTrace**, pode examinar os eventos que ocorreram numa instância de função no passado e reproduzir o contexto do tempo. Consulte [depuração um serviço de nuvem publicada com IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016) e [utilizando IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx).
>
>

### <a name="to-enable-remote-debugging-for-a-cloud-service"></a>Para ativar a depuração remota para um serviço em nuvem
1. Abra o menu de atalho para o projeto do Azure e, em seguida, selecione **publicar**.
2. Selecione o **transição** ambiente e o **depurar** configuração.

    Isto é apenas uma orientação. Pode optar por executar os seus ambientes de teste num ambiente de produção. No entanto, pode afetar negativamente os utilizadores se ativar a depuração remota no ambiente de produção. Pode escolher a configuração de versão, mas a configuração de depuração torna mais fácil a depuração.

    ![Escolha a configuração de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746717.gif)
3. Siga os passos habituais mas, selecione o **ativar remoto depurador para todas as funções** caixa de verificação a **definições avançadas** separador.

    ![Configuração de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746718.gif)

### <a name="to-attach-the-debugger-to-a-cloud-service-in-azure"></a>Ligar o depurador a um serviço em nuvem no Azure
1. No Explorador de servidores, expanda o nó para o seu serviço de nuvem.
2. Abra o menu de atalho para a função ou a instância de função nos quais pretende anexar e, em seguida, selecione **anexar depurador**.

    Se a depuração uma função, o depurador do Visual Studio anexa a cada instância de função. O depurador irá interromper a no ponto de interrupção para a primeira instância de função que executa dessa linha de código e cumpre as condições desse ponto de interrupção. Se a uma instância, o depurador anexa apenas a essa instância e as quebras de um ponto de interrupção apenas quando essa instância específica é executado dessa linha de código e cumpre as condições do ponto de interrupção de depuração.

    ![Anexar o depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746719.gif)
3. Depois do depurador liga a uma instância, depurar como habitualmente. O depurador anexa automaticamente para o processo de anfitrião adequado para a sua função. Consoante o que é a função, o depurador anexa w3wp.exe, WaWorkerHost.exe ou WaIISHost.exe. Para verificar o processo para o qual está ligado o depurador, expanda o nó de instância no Explorador de servidores. Consulte [arquitetura de função do Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obter mais informações sobre processos de Azure.

    ![Selecione a caixa de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
4. Para identificar os processos para o qual está ligado o depurador, abra a caixa de diálogo de processos por, no menu da barra, escolher depuração, Windows, os processos. (Teclado: Ctrl + Alt + Z) Para anular a exposição de um processo específico, abra o menu de atalho e, em seguida, selecione **desanexar processo**. Ou, localizar o nó de instância no Explorador de servidores, localizar o processo, abra o menu de atalho e, em seguida, selecione **desanexar processo**.

    ![Os processos de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC690787.gif)

> [!WARNING]
> Evitar deixa de tempo em pontos de interrupção quando remoto depuração. Azure trata de um processo que é interrompido durante um período superior a alguns minutos enquanto não responde e deixa de envio de tráfego para essa instância. Se parar há demasiado tempo, msvsmon.exe Desanexa do processo do.
>
>

Para desligar o depurador de todos os processos na sua instância ou a função, abra o menu de atalho para a função ou a instância que está a depuração e, em seguida, selecione **desanexar depurador**.

## <a name="limitations-of-remote-debugging-in-azure"></a>Limitações de depuração remota no Azure
Do Azure SDK 2.3, depuração remota tem as seguintes limitações.

* Com a depuração remota ativada, não é possível publicar um serviço em nuvem em que qualquer função tem mais de 25 instâncias.
* O depurador utiliza as portas 30400 para 30424, 31400 para 31424 e 32400 para 32424. Se tentar utilizar qualquer um destas portas, não será capaz de publicar o serviço e uma das seguintes mensagens de erro será apresentada no registo de atividade do Azure:

  * Erro ao validar o ficheiro. cscfg contra o ficheiro. csdef.
    O reservado intervalo 'intervalo de portas' para o ponto final Microsoft.WindowsAzure.Plugins.RemoteDebugger.Connector da função "função" sobrepõe-se um intervalo ou a porta já definida.
  * Falha na alocação. Tente novamente mais tarde, tente reduzir o tamanho da VM ou o número de instâncias de função ou experimente implementar noutra região.

## <a name="debugging-azure-virtual-machines"></a>A depuração de máquinas virtuais do Azure
Pode depurar programas que são executadas em máquinas virtuais do Azure utilizando o Explorador de servidores no Visual Studio. Quando ativar a depuração remota numa máquina virtual do Azure, Azure instala a extensão de depuração remota na máquina virtual. Em seguida, pode anexar aos processos na máquina virtual e de depuração como faria normalmente.

> [!NOTE]
> Máquinas virtuais criadas através da pilha de Gestor de recursos do Azure pode ser debugged remotamente através da utilização de Cloud Explorer no Visual Studio 2015. Para obter mais informações, consulte [gerir recursos do Azure com Cloud Explorer](http://go.microsoft.com/fwlink/?LinkId=623031).
>
>

### <a name="to-debug-an-azure-virtual-machine"></a>Para depurar uma máquina virtual do Azure
1. No Explorador de servidores, expanda o nó de máquinas virtuais e selecione o nó da máquina virtual que pretende depurar.
2. Abra o menu de contexto e selecione **ativar a depuração**. Quando lhe for perguntado se tem a certeza se pretender ativar a depuração na máquina virtual, selecione **Sim**.

    Azure instala a extensão de depuração remota na máquina virtual para ativar a depuração.

    ![Máquina virtual ativar comando de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registo de atividade do Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
3. Após a extensão de depuração remota conclusão da instalação, abra o menu de contexto da máquina virtual e selecione **anexar depurador...**

    Azure obtém uma lista dos processos na máquina virtual e apresenta-os a anexação à caixa de diálogo de processo.

    ![Anexar o comando do depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
4. No **anexar ao processo** caixa de diálogo, selecione **selecione** para limitar a lista de resultados para mostrar apenas os tipos de código que pretende depurar. Pode depurar código de 32 ou 64-bits gerido, o código nativo ou ambos.

    ![Selecione a caixa de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
5. Selecione os processos que pretende depurar na máquina virtual e, em seguida, selecione **anexar**. Por exemplo, poderá escolher o processo w3wp.exe se pretendesse depurar uma aplicação web na máquina virtual. Consulte [depurar uma ou mais processos no Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) e [arquitetura de função do Azure](http://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx) para obter mais informações.

## <a name="create-a-web-project-and-a-virtual-machine-for-debugging"></a>Criar um projeto web e uma máquina virtual para depuração
Antes de publicar o projeto do Azure, poderá achar útil para testá-lo num ambiente que suporta a depuração e testar cenários e onde pode instalar teste e monitorização programas contido. É uma forma de fazê-lo remotamente depurar a aplicação numa máquina virtual.

Projetos do Visual Studio ASP.NET oferecem uma opção para criar uma máquina virtual à mão, que pode utilizar para testar a aplicação. A máquina virtual inclui pontos finais necessários normalmente como o PowerShell, o ambiente de trabalho remoto e o WebDeploy.

### <a name="to-create-a-web-project-and-a-virtual-machine-for-debugging"></a>Para criar um projeto web e uma máquina virtual para depuração
1. No Visual Studio, crie uma nova aplicação Web de ASP.NET.
2. Na caixa de diálogo novo projeto ASP.NET, na secção do Azure, escolha **Máquina Virtual** na caixa de lista pendente. Deixe o **criar recursos remotos** caixa de verificação selecionada. Selecione **OK** para continuar.

    O **criar máquina virtual no Azure** é apresentada a caixa de diálogo.

    ![Criar caixa de diálogo de projeto de web ASP.NET](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746723.png)

    **Nota:** vai ser-lhe pedido para iniciar sessão sua conta do Azure se ainda não tem sessão iniciada.

1. Selecione as várias definições para a máquina virtual e, em seguida, selecione **OK**. Consulte [máquinas virtuais](http://go.microsoft.com/fwlink/?LinkId=623033) para obter mais informações.

    O nome que introduziu para nome DNS será o nome da máquina virtual.

    ![Criar máquina virtual na caixa de diálogo do Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746724.png)

    O Azure cria a máquina virtual e, em seguida, Aprovisiona e configura os pontos finais, como o ambiente de trabalho remoto e o Web Deploy
2. Depois da máquina virtual completamente estar configurada, selecione o nó da máquina virtual no Explorador de servidores.
3. Abra o menu de contexto e selecione **ativar a depuração**. Quando lhe for perguntado se tem a certeza se pretender ativar a depuração na máquina virtual, selecione **Sim**.

    Azure instala a extensão de depuração remota para a máquina virtual para ativar a depuração.

    ![Máquina virtual ativar comando de depuração](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746720.png)

    ![Registo de atividade do Azure](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746721.png)
4. Publicar o seu projeto conforme descrito na [como: implementar um projeto através de um clique publicação na Web no Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx). Porque pretende depurar na máquina virtual, no **definições** página do **publicar Web** assistente, selecione **depurar** como a configuração. Isto certifica-se de que os símbolos de código estão disponíveis durante a depuração.

    ![Definições de publicação](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718349.png)
5. No **opções de publicação do ficheiro**, selecione **remover ficheiros adicionais no destino** se o projeto já foi implementado cedo.
6. Depois do projeto publica, no menu de contexto da máquina virtual no Explorador de servidores, selecione **anexar depurador...**

    Azure obtém uma lista dos processos na máquina virtual e apresenta-os a anexação à caixa de diálogo de processo.

    ![Anexar o comando do depurador](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC746722.png)
7. No **anexar ao processo** caixa de diálogo, selecione **selecione** para limitar a lista de resultados para mostrar apenas os tipos de código que pretende depurar. Pode depurar código de 32 ou 64-bits gerido, o código nativo ou ambos.

    ![Selecione a caixa de diálogo de tipo de código](./media/vs-azure-tools-debug-cloud-services-virtual-machines/IC718346.png)
8. Selecione os processos que pretende depurar na máquina virtual e, em seguida, selecione **anexar**. Por exemplo, poderá escolher o processo w3wp.exe se pretendesse depurar uma aplicação web na máquina virtual. Consulte [depurar uma ou mais processos no Visual Studio](https://msdn.microsoft.com/library/jj919165.aspx) para obter mais informações.

## <a name="next-steps"></a>Passos seguintes
* Utilize **Intellitrace** para recolher um registo de eventos e as chamadas a partir de um servidor de versão. Consulte [depuração um serviço de nuvem publicada com IntelliTrace e o Visual Studio](http://go.microsoft.com/fwlink/?LinkID=623016).
* Utilize **diagnósticos do Azure** para registar informações detalhadas a execução de código dentro de funções, se as funções estão em execução no ambiente de desenvolvimento ou no Azure. Consulte [recolher dados de registo utilizando o Azure Diagnostics](http://go.microsoft.com/fwlink/p/?LinkId=400450).
