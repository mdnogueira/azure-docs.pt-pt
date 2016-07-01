<properties
    pageTitle="Funções da Web e de trabalho do Python com Ferramentas do Python 2.2 para Visual Studio | Microsoft Azure"
    description="Descrição geral da utilização das Ferramentas do Python para Visual Studio para a criação de Cloud Services do Azure, incluindo funções da Web e funções de trabalho."
    services="cloud-services"
    documentationCenter="python"
    authors="thraka"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.date="08/30/2015"
    ms.author="adegeo"/>




# Funções da Web e de trabalho do Python com Ferramentas de Python 2.2 para Visual Studio

Este artigo fornece uma descrição geral da utilização de funções da Web e de trabalho do Python através das [Ferramentas do Python para Visual Studio][].

## Pré-requisitos

 - Visual Studio 2013 ou 2015
 - [Ferramentas do Python 2.2 para Visual Studio][] (PTVS)
 - [Ferramentas do Azure SDK para VS 2013][] ou [Ferramentas do Azure SDK para VS 2015][]
 - [Python 2.7 de 32 bits][] ou [Python 3.4 de 32 bits][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## O que são as funções da Web e de trabalho do Python?

O Azure oferece três modelos de computação para a execução de aplicações: [funcionalidade de Web Apps no App Service do Azure][modelo de execução-Web Sites], [Virtual Machines do Azure][vms do modelo de execução] e [Cloud Services do Azure][Cloud Services do modelo de execução]. Os três modelos suportam o Python. Os Cloud Services, que incluem funções da Web e de trabalho, fornecem uma *Plataforma como Serviço (PaaS)*. Dentro de um serviço em nuvem, uma função da Web fornece um servidor Web de Serviços de Informação Internet (IIS) dedicado para alojar as Web Apps front-end, enquanto uma função de trabalho pode executar tarefas assíncronas, de execução longa ou perpétuas, independentes da interação ou da intervenção do utilizador.

Para obter mais informações, consulte [O que é um Serviço em Nuvem?].

> [AZURE.NOTE] *Pretende compilar um site simples?*
Se o seu cenário envolver apenas um front-end de um Web site simples, considere utilizar a funcionalidade Web Apps simples no App Service do Azure. Pode facilmente atualizar para uma Serviço em Nuvem à medida que o Web site cresce e os seus requisitos se alteram. Consulte os artigos do <a href="/develop/python/">Centro para Programadores do Python</a> relativos ao desenvolvimento da funcionalidade Web Apps no App Service do Azure.
<br />


## Criação do projeto

No Visual Studio, pode selecionar **Serviço em Nuvem do Azure** na caixa de diálogo **Novo Projeto**, em **Python**.

![Caixa de Diálogo Novo Projeto](./media/cloud-services-python-ptvs/new-project-cloud-service.png)

No assistente do Serviço em Nuvem do Azure, pode criar novas funções da Web e de trabalho.

![Caixa de Diálogo Serviço em Nuvem do Azure.](./media/cloud-services-python-ptvs/new-service-wizard.png)

O modelo de função de trabalho é fornecido com o código automático de ligação a uma conta do Storage do Azure ou ao Service Bus do Azure.

![Solução de Serviço em Nuvem](./media/cloud-services-python-ptvs/worker.png)

Pode adicionar funções da Web ou de trabalho a um serviço em nuvem existente em qualquer altura.  Pode optar por adicionar projetos existentes na sua solução ou criar novos.

![Adicionar Comando de Função](./media/cloud-services-python-ptvs/add-new-or-existing-role.png)

O serviço em nuvem pode conter funções implementadas em diferentes idiomas.  Por exemplo, pode ter uma função da Web do Python implementada utilizando Django, com o Python ou com funções de trabalho do C#.  Pode comunicar facilmente entre as funções de utilizar as filas do Service Bus ou as filas de armazenamento.

## Executar localmente

Se definir o seu projeto de serviço em nuvem como o projeto de arranque e premir F5, o serviço em nuvem será executado no emulador local do Azure.

Apesar de o PTVS suportar a iniciação no emulador, a depuração (por exemplo, pontos de interrupção) não funcionará.

Para depurar as suas funções da Web e de trabalho, pode optar por definir o projeto de função como o projeto de arranque e efetuar a depuração.  Também pode definir vários projetos de arranque.  Clique com o botão direito do rato na solução e, em seguida, selecione **Definir Projetos de Arranque**.

![Propriedades do Projeto de Arranque da Solução](./media/cloud-services-python-ptvs/startup.png)

## Publicar no Azure

Para publicar, clique com o botão direito do rato no projeto do serviço em nuvem na solução e, em seguida, selecione **Publicar**.

![Início de Sessão de Publicação do Microsoft Azure](./media/cloud-services-python-ptvs/publish-sign-in.png)

Na página de definições, selecione o serviço em nuvem no qual pretende publicar.

![Definições de Publicação do Microsoft Azure](./media/cloud-services-python-ptvs/publish-settings.png)

Pode criar um novo serviço em nuvem se ainda não tiver um disponível.

![Criar Caixa de Diálogo Serviço em Nuvem](./media/cloud-services-python-ptvs/publish-create-cloud-service.png)

Também é útil ativar as ligações de ambiente de trabalho remoto à(s) máquina(s) para a depuração de falhas.

![Caixa de Diálogo Configuração do Ambiente de Trabalho Remoto](./media/cloud-services-python-ptvs/publish-remote-desktop-configuration.png)

Quando tiver concluído a configuração de definições, clique em **Publicar**.

Será apresentado algum progresso na janela de resultados e, em seguida, verá a janela de Registo de Atividades do Microsoft Azure.

![Janela de Registo de Atividades do Microsoft Azure](./media/cloud-services-python-ptvs/publish-activity-log.png)

A implementação demorará alguns minutos a ser concluída. Em seguida, as funções da Web e de trabalho estarão em execução no Azure!

## Passos seguintes

Para obter informações mais detalhadas sobre como trabalhar com funções da Web e de trabalho nas Ferramentas do Python para Visual Studio, consulte a documentação das PTVS:

- [Projetos do Serviço em Nuvem][]

Para obter mais detalhes sobre a utilização de serviços do Azure a partir das suas funções da Web e de trabalho, tais como utilizar o Storage do Azure ou o Service Bus, consulte os seguintes artigos.

- [Serviço Blob][]
- [Serviço Tabela][]
- [Serviço Fila][]
- [Filas de Service Bus][]
- [Tópicos de Service Bus][]


<!--Link references-->

[O que é um Serviço em Nuvem?]: ./cloud-services/cloud-services-choose-me.md
[modelo de execução-Web Sites]: ./app-service-web/app-service-web-overview.md
[modelo de execução-vms]: ./virtual-machines/virtual-machines-windows-about.md
[modelo de execução-cloud services]: ./cloud-services/cloud-services-choose-me.md
[Centro para Programadores do Python]: /develop/python/

[Serviço Blob]: ./storage/storage-python-how-to-use-blob-storage.md
[Serviço Fila]: ./storage/storage-python-how-to-use-queue-storage.md
[Serviço Tabela]: ./storage/storage-python-how-to-use-table-storage.md
[Filas de Service Bus]: ./service-bus/service-bus-python-how-to-use-queues.md
[Tópicos de Service Bus]: ./service-bus/service-bus-python-how-to-use-topics-subscriptions.md


<!--External Link references-->

[Ferramentas do Python para Visual Studio]: http://aka.ms/ptvs
[Documentação das Ferramentas do Python para Visual Studio]: http://aka.ms/ptvsdocs
[Projetos do Serviço em Nuvem]: http://go.microsoft.com/fwlink/?LinkId=624028
[Ferramentas do Python 2.2 para Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Ferramentas do Azure SDK para VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Ferramentas do Azure SDK para VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 de 32 bits]: http://go.microsoft.com/fwlink/?LinkId=517191



<!--HONumber=Jun16_HO2-->


