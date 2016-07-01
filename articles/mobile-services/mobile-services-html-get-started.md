<properties
    pageTitle="Introdução aos Mobile Services do Azure para aplicações HTML/JavaScript | Microsoft Azure"
    description="Siga este tutorial para começar a utilizar os Mobile Services do Azure para o desenvolvimento de HTML."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html5"
    ms.devlang="javascript"
    ms.topic="get-started-article" 
    ms.date="11/30/2015"
    ms.author="glenga"/>


# <a name="getting-started"> </a>Introdução aos Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Descrição geral 

Este tutorial mostra como adicionar um serviço de back-end baseado na nuvem a uma aplicação móvel HTML utilizando os Mobile Services do Azure. Neste tutorial, irá criar um novo serviço móvel e uma aplicação *Lista de tarefas* simples que armazena os dados da aplicação no novo serviço móvel. Pode ver a seguinte versão em vídeo deste tutorial. 

> [AZURE.VIDEO mobile-get-started-html]
 
Abaixo é exibida uma captura de ecrã da aplicação concluída:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais dos Mobile Services para aplicações HTML. Para uma aplicação PhoneGap/Cordova, consulte a secção [Versão PhoneGap/Cordova](mobile-services-javascript-backend-phonegap-get-started.md) deste tutorial.

##Pré-requisitos

O seguinte é necessário para concluir este tutorial:

+ Tem de ter um dos seguintes servidores Web em execução no computador local:

    +  **No Windows**: IIS Express. O IIS Express é instalado pelo [Instalador de Plataforma Web da Microsoft].
    +  **No MacOS X**: Python, que já deve estar instalado.
    +  **No Linux**: Python. Tem de instalar a [versão mais recente do Python].

    Pode utilizar qualquer servidor Web para alojar a aplicação, mas estes são os servidores Web suportados pelos scripts transferidos.  

+ Um browser que suporte HTML5.
+ Uma conta do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-html%2F"%20target="_blank). 


## <a name="create-new-service"> </a>Criar um novo serviço móvel

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar uma nova aplicação HTML

Assim que tiver criado o seu serviço móvel, pode seguir um guia de introdução fácil no Portal Clássico do Azure para criar uma nova aplicação ou modificar uma aplicação existente para ligar ao seu serviço móvel.

Nesta secção, irá criar uma nova aplicação HTML que está ligada ao seu serviço móvel.

1.  No [Portal Clássico do Azure], clique em **Mobile Services** e, em seguida, clique no serviço móvel que acabou de criar.


2. No separador de início rápido, clique em **Windows** em **Escolher plataforma** e expanda **Criar uma nova aplicação HTML**.

    ![][6]

    Esta ação apresenta os três passos fáceis necessários para criar e alojar uma aplicação HTML ligada ao seu serviço móvel.

    ![][7]

3. Clique em **Criar tabela de Itens da Lista da Tarefas** para criar uma tabela para armazenar os dados da aplicação.

4. Em **Transferir e executar a aplicação**, clique em **Transferir**.

    Esta ação transfere os ficheiros do Web site para a aplicação _Lista de tarefas_ de exemplo que está ligada ao seu serviço móvel. Guarde o ficheiro comprimido no computador local e tome nota do local onde o guardou.

5. No separador **Configurar**, certifique-se de que `localhost` já está listado na lista **Permitir pedidos de nomes de anfitrião** em **Partilha de Recursos de Várias Origens (CORS)**. Se não estiver, introduza `localhost` no campo **Nome de anfitrião** e, em seguida, clique em **Guardar**.

    ![][9]

    > [AZURE.IMPORTANT] Se implementar a aplicação de início rápido num servidor Web que não seja localhost, tem de adicionar o nome de anfitrião do servidor Web à lista **Permitir pedidos de nomes de anfitrião**. Para obter mais informações, consulte [Partilha de recursos de várias origens](http://msdn.microsoft.com/library/windowsazure/dn155871.aspx).

## Alojar e executar a aplicação HTML

A etapa final deste tutorial consiste em alojar e executar a nova aplicação no computador local.

1. Navegue até à localização onde guardou os ficheiros de projeto comprimidos, expanda os ficheiros no computador e inicie um dos seguintes ficheiros de comando a partir da subpasta **servidor**.

    + **launch-windows** (computadores Windows)
    + **launch-mac.command** (computadores Mac OS X)
    + **launch-linux.sh** (computadores Linux)

    > [AZURE.NOTE] Num computador Windows, introduza `R` quando o PowerShell lhe pedir para confirmar que pretende executar o script. O browser poderá avisá-lo para não executar o script porque este foi transferido a partir da Internet. Quando isto acontecer, tem de solicitar que o browser carregue o script.

    Esta ação inicia um servidor Web no computador local para alojar a nova aplicação.

2. Abra o URL <a href="http://localhost:8000/" target="_blank">http://localhost:8000/</a> num browser para iniciar a aplicação.

3. Na aplicação, digite um texto significativo, tal como _Concluir o tutorial_, em **Introduzir nova tarefa**, e clique em **Adicionar**.

    ![][10]

    Esta ação envia um pedido POST para o novo serviço móvel alojado no Azure. Os dados do pedido são inseridos na tabela Item da Lista de Tarefas. Os itens armazenados na tabela são devolvidos pelo servidor móvel e os dados são apresentados na segunda coluna na aplicação.

    > [AZURE.NOTE] Pode rever o código que acede ao seu serviço móvel para consultar e inserir dados, o qual se encontra no ficheiro page.js.

4. No [Portal Clássico do Azure], clique no separador **Dados** e, em seguida, clique na tabela **Itens da Lista de Tarefas**.

    ![][11]

    Esta ação permite procurar os dados inseridos pela aplicação na tabela.

    ![][12]

## <a name="next-steps"> </a>Passos Seguintes
Agora que concluiu o guia de introdução, saiba como efetuar tarefas importantes adicionais nos Mobile Services:

* **[Adicionar autenticação à aplicação]**  
  Saiba como autenticar os utilizadores da aplicação com um fornecedor de identidade.

* **[Referência Conceptual sobre Procedimentos dos Mobile Services para HTML/JavaScript]**  
  Saiba mais sobre como utilizar os Mobile Services com HTML/JavaScript


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Introdução aos Mobile Services]:#getting-started
[Criar um novo serviço móvel]:#create-new-service
[Definir a instância do serviço móvel]:#define-mobile-service-instance
[Passos Seguintes]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-html-get-started/mobile-quickstart-completed-html.png

[6]: ./media/mobile-services-html-get-started/mobile-portal-quickstart-html.png
[7]: ./media/mobile-services-html-get-started/mobile-quickstart-steps-html.png

[9]: ./media/mobile-services-html-get-started/mobile-services-set-cors-localhost.png
[10]: ./media/mobile-services-html-get-started/mobile-quickstart-startup-html.png
[11]: ./media/mobile-services-html-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-html-get-started/mobile-data-browse.png


<!-- URLs. -->
[Adicionar autenticação à aplicação]: mobile-services-html-get-started-users.md

[Portal Clássico do Azure]: https://manage.windowsazure.com/
[Instalador de Plataforma Web da Microsoft]:  http://go.microsoft.com/fwlink/p/?LinkId=286333
[versão mais recente do Python]: http://go.microsoft.com/fwlink/p/?LinkId=286342
[Referência Conceptual sobre Procedimentos dos Mobile Services para HTML/JavaScript]: mobile-services-html-how-to-use-client-library.md
[Partilha de recursos de várias origens]: http://msdn.microsoft.com/library/azure/dn155871.aspx
 



<!--HONumber=Jun16_HO2-->


