---
title: Compreender o DNS na pilha do Azure | Microsoft Docs
description: "A compreensão das funcionalidades DNS e capacidades na pilha do Azure"
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 60f5ac85-be19-49ac-a7c1-f290d682b5de
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 8c023eda179ace41a082bf4a4fadc281c14db7ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="introducing-idns-for-azure-stack"></a>Introdução ao iDNS para pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

iDNS é uma funcionalidade na pilha do Azure que permite-lhe resolver nomes DNS externos (tal como http://www.bing.com).
Também permite-lhe registar nomes de rede virtual interna. Ao fazê-lo, pode resolver VMs na mesma rede virtual por nome em vez de endereço IP, sem ter de fornecer as entradas de servidor DNS personalizadas.

É algo que foi sempre existe no Azure, mas está disponível no Windows Server 2016 e o Azure pilha demasiado.

## <a name="what-does-idns-do"></a>O que fazer iDNS?
Com iDNS na pilha do Azure, obtenha as seguintes capacidades, sem ter de especificar as entradas de servidor DNS personalizadas.

* Partilhado serviços de resolução de nome DNS para cargas de trabalho inquilinas.
* Serviço DNS autoritativo para a resolução do nome e o registo DNS na rede virtual de inquilino.
* Serviço DNS recursivo para a resolução de nomes de Internet de VMs inquilinas. Os inquilinos já não tem de especificar as entradas de DNS personalizadas para resolver nomes de Internet (por exemplo, www.bing.com).

Pode ainda traga a sua própria DNS e utilizar servidores DNS personalizados, se pretender. Mas, agora, se acabou pretender ser capaz de resolver os nomes de DNS da Internet e de conseguir estabelecer ligação com outras máquinas virtuais na mesma rede virtual, não precisa de especificar nada e irá funcionar normalmente.

## <a name="what-does-idns-not-do"></a>O que faz iDNS não?
Que iDNS não permite-lhe fazer é criar um registo DNS para um nome que pode ser resolvido a partir de fora da rede virtual.

No Azure, tem a opção de especificar uma etiqueta de nome DNS que pode ser associada com um endereço IP público. Pode escolher a etiqueta (prefixo), mas o Azure escolhe o sufixo, que se baseia na região na qual criar o endereço IP público.

![Etiqueta de nome de captura de ecrã do DNS](media/azure-stack-understanding-dns-in-tp2/image3.png)

Na imagem que se segue, Azure irá criar um registo "A no DNS para a etiqueta do nome DNS especificado na zona" **westus.cloudapp.azure.com**. O prefixo e o sufixo em conjunto, compõem um completamente qualificado domínio nome (FQDN) que pode ser resolvido a partir em qualquer lugar na Internet pública.

Pilha do Azure só suporta iDNS para o registo do nome interno, pelo que não é possível efetuar o seguinte.

* Crie um registo DNS sob uma zona DNS alojada existente (por exemplo, local.azurestack.external).
* Crie uma zona DNS (por exemplo, Contoso.com).
* Crie um registo na sua própria zona DNS personalizado.
* Suporta a compra de nomes de domínio.

