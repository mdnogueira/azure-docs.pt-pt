---
title: "Definições personalizadas para ambientes do App Service"
description: "Definições de configuração personalizada para ambientes do App Service"
services: app-service
documentationcenter: 
author: stefsch
manager: nirma
editor: 
ms.assetid: 1d1d85f3-6cc6-4d57-ae1a-5b37c642d812
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2016
ms.author: stefsch
ms.openlocfilehash: 687475fae0c90713c15e8abbb92b71059eae81c0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="custom-configuration-settings-for-app-service-environments"></a>Definições de configuração personalizada para ambientes do App Service
## <a name="overview"></a>Descrição geral
Porque isoladas ambientes do App Service para um único cliente, existem determinadas definições de configuração que podem ser aplicadas exclusivamente para ambientes do App Service. Este artigo documentos de personalizações específicas vários que estão disponíveis para ambientes do App Service.

Se não tiver um ambiente de serviço de aplicações, consulte [como criar um ambiente de serviço de aplicações](app-service-web-how-to-create-an-app-service-environment.md).

Pode armazenar as personalizações de ambiente de serviço de aplicações através da utilização de uma matriz na nova **clusterSettings** atributo. Este atributo for encontrado no dicionário de "Propriedades" o *hostingEnvironments* entidade do Azure Resource Manager.

O seguinte abreviado mostra de fragmento de modelo do Resource Manager a **clusterSettings** atributo:

    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

O **clusterSettings** atributo pode ser incluído num modelo do Resource Manager para atualizar o ambiente de serviço de aplicações.

## <a name="use-azure-resource-explorer-to-update-an-app-service-environment"></a>Utilize o Explorador de recursos do Azure para atualizar um ambiente de serviço de aplicações
Em alternativa, pode atualizar o ambiente de serviço de aplicações ao utilizar [Explorador de recursos do Azure](https://resources.azure.com).  

1. No Explorador de recursos, vá para o nó para o ambiente de serviço de aplicações (**subscrições** > **resourceGroups** > **fornecedores**  >  **Microsoft** > **hostingEnvironments**). Em seguida, clique o ambiente de serviço de aplicações específicos que pretende atualizar.
2. No painel direito, clique em **leitura/escrita** na barra de ferramentas superior para permitir a edição no Explorador de recursos interativos.  
3. Clique na azul **editar** botão para tornar o modelo do Resource Manager editável.
4. Desloque-se na parte inferior do painel da direita. O **clusterSettings** atributo é muito na parte inferior, onde pode criar ou atualizar o respetivo valor.
5. Escreva (ou copie e cole) a matriz de valores de configuração que pretende incluir o **clusterSettings** atributo.  
6. Clique na verde **colocar** botão que foi localizado na parte superior do painel da direita para consolidar a alteração ao ambiente de serviço de aplicações.

No entanto, a submeter a alteração, que demora aproximadamente 30 minutos, multiplicados pelo número de front-ends no ambiente de serviço de aplicações para que a alteração surta efeito.
Por exemplo, se um ambiente de serviço de aplicações tem quatro extremidades front, irá demorar cerca de duas horas para concluir a atualização de configuração. Enquanto a alteração da configuração está a ser implementada, não existem operações de dimensionamento ou operações de alteração de configuração podem ocorrer no ambiente de serviço de aplicações.

## <a name="disable-tls-10"></a>Desativar TLS 1.0
Uma pergunta recorrente de clientes, especialmente os clientes que são lidar com a conformidade de PCI auditorias, é como desativar explicitamente TLS 1.0 para as suas aplicações.

TLS 1.0 pode ser desativado através do seguinte **clusterSettings** entrada:

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],

## <a name="change-tls-cipher-suite-order"></a>Ordem do conjunto de cifras TLS de alteração
Outra questão de clientes é se que podem modificar a lista de cifras negociada pelo seu servidor e isto pode ser alcançado ao modificar o **clusterSettings** conforme mostrado abaixo. É possível obter a lista de conjuntos de cifras disponíveis da [neste artigo do MSDN](https://msdn.microsoft.com/library/windows/desktop/aa374757\(v=vs.85\).aspx).

        "clusterSettings": [
            {
                "name": "FrontEndSSLCipherSuiteOrder",
                "value": "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P256,TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256"
            }
        ],

> [!WARNING]
> Se valores incorretos são definidos para o conjunto de cifras que não é possível compreender o SChannel, todas as comunicações de TLS para o servidor poderão parar de funcionar. Nesse caso, terá de remover o *FrontEndSSLCipherSuiteOrder* entrada de **clusterSettings** e submeter o modelo do Resource Manager atualizado para reverter para o conjunto de cifras predefinido definições.  Utilize esta funcionalidade com cuidado.
> 
> 

## <a name="get-started"></a>Introdução
O site de modelo de início rápido do Azure Resource Manager inclui um modelo com a definição de base para [criar um ambiente de serviço de aplicações](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/).

<!-- LINKS -->

<!-- IMAGES -->
