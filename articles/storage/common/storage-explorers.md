---
title: Ferramentas para trabalhar com o Storage do Azure | Microsoft Docs
description: Uma lista das ferramentas que lhe permitem ver/interagir com os seus dados de armazenamento do Azure.
services: storage
documentationcenter: 
author: dineshmurthy
manager: jahogg
editor: tysonn
ms.assetid: e4748642-98c4-437e-b0ed-4f9641c2e894
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.openlocfilehash: 5c2add48b128a3e5a632c048f0feb4413fcb26cc
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2017
---
# <a name="azure-storage-client-tools"></a>Ferramentas de Cliente do Armazenamento do Azure
Os utilizadores do Storage do Azure com frequência querem ser capazes de vista/interagir com os dados utilizando uma ferramenta de cliente do Storage do Azure. Nas tabelas abaixo, iremos lista várias ferramentas que permitem-lhe fazê-lo. Vamos colocar "X" em cada bloco se fornece a capacidade para a enumeração de e/ou aceder a abstração de dados. A tabela mostra também se as ferramentas está livre ou não. "Versão de avaliação" indica que existe uma versão de avaliação gratuita, mas o produto completo não está livre. "Y/N" indica que está disponível uma versão gratuitamente, enquanto está disponível para comprar uma versão diferente.

Só fornecemos um instantâneo das ferramentas de cliente do Storage do Azure disponíveis. Estas ferramentas podem continuar a evoluir e aumentar a funcionalidade. Se existirem atualizações ou correções, deixe um comentário para nos informar. O mesmo é verdadeiro que se souber de ferramentas que aparece aqui - ser iremos seria satisfeitos adicioná-los.

**Ferramentas de cliente de armazenamento do Microsoft Azure**

<table>
  <tr>
    <th rowspan="2">Ferramenta de cliente do Storage do Azure</th>
    <th rowspan="2">Blob de bloco</th>
    <th rowspan="2">Blob de página</th>
    <th rowspan="2">Blob de acréscimo</th>
    <th rowspan="2">Tabelas</th>
    <th rowspan="2">Filas</th>
    <th rowspan="2">Ficheiros</th>
    <th rowspan="2">Gratuito</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Portal do Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Explorador de Armazenamento do Microsoft Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Explorador de servidores do Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Ferramentas de cliente do Storage do Azure de terceiros**

Iremos não verificou a funcionalidade ou qualidade reivindicados pelas seguintes ferramentas de terceiros e as respetivas listagem não implica um endossamento pela Microsoft.

<table>
  <tr>
    <th rowspan="2">Ferramenta de cliente do Storage do Azure</th>
    <th rowspan="2">Blob de bloco</th>
    <th rowspan="2">Blob de página</th>
    <th rowspan="2">Blob de acréscimo</th>
    <th rowspan="2">Tabelas</th>
    <th rowspan="2">Filas</th>
    <th rowspan="2">Ficheiros</th>
    <th rowspan="2">Gratuito</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: O Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Avaliação</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Explorador do Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Explorador de armazenamento da Web do Azure</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">Explorador de cloudBerry</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>Y/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Combinação de nuvem</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Avaliação</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>S</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Nuvem Gladinet</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Avaliação</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
