---
title: "Orientação do Azure | Microsoft Docs"
description: "Melhores práticas e orientação para o Azure"
services: 
documentationcenter: na
author: bennage
manager: marksou
editor: 
tags: 
ms.assetid: de94c74a-fea7-4815-8484-553e421a7490
ms.service: guidance
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: christb
translationtype: Human Translation
ms.sourcegitcommit: 5f3ced657cf3d6587a63789b3dd3ca41cd2856f0
ms.openlocfilehash: 0061e1ff2ae2d6b8ed7b7c3bb60405e76d4cc91b


---
# <a name="azure-guidance"></a>Orientação do Azure
[!INCLUDE [pnp-header](../../includes/guidance-pnp-header-include.md)]

A equipa de padrões e práticas da Microsoft faz parte da Azure Customer Advisory Team. O nosso objetivo é ajudar programadores, arquitetos e profissionais de TI a terem êxito com a plataforma Microsoft Azure. Criámos orientações que mostram as melhores práticas para criar soluções na cloud no Azure.

## <a name="checklists"></a>Listas de verificação
Estas listas são uma referência rápida para rever os aspetos fundamentais da disponibilidade e da escalabilidade. 

* [Lista de Verificação de Disponibilidade][AvailabilityChecklist] 
  
    Um resumo das práticas recomendadas para garantir a disponibilidade.
* [Lista de Verificação de Escalabilidade][ScalabilityChecklist]
  
    Um resumo das práticas recomendadas para desenhar e implementar serviços dimensionáveis e para lidar com a gestão de dados.

## <a name="best-practices-articles"></a>Artigos de melhores práticas
Os artigos seguintes oferecem um debate aprofundado de conceitos importantes que estão, comummente, associados à informática na cloud. 

* [Design da API][APIDesign] 
  
    Um debate sobre os problemas de design que devem ser tidos em conta ao desenhar uma API Web.
* [Implementação da API][APIImplementation] 
  
    Um conjunto de práticas recomendadas para implementar e publicar APIs Web.
* [API security guidance (Orientação para a segurança das APIs)](https://github.com/mspnp/azure-guidance/blob/master/API-security.md) 
  
    Um debate relativo a questões de autenticação e autorização (por exemplo tipos de tokens, protocolos de autorização, fluxos de autorização e mitigação de ameaças).
* [Documentação de orientação sobre dimensionamento automático][AutoscalingGuidance] 
  
    Um resumo das considerações para dimensionar soluções sem intervenção manual.
* [Documentação de orientação sobre Tarefas de Fundo][BackgroundJobsGuidance] 
  
    Uma descrição das opções disponíveis e das práticas recomendadas para implementar tarefas que devem ser realizadas em segundo plano, independentemente de operações em primeiro plano ou interativas.
* [Documentação de orientação da Rede de Entrega de Conteúdos (CDN)][CDNGuidance] 
  
    Orientação geral e prática recomendada para utilizar a CDN, de modo a minimizar a carga nas suas aplicações e a maximizar a disponibilidade e o desempenho.
* [Documentação de orientação sobre a colocação em cache][CachingGuidance] 
  
    Um resumo sobre como utilizar a colocação em cache para melhorar o desempenho e a escalabilidade de um sistema.
* [Documentação de orientação sobre a Criação de Partições de Dados][DataPartitioningGuidance]
  
    Estratégias que pode utilizar para criar partições de dados, de modo a melhorar a escalabilidade, a reduzir a contenção e a otimizar o desempenho.
* [Documentação de orientação sobre Monitorização e Diagnóstico][MonitoringandDiagnosticsGuidance] 
  
    Orientação para controlar de que forma os seus utilizadores utilizam o seu sistema, rastrear a utilização de recursos e monitorizar, genericamente, o estado de funcionamento e o desempenho do seu sistema.
* [Convenções de nomenclatura recomendadas][naming-conventions] 
  
    Convenções de nomenclatura recomendadas para recursos do Azure.
* [Documentação de orientação Geral para Repetição][RetryGeneralGuidance] 
  
    Debate sobre os conceitos gerais para lidar com falhas transitórias.
* [Documentação de orientação específica para Repetição do Serviço][RetryServiceSpecificGuidance]
  
    Um resumo das funcionalidades de repetição para muitos serviços do Azure, incluindo informações para ajudar a utilizar, adaptar ou ampliar o mecanismo de repetição desses serviços.

## <a name="scenario-guides"></a>Guias para cenários
* [Execução do Elasticsearch no Azure][elasticsearch] 
  
    Elasticsearch é um motor de pesquisa e uma base de dados de código aberto e altamente dimensionável. É adequado para situações que necessitam de análise rápida e de deteção de informações contidas em grandes conjuntos de dados. Esta orientação aborda alguns aspetos-chave a considerar ao desenhar um cluster de Elasticsearch.
* [Gestão de identidades para aplicações multi-inquilino][identity-multitenant] 
  
    “Multitenancy” é uma arquitetura na qual vários inquilinos partilham uma aplicação, mas estão isolados uns dos outros. Esta orientação mostra-lhe como gerir identidades de utilizador numa aplicação multi-inquilino com o [Azure Active Directory][AzureAD] para processar o início de sessão e a autenticação.
* [Developing big data solutions (Desenvolver soluções de macrodados)](https://msdn.microsoft.com/library/dn749874.aspx)
  
    Este guia explora a utilização do HDInsight para cenários como a exploração iterativa, como um armazém de dados, para fins de ETL, e a integração em sistemas de BI existentes. Também inclui orientações para compreender os conceitos de macrodados, o planeamento e o desenho de soluções de macrodados e como implementar estas soluções.

## <a name="patterns"></a>Padrões
* [Cloud Design Patterns: Prescriptive Architecture Guidance for Cloud Applications (Padrões de Desenho da Cloud: Orientação de Arquitetura Prescritiva para Aplicações na Cloud)](https://msdn.microsoft.com/library/dn568099.aspx)
  
    O Cloud Design Patterns (Padrões de Desenho da Cloud) é uma biblioteca de padrões de desenho e tópicos de orientação relacionados. Articula a vantagem de aplicar padrões, ao mostrar como cada parte pode ser integrada em arquiteturas de aplicações na cloud.
* [Optimizing Performance for Cloud Applications (Otimizar o Desempenho de Aplicações na Cloud)](https://github.com/mspnp/performance-optimization)
  
    Esta orientação explora padrões antagónicos comuns que impedem o dimensionamento de aplicações quando estão em carga. Inclui exemplos que demonstram oito padrões antagónicos, um [manual básico de análise de desempenho](https://github.com/mspnp/performance-optimization/blob/master/Performance-Analysis-Primer.md) e um guia para [avaliar o desempenho em relação a métricas-chave](https://github.com/mspnp/performance-optimization/blob/master/Assessing-System-Performance-Against-KPI.md).

## <a name="reference-architectures"></a>Arquiteturas de referência
As nossas arquiteturas de referência estão organizadas por cenário.
Cada arquitetura individual oferece práticas recomendadas e passos prescritivos e um componente executável que incorpora as recomendações.

A biblioteca atual de arquiteturas de referência está disponível em [http://aka.ms/architecture](http://aka.ms/architecture).

## <a name="resiliency-guidance"></a>Orientação sobre resiliência
Estes tópicos descrevem como desenhar aplicações resilientes a falhas num ambiente na cloud distribuído.   

* [Descrição geral da resiliência][ResiliencyOvervew]
  
     Como criar aplicações na plataforma do Azure que podem recuperar de falhas e continuar a funcionar. Descreve uma abordagem estruturada para obter resiliência, desde o design à implementação e ao funcionamento.
* [Lista de verificação da resiliência][resiliency-checklist]
  
    Uma lista de recomendações que o vão ajudar a planear vários modos de falhas que podem ocorrer.
* [Análise do modo de falhas][resiliency-fma] 
  
    A Análise do modo de falhas (FMA) é um processo para conferir resiliência a um sistema mediante a identificação de possíveis pontos de falhas. Como ponto de partida para o seu processo de MFA, este artigo inclui um catálogo de potenciais modos de falhas e respetivas mitigações. 

<!-- links -->

[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/

[PerformanceOptimization]: https://github.com/mspnp/performance-optimization

[APIDesign]: ../best-practices-api-design.md
[APIImplementation]: ../best-practices-api-implementation.md
[AutoscalingGuidance]: ../best-practices-auto-scaling.md
[BackgroundJobsGuidance]: ../best-practices-background-jobs.md
[CDNGuidance]: ../best-practices-cdn.md
[CachingGuidance]: ../best-practices-caching.md
[DataPartitioningGuidance]: ../best-practices-data-partitioning.md
[MonitoringandDiagnosticsGuidance]: ../best-practices-monitoring.md
[RetryGeneralGuidance]: ../best-practices-retry-general.md
[RetryServiceSpecificGuidance]: ../best-practices-retry-service-specific.md
[RetryPolicies]: Retry-Policies.md
[ScalabilityChecklist]: ../best-practices-scalability-checklist.md
[AvailabilityChecklist]: ../best-practices-availability-checklist.md
[naming-conventions]: guidance-naming-conventions.md

<!-- guidance projects -->
[elasticsearch]: guidance-elasticsearch.md
[identity-multitenant]: guidance-multitenant-identity.md

<!-- reference architectures -->
[ref-arch-single-vm-windows]: guidance-compute-single-vm.md
[ref-arch-single-vm-linux]: guidance-compute-single-vm-linux.md
[ref-arch-multi-vm]: guidance-compute-multi-vm.md
[ref-arch-3-tier]: guidance-compute-3-tier-vm.md
[ref-arch-n-tier-windows]: guidance-compute-n-tier-vm.md
[ref-arch-n-tier-linux]: guidance-compute-n-tier-vm-linux.md
[ref-arch-multi-dc-windows]: guidance-compute-multiple-datacenters.md
[ref-arch-multi-dc-linux]: guidance-compute-multiple-datacenters-linux.md

<!-- resiliency -->
[resiliency-fma]: guidance-resiliency-failure-mode-analysis.md
[resiliency-checklist]: guidance-resiliency-checklist.md
[ResiliencyOvervew]: guidance-resiliency-overview.md




<!--HONumber=Jan17_HO3-->


