---
title: "Introdução ao Catálogo de Dados do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral do Catálogo de Dados do Microsoft Azure, incluindo as suas funcionalidades e os problemas que se destina a resolver. O Catálogo de Dados fornece capacidades que permitem a qualquer utilizador – dos analistas a cientistas de dados aos programadores – registar, detetar, compreender e consumir origens de dados."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: cc733907-17ec-4153-9f0c-5b3754b2db19
ms.service: data-catalog
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: cd3184059a0970bb7c9b59803cf37125abbbd879
ms.openlocfilehash: 19d4c3990c88f82f60cd253ffdcbd2005d00ad79


---
# <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
O Catálogo de Dados do Azure é um serviço em nuvem completamente gerido que permite aos utilizadores detetar as origens de dados de que precisam e compreender as origens de dados que detetam, ajudando também as organizações a obter maior valor dos investimentos existentes. O Catálogo de Dados fornece capacidades que permitem a qualquer utilizador – dos analistas a cientistas de dados aos programadores – detetar, compreender e consumir origens de dados. O Catálogo de Dados inclui um modelo de crowdsourcing de metadados e anotações, e permite que todos os utilizadores contribuam com os seus conhecimentos para criar uma comunidade e cultura de dados.

## <a name="discovery-challenges-for-data-consumers"></a>Desafios de deteção para os consumidores de dados
Tradicionalmente, detetar origens de dados empresariais era um processo orgânico com base em conhecimento tribal. Isto apresenta inúmeros desafios para as empresas que pretendem obter o máximo valor dos recursos de informações.

* Os utilizadores não têm conhecimento de que as origens de dados existem, a menos que entrem em contacto com elas como parte de outro processo; não existe uma localização central onde as origens de dados estão registadas.
* A menos que um utilizador conheça a localização de uma origem de dados, ele não pode ligar aos dados com uma aplicação de cliente; as experiências de consumo de dados exigem que os utilizadores conheçam a cadeia de ligação ou o caminho.
* A menos que um utilizador conheça a localização da documentação de uma origem de dados, ele não consegue compreender as utilizações previstas dos dados; as origens de dados e a documentação estão em locais diferentes e são consumidas através de experiências diferentes.
* Se um utilizador tiver questões sobre um recurso de informações, ele tem de localizar o especialista ou a equipa responsável pelos dados e interagir com esses especialistas offline; não existe nenhuma ligação explícita entre os dados e as pessoas com perspetivas especialistas na sua utilização.
* A menos que um utilizador compreenda o processo para pedir acesso à origem de dados, a deteção da origem de dados e a respetiva documentação continua a não lhe permitir o acesso aos dados que ele precisa.

## <a name="discovery-challenges-for-data-producers"></a>Desafios de deteção para os produtores de dados
Enquanto os consumidores de dados enfrentam estes desafios, os utilizadores responsáveis pela produção e manutenção dos recursos de informações enfrentam os seus próprios desafios.

* Anotar origens de dados com metadados descritivos é frequentemente um esforço perdido; normalmente, as aplicações de cliente ignoram as descrições armazenadas na origem de dados.
* A criação de documentação para origens de dados é frequentemente um esforço perdido; manter a documentação sincronizada com a origem de dados é uma responsabilidade contínua, e os utilizadores não têm confiança na documentação, pois é frequentemente interpretada como estando desatualizada.
* Restringir o acesso à origem de dados e garantir que os consumidores de dados sabem como pedir acesso é um desafio contínuo.

Criar e manter a documentação de uma origem de dados é complexo e um processo demorado. O desafio de disponibilizar essa documentação prontamente para todos os utilizadores da origem de dados é ainda mais complexo.

Ao serem combinados, estes desafios apresentam um barreira significativa para as empresas que pretendem encorajar e promover a utilização e a compreensão dos dados empresariais.

## <a name="azure-data-catalog-can-help"></a>O Catálogo de Dados do Azure pode ajudar
O Catálogo de Dados foi concebido para resolver estes problemas e para permitir às empresas obter o máximo valor dos recursos de informações existentes. O Catálogo de Dados ajuda, ao tornar os dados facilmente detetáveis e compreensíveis pelos utilizadores que precisam dos dados que gerem.

O Catálogo de Dados fornece um serviço baseado na nuvem, no qual que a origem de dados pode ser registada. Os dados permanecem na localização existente, mas uma cópia dos metadados é adicionada ao Catálogo de Dados, juntamente com uma referência para a localização da origem de dados. Estes metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

Depois de uma origem de dados ser registada, os metadados podem então ser enriquecidos pelo utilizador que efetuou o registo ou por outros utilizadores na empresa. Qualquer utilizador pode anotar uma origem de dados, fornecendo as descrições, etiquetas ou outros metadados, como a documentação e os processos para pedir acesso à origem de dados. Estes metadados descritivos complementam os metadados estruturais (como nomes de colunas e tipos de dados) registados a partir da origem de dados.

Detetar e compreender origens de dados e a sua utilização são o objetivo principal de registar as origens. Quando os utilizadores empresariais precisam de dados para os seus esforços (que podem ser business intelligence, desenvolvimento de aplicações, ciência de dados ou qualquer outra tarefa onde são necessários dados adequados) podem utilizar a experiência de deteção do Catálogo de Dados para localizar rapidamente dados que correspondam às suas necessidades, compreender os dados para avaliar a respetiva adequação à finalidade e consumir dados, abrindo a origem de dados na ferramenta de sua escolha. Ao mesmo tempo, o Catálogo de Dados permite aos utilizadores contribuir para o catálogo, etiquetando, documentando e anotando origens de dados que já foram registadas, e registando novas origens de dados que podem então ser detetadas, compreendidas e consumidas pela comunidade de utilizadores do catálogo.

![Capacidades do Catálogo de Dados](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="get-started-with-data-catalog"></a>Introdução ao Catálogo de Dados
Para começar a utilizar hoje o Catálogo de Dados, visite [www.azuredatacatalog.com](https://www.azuredatacatalog.com).

Está disponível um guia de introdução [aqui](data-catalog-get-started.md).

## <a name="learn-more-about-data-catalog"></a>Saiba mais sobre o Catálogo de Dados
Para saber mais sobre as funcionalidades do Catálogo de Dados, veja:

* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Como descobrir origens de dados](data-catalog-how-to-discover.md)
* [Como anotar origens de dados](data-catalog-how-to-annotate.md)
* [Como documentar origens de dados](data-catalog-how-to-documentation.md)
* [Como ligar a origens de dados](data-catalog-how-to-connect.md)
* [Como trabalhar com macrodados](data-catalog-how-to-big-data.md)
* [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)
* [Como configurar o Glossário Comercial](data-catalog-how-to-business-glossary.md)
* [Perguntas Mais Frequentes](data-catalog-frequently-asked-questions.md)



<!--HONumber=Jan17_HO4-->


