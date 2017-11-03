---
title: "Gerir a imagem de máquina virtual no Azure Marketplace | Microsoft Docs"
description: "Guia de detalhado sobre como gerir a imagem de máquina virtual no Azure Marketplace após a publicação inicial"
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ms.openlocfilehash: e1f90650e71345957c2d353774cb8bef62c1868b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guia de produção de pós-implementação para ofertas de máquina virtual no Azure Marketplace
Este artigo explica como pode atualizar uma oferta de máquinas de virtuais em direto no Azure Marketplace. Orienta-o durante o processo de adicionar um ou mais SKUs de novo a uma oferta existente. É também orienta-o processo de remoção de uma oferta de máquinas de virtuais em direto ou o SKU do Marketplace.

Depois de uma oferta/SKU é testado no [portal do Azure](http://portal.azure.com), não é possível alterar as caixas de texto seguinte:

* **Oferecer identificador**: portal na publicação, aceda a **máquinas virtuais** e selecione a sua oferta. Em seguida, clique em **imagens da VM** > **oferecem identificador**.
* **Identificador de SKU**: portal na publicação, aceda a **máquinas virtuais** e selecione a sua oferta. Em seguida, clique em **SKUS** > **adicionar um SKU**.
* **Espaço de nomes do publicador**: portal na publicação, aceda a **máquinas virtuais** > **instruções** > **informe-nos sobre a sua empresa** (localizado em "Passo 2 registar a sua empresa") > **espaço de nomes do publicador** > **espaço de nomes**.

Depois de oferta/SKU está listado no [Marketplace](http://azure.microsoft.com/marketplace), não é possível alterar as caixas de texto seguinte:

* **Oferecer identificador**: portal na publicação, aceda a **máquinas virtuais** e selecione a sua oferta. Em seguida, clique em **imagens da VM** > **oferecem identificador**.
* **Identificador de SKU**: portal na publicação, aceda a **máquinas virtuais** e selecione a sua oferta. Em seguida, clique em **SKUS** > **adicionar um SKU**.
* **Espaço de nomes do publicador**: portal na publicação, aceda a **máquinas virtuais** > **instruções** > **informe-nos sobre a sua empresa** (localizado em "Registar passo 2") **espaço de nomes do publicador** > **espaço de nomes**.
* **Portas**: portal na publicação, aceda a **máquinas virtuais** e selecione a sua oferta. Em seguida, clique em **imagens da VM** > **abrir portas**.
* **Alteração de SKU(s) listadas de preço**
* **Alteração de modelo de faturação do SKU(s) listadas**
* **Remoção de faturação regiões de SKU(s) listadas**
* **Alterar a contagem de discos de dados de SKU(s) listadas**

## <a name="update-the-technical-details-of-a-sku"></a>Atualizar os detalhes técnicos de um SKU
Para adicionar uma nova versão para o SKU listado e voltar a publicar oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **imagens da VM** separador.
4. No **SKUs** secção, localize o SKU que pretende atualizar.
5. Adicionar um novo número de versão para o SKU e clique em de  **+**  botão. A nova versão deve estar num formato X.Y.Z, onde X, Y e Z são números inteiros. Alterações de versão só devem ser incrementais.
6. No **URL de VHD de SO** caixa, introduza a assinatura de acesso partilhado URI criado para o VHD de sistema operativo e guardar as alterações.

   > [!IMPORTANT]
   > -Não é possível incremento/diminuir a contagem de discos de dados de um SKU listada. Terá de criar um novo SKU neste caso. Para obter orientações detalhadas, consulte a secção [adicionar um novo SKU sob uma oferta listada](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Imagens de VM](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Atualizar os detalhes nontechnical de uma oferta ou um SKU
Pode atualizar o nontechnical (de marketing, legais, suportam, categorias) detalhes da sua oferta em direto ou o SKU no Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Atualizar a descrição de oferta e logótipos
Para atualizar os detalhes de oferta e voltar a publicar oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **MARKETING** separador.
4. Clique em **inglês (EUA)**.
5. Clique em de **detalhes** separador. No **Descrição** secção, atualize a oferta **título**, **resumo**, e **resumo EXTENSO** e guardar as alterações.

   > [!NOTE]
   > Quando atualizar os detalhes do SKU, tenha em atenção estes restrições: 
   * Não introduza texto duplicado para a descrição da oferta e a descrição do SKU.
   * Não introduza texto duplicado para o título SKU e a oferta longa resumo. 
   * Não introduza texto duplicado para o título SKU e o resumo de oferta.
   >
   >

    ![Detalhes](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. No **LOGÓTIPOS** secção o **detalhes** separador, atualize os logótipos. Certifique-se de que o logótipos siga a [diretrizes de Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Um ícone de heroína é opcional. Pode optar por não carregue um ícone de heroína. No entanto, depois de um ícone de heroína é carregado, não há nenhum aprovisionar eliminá-la a publicação portal. Siga o [diretrizes de ícone heroína](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Logótipos](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Atualizar a descrição de SKU
Para atualizar os detalhes SKU e voltar a publicar oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **MARKETING** separador.
4. Clique em **inglês (EUA)**.
5. Clique em de **planos** separador. No **SKUs** secção, atualize o SKU **título**, **resumo**, e **Descrição** e guardar as alterações.

   > [!NOTE]
   > Quando atualizar os detalhes do SKU, tenha em atenção estes restrições: 
   * Não introduza texto duplicado para a descrição da oferta e a descrição do SKU. 
   * Não introduza texto duplicado para o título SKU e a oferta longa resumo. 
   * Não introduza texto duplicado para o título SKU e o resumo de oferta.
   >
   >
6. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
7. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Planos](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Alterar as ligações existentes ou adicionar novas ligações
Para alterar as ligações existentes ou adicionar novas ligações e, em seguida, voltar a publicar a sua oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **MARKETING** separador.
4. Clique em **inglês (EUA)**.
5. Clique em de **ligações** separador.
6. Para adicionar uma nova ligação entre o **ligações** secção, clique em **+ adicionar ligação**. No **adicionar ligação** caixa de diálogo, introduza a ligação **título** e **URL** e guardar as alterações. Pode introduzir qualquer ligação, que contém informações que podem ajudar os clientes.
7. Para atualizar ou eliminar uma ligação existente, selecione a ligação e clique em de **editar** botão ou a **eliminar** botão.

   > [!NOTE]
   > Certifique-se de que as ligações que introduzir nesta secção estão a funcionar corretamente, porque estas ligações obterem validadas durante o processo de pedido de produção.
   >
   >
8. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Ligações](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Adicionar ligação](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Alterar uma imagem de exemplo existente ou adicionar uma nova imagem de exemplo
Para alterar uma imagem de exemplo existente ou adicionar novas imagens de exemplo e, em seguida, voltar a publicar a sua oferta, siga estes passos:

> [!NOTE]
> Imagem de apenas um exemplo é apresentada no [portal do Azure](https://portal.azure.com).
>
>

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **MARKETING** separador.
4. Clique em **inglês (EUA)**.
5. Clique em de **imagens de exemplo** separador.
6. Para adicionar uma nova imagem de exemplo, o **imagens de exemplo** secção, clique em **carregar A nova imagem** e guardar as alterações.

   > [!NOTE]
   > É opcional, incluindo uma imagem de exemplo.
   >
7. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Imagens de exemplo](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Atualizar o conteúdo legal
Para atualizar o conteúdo legal e voltar a publicar oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **MARKETING** separador.
4. Clique em **inglês (EUA)**.
5. Clique em de **legais** separador. No **legais** secção, atualizar os políticas de termos de utilização. Introduza ou cole os políticas de termos do **os termos de utilização** caixa e guardar as alterações.
6. O limite de carateres para os termos legais de utilização é milhões de 1 carateres.
7. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
8. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Informações Legais](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Atualizar as informações de suporte
Para atualizar as informações de suporte e voltar a publicar oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **suporte** separador.
4. No **contacte engenharia** secção, atualize os detalhes de contactos de engenharia. Estes detalhes são utilizadas para comunicação interna apenas entre o parceiro e a Microsoft.
5. No **suporte ao cliente** secção, atualize os detalhes de contacto de suporte e **SUPORTA URL**. Estes detalhes são utilizadas para comunicação interna apenas entre o parceiro e a Microsoft.

   > [!NOTE]
   > Se pretender fornecer apenas o suporte de e-mail, introduza um número de telefone fictício no **suporte ao cliente** secção. Neste caso, o e-mail que indicou é utilizado em vez disso.
   >
   >
6. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
7. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Suporte](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>As categorias de atualização
Para atualizar a secção de categorias para a sua oferta e voltar a publicar oferta, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **categorias** separador.
4. No **categorias** secção, Atualize as categorias para a sua oferta e guardar as alterações. Pode escolher até cinco categorias para a galeria do Azure Marketplace.
5. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
6. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![Categorias](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Adicionar um novo SKU sob uma oferta listada
Para adicionar um novo SKU na oferta em direto, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **SKUS** separador. Em seguida, clique em **adicionar um SKU**. 
4. Na caixa de diálogo, introduza um **identificador de SKU** em minúsculas. Selecione o **traga o seu próprio modelo de faturação de licença (BYOL)** caixa de verificação se pretender publicar o SKU nova com um modelo de faturação BYOL. Caso contrário, desmarque a caixa de verificação. Clique na marca de escala para criar um SKU de novo. Se não escolher o modelo de faturação BYOL, o modelo de faturação é automaticamente definido para a cada hora. Se pretender que a versão de avaliação gratuita de 30 dias para o modelo de faturação por hora, selecione **um mês** para **é uma versão de avaliação gratuita disponível?** Caso contrário, selecione **não avaliação**. (**é uma versão de avaliação gratuita disponível?**  aparece apenas se não selecionou BYOL ao criar o SKU de novo.)

   > [!IMPORTANT]
   > **Ocultar esta SKU do Marketplace porque deve sempre ser comprou através de um modelo de solução** deve ser **Sim** *apenas* se estiver a ser aprovado para publicar um modelo de solução. Caso contrário, esta opção deve ser sempre **não**.
   >
   >
4. No menu à esquerda, clique em de **imagens da VM** separador e saber o SKU novo que criou.
5. Para configurar o SKU de novo, consulte [obter certificação para a imagem VM](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) para obter orientações.
6. Para adicionar os materiais de marketing para o SKU de novo, consulte [Marketplace fornecer conteúdo de marketing](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Para adicionar informações sobre os preços para o SKU de novo, consulte [definir os seus preços](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
9. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

    ![SKUs](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Adicionar um SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Alterar a contagem de discos de dados para um SKU listada
-Não é possível incremento/diminuir a contagem de discos de dados de um SKU listada. Terá de criar um novo SKU neste caso. Para obter orientações detalhadas, consulte a secção [adicionar um novo SKU sob uma oferta listada](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Eliminar uma oferta listada no Marketplace
Vários aspetos tem de ser tratado no caso de um pedido para remover uma oferta em direto. Para obter documentação de orientação da equipa de suporte para remover uma listadas oferta do Marketplace, siga estes passos:

1. Emitir um pedido de suporte no [criar um incidente](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) página.

2. Selecione **tipo de problema** como **gerir ofertas**e selecione **categoria** como **modificar uma oferta e/ou SKU já na produção**.
3. Submeta o pedido.

A equipa de suporte orienta-o ao longo do processo de eliminação de oferta/SKU.

> [!NOTE]
> Pode sempre eliminar a oferta enquanto este estiver no estado de rascunho (mas não teste ou de produção). No **histórico** separador, clique em **REJEITAR rascunho**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Eliminar um SKU listado no Marketplace
Para eliminar um SKU listado do Marketplace, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).

2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No painel à esquerda, clique o **SKUS** separador.
4. Selecione o SKU que pretende eliminar e clique em de **eliminar** botão.
5. Vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.
6. Depois da oferta é republicada no Marketplace, o SKU não é eliminada do Marketplace e o portal do Azure.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Eliminar a versão atual de um SKU listada no Marketplace
Para eliminar a versão atual de um SKU listada do Marketplace, siga estes passos: 

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).

2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **imagens da VM** separador.
4. Selecione o SKU cuja versão atual que pretende eliminar e clique em de **eliminar** botão.
5. Vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.
6. Depois da oferta obtém republicada no Marketplace, a versão atual do SKU listado é eliminada do Marketplace e o portal do Azure. O SKU, em seguida, é revertido para a versão anterior.

## <a name="revert-the-listing-price-to-production-values"></a>Reverter o preço de listagem para valores de produção
Para reverter o preço de listagem para valores de produção, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).
2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **preços** separador.
4. Selecione uma região cujo preços que pretende repor.

    ![Preços regiões](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Para SKUs com um modelo de faturação por hora, repor os preços para todos os núcleos, dado que estão em produção para a região selecionada. Para SKUs com um modelo de faturação BYOL, disponibilizar o SKU na região, selecionando a caixa de verificação para o SKU no **EXTERNALLY-LICENSED (BYOL) SKU disponibilidade** secção.

    ![Modelos de preços](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Clique em **AUTOPRICE outros mercados com base nos preços nos Estados Unidos**.

   > [!NOTE]
   > Etiqueta do botão poderão ser diferente consoante a região que selecionou. Porque selecionamos dos Estados Unidos, o botão assinalada como **AUTOPRICE outras mercados com base nos preços em Unidas Estados**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Na página 1 do assistente Autoprice, escolha o mercado base e clique em de **seta** botão.

    ![Mercado base](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Na página 2, escolha os planos de serviço e medidores (núcleos) e clique em de **seta** botão.

    ![Planos de serviços e medidores](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Na página 3, clique em **todos os ativar/desativar** para selecionar todas as regiões. Ou pode selecionar manualmente as caixas de verificação para regiões específicas. Clique em de **seta** botão.

    ![Escolha mercados](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Na página 4, reveja as taxas de exchange e clique em **concluir**. O assistente repõe os preços de acordo com as suas seleções.

11. No **preços** separador, clique em **vista de resumo e as alterações**.
    Para **vista versão**, selecione **rascunho**e para **comparar com**, selecione **produção**. Se vir não existe diferença de preços, o preço revertida com êxito para os valores de produção.

    ![Ver resumo e alterações](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
13. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Reverter o modelo de faturação para valores de produção
Para reverter o modelo de faturação para valores de produção, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).

2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **SKUS** separador.
4. Clique em de **editar** botão para reverter o modelo de faturação. Na janela que se abre, selecione ou desmarque o **faturação e licenciamento é feito externamente a partir do Azure (também conhecido como traga a sua própria licença)** caixa de verificação.

    ![Editar faturação](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Siga os passos em "Revert o preço de listagem para valores de produção" neste artigo.
6. Vá para o **publicar** separador e clique em **PUSH para o teste**. Para obter orientações detalhadas sobre a oferta de teste no ambiente de teste, consulte [testar a sua oferta VM para o mercado](marketplace-publishing-vm-image-test-in-staging.md).
7. Depois de ter testado a sua oferta no modo de transição, vá para o **publicar** separador a publicação do portal. Clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Reverter a definição de visibilidade de um SKU listado para o valor de produção
Para reverter a definição de visibilidade de um SKU listado para o valor de produção, siga estes passos:

1. Iniciar sessão para o [publicação portal](https://publish.windowsazure.com).

2. Vá para o **máquinas virtuais** separador e selecione a sua oferta.
3. No menu à esquerda, clique em de **SKUS** separador.
4. Selecione o SKU e reverter a definição de visibilidade do SKU no valor de produção.

    ![Visibilidade](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Depois de terminar com as alterações, clique em **solicitar aprovação para PUSH para produção** republicar a oferta no Marketplace.

## <a name="see-also"></a>Consultar também
* [Introdução Get: Publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)
* [Compreender a relatórios de dividendos](marketplace-publishing-report-payout.md)
* [Alterar a sua incentivo de revendedor a partir do fornecedor de solução em nuvem](marketplace-publishing-csp-incentive.md)
* [Resolver problemas comuns de publicação no Marketplace](marketplace-publishing-support-common-issues.md)
* [Obter suporte como publicador](marketplace-publishing-get-publisher-support.md)
* [Criar uma imagem VM no local](marketplace-publishing-vm-image-creation-on-premise.md)
* [Criar uma máquina virtual com o Windows no portal de pré-visualização do Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
