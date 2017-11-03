---
title: Integrar uma conta de armazenamento do Azure com o Azure CDN | Microsoft Docs
description: "Saiba como utilizar a rede de entrega de conteúdos (CDN) do Azure para fornecer conteúdo de largura de banda alta ao colocar em cache blobs do armazenamento do Azure."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 511076935d06ed0908341044e37069e74530be49
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-an-azure-storage-account-with-azure-cdn"></a>Integrar uma conta de armazenamento do Azure CDN do Azure
CDN pode ser ativada para o conteúdo da cache do seu armazenamento do Azure. Oferece aos programadores uma solução global para a entrega de conteúdo de largura de banda alta ao colocar em cache blobs e conteúdo estático de instâncias de computação em nós físicos nos Estados Unidos, Europa, Ásia, Austrália e América do Sul.

## <a name="step-1-create-a-storage-account"></a>Passo 1: Criar uma conta de armazenamento
Utilize o procedimento seguinte para criar uma nova conta de armazenamento para uma subscrição do Azure. Uma conta de armazenamento dá acesso aos serviços de armazenamento do Azure. A conta de armazenamento representa o nível mais alto de espaço de nomes para aceder a cada uma dos componentes do serviço de armazenamento do Azure: BLOBs serviços, serviços da fila e serviços de tabela. Para obter mais informações, consulte o [introdução ao Storage do Microsoft Azure](../storage/common/storage-introduction.md).

Para criar uma conta do storage, tem de ser administrador de serviço ou coadministrador da subscrição associada.

> [!NOTE]
> Existem vários métodos que pode utilizar para criar uma conta de armazenamento, incluindo o Portal do Azure e o Powershell.  Para este tutorial, iremos utilizar o Portal do Azure.  
> 
> 

**Para criar uma conta de armazenamento para uma subscrição do Azure**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. No canto superior esquerdo, selecione **novo**. No **novo** caixa de diálogo, selecione **dados + armazenamento**, em seguida, clique em **conta de armazenamento**.
    
    O **criar conta de armazenamento** é apresentado o painel.   

    ![Criar conta de armazenamento][create-new-storage-account]  

3. No **nome** campo, escreva um nome de subdomínio. Esta entrada pode conter 3 a 24 letras minúsculas e números.
   
    Este valor torna-se o nome de anfitrião no URI que é utilizado para endereçar os recursos de Blob, fila ou tabela para a subscrição. Para resolver um recurso de contentor no serviço Blob, teria de utilizar um URI no seguinte formato, onde  *&lt;StorageAccountLabel&gt;*  refere-se para o valor que escreveu no **introduzir um URL**:
   
    http://*&lt;StorageAcountLabel&gt;*.blob.core.windows.net/*&lt;mycontainer&gt;*
   
    **Importante:** o URL de etiqueta formulários o subdomínio da conta do storage URI e têm de ser exclusivo entre todos os serviços alojados no Azure.
   
    Este valor é também utilizado como o nome desta conta de armazenamento no portal ou ao aceder a esta conta através de programação.
4. Deixe as predefinições para **modelo de implementação**, **conta kind**, **desempenho**, e **replicação**. 
5. Selecione o **subscrição** que será utilizada a conta de armazenamento com.
6. Selecione ou crie um **Grupo de Recursos**.  Para mais informações sobre os Grupos de Recursos, veja [Descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).
7. Selecione uma localização para a sua conta de armazenamento.
8. Clique em **Criar**. O processo de criação de conta do storage poderá demorar vários minutos a concluir.

## <a name="step-2-enable-cdn-for-the-storage-account"></a>Passo 2: Ativar a CDN para a conta de armazenamento

Com a integração mais recente, agora, pode ativar CDN para a sua conta de armazenamento sem sair da extensão de portal de armazenamento. 

1. Selecione a conta de armazenamento, pesquisar "CDN" ou desloque para baixo no menu de navegação esquerdo e, em seguida, clique em "CDN do Azure".
    
    O **CDN do Azure** é apresentado o painel.

    ![CDN ativar navegação][cdn-enable-navigation]
    
2. Crie um novo ponto final ao introduzir as informações necessárias
    - **Perfil da CDN**: pode criar uma nova ou utilize um perfil existente.
    - **Escalão de preço**: apenas tem de selecionar um escalão de preço, se criar um novo perfil da CDN.
    - **Nome do ponto final CDN**: introduza um nome de ponto final à sua escolha.

    > [!TIP]
    > Ponto final de CDN criado utiliza o nome do anfitrião da conta de armazenamento como origem, por predefinição.

    ! [cdn nova criação do ponto final] [cdn-novo--criação do ponto final]

3. Após a criação, o novo ponto final irá aparecer na lista de ponto final acima.

    ![ponto final da CDN armazenamento novo][cdn-storage-new-endpoint]

> [!NOTE]
> Também pode aceder à extensão de CDN do Azure para ativar a CDN. [Tutorial](#Tutorial-cdn-create-profile).
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]  

## <a name="step-3-enable-additional-cdn-features"></a>Passo 3: Ativar funcionalidades adicionais do CDN

No painel de "CDN do Azure" da conta de armazenamento, clique em ponto final de CDN da lista para abrir o painel de configuração da CDN. Pode ativar funcionalidades adicionais da CDN para a entrega, tais como compressão, cadeia de consulta, a filtragem de georreplicação. Também pode adicionar mapeamento de domínio personalizado para o ponto final de CDN e ativar o domínio personalizado HTTPS.
    
![configuração de cdn de armazenamento de CDN][cdn-storage-cdn-configuration]

## <a name="step-4-access-cdn-content"></a>Passo 4: Acesso conteúdo do CDN
Para aceder a conteúdo em cache na CDN, utilize o URL de CDN fornecido no portal. O endereço para um blob em cache será semelhante ao seguinte:

http://&lt*EndpointName*\>.azureedge.net/ <*myPublicContainer*\>/<*BlobName*\>

> [!NOTE]
> Depois de ativar o CDN acesso a uma conta de armazenamento, todos os objetos publicamente disponíveis são elegíveis para CDN edge a colocação em cache. Se modificar um objeto que está atualmente em cache na CDN, o conteúdo novo não estarão disponível através da CDN, até que a CDN atualiza o respetivo conteúdo, quando o período time-to-live de conteúdo em cache expira.
> 
> 

## <a name="step-5-remove-content-from-the-cdn"></a>Passo 5: Remover o conteúdo da CDN
Se já não pretender colocar em cache um objeto na rede do Azure da entrega de conteúdos (CDN), pode efetuar um dos seguintes passos:

* Pode efetuar o contentor privado em vez de público. Consulte [Gerir o acesso de leitura anónimo a contentores e blobs](../storage/blobs/storage-manage-access-to-resources.md) para obter mais informações.
* Pode desativar ou eliminar o ponto final CDN com o Portal de gestão.
* Pode modificar o serviço alojado já não responda a pedidos para o objeto.

Um objeto já em cache na CDN irá permanecer em cache até que o período de tempo-to-live de mensagens em fila para o objeto expirar ou até que o ponto final é removido. Quando o período de tempo-to-live expirar, a CDN irá verificar se o ponto final de CDN ainda é válido e o objeto ainda anonimamente acessível. Se não for, em seguida, o objeto serão já não ser colocadas em cache.

## <a name="additional-resources"></a>Recursos adicionais
* [Como Mapear Conteúdo da CDN para um Domínio Personalizado](cdn-map-content-to-custom-domain.md)
* [Ativar HTTPS para o domínio personalizado](cdn-custom-ssl.md)

[create-new-storage-account]: ./media/cdn-create-a-storage-account-with-cdn/CDN_CreateNewStorageAcct.png
[cdn-enable-navigation]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-creation.png
[cdn-storage-new-endpoint]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-new-endpoint-list.png
[cdn-storage-cdn-configuration]: ./media/cdn-create-a-storage-account-with-cdn/cdn-storage-endpoint-configuration.png 
