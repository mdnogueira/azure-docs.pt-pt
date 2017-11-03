---
title: "Instruções de infraestrutura do Azure de exemplo | Microsoft Docs"
description: "Saiba mais sobre as diretrizes de conceção e implementação chaves para implementar uma infraestrutura de exemplo no Azure."
documentationcenter: 
services: virtual-machines-linux
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 281fc2c0-b533-45fa-81a3-728c0049c73d
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/26/2017
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b18be0d81d6fad7328edb47c9b69af4eecd3b971
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="example-azure-infrastructure-walkthrough-for-linux-vms"></a>Instruções de infraestrutura do Azure de exemplo para VMs com Linux

[!INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

Este artigo explica de conceção de uma infraestrutura de aplicação de exemplo. Iremos detalhe conceber uma infraestrutura para uma loja online simple que reúne as diretrizes e as decisões relativamente convenções de nomenclatura, conjuntos de disponibilidade, redes virtuais e Balanceadores de carga e a implementação, na verdade, as máquinas virtuais (VMs).

## <a name="example-workload"></a>Carga de trabalho de exemplo
Adventure Works Cycles pretende criar uma aplicação da loja online no Azure que é composta por:

* Dois servidores nginx a executar o cliente front-end numa camada web
* Dois servidores de nginx processar dados e as ordens de uma camada de aplicação
* Parte de servidores do MongoDB dois de um cluster em partição horizontal para armazenar dados de produto e as ordens numa camada de base de dados
* Dois controladores de domínio do Active Directory para contas de cliente e fornecedores uma camada de autenticação
* Todos os servidores estão localizados em duas sub-redes:
  * uma sub-rede para servidores web front-end 
  * uma sub-rede de back-end para os servidores de aplicações, MongoDB cluster e os controladores de domínio

![Diagrama de diferentes camadas para a infraestrutura de aplicação](./media/infrastructure-example/example-tiers.png)

Entrada segura tráfego web tem de ser com balanceamento de carga entre os servidores web como os clientes procurar o arquivo de online. Ordem de processamento de tráfego sob a forma de HTTP pedidos da web servidores tem de ser com balanceamento de carga entre os servidores de aplicação. Além disso, a infraestrutura têm de ser concebida para elevada disponibilidade.

Tem de incorporar o design resultante:

* Uma conta e subscrição do Azure
* Um grupo de recursos única
* Managed Disks do Azure
* Uma rede virtual com duas sub-redes
* Conjuntos de disponibilidade para as VMs com uma função semelhante
* Máquinas virtuais

Todos os anteriores, siga estas convenções de nomenclatura:

* Adventure Works Cycles utiliza **[carga de trabalho IT]-[localização]-[recursos do Azure]** como prefixo
  * Neste exemplo, "**azos**" (Azure on-line Store) é o nome de carga de trabalho de TI e "**utilizar**" (EUA Leste 2) é a localização
* Redes virtuais utilizam AZOS-utilização-VN**[número]**
* Conjuntos de disponibilidade utilizam azos-utilizar-como-**[função]**
* Nomes de máquina virtual utilizar azos-utilizar-vm -**[vmname]**

## <a name="azure-subscriptions-and-accounts"></a>Contas e as subscrições do Azure
Adventure Works Cycles está a utilizar a subscrição do Enterprise, com o nome de subscrição de empresa do Adventure Works, para fornecer a faturação para esta carga de trabalho IT.

## <a name="storage"></a>Armazenamento
Adventure Works Cycles determinar que utilizam discos gerida do Azure. Durante a criação de VMs, ambas as camadas de armazenamento disponível armazenamento são utilizadas:

* **Armazenamento Standard** para os servidores web, servidores de aplicações e controladores de domínio e os respetivos discos de dados.
* **Armazenamento Premium** para os servidores de cluster em partição horizontal do MongoDB e os respetivos discos de dados.

## <a name="virtual-network-and-subnets"></a>Rede virtual e sub-redes
Porque a rede virtual não precisa de conectividade em curso para a rede no local de ciclos de trabalho Adventure, estes decidiu numa rede virtual apenas na nuvem.

Se criaram uma rede virtual apenas na nuvem com as seguintes definições no portal do Azure:

* Nome: AZOS-utilização-VN01
* Localização: EUA Leste 2
* Espaço de endereços de rede virtual: 10.0.0.0/8
* Primeira sub-rede:
  * Nome: front-end
  * Espaço de endereços: 10.0.1.0/24
* Segunda sub-rede:
  * Nome: back-end
  * Espaço de endereços: 10.0.2.0/24

## <a name="availability-sets"></a>Conjuntos de disponibilidade
Para manter a elevada disponibilidade de todos os escalões de quatro da respetiva loja online, Adventure Works Cycles decidir quatro conjuntos de disponibilidade:

* **azos utilize como web** para os servidores web
* **azos utilize como aplicação** para os servidores de aplicações
* **azos utilize como db** para os servidores no cluster a MongoDB
* **azos utilize como dc** para os controladores de domínio

## <a name="virtual-machines"></a>Máquinas virtuais
Adventure Works Cycles decidir os seguintes nomes para as respetivas VMs do Azure:

* **azos-utilização-vm-web01** para o primeiro servidor web
* **azos-utilização-vm-web02** para o segundo servidor web
* **azos-utilização-vm-app01** para o primeiro servidor de aplicação
* **azos-utilização-vm-app02** para o segundo servidor de aplicação
* **azos-utilização-vm-db01** para o primeiro servidor MongoDB no cluster
* **azos-utilização-vm-db02** para o segundo servidor MongoDB no cluster
* **azos-utilização-vm-dc01** para o primeiro controlador de domínio
* **azos-utilização-vm-dc02** segundo controlador de domínio

Segue-se a configuração resultante.

![Infraestrutura de aplicação final implementada no Azure](./media/infrastructure-example/example-config.png)

Incorpora esta configuração:

* Uma rede virtual apenas na nuvem com duas sub-redes (front-end e back-end)
* Discos do Azure gerida utilizando discos Standard e Premium
* Quatro conjuntos de disponibilidade, um para cada camada do loja online
* Máquinas virtuais para as quatro camadas
* Um conjunto com balanceamento de carga externo para o tráfego de web baseado em HTTPS da Internet para os servidores web
* Conjunto para o tráfego de web sem encriptação dos servidores web para os servidores de aplicações com balanceamento de uma carga interno
* Um grupo de recursos única
