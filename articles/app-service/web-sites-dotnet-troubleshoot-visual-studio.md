---
title: "Resolver problemas de uma aplicação web no serviço de aplicações do Azure com o Visual Studio"
description: "Saiba como resolver problemas de uma aplicação web do Azure através da utilização de depuração remota, o rastreio e ferramentas de registo que são incorporadas no Visual Studio 2013."
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: 
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: rachelap
ms.openlocfilehash: 1e3aff1898665c834a70e6c49f23e408a508b10a
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2017
---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Resolver problemas de uma aplicação web no serviço de aplicações do Azure com o Visual Studio
## <a name="overview"></a>Descrição geral
Este tutorial mostra como utilizar as ferramentas do Visual Studio para o ajudar a depurar uma aplicação web no [do serviço de aplicações](http://go.microsoft.com/fwlink/?LinkId=529714), executando na [modo de depuração](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) remotamente ou visualizando os registos de aplicações e os registos do servidor web.

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

Irá aprender:

* As funções de gestão de aplicações web do Azure estão disponíveis no Visual Studio.
* Como utilizar a vista remota do Visual Studio para faça pequenas alterações numa aplicação web remoto.
* Como executar o modo de depuração remotamente durante um projeto está em execução no Azure, para uma aplicação web e um trabalho Web.
* Como criar registos de rastreio de aplicação e visualizá-los durante a aplicação está a criá-los.
* Como ver registos do servidor web, incluindo mensagens de erro detalhadas e de pedidos falhados.
* Como enviar registos de diagnóstico para um Storage do Azure da conta e visualizá-los não existe.

Se tiver o Visual Studio Ultimate, também pode utilizar [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) de depuração. IntelliTrace não está abrangido neste tutorial.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial funciona com o ambiente de desenvolvimento, o projeto web e a aplicação web do Azure que configurou no [introdução ao Azure e ao ASP.NET][GetStarted]. Para as secções de WebJobs, terá da aplicação que criar no [começar com o SDK de WebJobs do Azure][GetStartedWJ].

Os exemplos de código mostrados neste tutorial são para uma aplicação web de MVC c#, mas os procedimentos de resolução de problemas são os mesmos para aplicações de Visual Basic e formulários Web.

O tutorial parte do princípio de que está a utilizar o Visual Studio 2017. 

Os registos de transmissão em fluxo funcionalidade só funciona para aplicações .NET Framework 4 ou posterior.

## <a name="sitemanagement"></a>Gestão e configuração de aplicação Web
O Visual Studio fornece acesso a um subconjunto das funções de gestão de aplicações web e as definições de configuração disponíveis no [portal do Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Nesta secção, irá ver o que está disponível utilizando **Explorador de servidores**. Para ver as funcionalidades de integração do Azure mais recentes, experimentar **Cloud Explorer** também. Pode abrir ambas as janelas do **vista** menu.

1. Se não estiverem já iniciou sessão no Azure no Visual Studio, clique com botão direito **Azure** e selecione a ligar ao **subscrição do Microsoft Azure** no **Explorador de servidores**.

    É uma alternativa para instalar um certificado de gestão que permite o acesso à sua conta. Se optar por instalar um certificado, clique com botão direito do **Azure** no nó **Explorador de servidores**e, em seguida, selecione **gerir e subscrições de filtro** no menu de contexto. No **gerir subscrições do Microsoft Azure** caixa de diálogo, clique em de **certificados** separador e, em seguida, clique em **importação**. Siga as indicações para transferir e, em seguida, importar um ficheiro de subscrição (também designado por um *. publishsettings* ficheiro) para a sua conta do Azure.

   > [!NOTE]
   > Se transferir um ficheiro de subscrição, guarde-o para uma pasta de fora os diretórios do código de origem (por exemplo, na pasta transferências) e, em seguida, elimine-o assim que a importação foi concluída. Um utilizador mal intencionado que obtém acesso para o ficheiro de subscrição pode editar, criar e eliminar os serviços do Azure.
   >
   >

    Para obter mais informações sobre como ligar a recursos do Azure a partir do Visual Studio, consulte [gerir contas, subscrições e funções administrativas](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert).
2. No **Explorador de servidores**, expanda **Azure** e expanda **do serviço de aplicações**.
3. Expanda o grupo de recursos que inclui a aplicação web que criou no [criar uma aplicação web ASP.NET no Azure][app-service-web-get-started-dotnet.md] e, em seguida, clique no nó da aplicação web e clique em **ver definições**.

    ![Definições de visualização no Explorador de servidores](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    O **aplicação Web do Azure** é apresentado o separador e existe pode ver as tarefas de gestão e configuração de aplicação de web que estão disponíveis no Visual Studio.

    ![Janela de aplicação Web do Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    Neste tutorial, irá utilizar o registo e rastreio listas pendentes. Também iremos utilizar depuração remota, mas irá utilizar um método diferente para ativá-la.

    Para obter informações sobre as caixas de cadeias de ligação e as definições de aplicação nesta janela, consulte [Web Apps do Azure: como as cadeias de aplicação e de trabalho de cadeias de ligação](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Se pretender efetuar uma tarefa de gestão de aplicações web que não é possível efetuar nesta janela, clique em **abrir no Portal de gestão** para abrir uma janela do browser para o portal do Azure.

## <a name="remoteview"></a>Acesso aos ficheiros de aplicação web no Explorador de servidores
Normalmente, implementa um projeto web com o `customErrors` sinalizador no ficheiro Web. config definido como `On` ou `RemoteOnly`, que significa não é apresentada uma mensagem de erro úteis quando algo não bate certo. Para muitos erros, tudo obtiver é uma página como um dos seguintes:

**Erro de servidor na aplicação '/':**

![Página de erro inúteis](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**Ocorreu um erro:**

![Página de erro inúteis](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**O Web site não é possível apresentar a página**

![Página de erro inúteis](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

Frequentemente a forma mais fácil para descobrir a causa do erro é permitir mensagens de erro detalhadas que o primeiro as capturas de ecrã anteriores explica como fazê-lo. Que requer uma alteração no ficheiro Web. config implementado. Pode editar o *Web. config* de ficheiros no projeto e volte a implementar o projeto ou crie um [transformação Web. config](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) e implementar uma compilação de depuração, mas não existe uma forma mais rápida: no **Explorador de soluções** , pode ver e editar ficheiros na aplicação web remoto, utilizando diretamente o *vista remota* funcionalidade.

1. No **Explorador de servidores**, expanda **Azure**, expanda **do serviço de aplicações**, expanda o grupo de recursos que a aplicação web está localizada em e, em seguida, expanda o nó para a sua aplicação web.

    Consulte o artigo de nós que lhe conceder acesso a ficheiros de registo e ficheiros de conteúdo da aplicação web.
2. Expanda o **ficheiros** nós e faça duplo clique o *Web. config* ficheiro.

    ![Abra o Web. config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio abre o ficheiro Web. config da aplicação web remoto e mostra [remoto] junto ao nome do ficheiro na barra de título.
3. Adicione a seguinte linha para o `system.web` elemento:

    `<customErrors mode="Off"></customErrors>`

    ![Editar o Web. config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Atualize o browser que é apresentada a mensagem de erro inúteis e agora receber uma mensagem de erro detalhadas, tais como o exemplo seguinte:

    ![Mensagem de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (O erro apresentado foi criado, adicionando a linha mostrada a vermelho para *Views\Home\Index.cshtml*.)

Editar o ficheiro Web. config é apenas um exemplo de cenários em que a capacidade de ler e editar ficheiros na sua aplicação web do Azure tornar mais fácil de resolução de problemas.

## <a name="remotedebug"></a>Aplicações web do depuração remota
Se a mensagem de erro detalhadas não fornece informações suficientes e não é possível recriar o erro localmente, outra forma de resolver é executado no modo de depuração remotamente. Pode configurar pontos de interrupção, manipular diretamente a memória, siga os passos de código e mesmo alterar o caminho de código.

Depuração remota não funcionam nas edições Express do Visual Studio.

Esta secção mostra como depurar remotamente utilizando o projeto que criar no [criar uma aplicação web ASP.NET no Azure][app-service-web-get-started-dotnet.md].

1. Abra o projeto web que criou no [criar uma aplicação web ASP.NET no Azure][app-service-web-get-started-dotnet.md].

2. Abra *Controllers\HomeController.cs*.

3. Eliminar o `About()` método e inserir o seguinte código no seu lugar.

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }
4. [Definir um ponto de interrupção](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) no `ViewBag.Message` linha.

5. No **Explorador de soluções**, clique com o botão direito no projeto e, em **publicar**.

6. No **perfil** na lista pendente, selecione o mesmo perfil que é utilizado em [criar uma aplicação web ASP.NET no Azure][app-service-web-get-started-dotnet.md]. Em seguida, clique em definições.

7. No **publicar** caixa de diálogo, clique em de **definições** separador e, em seguida, altere **configuração** para **depurar**e, em seguida, clique em  **Guardar**.

    ![Publicar no modo de depuração](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

8. Clique em **Publicar**. Após a implementação estiver concluído e o browser abre-se ao URL do Azure da sua aplicação web, feche o browser.

9. No **Explorador de servidores**, a aplicação web com o botão direito e, em seguida, clique em **anexar depurador**.

    ![Anexar o depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    O browser abre-se automaticamente à sua home page em execução no Azure. Poderá ter de aguardar 20 segundos ou, pelo que, enquanto o Azure configura o servidor de depuração. Este atraso só acontece na primeira vez que executar no modo de depuração numa aplicação web num período de 48 horas. Quando iniciar a depuração dentro do mesmo período, não é um atraso.

    > [!NOTE] 
    > Se tiver quaisquer problemas em iniciar o depurador, tentar fazê-lo utilizando **Cloud Explorer** em vez de **Explorador de servidores**.
    >

10. Clique em **sobre** no menu.

     Visual Studio para no ponto de interrupção e o código está em execução no Azure, não no computador local.

11. Coloque o cursor sobre o `currentTime` variável para ver o valor de tempo.

     ![Variável de vista no modo de depuração em execução no Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     A hora apresentada é a hora do servidor do Azure, que pode ser um fuso horário diferente do seu computador local.

12. Introduza um novo valor para o `currentTime` variável, tal como "Agora em execução no Azure".

13. Prima F5 para continuar a ser executado.

     A página de acerca em execução no Azure apresenta o novo valor que introduziu para a variável de currentTime.

     ![Sobre página com o novo valor](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a>WebJobs depuração remotas
Esta secção mostra como depurar remotamente utilizando a projeto e a aplicação web que criar no [começar com o SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

As funcionalidades apresentadas nesta secção estão disponíveis apenas no Visual Studio 2013 com a atualização 4 ou posterior.

Depuração remota funciona apenas com WebJobs contínuos. WebJobs agendadas e a pedido não suporta a depuração.

1. Abra o projeto web que criou no [começar com o SDK de WebJobs do Azure][GetStartedWJ].

2. No projeto ContosoAdsWebJob, abra *Functions.cs*.

3. [Definir um ponto de interrupção](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx) na primeira instrução no `GnerateThumbnail` método.

    ![Ponto de interrupção do conjunto](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. No **Explorador de soluções**, clique com o botão direito no projeto web (e não o projeto do trabalho Web) e, em **publicar**.

5. No **perfil** na lista pendente, selecione o mesmo perfil que é utilizado em [começar com o SDK de WebJobs do Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

6. Clique em de **definições** separador e alterar **configuração** para **depurar**e, em seguida, clique em **publicar**.

    O Visual Studio implementa o web e projetos de trabalho Web e o browser abre-se ao URL do Azure da sua aplicação web.

7. No **Explorador de servidores**, expanda **Azure > do serviço de aplicações > o grupo de recursos > a aplicação web > WebJobs > Continuous**e, em seguida, clique com botão direito **ContosoAdsWebJob**.

8. Clique em **anexar depurador**.

    ![Anexar o depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    O browser abre-se automaticamente à sua home page em execução no Azure. Poderá ter de aguardar 20 segundos ou, pelo que, enquanto o Azure configura o servidor de depuração. Este atraso só acontece na primeira vez que executar no modo de depuração numa aplicação web num período de 48 horas. Quando iniciar a depuração dentro do mesmo período, não é um atraso.

9. No browser que é aberto para a home page de anúncios da Contoso, crie um novo anúncio.

    Criar um anúncio faz com que uma mensagem de fila ser criado, que é captada pelo trabalho Web e processada. Quando o SDK de WebJobs chama a função para processar a mensagem da fila, os pedidos de código o ponto de interrupção.

10. Quando interrompe o depurador, o ponto de interrupção, pode examinar e alterar os valores das variáveis enquanto o programa está a executar a nuvem. Na ilustração seguinte, o depurador mostra os conteúdos do objeto blobInfo que foi transmitido para o `GenerateThumbnail` método.

     ![objeto de blobInfo no depurador](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Prima F5 para continuar a ser executado.

     O `GenerateThumbnail` método acaba de criar a miniatura.

12. No browser, atualize a página índice e ver a miniatura.

13. No Visual Studio, prima SHIFT + F5 para pare a depuração.

14. No **Explorador de servidores**, faça duplo clique no nó ContosoAdsWebJob e clique em **vista de Dashboard**.

15. Inicie sessão com as suas credenciais do Azure e, em seguida, clique no nome do WebJob para ir para a página para o trabalho Web.

     ![Clique em ContosoAdsWebJob](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     O Dashboard mostra que o `GenerateThumbnail` função executada recentemente.

     (Da próxima vez que clicar em **vista de Dashboard**, não tem a sessão e o browser vai diretamente para a página para o trabalho Web.)

16. Clique no nome de função para ver detalhes sobre a execução de função.

     ![Detalhes de função](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Se a função [escreveu registos](https://github.com/Azure/azure-webjobs-sdk/wiki), pode clicar em **ToggleOutput** para vê-los.

## <a name="notes-about-remote-debugging"></a>Notas sobre depuração remota

* Não é recomendado em execução no modo de depuração na produção. Se a sua aplicação web de produção não é ampliar a várias instâncias de servidor, depuração impede que o servidor web responder a pedidos de outros. Se tiver várias instâncias do servidor web, quando ligar ao depurador do, obtém uma instância aleatória e não tem nenhuma forma para se certificar de que os pedidos subsequentes de browser Ir para a mesma instância. Além disso, normalmente, não implementa uma compilação de depuração para produção e otimizações de compilador para compilações de versão poderão executá-lo Mostrar o que acontece linha por linha no seu código de origem. Para resolver problemas de produção, o seu recurso melhor é aplicação web e de rastreio de registos do servidor.
* Evitar deixa de tempo em pontos de interrupção quando remoto depuração. Azure trata de um processo que é interrompido durante um período superior a alguns minutos, como um processo responder e será encerrado-lo.
* Enquanto estiver a depuração, o servidor está a enviar dados para o Visual Studio, que podem afetar a custos de largura de banda. Para obter informações sobre as taxas de largura de banda, consulte [preços do Azure](https://azure.microsoft.com/pricing/calculator/).
* Certifique-se de que o `debug` atributo do `compilation` elemento o *Web. config* ficheiro está definido como true. Está definido para verdadeiro por predefinição quando publica uma configuração de compilação de depuração.

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>
* Se achar que o depurador não avance para o código que pretende depurar, poderá ter de alterar a definição de apenas código My.  Para obter mais informações, consulte [restringir avance para apenas código My](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code).
* Inicia um temporizador no servidor, quando ativar a funcionalidade de depuração remota e, depois de 48 horas a funcionalidade é automaticamente desativada. Este limite de 48 horas é feita por motivos de segurança e desempenho. Pode facilmente ativar a funcionalidade novamente como número de vezes que for necessário. Recomendamos a inclusão desativada quando está não ativamente a depurar.
* Pode anexar manualmente o depurador qualquer processo, não apenas o web app processo (w3wp.exe). Para obter mais informações sobre como utilizar o modo de depuração no Visual Studio, consulte [depuração no Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx).

## <a name="logsoverview"></a>Descrição geral de registos de diagnóstico
Uma aplicação de ASP.NET que é executado numa aplicação web do Azure, pode criar os seguintes tipos de registos:

* **Registos de rastreio de aplicação**<br/>
  A aplicação cria estes registos ao chamar os métodos do [Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) classe.
* **Registos do servidor Web**<br/>
  O servidor web cria uma entrada de registo para todos os pedidos HTTP para a aplicação web.
* **Registos de mensagem de erro detalhadas**<br/>
  O servidor web cria uma página HTML com algumas informações adicionais para pedidos HTTP falhados (pedidos que resultam num código de estado 400 ou superior).
* **Falha de registos de rastreio de pedido**<br/>
  O servidor web cria um ficheiro XML com informações detalhadas de rastreio para pedidos HTTP falhados. O servidor web fornece também um ficheiro XSL para formatar o XML no browser.

Registo afeta o desempenho de aplicações web, pelo que o Azure dá-lhe a capacidade para ativar ou desativar cada tipo de registo, conforme necessário. Para os registos de aplicações, pode especificar que apenas os registos acima de um determinado nível de gravidade devem ser escritos. Quando cria uma nova aplicação web, por predefinição todos os registo está desativado.

Os registos são escritos em ficheiros num *LogFiles* pasta no sistema de ficheiros da sua aplicação web e estão acessível através de FTP. Registos do servidor Web e os registos de aplicações também podem ser escritos para uma conta de armazenamento do Azure. Pode manter um volume maior de registos numa conta de armazenamento que é possível no sistema de ficheiros. Está limitado a um máximo de 100 megabytes de registos ao utilizar o sistema de ficheiros. (Os registos do sistema de ficheiros são apenas para a retenção de curto prazo. Azure elimina os ficheiros de registo antigos para disponibilizar espaço para novos após ter sido atingido o limite.)  

## <a name="apptracelogs"></a>Criar e ver registos de rastreio de aplicação
Nesta secção, efetue as seguintes tarefas:

* Adicionar declarações de rastreio para o projeto web que criou no [introdução ao Azure e ao ASP.NET][GetStarted].
* Ver os registos quando executar o projeto localmente.
* Ver os registos, que são gerados pela aplicação em execução no Azure.

Para obter informações sobre como criar aplicações que inicia sessão no WebJobs, consulte [como trabalhar com armazenamento de filas do Azure utilizando o SDK de WebJobs - como escrever registos](https://github.com/Azure/azure-webjobs-sdk/wiki). As seguintes instruções para visualização de registos e controlar a forma como estiver armazenados no Azure aplicam-se também para os registos de aplicações criados pelo WebJobs.

### <a name="add-tracing-statements-to-the-application"></a>Adicionar declarações de rastreio à aplicação
1. Abra *Controllers\HomeController.cs*e substitua o `Index`, `About`, e `Contact` métodos com o seguinte código para poder adicionar `Trace` instruções e um `using` declaração do `System.Diagnostics`:

        public ActionResult Index()
        {
            Trace.WriteLine("Entering Index method");
            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Index method");
            return View();
        }

        public ActionResult About()
        {
            Trace.WriteLine("Entering About method");
            ViewBag.Message = "Your app description page.";
            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
            Trace.WriteLine("Leaving About method");
            return View();
        }

        public ActionResult Contact()
        {
            Trace.WriteLine("Entering Contact method");
            ViewBag.Message = "Your contact page.";
            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Contact method");
            return View();
        }        
2. Adicionar um `using System.Diagnostics;` declaração na parte superior do ficheiro.

### <a name="view-the-tracing-output-locally"></a>Ver o resultado do rastreio localmente
1. Prima F5 para executar a aplicação no modo de depuração.

    O serviço de escuta de rastreio predefinido escreve todos os resultados de rastreio para o **saída** janela, juntamente com outra saída de depuração. A ilustração seguinte mostra a saída das declarações de rastreio que adicionou ao `Index` método.

    ![Na janela de depuração de rastreio](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Os passos seguintes mostram como ver resultados de rastreio numa página web, sem a compilação no modo de depuração.
2. Abra o ficheiro Web. config da aplicação (aquele localizado na pasta do projeto) e adicione um `<system.diagnostics>` elemento no fim do ficheiro, imediatamente antes da tag de fecho `</configuration>` elemento:

          <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener,
                    System.Web,
                    Version=4.0.0.0,
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    O `WebPageTraceListener` saída de rastreio permite visualizar, navegando até `/trace.axd`.
3. Adicionar um <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">rastreio elemento</a> em `<system.web>` no ficheiro Web. config, tais como o exemplo seguinte:

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
4. Prima CTRL+F5 para executar a aplicação.
5. Na barra de endereço da janela do browser, adicione *trace.axd* para o URL e, em seguida, prima Enter (o URL é semelhante à http://localhost:53370/trace.axd).
6. No **aplicação rastreio** página, clique em **ver detalhes** na primeira linha (não a linha BrowserLink).

    ![TRACE.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    O **detalhes do pedido** página for apresentada e no **informações de rastreio** secção vê o resultado das declarações de rastreio que adicionou ao `Index` método.

    ![TRACE.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    Por predefinição, `trace.axd` só está disponível localmente. Se quisesse disponibilizá-lo a partir de uma aplicação web remoto, pode adicionar `localOnly="false"` para o `trace` elemento o *Web. config* ficheiro, conforme mostrado no exemplo seguinte:

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    No entanto, ativar `trace.axd` numa web de produção a aplicação não é recomendada por motivos de segurança. As secções seguintes, irá ver uma forma mais fácil de ler os registos de rastreio numa aplicação web do Azure.

### <a name="view-the-tracing-output-in-azure"></a>Ver a saída de rastreio no Azure
1. No **Explorador de soluções**, clique com o botão direito no projeto web e clique em **publicar**.
2. No **publicar Web** caixa de diálogo, clique em **publicar**.

    Depois do Visual Studio publica a atualização, é aberta uma janela do browser para a sua home page (partindo do princípio de que não tenha limpar **URL de destino** no **ligação** separador).
3. No **Explorador de servidores**, a aplicação web com o botão direito e selecione **ver registos de transmissão em fluxo**.

    ![Ver registos de transmissão em fluxo no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    O **saída** janela mostra que estão ligados ao serviço de registo para transmissão em fluxo e adiciona uma linha de notificação a cada minuto que passa sem um registo para apresentar.

    ![Ver registos de transmissão em fluxo no menu de contexto](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. Na janela do browser mostra a home page da aplicação, clique em **contacte**.

    Dentro de alguns segundos, o resultado do nível de erro que adicionou ao analisar o `Contact` método aparece no **saída** janela.

    ![Rastreio de erro na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio só está visível rastreios de nível de erro uma vez que é a predefinição quando ativar o registo do serviço de monitorização. Quando cria uma nova aplicação web do Azure, todos os registo está desativado por predefinição, conforme mostrado quando abrir a página de definições anteriormente:

    ![Aplicação terminar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    No entanto, se tiver selecionado **ver registos de transmissão em fluxo**, Visual Studio alteradas automaticamente **aplicação Logging(File System)** para **erro**, que significa que os registos de nível de erro obter reportados. Para ver todos os seus registos de rastreio, pode alterar esta definição para **verboso**. Quando seleciona um nível de gravidade inferior de erro, todos os registos para níveis de gravidade superiores também são reportados. Por isso, quando seleciona verboso, também verá registos de erro, aviso e informações.  

1. No **Explorador de servidores**, faça duplo clique na aplicação web e, em seguida, clique em **ver definições** como fez anteriormente.
2. Alteração **registo na aplicação (sistema de ficheiros)** para **verboso**e, em seguida, clique em **guardar**.

    ![A definição de nível de rastreio verboso](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. Na janela do browser que está agora a ser mostrada a **contacte** página, clique em **home page**, em seguida, clique em **sobre**e, em seguida, clique em **contacte**.

    Dentro de alguns segundos, o **saída** janela mostra todos os seus saída de rastreio.

    ![Resultados de rastreio verboso](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    Nesta secção, ativada e desativar o registo, utilizando as definições de aplicação web do Azure. Também pode ativar e desativar os serviços de escuta de rastreio ao modificar o ficheiro Web. config. No entanto, modificar o ficheiro Web. config faz com que o domínio de aplicação reciclar, ao ativar o registo através da configuração de aplicação web não fazê-lo. Se o problema demora muito tempo a reproduzir, ou intermitente, reciclar o domínio de aplicação poderá "corrigir" e exigir que deve aguardar até que ocorre novamente. Ativar o diagnóstico na Azure permite-lhe iniciar imediatamente a capturar informações de erro sem reciclar o domínio de aplicação.

### <a name="output-window-features"></a>Funcionalidades de janela de saída
O **registos do Microsoft Azure** separador do **saída** janela tem vários botões e uma caixa de texto:

![Botões de separador registos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

Estes efetuam as seguintes funções:

* Limpar o **saída** janela.
* Ativar ou desativar a moldagem do word.
* Iniciar ou parar a monitorização de registos.
* Especifique que os registos para monitorizar.
* Transferir os registos.
* Filtre registos com base numa cadeia de procura ou uma expressão regular.
* Fechar o **saída** janela.

Se introduzir uma cadeia de procura ou expressão regular, o Visual Studio filtra as informações de registo do cliente. Isto significa que pode introduzir os critérios depois dos registos são apresentados no **saída** janela e o utilizador pode alterar os critérios de filtragem sem ter de voltar a gerar os registos.

## <a name="webserverlogs"></a>Ver registos de servidor web
Registos do servidor Web registam toda a atividade de HTTP da aplicação web. Para poder vê-los no **saída** janela, tem de ativá-los para a aplicação web e indique ao Visual Studio que pretende que a monitorizá-las.

1. No **configuração de aplicação Web do Azure** separador abertos a partir de **Explorador de servidores**, alterar o registo de servidor Web para **no**e, em seguida, clique em **guardar**.

    ![Ativar o registo de servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. No **saída** janela, clique em de **especificar que os registos de Microsoft Azure para monitorizar** botão.

    ![Especificar os registos do Azure para monitorizar](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. No **opções de registo do Microsoft Azure** caixa de diálogo, selecione **registos do servidor de Web**e, em seguida, clique em **OK**.

    ![Monitorize os registos do servidor web](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Na janela do browser mostra a aplicação web, clique em **home page**, em seguida, clique em **sobre**e, em seguida, clique em **contacte**.

    Os registos da aplicação, geralmente, são apresentados primeiro, seguido dos registos do servidor web. Poderá ter de Aguarde algum tempo para os registos a aparecer.

    ![Servidor Web regista os na janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Por predefinição, quando ativa primeiro a registos do servidor web utilizando o Visual Studio, o Azure escreve os registos para o sistema de ficheiros. Como alternativa, pode utilizar o portal do Azure para especificar que o servidor web devem ser escritos registos para um contentor de BLOBs numa conta do storage.

Se utilizar o portal para ativar o servidor web, registo de uma conta de armazenamento do Azure e, em seguida, desative o registo no Visual Studio, quando voltar a ativar o registo no Visual Studio definições da conta de armazenamento estão restauradas.

## <a name="detailederrorlogs"></a>Ver registos de mensagem de erro detalhadas
Registos de erros detalhados fornecem algumas informações adicionais sobre pedidos de HTTP que resultam de códigos de resposta de erro (400 ou acima). Para poder vê-los no **saída** janela, tem de ativá-los para a aplicação web e indique ao Visual Studio que pretende que a monitorizá-las.

1. No **configuração de aplicação Web do Azure** separador abertos a partir de **Explorador de servidores**, alterar **mensagens de erro detalhadas** para **no**e, em seguida, Clique em **guardar**.

    ![Ativar mensagens de erro detalhadas](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. No **saída** janela, clique em de **especificar que os registos de Microsoft Azure para monitorizar** botão.

3. No **opções de registo do Microsoft Azure** caixa de diálogo, clique em **todos os registos**e, em seguida, clique em **OK**.

    ![Monitorizar todos os registos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Na barra de endereço da janela do browser, adicione um caráter adicional para o URL de causar um erro 404 (por exemplo, `http://localhost:53370/Home/Contactx`), e prima Enter.

    Após alguns segundos, o registo de erros detalhados aparece no Visual Studio **saída** janela.

    ![Registo de erros detalhados - janela de saída](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Controlar + clique na ligação para ver a saída de registo a formatados num browser:

    ![Registo de erros detalhados - janela do browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>Transferir os registos do sistema de ficheiros
Os registos de monitorização no **saída** janela também pode ser transferida como um *. zip* ficheiro.

1. No **saída** janela, clique em **transferir os registos de transmissão em fluxo**.

    ![Botões de separador registos](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    Explorador de ficheiros abre-se a sua *transfere* pasta com o ficheiro transferido selecionado.

    ![Ficheiro transferido](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. Extrair o *. zip* ficheiros e ver a seguinte estrutura de pasta:

    ![Ficheiro transferido](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * Registos de rastreio de aplicação estão na *. txt* ficheiros no *LogFiles\Application* pasta.
   * Registos de servidores Web estão em *. log* ficheiros no *LogFiles\http\RawLogs* pasta. Pode utilizar uma ferramenta como [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) para visualizar e manipular estes ficheiros.
   * Os registos de mensagem de erro detalhadas são no **.HTML* ficheiros no *LogFiles\DetailedErrors* pasta.

    (O *implementações* pasta destina-se os ficheiros criados pelo controlo de origem publicação; não tem nada relacionadas com publicação do Visual Studio. O *Git* pasta destina-se rastreios relacionadas com controlo de origem a publicação e o ficheiros de registo serviço de transmissão em fluxo.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](http://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="failedrequestlogs"></a>Ver registos de rastreio de pedidos falhados
Registos de rastreio de pedidos falhados são úteis quando tem de compreender os detalhes da forma como o IIS é processar um pedido HTTP, nos cenários tais como problemas de autenticação ou conversão de URL.

As aplicações web do Azure utilizam a mesma funcionalidade de rastreio de pedido falhado que está disponíveis no IIS 7.0 e posterior. Não tem acesso às definições do IIS que configurar os erros são registados, no entanto. Quando ativar o rastreio de pedidos falhados, todos os erros são capturados.

Pode ativar o rastreio de pedidos falhados ao utilizar o Visual Studio, mas não é possível vê-las no Visual Studio. Estes registos são ficheiros XML. O serviço de registo de transmissão em fluxo monitoriza apenas os ficheiros que são considerados legíveis no modo de texto simples: *. txt*, **.HTML*, e *. log* ficheiros.

Pode ver os registos de rastreio de pedidos falhados num browser diretamente através do FTP ou localmente depois de utilizar uma ferramenta FTP para transferi-los para o seu computador local. Nesta secção, irá vê-las num browser diretamente.

1. No **configuração** separador do **aplicação Web do Azure** janela abertos a partir de **Explorador de servidores**, alterar **rastreio de pedidos falhados** para **No**e, em seguida, clique em **guardar**.

    ![Ativar o rastreio de pedidos falhados](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Na barra de endereço da janela do browser que mostra a aplicação web, adicione um caráter adicional para o URL e clique em Enter para fazer com que um erro 404.

    Isto faz com que um registo de rastreio de pedido falhado ser criado e os passos seguintes mostram como ver ou transferir o registo.

3. No Visual Studio, no **configuração** separador do **aplicação Web do Azure** janela, clique em **aberto no Portal de gestão**.

4. No [portal do Azure](https://portal.azure.com) **definições** página para a sua aplicação web, clique em **as credenciais de implementação**e, em seguida, introduza um novo nome de utilizador e palavra-passe.

    ![Novo nome de utilizador FTP e a palavra-passe](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Quando iniciar sessão, terá de utilizar o nome de utilizador completo com o nome da aplicação web prefixo ao mesmo. Por exemplo, se introduzir "myid" como um nome de utilizador e o site é "omeuexemplo", pode iniciar sessão como "myexample\myid".
    >

5. Na nova janela do browser, aceda ao URL que é apresentado em **FTP hostname** ou **FTPS hostname** no **descrição geral** página para a sua aplicação web.

6. Inicie sessão com as credenciais FTP que criou anteriormente (incluindo web app prefixo de nome para o nome de utilizador).

    O browser apresenta a pasta raiz da aplicação web.

7. Abra o *LogFiles* pasta.

    ![Abra a pasta de LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Abra a pasta com o nome W3SVC e um valor numérico.

    ![Abra a pasta de W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    A pasta contém os ficheiros XML para quaisquer erros que foram registados depois de ativar o rastreio de pedidos falhados e um ficheiro XSL que pode utilizar um browser para formatar o XML.

    ![Pasta de W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Clique no ficheiro XML para o pedido falhado que pretende ver informações de rastreio para.

    A ilustração seguinte mostra a parte das informações de rastreio para um erro de exemplo.

    ![Pedidos falhados no browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>Passos Seguintes
Que viu como Visual Studio torna mais fácil ver registos criados por uma aplicação web do Azure. As secções seguintes fornecem ligações para recursos mais nos tópicos relacionados:

* Resolução de problemas de aplicações web do Azure
* Depuração no Visual Studio
* Remoto depuração no Azure
* Rastreio em aplicações do ASP.NET
* Analisa registos do servidor web
* Analisa registos de rastreio de pedidos falhados
* Depuração serviços Cloud

### <a name="azure-web-app-troubleshooting"></a>Resolução de problemas de aplicações web do Azure
Para obter mais informações sobre resolução de problemas de web apps no App Service do Azure, consulte os seguintes recursos:

* [Como monitorizar aplicações web](/manage/services/web-sites/how-to-monitor-websites/)
* [Investigar fugas de memória em aplicações Web do Azure com o Visual Studio 2013](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx). Mensagem de blogue do Microsoft ALM sobre as funcionalidades do Visual Studio para analisar problemas de memória gerido.
* [Ferramentas de online de aplicações de web do Azure que deve conhecer sobre](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/). Mensagem de blogue por Amit Apple.

Para obter ajuda com uma pergunta de resolução de problemas específica, inicie um thread dos Fóruns do seguintes:

* [O Fórum do Azure no site do ASP.NET](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [O Fórum do Azure no MSDN](http://social.msdn.microsoft.com/Forums/windowsazure/).
* [StackOverflow.com](http://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Depuração no Visual Studio
Para obter mais informações sobre como utilizar o modo de depuração no Visual Studio, consulte [depuração no Visual Studio](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx) e [sugestões de depuração com o Visual Studio 2010](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx).

### <a name="remote-debugging-in-azure"></a>Remoto depuração no Azure
Para obter mais informações sobre depuração remota para aplicações web do Azure e WebJobs, consulte os seguintes recursos:

* [Introdução às Web Apps do App Service do Azure a depuração de remoto](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/).
* [Introdução à parte de remoto depuração Azure Web Apps do App Service 2 - dentro de depuração remota](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introdução ao depuração remota na peça Web Apps do Azure App Service 3 - GIT e de ambiente de várias instâncias](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs depuração (vídeo)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Se a sua aplicação web utiliza um back-end de API Web do Azure ou Mobile Services e tiver de depurar que, consulte [depuração back-end .NET no Visual Studio](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx).

### <a name="tracing-in-aspnet-applications"></a>Rastreio em aplicações do ASP.NET
Não existem não existem introduções de detalhado e atualizadas para o rastreio de ASP.NET disponíveis na Internet. O melhor pode fazê-lo é comece antigos materiais introdutórias escritos para formulários Web porque não foi ainda existe MVC e complementar que com mais recente blogue mensagens que foco no problemas específicos. Alguns locais boas iniciar são os seguintes recursos:

* [Monitorização e de telemetria (compilar aplicações de nuvem reais com o Azure)](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry).<br>
  E-Book transferível capítulo com recomendações para o rastreio em aplicações em nuvem do Azure.
* [Rastreio de ASP.NET](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  Antigo, mas ainda um recurso boa para uma introdução básica para o assunto.
* [Os serviços de escuta de rastreio](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  Informações sobre os serviços de escuta de rastreio, mas não mencionar o [WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx).
* [Instruções: Integrar o rastreio de ASP.NET System.Diagnostics rastreio](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  Este artigo também é antigo, mas inclui algumas informações adicionais que não abrange o artigo introdutório.
* [Rastreio no ASP.NET MVC Razor vistas](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Para além de rastreio nas vistas de Razor, a mensagem também explica como criar um filtro de erro para iniciar a sessão de todas as exceções não processadas numa aplicação MVC. Para informações sobre como iniciar sessão exceções todos os não processadas numa aplicação formulários Web, consulte o exemplo de global. asax na [exemplo completo para processadores erro](http://msdn.microsoft.com/library/bb397417.aspx) no MSDN. Em MVC ou formulários Web, se pretender registar determinadas exceções, mas permitir a estrutura da predefinição processamento ativado para os mesmos, pode detetar e rethrow como no exemplo seguinte:

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        }
* [Transmissão em fluxo rastreio de diagnóstico registo a partir da linha de comandos do Azure (mais Glimpse!)](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Como utilizar a linha de comandos para que este tutorial mostra como fazê-lo no Visual Studio. [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) é uma ferramenta para depuração de aplicações do ASP.NET.
* [Com as Web Apps do registo e diagnóstico - David Ebbo](/documentation/videos/azure-web-site-logging-and-diagnostics/) e [registos de aplicações Web - com o David Ebbo de transmissão em fluxo](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Vídeos através da autoria de Scott Hanselman e David Ebbo.

Registo de erros, uma alternativa ao escrever o seu próprio código de rastreio é utilizar uma arquitetura de registo de open source como [ELMAH](http://nuget.org/packages/elmah/). Para obter mais informações, consulte [mensagens do blogue de autoria de Scott Hanselman sobre ELMAH](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Além disso, não precisa de utilizar o ASP.NET ou `System.Diagnostics` registos de rastreio para obter a transmissão em fluxo a partir do Azure. A aplicação web do Azure, o serviço de registo de transmissão em fluxo fluxos qualquer *. txt*, **.HTML*, ou *. log* ficheiro que se encontra no *LogFiles* pasta. Por conseguinte, é possível criar o seu próprio sistema de registo que escreve para o sistema de ficheiros da aplicação web, e o ficheiro é automaticamente transmissão em fluxo e transferido. Tudo o que precisa de executar é escrita código da aplicação que cria ficheiros de *d:\home\logfiles* pasta.

### <a name="analyzing-web-server-logs"></a>Analisa registos do servidor web
Para obter mais informações sobre como analisar os registos do servidor web, consulte os seguintes recursos:

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Uma ferramenta para visualizar os dados no registos do servidor web (*. log* ficheiros).
* [Resolução de problemas de desempenho do IIS ou erros de aplicações utilizando LogParser](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Uma introdução à ferramenta Analisador de registo que pode utilizar para analisar os registos do servidor web.
* [Mensagens do blogue por Robert McMurray utilizando LogParser](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [O código de estado HTTP no IIS 7.0, IIS 7.5 e IIS 8.0](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analisa registos de rastreio de pedidos falhados
O Web site Microsoft TechNet inclui um [utilizando o rastreio de pedidos falhados](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing) secção, o que poderá ser útil para compreender como utilizar estes registos. No entanto, esta documentação centra-se principalmente na configuração de rastreio de pedidos falhados no IIS, que não pode fazer no Web Apps do Azure.

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
