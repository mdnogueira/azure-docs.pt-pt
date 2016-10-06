<properties
    pageTitle="Criar uma conta de Media Services | Microsoft Azure"
    description="Descreve como criar uma nova conta de Media Services do Azure no Azure."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="juliako"/>



# Criar uma conta dos Azure Media Services

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](http://msdn.microsoft.com/library/azure/dn194267.aspx)


> [AZURE.NOTE] Para concluir este tutorial, precisa de uma conta do Azure. Para obter mais detalhes, consulte [Avaliação Gratuita do Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
 
O Portal Clássico do Azure fornece uma forma para criar rapidamente uma conta de Media Services do Azure. Pode utilizar a sua conta para aceder aos Media Services que permitem armazenar, encriptar, codificar, gerir e transmitir conteúdo no Azure. No momento em que cria uma conta de Media Services, também cria uma conta do Storage associada (ou utilize uma já existente) na mesma região geográfica da conta de Media Services.

Este artigo explica como utilizar o método de Criação Rápida para criar uma nova conta de Media Services e, em seguida, associá-la a uma conta do Storage.

<a id="concepts"></a>
## Conceitos

O acesso aos Media Services requer duas contas associadas:

-   **Uma conta de Media Services**. A sua conta dá-lhe acesso a um conjunto de Media Services baseados na nuvem disponíveis no Azure. Uma conta de Media Services não armazena conteúdo de multimédia real. Em vez disso, armazena os metadados sobre o conteúdo de multimédia e as tarefas de processamento de multimédia na sua conta. No momento que criar a conta, seleciona uma região de Media Services disponível. A região que selecionou é um datacenter que armazena os registos de metadados para a sua conta.

    As regiões dos Media Services Disponíveis (AMS) incluem: Europa do Norte, Europa Ocidental, EUA Oeste, EUA Leste, Sudeste Asiático, Ásia Oriental, Oeste do Japão, Leste do Japão. Os Media Services não utilizam grupos de afinidade.
    
    As AMS estão agora disponíveis também nos seguintes centros de dados: Sul do Brasil, Índia Ocidental, Sul da Índia e Índia Central. Agora pode utilizar o Portal Clássico do Azure para [criar contas de Serviço Multimédia](media-services-create-account.md#create-a-media-services-account-using-quick-create) e realizar várias tarefas descritas [aqui](https://azure.microsoft.com/documentation/services/media-services/). No entanto, o Live Encoding não está ativado nestes centros de dados. Além disso, nem todos os tipos de Unidades de Codificação Reservadas estão disponíveis nestes centros de dados.
    
    - Sul do Brasil:                                          Estão disponíveis apenas Unidades de Codificação reservadas Básicas e Standard
    - Índia Ocidental, Sul da Índia e Índia Central:              Estão disponíveis apenas Unidades de Codificação Reservadas Básicas


-   **Uma conta do Storage associada**. A sua conta do Storage é uma conta do Storage do Azure associada à sua conta de Media Services. A conta do Storage fornece Blob Storage para ficheiros de multimédia e tem de estar localizada na mesma região geográfica que a conta de Media Services. Quando cria uma conta de Media Services, pode optar por uma conta do Storage existente na mesma região ou pode criar uma nova conta do Storage na mesma região. Se eliminar uma conta de Media Services, os blobs na sua conta do Storage relacionada não são eliminados.

<a id="quick"></a>
## Criar uma conta de Media Services utilizando Criação Rápida

1. No [Portal Clássico do Azure][], clique em **Novo**, em **Serviço de Multimédia** e em **Criação Rápida**.

![Criação Rápida dos Media Services](./media/media-services-create-account/wams-QuickCreate.png)

2. Em **NOME**, introduza o nome da nova conta. Um nome de conta dos Media Services é composto por números ou letras minúsculas sem espaços, com 3 a 24 carateres de comprimento.

3. Em **REGIÃO**, selecione a região geográfica que será utilizada para armazenar os registos de metadados da conta dos Media Services. Apenas as regiões dos Media Services disponíveis são apresentadas na lista pendente.

4. Em **CONTA DO STORAGE**, selecione uma conta do Storage para fornecer o Blob Storage do conteúdo de multimédia da conta de Media Services. Pode selecionar uma conta do Storage existente na mesma região geográfica da conta dos Media Services ou pode criar uma nova conta do Storage. É criada uma nova conta do Storage na mesma região.

5. Se tiver criado uma nova conta do Storage, em **NOVO NOME DA CONTA DO STORAGE**, introduza um nome para a conta do Storage. As regras para os nomes da conta do Storage são iguais às das contas dos Media Services.

6. Clique em **Criação Rápida** na parte inferior do formulário.

Pode monitorizar o estado do processo na área de mensagens na parte inferior da janela.

O estado é alterado para Ativo quando a conta é criada com êxito. A página **Media Services** abre apresentando a nova conta.

Na parte inferior da página, o botão **GERIR CHAVES** aparece. Ao clicar neste botão, é apresentada uma página com o nome da conta dos Media Services e as chaves primárias e secundárias. Serão necessários o nome da conta e as informações da chave primária para aceder através de programação à conta dos Media Services.

![Página Media Services](./media/media-services-create-account/wams-mediaservices-page.png)

Por predefinição, quando faz duplo clique no nome da conta, a página **Início Rápido** é apresentada. Esta página permite-lhe realizar algumas tarefas de gestão que também estão disponíveis noutras páginas do portal. Por exemplo, pode carregar um ficheiro de vídeo a partir desta página ou fazê-lo a partir da página **CONTEÚDO**.

Além disso, pode ver o código que utiliza o SDK dos Media Services do Azure para realizar as seguintes tarefas: carregar, codificar e publicar vídeos. Pode clicar numa das ligações na secção **ESCREVER ALGUM CÓDIGO**, copie o código e utilize-o na sua aplicação.



##Percursos de aprendizagem dos Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Enviar comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


## Passos seguintes

- [Introdução à distribuição de conteúdos de Vídeo a Pedido (VoD) utilizando o SDK do .NET](media-services-dotnet-get-started.md)

- [Utilize o SDK do .NET para criar canais que realizam live encoding a partir de uma transmissão em fluxo de velocidade de transmissão única para velocidade de transmissão múltipla](media-services-dotnet-creating-live-encoder-enabled-channel.md)

<!-- Reusable paths. -->

<!-- Anchors. -->
  [Conceitos]: #concepts
  [Antes de começar]: #begin
  [Como: criar uma conta de Media Services utilizando Criação Rápida]: #quick

<!-- URLs. -->
  [Instalador de Plataforma Web]: http://go.microsoft.com/fwlink/?linkid=255386

  [Portal Clássico do Azure]: http://manage.windowsazure.com/



<!--HONumber=Sep16_HO4-->


