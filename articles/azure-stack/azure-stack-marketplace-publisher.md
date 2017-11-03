---
title: Utilize o toolkit de Marketplace para criar e publicar itens do marketplace | Microsoft Docs
description: "Saiba como criar rapidamente os itens do marketplace com a publicação Toolkit"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: ByronR
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: helaw
ms.openlocfilehash: 5b2c04d2cbc06e1572dc2e40712f6cf9d886aa1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Adicionar itens do marketplace utilizando a ferramenta de publicação
Adicionar o conteúdo para o [Azure pilha Marketplace](azure-stack-marketplace.md) disponibiliza as suas soluções e inquilinos para a implementação.  O Toolkit de Marketplace cria ficheiros de Azure Marketplace pacotes (.azpkg) com base nos seus modelos do IaaS do Azure Resource Manager ou extensões de VM.  Também pode utilizar o Toolkit de Marketplace para publicar ficheiros .azpkg, criado com a ferramenta ou utilizar [manual](azure-stack-create-and-publish-marketplace-item.md) passos.  Este tópico orienta-o transferir a ferramenta, criar um item do marketplace com base num modelo de VM e, em seguida, publicar este item para a pilha do Azure Marketplace.     


## <a name="prerequisites"></a>Pré-requisitos
 - Tem de executar o toolkit no anfitrião do Azure pilha ou ter [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) conectividade a partir do computador onde a ferramenta é executada.

 - Transferir o [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) e extrair.

 - Transferir o [ferramenta de empacotamento de galeria do Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - A publicação no Marketplace requer ícones e um ficheiro em miniatura.  Pode utilizar a sua própria ou guardar o [exemplo](azure-stack-marketplace-publisher.md#support-files) ficheiros localmente para este exemplo.

## <a name="download-the-tool"></a>Transferir a ferramenta
O Toolkit de Marketplace pode ser [transferido a partir do repositório Azure pilha ferramentas](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Criar itens do marketplace
Nesta secção, utilize o Toolkit de Marketplace para criar um pacote de item do marketplace em formato .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Fornecer informações de marketplace com o Assistente
1. Execute o Toolkit de Marketplace a partir de uma sessão do PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Clique em de **solução** separador.  Este ecrã aceita informações sobre o item do marketplace. Introduza informações sobre o item que pretende que aparecem no marketplace.  Também pode especificar um [ficheiro de parâmetros](azure-stack-marketplace-publisher.md#use-a-parameters-file) para pré-povoar o formulário.  
    
    ![captura de ecrã do ecrã primeiro Toolkit do Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Clique em **procurar** e selecione um ficheiro de imagem para cada campo ícone e captura de ecrã.  Pode utilizar os seus próprios ícones ou os ícones de exemplo no [ficheiros de suporte](azure-stack-marketplace-publisher.md#support-files) secção.
4. Depois de todos os campos são preenchidos, selecione "Pré-visualização a solução" para uma versão de pré-visualização da solução dentro do Marketplace.  Pode rever e editar o texto, imagens e captura de ecrã antes de clicar em **seguinte**.  

### <a name="import-template-and-create-package"></a>Importar o modelo e criar pacote
Nesta secção, importar o modelo e trabalhar com entrada para a sua solução.

1.  Clique em **procurar** e selecione o *azuredeploy. JSON* da pasta 101-simples-Windows-VM nos modelos de transferido.

    ![captura de ecrã do ecrã segundo Toolkit do Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  O Assistente de implementação é preenchido com um *básico* itens passo e a entrada para cada parâmetro especificada no modelo.  Pode adicionar mais passos e mover entradas entre passos.  Por exemplo, poderá pretender que os passos de "Configuração de front-end" e "Configuração de Back-End" para a sua solução.
3.  Especifique o caminho para AzureGalleryPackager.exe.  
4.  Clique em **criar** e o Toolkit de Marketplace pacotes a solução para um ficheiro de .azpkg.  Depois de concluído, o assistente apresenta o caminho para o ficheiro de solução e dão-lhe a opção para continuar a publicar o seu pacote de pilha do Azure.


## <a name="publish-marketplace-items"></a>Publicar itens do marketplace
Nesta secção, publicar o item do marketplace para a pilha do Azure Marketplace.

![captura de ecrã do ecrã primeiro Toolkit do Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  O assistente necessita de informações para publicar a sua solução:
    
    |Campo|Descrição|
    |-----|-----|
    | Nome do serviço de administração | Conta de administrador de serviço.  Exemplo:ServiceAdmin@mydomain.onmicrosoft.com |
    | Palavra-passe | Palavra-passe da conta de administrador de serviço. |
    | Ponto final de API | Ponto final do Gestor de recursos do Azure de pilha do Azure.  Exemplo: management.local.azurestack.external |
2.  Clique em **publicar** e é apresentado o registo de publicação.
3.  Agora é possível implementar o item publicada através do portal do Azure pilha.


## <a name="use-a-parameters-file"></a>Utilizar um ficheiro de parâmetros
Também pode utilizar um ficheiro de parâmetros para concluir as informações de item do marketplace.  

O Toolkit de Marketplace inclui um *solution.parameters.ps1* pode utilizar para criar o seu próprio ficheiro de parâmetros.


## <a name="support-files"></a>Ficheiros de suporte
| Descrição | Exemplo |
| ----- | ----- |
| ícone de PNG 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| ícone de PNG 90 x 90 | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| ícone de PNG 115 x 115 | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| ícone de PNG 255 x 115 | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533 x 324 PNG miniatura | ![](./media/azure-stack-marketplace-publisher/image5.png) |


