---
title: "Introdução à CDN do Azure | Microsoft Docs"
description: "Este tópico mostra como ativar a Rede de Entrega de Conteúdos (CDN) do Azure. O tutorial explica a criação de um novo perfil e ponto final da CDN."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: bdf6e27463fcc6186a3b15a55653fa468da91bdc
ms.openlocfilehash: d263e911d0d0b3cdc1e48e300a3c8a0994b38c39


---
# <a name="getting-started-with-azure-cdn"></a>Introdução à CDN do Azure
Este tópico explica a ativação da CDN do Azure através da criação de um novo perfil e ponto final da CDN.

> [!IMPORTANT]
> Para uma introdução ao funcionamento da CDN, bem como uma lista de funcionalidades, consulte a [Descrição geral da CDN](cdn-overview.md).
> 
> 

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil da CDN
Um perfil da CDN é uma coleção de pontos finais da CDN.  Cada perfil contém um ou mais pontos finais da CDN.  Pode pretender utilizar vários perfis para organizar os seus pontos finais da CDN por domínio de Internet, aplicação Web ou alguns outros critérios.

> [!NOTE]
> Por predefinição, uma subscrição do Azure individual está limitada a oito perfis da CDN. Cada perfil da CDN está limitado a dez pontos finais da CDN.
> 
> Os preços da CDN são aplicados ao nível do perfil da CDN. Se pretende utilizar uma combinação de escalões de preço da CDN do Azure, necessitará de vários perfis da CDN.
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final da CDN
**Para criar um novo ponto final da CDN**

1. No [Portal do Azure](https://portal.azure.com), navegue para o seu perfil da CDN.  É possível que o tenha afixado ao dashboard no passo anterior.  Se não o tiver feito, pode encontrá-lo ao clicar em **Procurar** e, em seguida, **Perfis da CDN** e clicar no perfil ao qual planeia adicionar o seu ponto final.
   
    É apresentado o painel do perfil da CDN.
   
    ![Perfil da CDN][cdn-profile-settings]
2. Clique no botão **Adicionar Ponto Final**.
   
    ![Botão Adicionar ponto final][cdn-new-endpoint-button]
   
    O painel **Adicionar um ponto final** aparece.
   
    ![Painel Adicionar ponto final][cdn-add-endpoint]
3. Introduza um **Nome** para este ponto final da CDN.  Este nome será utilizado para aceder aos seus recursos em cache no domínio `<endpointname>.azureedge.net`.
4. Na lista pendente **Tipo de origem**, selecione o seu tipo de origem.  Selecione **Armazenamento** para uma conta de Armazenamento do Azure, **Serviço em Nuvem** para um Serviço em Nuvem do Azure, **Aplicação Web** para uma Aplicação Web do Azure ou **Origem personalizada** para outra origem de servidor Web publicamente acessível qualquer (alojada no Azure ou noutro local).
   
    ![Tipo de origem da CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
5. Na lista pendente **Nome do anfitrião de origem**, selecione ou escreva o seu domínio de origem.  A lista pendente apresentará todas as origens disponíveis do tipo que especificou no passo 4.  Se tiver selecionado *Origem personalizada* como o seu **Tipo de origem**, deve escrever no domínio da sua origem personalizada.
6. Na caixa de texto **Caminho de origem**, introduza o caminho para os recursos que pretende colocar em cache ou deixe em branco para permitir que a colocação em cache de qualquer recurso no domínio que especificou no passo 5.
7. No **Cabeçalho de anfitrião de origem**, introduza o cabeçalho de anfitrião que pretende que a CDN envie com cada pedido ou deixe a predefinição.
   
   > [!WARNING]
   > Alguns tipos de origens, como o Armazenamento e as Aplicações Web do Azure, requerem que o cabeçalho de anfitrião coincida com o domínio da origem. A menos que tenha uma origem que necessite de um cabeçalho de anfitrião diferente do respetivo domínio, deve deixar o valor predefinido.
   > 
   > 
8. Para **Protocolo** e **Porta de origem**, especifique os protocolos e as portas utilizadas para aceder aos seus recursos na origem.  Tem de ser selecionado pelo menos um protocolo (HTTP ou HTTPS).
   
   > [!NOTE]
   > A **Porta de origem** afeta apenas a porta que o ponto final utiliza para obter informações a partir da origem.  O próprio ponto final só estará disponível para clientes finais nas portas HTTP ou HTTPS predefinidas (80 e 443), independentemente da **Porta de origem**.  
   > 
   > Os pontos finais da **CDN do Azure da Akamai** não permitem o intervalo de portas TCP completo para origens.  Para obter uma lista das portas de origem que não são permitidas, consulte [Portas de Origem Permitidas do Azure CDN da Akamai](https://msdn.microsoft.com/library/mt757337.aspx).  
   > 
   > O acesso ao conteúdo da CDN através de HTTPS tem as seguintes restrições:
   > 
   > * Tem de utilizar o certificado SSL fornecido pela CDN. Não são suportados certificados de terceiros.
   > * Tem de utilizar o domínio fornecido pela CDN (`<endpointname>.azureedge.net`) para aceder a conteúdo HTTPS. O suporte de HTTPS não está disponível para nomes de domínio personalizado (CNAMEs), uma vez que a CDN não suporta certificados personalizados neste momento.
   > 
   > 
9. Clique no botão **Adicionar** para criar o novo ponto final.
10. Quando o ponto final for criado, aparece numa lista de pontos finais para o perfil. A vista de lista mostra o URL a utilizar para aceder ao conteúdo em cache, bem como o domínio de origem.
    
    ![Ponto final da CDN][cdn-endpoint-success]
    
    > [!IMPORTANT]
    > O ponto final não estará imediatamente disponível para utilização, pois a propagação do registo pela CDN demora algum tempo.  Para os perfis <b>CDN do Azure da Akamai</b>, a propagação normalmente fica concluída num minuto.  Para os perfis <b>CDN do Azure da Verizon</b>, a propagação normalmente fica concluída no prazo de 90 minutos, mas em certos casos pode demorar mais tempo.
    > 
    > Os utilizadores que tentam utilizar o nome de domínio da CDN antes de a configuração do ponto final tiver sido propagada para os POPs recebem códigos de resposta HTTP 404.  Se já tiverem passado várias horas desde que criou o ponto final e ainda estiver a receber respostas 404, consulte [Resolução de problemas em pontos finais da CDN que devolvem estados 404](cdn-troubleshoot-endpoint.md).
    > 
    > 

## <a name="see-also"></a>Veja também
* [Controlar o comportamento de colocação em cache de pedidos com cadeias de consulta](cdn-query-string.md)
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado](cdn-map-content-to-custom-domain.md)
* [Pré-carregar recursos num ponto final da CDN do Azure](cdn-preload-endpoint.md)
* [Remover um Ponto Final da CDN do Azure](cdn-purge-endpoint.md)
* [Resolução de problemas em pontos finais da CDN que devolvem estados 404](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png



<!--HONumber=Jan17_HO4-->


