---
title: Criar e publicar um item do Marketplace na pilha do Azure | Microsoft Docs
description: Criar e publicar um item do Marketplace na pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 77e5f60c-a86e-4d54-aa8d-288e9a889386
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: erikje
ms.openlocfilehash: 64203ce186665aada98fbe8daed971164a650399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-publish-a-marketplace-item"></a>Criar e publicar itens do Marketplace

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

## <a name="create-a-marketplace-item"></a>Criar um item do Mercado
1. [Transferir](http://www.aka.ms/azurestackmarketplaceitem) a ferramenta de Packager de galeria do Azure e o item do Marketplace de pilha do Azure de exemplo.
2. Abra o item do Marketplace de exemplo e mudar o nome de **SimpleVMTemplate** pasta. (Utilizar o mesmo nome como o item do Marketplace, por exemplo, **Contoso.TodoList**.) Esta pasta contém:
   
       /Contoso.TodoList/
       /Contoso.TodoList/Manifest.json
       /Contoso.TodoList/UIDefinition.json
       /Contoso.TodoList/Icons/
       /Contoso.TodoList/Strings/
       /Contoso.TodoList/DeploymentTemplates/
3. [Criar um modelo Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) ou escolha um modelo a partir do GitHub. O item do Marketplace utiliza este modelo para criar um recurso.
4. Para se certificar de que o recurso pode ser implementado com êxito, teste o modelo com as APIs de pilha do Microsoft Azure.
5. Se o seu modelo depende de uma imagem de máquina virtual, siga as instruções para [adicionar uma imagem de máquina virtual do Azure pilha](azure-stack-add-vm-image.md).
6. Guardar o modelo Azure Resource Manager no **/Contoso.TodoList/DeploymentTemplates/** pasta.
7. Escolha o ícones e texto para o item do Marketplace. Ícones para adicionar o **ícones** pasta e adicione o texto ao **recursos** ficheiros o **cadeias** pasta. Utilize a Convenção de nomenclatura pequena, média, grande e ao nível de ícones. Consulte [referência à IU do item do Marketplace](#reference-marketplace-item-ui) para uma descrição detalhada.
   
   > [!NOTE]
   > Todos os tamanhos de ícone quatro (wide pequena, média e grande,) são necessários para criar o item do Marketplace corretamente.
   > 
   > 
8. No **manifest.json** de ficheiros, alterar **nome** com o nome do item do Marketplace. Também alterar **publicador** ao seu nome ou da empresa.
9. Em **artefactos**, alterar **nome** e **caminho** para as informações corretas para o modelo Azure Resource Manager que incluiu.
   
         "artifacts": [
            {
                "name": "Type your template name",
                "type": "Template",
                "path": "DeploymentTemplates\\Type your path",
                "isDefault": true
            }
10. Substitua **os meus itens do Marketplace** com uma lista de categorias em que o item do Marketplace deve aparecer.
    
             "categories":[
                 "My Marketplace Items"
              ],
11. Para todas as edições adicionais para manifest.json, consulte [referência: manifest.json de item do Marketplace](#reference-marketplace-item-manifestjson).
12. Para criar o pacote de pastas para um ficheiro de .azpkg, abra uma linha de comandos e execute o seguinte comando:
    
        AzureGalleryPackager.exe package –m <path to manifest.json> -o <output location for the package>
    
    > [!NOTE]
    > O caminho completo para o pacote de saída tem de existir. Por exemplo, se o caminho de saída for C:\MarketPlaceItem\yourpackage.azpkg, a pasta C:\MarketPlaceItem tem de existir.
    > 
    > 

## <a name="publish-a-marketplace-item"></a>Publicar um item do Mercado
1. Utilize o PowerShell ou o Explorador de armazenamento do Azure para carregar o item do Marketplace (.azpkg) para o Blob storage do Azure. Pode carregar para o armazenamento de Azure pilha local ou carregar para o Storage do Azure. (Esta é uma localização temporária para o pacote.) Certifique-se de que o blob está acessível publicamente.
2. A máquina virtual do cliente no ambiente de pilha do Microsoft Azure, certifique-se de que a sessão do PowerShell está configurada com as suas credenciais de administrador de serviço. Pode encontrar instruções sobre como autenticar PowerShell na pilha do Azure no [implementar um modelo com o PowerShell](user/azure-stack-deploy-template-powershell.md).
3. Utilize o **adicionar AzureRMGalleryItem** cmdlet do PowerShell para publicar o item do Marketplace com pilha do Azure. Por exemplo:
   
       Add-AzureRMGalleryItem -GalleryItemUri `
       https://sample.blob.core.windows.net/gallerypackages/Microsoft.SimpleTemplate.1.0.0.azpkg –Verbose
   
   | Parâmetro | Descrição |
   | --- | --- |
   | SubscriptionID |ID de subscrição de Admin. Pode obtê-lo utilizando o PowerShell. Se preferir obtê-lo no portal, aceda à subscrição do fornecedor e copie o ID da subscrição. |
   | GalleryItemUri |URI de blob do seu pacote de galeria que já foi carregado para o armazenamento. |
   | Apiversion |Definir como **2015-04-01**. |
4. Vá para o portal. Agora, pode ver o item do Marketplace no portal – como um operador ou como um utilizador.
   
   > [!NOTE]
   > O pacote pode demorar alguns minutos a aparecer.
   > 
   > 
5. O item do Marketplace agora foi guardado para a pilha do Azure Marketplace. Pode optar por eliminá-la a localização de armazenamento de Blobs.
6. Pode remover um item do Marketplace utilizando o **remover AzureRMGalleryItem** cmdlet. Exemplo:
   
        Remove-AzureRMGalleryItem -Name Microsoft.SimpleTemplate.1.0.0  –Verbose
   
   > [!NOTE]
   > A IU do Marketplace poderá mostrar um erro depois de remover um item. Para corrigir o erro, clique em **definições** no portal. Em seguida, selecione **rejeitar modificações** em **personalização do Portal**.
   > 
   > 

## <a name="reference-marketplace-item-manifestjson"></a>Referência: Manifest.json de item do Marketplace
### <a name="identity-information"></a>Informações de identidade
| Nome | Necessário | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| Nome |X |Cadeia |[A-Za-z0-9] + | |
| Publicador |X |Cadeia |[A-Za-z0-9] + | |
| Versão |X |Cadeia |[SemVer v2](http://semver.org/) | |

### <a name="metadata"></a>Metadados
| Nome | Necessário | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| displayName |X |Cadeia |Recomendação de 80 carateres |O portal pode não apresentar o nome do item de lidar graciosamente se for mais de 80 carateres. |
| PublisherDisplayName |X |Cadeia |Recomendação de 30 carateres |O portal pode não apresentar o nome do publicador corretamente se for mais de 30 carateres. |
| PublisherLegalName |X |Cadeia |Máximo de 256 carateres | |
| Resumo |X |Cadeia |60 a 100 carateres | |
| LongSummary |X |Cadeia |140 para 256 carateres |Não ainda aplicável na pilha do Azure. |
| Descrição |X |[HTML](https://auxdocs.azurewebsites.net/en-us/documentation/articles/gallery-metadata#html-sanitization) |500 a 5.000 carateres | |

### <a name="images"></a>Imagens
O Marketplace utiliza os ícones seguintes:

| Nome | Largura | Altura | Notas |
| --- | --- | --- | --- |
| Grande |255 px |115 px |Sempre necessário |
| Grande |115 px |115 px |Sempre necessário |
| Médio |90 px |90 px |Sempre necessário |
| Pequeno |40 px |40 px |Sempre necessário |
| Captura de ecrã |533 px |32 px |Opcional |

### <a name="categories"></a>Categorias
Cada item do Marketplace deve ser etiquetado com uma categoria que identifica a onde o item é apresentado na IU do portal. Pode escolher uma das categorias existentes na pilha do Azure (computação, dados + armazenamento, etc.) ou escolher uma nova.

### <a name="links"></a>Ligações
Cada item do Marketplace pode incluir várias ligações para conteúdo adicional. As ligações são especificadas como uma lista de nomes e os URIs.

| Nome | Necessário | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| displayName |X |Cadeia |Máximo de 64 carateres | |
| URI |X |URI | | |

### <a name="additional-properties"></a>Propriedades adicionais
Para além dos metadados anterior, os autores de Marketplace podem fornecer dados de par chave/valor personalizado no seguinte formato:

| Nome | Necessário | Tipo | Restrições | Descrição |
| --- | --- | --- | --- | --- |
| displayName |X |Cadeia |Máximo de 25 carateres | |
| Valor |X |Cadeia |Máximo de 30 carateres | |

### <a name="html-sanitization"></a>Sanitização de HTML
Para qualquer campo que lhe permite HTML, são permitidos os seguintes elementos e atributos:

H1, h2, h3, h4, h5, p, ol, ul, li, uma [destino | href], br, segura, IT, o b, posso

## <a name="reference-marketplace-item-ui"></a>Referência: O item do Marketplace IU
Ícones e o texto de itens do Marketplace visto no portal do Azure pilha são as seguintes.

### <a name="create-blade"></a>Criar painel
![Criar painel](media/azure-stack-marketplace-item-ui-reference/image1.png)

### <a name="marketplace-item-details-blade"></a>Painel de detalhes de item do Marketplace
![Painel de detalhes de item do Marketplace](media/azure-stack-marketplace-item-ui-reference/image3.png)

