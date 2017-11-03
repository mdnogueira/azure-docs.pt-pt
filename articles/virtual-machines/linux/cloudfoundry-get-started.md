---
title: "Introdução ao Cloud Foundry no Microsoft Azure | Microsoft Docs"
description: Executar OSS ou Foundry Pivotal nuvem no Microsoft Azure
services: virtual-machines-linux
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 2a15ffbf-9f86-41e4-b75b-eb44c1a2a7ab
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/19/2017
ms.author: seanmck
ms.openlocfilehash: 94fbde7707ea9a91076780fdefc3f5a827e0e7b2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-foundry-on-azure"></a>Cloud Foundry no Azure

Foundry de nuvem é um open source plataforma-como-um-serviço (PaaS) para criar, implementar e operar desenvolvidas em várias linguagens e arquiteturas de aplicações de 12 fatores. Este documento descreve as opções que tem para que executem Foundry de nuvem do Azure e como pode começar a utilizar.

## <a name="cloud-foundry-offerings"></a>Ofertas de nuvem de Foundry

Existem duas formas de Foundry nuvem disponível para ser executada no Azure: open source nuvem Foundry (OSS CF) e Pivotal nuvem Foundry (PCF). OSS CF é um inteiramente [open source](https://github.com/cloudfoundry) versão de nuvem Foundry geridos pelo Foundation de Foundry na nuvem. Pivotal Foundry de nuvem é uma distribuição enterprise Cloud Foundry da Pivotal Software Inc. Vamos ver algumas das diferenças entre as ofertas de dois.

### <a name="open-source-cloud-foundry"></a>Nuvem de open source Foundry

Pode implementar OSS Foundry de nuvem no Azure ao primeiro implementar um director BOSH e, em seguida, implementar Foundry de nuvem, utilizando o [instruções fornecidas no GitHub](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/blob/master/docs/guidance.md). Para saber mais sobre como utilizar OSS CF, consulte o [documentação](https://docs.cloudfoundry.org/) forneceu a base de Foundry de nuvem.

A Microsoft fornece suporte de melhor esforço para OSS CF através dos seguintes canais de Comunidade:

- #<a name="bosh-azure-cpi-channel-on-cloud-foundry-slackhttpsslackcloudfoundryorg"></a>canal de bosh-azure-cpi no [nuvem Foundry Slack](https://slack.cloudfoundry.org/)
- [lista de mailing cf bosh](https://lists.cloudfoundry.org/pipermail/cf-bosh)
- Problemas de GitHub para o [CPI](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/issues) e [Mediador de serviço](https://github.com/Azure/meta-azure-service-broker/issues)

>[!NOTE]
> O nível de suporte para os recursos do Azure, tais como as máquinas virtuais em que é executado na nuvem Foundry, se baseia no seu contrato de suporte do Azure. Suporte da Comunidade de melhor esforço aplica-se apenas para os componentes de nuvem Foundry específicos.

### <a name="pivotal-cloud-foundry"></a>Nuvem Pivotal Foundry

Pivotal nuvem Foundry inclui a mesma plataforma principal como a distribuição de OSS, juntamente com um conjunto de ferramentas de gestão proprietárias e suporte da empresa. Para executar PCF no Azure, tem de adquirir uma licença do Pivotal. A oferta PCF no Azure Marketplace inclui uma licença de avaliação de 90 dias.

As ferramentas incluem [Pivotal do Operations Manager](http://docs.pivotal.io/pivotalcf/customizing/), uma aplicação web que simplifica a implementação e gestão de uma base de nuvem Foundry e [Pivotal aplicações Manager](https://docs.pivotal.io/pivotalcf/console/), uma aplicação web para gerir utilizadores e aplicações.

Além dos canais de suporte listados para OSS CF acima, uma licença PCF com poderá contactar Pivotal para obter suporte. Microsoft e Pivotal também tem ativado os fluxos de trabalho de suporte que lhe permitem contactar a terceiros para obter ajuda e ter a sua consulta encaminhar corretamente, dependendo de onde se situa o problema.

## <a name="azure-service-broker"></a>Mediador de serviço do Azure

Nuvem Foundry encoraja o ["aplicação doze fatores"](https://12factor.net/) metodologia, que promove uma separação de processos de aplicações sem monitorização de estado e com monitorização de estado dos serviços de segurança limpa. [Mediadores de serviço](https://docs.cloudfoundry.org/services/api.html) oferecem uma forma consistente para aprovisionar e vincular os serviços de cópia de segurança para aplicações. O [Mediador de serviço do Azure](https://github.com/Azure/meta-azure-service-broker) fornece alguns dos serviços do Azure chaves através deste canal, incluindo o armazenamento do Azure e SQL do Azure.

Se estiver a utilizar Pivotal Foundry de nuvem, o Mediador de serviço está também [disponível como um mosaico](https://docs.pivotal.io/azure-sb/installing.html) da rede Pivotal.

## <a name="related-resources"></a>Recursos relacionados

### <a name="visual-studio-team-services-plugin"></a>Visual Studio Team Services, o plug-in

Nuvem Foundry é também adequada para o desenvolvimento de software seja ágil, incluindo a utilização de integração contínua (CI) e entrega contínua (CD). Se utilizar os serviços de equipa do Visual Studio (VSTS) para gerir os seus projetos e pretender definir se um CI/CD pipeline filtragem Foundry de nuvem, pode utilizar o [extensão de compilação de VSTS nuvem Foundry](https://marketplace.visualstudio.com/items?itemName=ms-vsts.cloud-foundry-build-extension). O plug-in torna simples de configurar e automatizar implementações de nuvem Foundry, se em execução no Azure ou outro ambiente.

## <a name="next-steps"></a>Passos seguintes

- [Implementar Foundry Pivotal na nuvem do Azure Marketplace](https://azure.microsoft.com/en-us/marketplace/partners/pivotal/pivotal-cloud-foundryazure-pcf/)
- [Implementar uma aplicação na nuvem Foundry no Azure](./cloudfoundry-deploy-your-first-app.md)