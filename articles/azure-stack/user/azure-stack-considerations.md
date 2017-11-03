---
title: "Diferenças entre o Azure e Azure pilha de chave ao utilizar os serviços e criar aplicações | Microsoft Docs"
description: "O que é necessário saber quando utilizar os serviços ou criar aplicações para a pilha do Azure."
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: 1e170f320292e3dbe920907a4ed81ab0d1eb388b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Considerações da chave: utilizar os serviços ou na criação de aplicações para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Quando utilizar os serviços ou criar aplicações para a pilha do Azure, tem de compreender que existem diferenças entre a pilha do Azure e do Azure. Este artigo fornece uma descrição geral das considerações de chaves quando a pilha do Azure de destino como o ambiente de desenvolvimento de nuvem híbrida.

## <a name="overview"></a>Descrição geral

O Azure Stack é uma plataforma de cloud híbrida que lhe permite utilizar serviços do Azure a partir do datacenter da sua empresa ou do seu fornecedor de serviços. Como um programador pode criar aplicações que são executadas na pilha do Azure. Em seguida, pode implementar estas aplicações para a pilha do Azure, para o Azure, ou verdadeiramente podem criar aplicações híbridas que tiram partido a conectividade entre uma nuvem de pilha do Azure e o Azure.

O operador de pilha do Azure irá permitir-lhe saber que serviços estão disponíveis para utilização e como obter suporte. Oferece estes serviços através do respetivo planos personalizados e ofertas.

O conteúdo técnico do Azure parte do princípio de que aplicações estão a ser desenvolvidas para um serviço do Azure em vez de pilha do Azure. Quando criar e implementar aplicações para a pilha do Azure, tem de compreender algumas diferenças fundamentais, tais como:

* Pilha do Azure fornece um subconjunto de serviços e funcionalidades que estão disponíveis no Azure.
* O fornecedor de serviço ou da empresa pode escolher quais os serviços que pretendem oferecer. Isto inclui aplicações ou serviços personalizados. Estes poderão oferecer a sua própria documentação personalizada.
* Tem de utilizar os pontos finais de específica de pilha do Azure (por exemplo, os URLs para o endereço de portal e o ponto final do Azure Resource Manager).
* Tem de utilizar o PowerShell e API versões que são suportadas pela pilha de Azure. Efetuar este procedimento assegura que as aplicações funcionarão na pilha do Azure e no Azure.

## <a name="cheat-sheet-high-level-differences"></a>Cheat folha: diferenças de alto nível

A tabela seguinte descreve as diferenças de alto nível entre pilha do Azure e o Azure. Manter estes em consideração quando desenvolver para a pilha do Azure ou utilizar os serviços de pilha do Azure.

| Área | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| Que opera-lo? | Microsoft | O fornecedor de serviço ou organização.|
| Quem poderá contactar para obter suporte? | Microsoft | Para um sistema integrado, contacte o operador de pilha do Azure (no seu fornecedor de serviço ou organização) para o suporte.<br><br>Para obter suporte do Azure pilha Development Kit, visite o [fóruns do Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Porque o kit de desenvolvimento é um ambiente de avaliação, não há nenhum oficial de suporte oferecida através da Microsoft ao cliente suportar (CSS).
| Serviços disponíveis | Ver a lista de [produtos Azure](https://azure.microsoft.com/services/?b=17.04b). Serviços disponíveis variam consoante a região do Azure. | Pilha do Azure suporta um subconjunto de serviços do Azure. Serviços reais irão variar em que o fornecedor de serviço ou organização escolhe oferecer.
| Azure Resource Manager endpoint * | https://management.azure.com | Para um sistema de pilha do Azure integrado, utilize o ponto final que o operador de pilha do Azure fornecido.<br><br>Para o kit de desenvolvimento, utilize: https://management.local.azurestack.external
| Portal URL * | [https://portal.Azure.com](https://portal.azure.com) | Para um sistema de pilha do Azure integrado, avance para o URL que o operador de pilha do Azure fornecido.<br><br>Para o kit de desenvolvimento, utilize: https://portal.local.azurestack.external
| Região | Pode selecionar qual região que pretende implementar. | Para um sistema de pilha do Azure integrado, utilize a região que está disponível no seu sistema.<br><br>Para o kit de desenvolvimento, região será sempre **local**.
| Grupos de recursos | Um grupo de recursos pode abranger regiões. | Para sistemas integrados e o kit de desenvolvimento, há apenas uma região.
|Espaços de nomes suportados, tipos de recursos e versões de API | A versão mais recente (ou versões anteriores que ainda não foram preteridas). | Pilha do Azure suporta versões específicas. Consulte a secção "Requisitos de versão" deste artigo.
| | |

* Se um operador de pilha do Azure, consulte o artigo [através do portal de administrador](../azure-stack-manage-portals.md) e [Noções básicas da administração](../azure-stack-manage-basics.md) para obter mais informações.

## <a name="helpful-tools-and-best-practices"></a>Ferramentas úteis e melhores práticas
 
 A Microsoft fornece várias ferramentas e documentação de orientação que o ajuda a desenvolver para a pilha do Azure.

| Recomendação | Referências | 
| -------- | ------------- | 
| Instale as ferramentas corretas na sua estação de trabalho de programador. | - [Instale o PowerShell](azure-stack-powershell-install.md)<br>- [Descarregar as ferramentas](azure-stack-powershell-download.md)<br>- [Configurar o PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalar o Visual Studio](azure-stack-install-visual-studio.md) 
| Reveja as informações sobre o seguinte:<br>-Considerações sobre os modelos do Resource Manager as do azure<br>-Como localizar modelos de início rápido<br>-Utilizar um módulo de política para o ajudar a utilizar o Azure para desenvolver para a pilha do Azure | [Desenvolver para o Azure Stack](azure-stack-developer.md) | 
| Reveja e siga as melhores práticas para modelos. | [Modelos de início rápido do Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisitos da versão

Pilha do Azure suporta versões específicas do Azure PowerShell e APIs de serviço do Azure. Tem de utilizar as versões suportadas para se certificar de que a aplicação pode implementar para ambos os pilha do Azure e no Azure.

Para se certificar de que utiliza uma versão correta do Azure PowerShell, utilize [perfis de versão de API](azure-stack-version-profiles.md). Para determinar o perfil de versão de API mais recente que pode utilizar, tem de saber qual criar da pilha do Azure que está a utilizar. Pode obter estas informações ao administrador de pilha do Azure.

>[!NOTE]
 Se estiver a utilizar o Kit de desenvolvimento de pilha do Azure e têm acesso administrativo, consulte a secção "Determinar a versão atual" [gerir atualizações](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) para determinar a compilação de pilha do Azure.

Para outras APIs, executadas o seguinte comando do PowerShell para os espaços de nomes, tipos de recursos e as versões de API que são suportadas na sua subscrição do Azure pilha de saída. Tenha em atenção que poderá ainda diferenças um nível de propriedade. (Para este comando funcione, tem de ter já [instalado](azure-stack-powershell-install.md) e [configurado](azure-stack-powershell-configure-user.md) PowerShell para um ambiente de pilha do Azure. Tem também de ter uma subscrição para uma oferta de pilha do Azure.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exemplo de saída (truncado): ![exemplo da saída do comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Passos seguintes

Para obter informações mais detalhadas sobre as diferenças um nível de serviço, consulte:

* [Considerações para máquinas virtuais na pilha do Azure](azure-stack-vm-considerations.md)
* [Considerações para armazenamento na pilha do Azure](azure-stack-acs-differences.md)
* [Considerações para redes de pilha do Azure](azure-stack-network-differences.md)
