---
title: DNS na pilha do Azure | Microsoft Docs
description: DNS no Azure Stack
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
ms.openlocfilehash: ac93b5eb4228cef373428b7b69932d5993d54fa0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="dns-in-azure-stack"></a>DNS no Azure Stack

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Pilha do Azure inclui as seguintes funcionalidades DNS:
* Suporte para a resolução do nome de anfitrião DNS
* Criar e gerir zonas DNS e registos utilizando a API

## <a name="support-for-dns-hostname-resolution"></a>Suporte para a resolução do nome de anfitrião DNS
Pode especificar uma etiqueta de nome de domínio DNS para um recurso IP público, que cria um mapeamento para *domainnamelabel.location*. cloudapp.azurestack.external para o endereço IP público na pilha do Azure gerida servidores DNS.  

Por exemplo, se criar um recurso IP público com **contoso** como uma etiqueta de nome de domínio na localização de pilha do Local do Azure, o nome de domínio completamente qualificado (FQDN) **contoso.local.cloudapp.azurestack.external**é resolvido para o endereço IP público do recurso. Pode utilizar este FQDN para criar um registo CNAME que aponta para o endereço IP público na pilha do Azure de domínio personalizado.

> [!IMPORTANT]
> Cada etiqueta do nome de domínio criada tem de ser exclusiva dentro da respetiva localização de pilha do Azure.

Se criar o endereço IP público a utilizar o portal, este aspeto:

![Criar endereço IP público](media/azure-stack-whats-new-dns/image01.png)

Esta configuração é útil se pretender associar um endereço IP público com um recurso com balanceamento de carga. Por exemplo, pode ter um balanceador de carga a processar pedidos de uma aplicação web. Atrás a carga balanceador é um web site localizados num ou mais máquinas virtuais. Agora pode aceder ao web site com balanceamento de carga por um nome DNS, em vez de um endereço IP.

## <a name="create-and-manage-dns-zones-and-records-using-api"></a>Criar e gerir zonas DNS e registos utilizando a API
Pode criar e gerir registos na pilha do Azure e zonas DNS.  

Pilha do Azure fornece um serviço DNS como do Azure, utilizando APIs que são consistentes com as APIs de DNS do Azure.  Por que aloja os seus domínios no DNS de pilha do Azure, pode gerir os registos DNS com as mesmas credenciais, APIs, ferramentas, faturação e suporte como outros serviços do Azure. 

Por motivos de óbvios, a infraestrutura de DNS de pilha do Azure é mais compacta que do Azure. Assim, o âmbito, dimensionamento e desempenho dependem a escala da implementação de pilha do Azure e o ambiente onde está implementada.  Por isso, coisas como desempenho, disponibilidade, distribuição global e elevada disponibilidade (HA) podem variar de implementação para implementação.

## <a name="comparison-with-azure-dns"></a>Comparação com o DNS do Azure
O DNS na pilha do Azure é semelhante ao DNS no Azure, com duas exceções principais:
* **Não suporta registos AAAA**

    Pilha do Azure não suporta registos AAAA porque a pilha do Azure não suporta endereços IPv6.  Este é uma principal diferença entre o DNS no Azure e pilha do Azure.
* **Não se trata de multi-inquilino**

    Ao contrário do Azure, o serviço DNS na pilha do Azure não está multi-inquilino. Para cada inquilino não é possível criar a zona DNS mesma. Apenas a primeiro subscrição tenta criar a zona com êxito e falharem os pedidos subsequentes.  É um problema conhecido e uma principal diferença entre o Azure e o DNS de pilha do Azure. Este problema será resolvido numa versão futura.

Além disso, existem algumas pequenas diferenças na forma como DNS de pilha do Azure implementa etiquetas, metadados, Etags e limites.

As seguintes informações aplicam-se especificamente para DNS de pilha do Azure e varia ligeiramente do DNS do Azure. Para saber mais sobre o DNS do Azure, consulte o artigo [zonas DNS e as regista](../../dns/dns-zones-records.md) no site de documentação do Microsoft Azure.

### <a name="tags-metadata-and-etags"></a>As etiquetas, metadados e Etags

**Etiquetas**

O DNS de pilha do Azure suporta utilizando tags de Azure Resource Manager em recursos de zona DNS. Não suporta etiquetas em conjuntos de registos de DNS, embora como alternativa 'metadata' é suportada no registo DNS define conforme explicado seguinte.

**Metadados**

Como alternativa para etiquetas do conjunto de registos, o DNS de pilha do Azure suporta anotar os conjuntos de registos com 'metadata'. Semelhante ao etiquetas, metadados permite-lhe associar pares nome-valor de cada conjunto de registos. Por exemplo, isto pode ser útil para registar o objetivo de cada conjunto de registos. Ao contrário das etiquetas, os metadados não podem ser utilizado para fornecer uma vista filtrada da sua fatura do Azure e não podem ser especificado numa política do Azure Resource Manager.

**Etags são**

Suponhamos duas pessoas ou dois processos tentam modificar um registo DNS ao mesmo tempo. Qual delas wins? E o winner sabe que que tem substituído alterações criadas por outra pessoa?

O DNS de pilha do Azure utiliza Etags para processar alterações em simultâneo ao mesmo recurso em segurança. Os etags são estão separados do Azure Resource Manager 'Etiquetas'. Cada recurso DNS (zona ou conjunto de registos) tem uma Etag associada à mesma. Sempre que é obtido um recurso, a Etag também é obtido. Ao atualizar um recurso, pode optar por transmitir volta a Etag para que DNS de pilha do Azure pode verificar que a Etag correspondências de servidor. Uma vez que cada atualização para um recurso resulta num Etag a ser regenerada, um erro de correspondência de Etag indica que tiver ocorrido uma alteração em simultâneo. Os etags são também podem ser utilizados ao criar um novo recurso para se certificar de que o recurso já existe.

Por predefinição, o Azure PowerShell de DNS de pilha utiliza Etags para bloquear as alterações em simultâneo para zonas e conjuntos de registos. O opcional *-substituir* parâmetro pode ser utilizado para suprimir verificações Etag, nesse caso simultâneas quaisquer alterações ocorridas serão substituídas.

A nível da API de REST de DNS do Azure pilha Etags são especificadas utilizando os cabeçalhos de HTTP. Respetivo comportamento é fornecido na tabela seguinte:

| Cabeçalho | Comportamento|
|--------|---------|
| Nenhuma   | PUT sempre é concluída com êxito (verificações de Etag)|
| IF-match| PUT apenas for bem sucedida se o recurso existe e Etag corresponde|
| IF-match *| PUT apenas for bem sucedida se o recurso existe|
| IF-none-match *| PUT apenas for bem sucedida se o recurso não existe|

### <a name="limits"></a>Limites

Os limites predefinidos seguintes aplicam-se ao utilizar o DNS de pilha do Azure:

| Recurso| Limite predefinido|
|---------|--------------|
| Zonas por subscrição| 100|
| Conjuntos de registos por zona| 5000|
| Registos por conjunto de registos| 20|

## <a name="next-steps"></a>Passos seguintes
[Introdução ao iDNS para pilha do Azure](azure-stack-understanding-dns.md)
