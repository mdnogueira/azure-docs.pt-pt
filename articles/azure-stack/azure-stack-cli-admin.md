---
title: Ativar o CLI do Azure para utilizadores de pilha do Azure | Microsoft Docs
description: Saiba como utilizar a interface de linha de comandos (CLI) de plataforma para gerir e implementar os recursos na pilha do Azure
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: sngun
ms.openlocfilehash: d854c106fbce7e3f01c2878bb9828bdffa4d42a5
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="enable-azure-cli-for-azure-stack-users"></a>Ativar o CLI do Azure para utilizadores de pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Não sabemos de quaisquer tarefas específicas de operador de pilha do Azure que pode efetuar ao utilizar a CLI do Azure. Mas antes dos utilizadores podem gerir recursos através do CLI, operadores de pilha do Azure tem de fornecê-los com o seguinte:

* **O certificado de raiz da AC de pilha do Azure** é necessário se os utilizadores utilizam CLI de uma estação de trabalho fora do Kit de desenvolvimento de pilha do Azure.  

* **O ponto final da máquina virtual aliases** fornece um alias, como "UbuntuLTS" ou "Win2012Datacenter," que faça referência a um publicador de imagem, oferta, SKU e versão como um único parâmetro quando implementar VMs.  

As secções seguintes descrevem como obter estes valores.

## <a name="export-the-azure-stack-ca-root-certificate"></a>Exportar o certificado de raiz de AC de pilha do Azure

O certificado de raiz da AC de pilha do Azure está disponível no kit de desenvolvimento e numa máquina virtual de inquilino que está a ser executado no ambiente do kit de desenvolvimento. Para exportar o certificado de raiz de pilha do Azure no formato PEM, inicie sessão no kit de desenvolvimento ou a máquina virtual de inquilino e execute o seguinte script:

```powershell
$label = "AzureStackSelfSignedRootCert"
Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
$root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
if (-not $root)
{
    Log-Error "Certificate with subject CN=$label not found"
    return
}

Write-Host "Exporting certificate"
Export-Certificate -Type CERT -FilePath root.cer -Cert $root

Write-Host "Converting certificate to PEM format"
certutil -encode root.cer root.pem
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Configurar o ponto final aliases de máquina virtual

Os operadores do Azure da pilha devem configurar um ponto final acessível publicamente que aloja um ficheiro de alias de máquina virtual. O ficheiro de alias de máquina virtual é um ficheiro JSON que fornece um nome comum para uma imagem. Esse nome é especificado, subsequentemente, quando uma VM é implementada como um parâmetro da CLI do Azure.  

Antes de adicionar uma entrada para um ficheiro de alias, certifique-se de que a [transferir imagens do Azure Marketplace](azure-stack-download-azure-marketplace-item.md), ou ter [publicado a sua imagem personalizada](azure-stack-add-vm-image.md). Se publicar uma imagem personalizada, anote as informações do publicador, oferta, SKU e versão que especificou durante a publicação. Se se tratar de uma imagem do marketplace, pode ver as informações utilizando a ```Get-AzureVMImage``` cmdlet.  
   
A [um ficheiro de exemplo alias](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) com muitos imagem comuns aliases está disponível. Pode utilizar isso como um ponto de partida. Este ficheiro num espaço de onde os clientes CLI podem contactar o anfitrião. É uma forma alojar o ficheiro na conta do blob storage e partilhar o URL com os seus utilizadores:

1. Transferir o [ficheiro de exemplo](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) a partir do GitHub.
2. Crie uma nova conta de armazenamento na pilha do Azure. Quando que estiver concluída, crie um novo contentor de blob. Definir a política de acesso para "público".  
3. Carregue o ficheiro JSON para o novo contentor. Quando que estiver concluída, pode ver o URL do blob ao clicar no nome de blob e, em seguida, selecionar o URL das propriedades de blob.


## <a name="next-steps"></a>Passos seguintes

[Implementar modelos com a CLI do Azure](azure-stack-deploy-template-command-line.md)

[Ligar com o PowerShell](azure-stack-connect-powershell.md)

[Gerir permissões de utilizador](azure-stack-manage-permissions.md)

