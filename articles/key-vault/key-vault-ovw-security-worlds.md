---
ms.assetid: 
title: "Universos de segurança do Cofre de chaves do Azure | Microsoft Docs"
ms.service: key-vault
author: BrucePerlerMS
ms.author: bruceper
manager: mbaldwin
ms.date: 07/03/2017
ms.openlocfilehash: 921bbd109c9ea98d8b5c286a7512bed026412d26
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Universos de segurança do Cofre de chaves do Azure e limites geográficos

O Cofre de chaves do Azure é um serviço de multi-inquilino e utiliza um conjunto de módulos de segurança de Hardware (HSMs) em cada localização do Azure. 

Todos os HSMs do Azure localizações na mesma região geográfica partilham o mesmo limite de criptografia (universo de segurança da Thales). Por exemplo, EUA leste e EUA oeste partilham o mesmo universo de segurança porque pertencem a localização de georreplicação EUA. Da mesma forma, todas as localizações do Azure no Japão partilham o mesmo universo de segurança e todas as localizações do Azure da Austrália, Índia e assim sucessivamente. 

## <a name="backup-and-restore-behavior"></a>Comportamento de cópia de segurança e restauro

É possível restaurar uma cópia de segurança criada uma chave de um cofre de chaves numa única localização do Azure para um cofre de chaves noutra localização do Azure, desde que ambas estas condições forem verdadeiras:

- Ambas as localizações do Azure pertencem à mesma localização geográfica
- Ambos os cofres de chaves de pertencer à mesma subscrição do Azure

Por exemplo, uma cópia de segurança criada por uma determinada subscrição de uma chave de um cofre de chaves no oeste da Índia, só pode ser restaurado para outro Cofre de chaves na mesma subscrição e localização georreplicação; Índia Ocidental, Índia Central ou sul da Índia

## <a name="regions-and-products"></a>Regiões e produtos

- [Regiões do Azure](https://azure.microsoft.com/regions/)
- [Produtos da Microsoft por região](https://azure.microsoft.com/regions/services/)

Regiões estão mapeadas para universos de segurança, mostrados como cabeçalhos de principais nas tabelas:

Os produtos por artigo de região, por exemplo, o **Americas** separador contém Leste dos EUA, EUA CENTRAL, EUA oeste todos os mapeamentos para a região Americas. 

>[!NOTE]
>Uma exceção é que dos EUA Leste DOD e nos DOD CENTRAL têm os seus próprios universos de segurança. 

Da mesma forma, no **Europa** separador, Europa do Norte e Europa Ocidental estão ambos mapeados para a região Europa. O mesmo é verdadeiro no **Ásia-Pacífico** separador.



