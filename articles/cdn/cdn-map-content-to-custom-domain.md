---
title: "Adicionar um domínio personalizado para o ponto final de CDN | Microsoft Docs"
description: "Saiba como mapear conteúdo da CDN do Azure para um domínio personalizado."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 289f8d9e-8839-4e21-b248-bef320f9dbfc
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/09/2017
ms.author: mazha
ms.openlocfilehash: 98d4900e28f1850050dc4fbe1f97435e52afaf08
ms.sourcegitcommit: 3e3a5e01a5629e017de2289a6abebbb798cec736
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2017
---
# <a name="add-a-custom-domain-to-your-cdn-endpoint"></a>Adicionar um domínio personalizado para o ponto final de CDN
Depois de criar um perfil, normalmente, também cria um ou mais CDN [pontos finais](cdn-create-new-endpoint.md#create-a-new-cdn-endpoint) (um subdomínio `azureedge.net`) para entregar o conteúdo utilizando HTTP e HTTPS. Por predefinição, este ponto final está incluído em todas as suas URLs (por exemplo, `https://contoso.azureedge.net/photo.png`). Para sua comodidade, o CDN do Azure permite-lhe associar um domínio personalizado (por exemplo, `www.contoso.com`) com o ponto final. Com esta opção, utilize um domínio personalizado para entregar o conteúdo em vez do ponto final. Esta opção é útil se, por exemplo, pretende que o seu próprio nome de domínio seja visível para os seus clientes para fins de imagem corporativa.

Se ainda não tiver um domínio personalizado, tem primeiro de comprar uma com um fornecedor de domínio. Após obter um domínio personalizado, siga estes passos:
1. [Aceder os registos DNS do seu fornecedor de domínio](#step-1-access-dns-records-by-using-your-domain-provider)
2. [Criar o record(s) CNAME DNS](#step-2-create-the-cname-dns-records)
    - Opção 1: Mapeamento direto do seu domínio personalizado para o ponto final de CDN
    - Opção 2: Mapeamento do seu domínio personalizado para o ponto final de CDN utilizando cdnverify 
3. [Ativar o mapeamento de registo CNAME no Azure](#step-3-enable-the-cname-record-mapping-in-azure)
4. [Certifique-se de que o subdomínio personalizado referencia o ponto final de CDN](#step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint)
5. [(Passo dependente) Mapear o domínio personalizado permanente para o ponto final de CDN](#step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint)

## <a name="step-1-access-dns-records-by-using-your-domain-provider"></a>Passo 1: Registos de DNS de acesso utilizando o fornecedor de domínio

Se estiver a utilizar o Azure para alojar o [domínios DNS](https://docs.microsoft.com/en-us/azure/dns/dns-overview), deve delegar DNS o fornecedor de domínio para um DNS do Azure. Para obter mais informações, consulte [delegar um domínio ao DNS do Azure](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)

Caso contrário, se estiver a utilizar o seu fornecedor de domínio para processar o seu domínio DNS, inicie sessão no Web site do seu fornecedor de domínio. Localizar a página para gerir registos DNS ao consultar a documentação do fornecedor ou ao procurá áreas do web site com a etiqueta **nome de domínio**, **DNS**, ou **nome do servidor de gestão**. Muitas vezes, pode encontrar a página de registos DNS ao visualizar as informações da conta e à procura de uma ligação como **meu domínios**. Alguns fornecedores tem ligações diferentes para adicionar os diferentes tipos de registos.

> [!NOTE]
> Para alguns fornecedores, como a GoDaddy, as alterações aos registos DNS só entram em vigor quando selecionar uma ligação **Guardar Alterações** separada. 


## <a name="step-2-create-the-cname-dns-records"></a>Passo 2: Criar o record(s) CNAME DNS

Antes de poder utilizar um domínio personalizado com um ponto final de CDN do Azure, primeiro tem de criar um registo de nome canónico (CNAME) com o seu fornecedor de domínio. Um registo CNAME é um tipo de registo no domínio Name System (DNS) que mapeia um domínio de origem para um domínio de destino, especificando um nome de domínio de alias para o nome de domínio "canónica" ou verdadeiro. Para a CDN do Azure, o domínio de origem é o domínio personalizado (e o subdomínio) e o domínio de destino é o ponto final de CDN. CDN do Azure verifica o registo CNAME DNS quando adicionar o domínio personalizado para o ponto final a partir do portal ou a API. 

Um registo CNAME mapeia um domínio específico e subdomínio, tais como `www.contoso.com` ou `cdn.contoso.com`; não é possível mapear um registo CNAME para um domínio de raiz, tais como `contoso.com`. Um subdomínio pode ser associado com apenas um ponto final da CDN e o registo CNAME que criar irá encaminhar todo o tráfego endereçado para o subdomínio para o ponto final especificado. Por exemplo, se associar `www.contoso.com` com o ponto final de CDN, não pode associá-lo com outro endpoint do Azure, tal como um ponto final de conta de armazenamento ou um ponto final de serviço de nuvem. No entanto, pode utilizar os subdomínios diferentes ao mesmo domínio para pontos finais de serviço diferente. Também pode mapear os subdomínios diferentes para o mesmo ponto final da CDN.

Utilize uma das seguintes opções para mapear o domínio personalizado para um ponto final de CDN:

- Opção 1: O mapeamento direto. Se o domínio personalizado o tráfego de não produção, pode mapear um domínio personalizado para um ponto final de CDN diretamente. O processo de mapeamento do domínio personalizado para o ponto final de CDN poderá resultar num breve período de tempo de inatividade para o domínio, enquanto está a registar o domínio no portal do Azure. A entrada de mapeamento CNAME deve ter este formato: 
 
  | NOME             | TIPO  | VALOR                  |
  |------------------|-------|------------------------|
  | www\.consoto.com | CNAME | consoto\.azureedge.net |


- Opção 2: Mapeamento com o **cdnverify** subdomínio. Se o domínio personalizado o tráfego de produção que não pode ser interrompido, pode criar um mapeamento CNAME temporário para o ponto final de CDN. Com esta opção, pode utiliza o Azure **cdnverify** ocorre subdomínio para fornecer um passo intermédio de registo para que os utilizadores podem aceder ao seu domínio sem interrupção durante o mapeamento de DNS.

   1. Criar um novo registo CNAME e fornecer um alias de subdomínio, que inclui o **cdnverify** subdomínio. Por exemplo, **cdnverify.www** ou **cdnverify.cdn**. 
   2. Forneça o nome de anfitrião, o que é o ponto final de CDN, no seguinte formato: `cdnverify.<EndpointName>.azureedge.net`. A entrada de mapeamento CNAME deve ter este formato: 

   | NOME                       | TIPO  | VALOR                            |
   |----------------------------|-------|----------------------------------|
   | cdnverify.www\.consoto.com | CNAME | cdnverify.consoto\.azureedge.net | 


## <a name="step-3-enable-the-cname-record-mapping-in-azure"></a>Passo 3: Ativar o mapeamento de registo CNAME no Azure

Depois de ter registado o domínio personalizado utilizando um dos procedimentos anteriores, em seguida, pode ativar a funcionalidade do domínio personalizado na CDN do Azure. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) e navegue para o perfil CDN com o ponto final que pretende mapear para um domínio personalizado.  
2. No **perfil da CDN** painel, selecione o ponto final de CDN com o qual pretende associar o subdomínio.
3. No canto superior esquerdo do painel de ponto final, clique em **domínio personalizado**. 

   ![Botão de domínio personalizado](./media/cdn-map-content-to-custom-domain/cdn-custom-domain-button.png)

4. No **personalizada hostname** texto, introduza o seu domínio personalizado, incluindo o subdomínio. Por exemplo, `www.contoso.com` ou `cdn.contoso.com`.

   ![Adicionar uma caixa de diálogo de domínio personalizado](./media/cdn-map-content-to-custom-domain/cdn-add-custom-domain-dialog.png)

5. Clique em **Adicionar**.

   O Azure verifica se o registo CNAME existe para o nome de domínio que introduziu. Se o CNAME estiver correto, o seu domínio personalizado é validado. Pode demorar algum tempo para o registo CNAME propagar para os servidores de nomes. Se o seu domínio não é validado imediatamente, certifique-se de que o registo CNAME está correto, em seguida, aguarde alguns minutos e tente novamente. Para **CDN do Azure da Verizon** pontos finais (Standard e Premium), pode demorar até 90 minutos para as definições de domínio personalizado propagadas a todos os nós de limite da CDN.  


## <a name="step-4-verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Passo 4: Certifique-se de que o subdomínio personalizado referencia o ponto final de CDN

Depois de concluir o registo do seu domínio personalizado, certifique-se de que o subdomínio personalizado referencia o ponto final de CDN.
 
1. Certifique-se de que tem conteúdo público, que é colocado em cache o ponto final. Por exemplo, se o ponto final de CDN está associado uma conta de armazenamento, a CDN coloca em cache conteúdo nos contentores do blob público. Para testar o domínio personalizado, certifique-se de que o contentor está definido para permitir o acesso público e contém pelo menos um blob.

2. No seu browser, navegue para o endereço de blob, utilizando o domínio personalizado. Por exemplo, se o domínio personalizado é `cdn.contoso.com`, o URL para o blob em cache deve ser semelhante ao seguinte URL: `http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg`.


## <a name="step-5-dependent-step-map-the-permanent-custom-domain-to-the-cdn-endpoint"></a>Passo 5 (passo dependente): mapear o domínio personalizado permanente para o ponto final de CDN

Este passo é dependente do passo 2, a opção 2 (mapeamento com o **cdnverify** subdomínio). Se estiver a utilizar o temporário **cdnverify** subdomínio e tiver verificado que funciona, em seguida, pode mapear o domínio personalizado permanente para o ponto final da CDN.

1. No web site do seu fornecedor de domínio, crie um registo CNAME DNS para mapear o domínio personalizado permanente para o ponto final da CDN. A entrada de mapeamento CNAME deve ter este formato: 
 
   | NOME             | TIPO  | VALOR                  |
   |------------------|-------|------------------------|
   | www\.consoto.com | CNAME | consoto\.azureedge.net |
2. Elimine o registo CNAME com o **cdnverify** subdomínio que criou anteriormente.

## <a name="see-also"></a>Veja Também
[Como ativar a rede de entrega de conteúdos (CDN) do Azure](cdn-create-new-endpoint.md)  
[Delegar o domínio ao DNS do Azure](../dns/dns-domain-delegation.md)