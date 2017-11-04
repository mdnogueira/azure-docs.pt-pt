---
title: "Cenários comuns do catálogo de dados do Azure | Microsoft Docs"
description: "Uma descrição geral dos cenários comuns do catálogo de dados Azure, incluindo o registo e a deteção de origens de dados de elevado valor, ativação de business intelligence personalizada e a captura de dados de conhecimento existente sobre origens de dados e processos."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 60930d78-d2d4-4d5d-9651-bdda50b0da0e
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/15/2017
ms.author: maroche
ms.openlocfilehash: 8f333b96165144eb46e730a7a1dff52d064532bb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-catalog-common-scenarios"></a>Cenários comuns do Catálogo de Dados do Azure
Este artigo apresenta os cenários comuns em que o catálogo de dados do Azure pode ajudar a sua organização obter o valor mais das suas origens de dados existente.

## <a name="scenario-1-registration-of-central-data-sources"></a>Cenário 1: Registo de origens de dados central
As organizações têm, muitas vezes, várias origens de dados de elevado valor. Estas origens de dados incluem a transação de linha de negócio, online (OLTP) sistemas, os armazéns de dados e bases de dados de business intelligence/análise de processamento. O número de sistemas e sobreposição entre eles, normalmente aumenta ao longo do tempo, conforme as necessidades de negócio evoluem e o negócio próprio medida que evolui através de, por exemplo, fusões e aquisições.

Pode ser difícil para os membros da organização saber onde localizar os dados dentro destas origens de dados. São todos os demasiado comuns perguntas como o seguinte:

* Dos sistemas de RH três utilizados dentro da empresa, que deve utilizar para criar este tipo de relatório?
* Onde deve passar para obter os números de vendas certificados para o ano fiscal que apenas terminado?
* Quem posso peça ou o que é o processo que deve utilizar para aceder ao armazém de dados?
* Posso não sabe se estes números estão corretos. Quem pode posso pedir para obter informações sobre como deve ser utilizada antes de posso partilhar este dashboard com os meus equipa estes dados?

Para estas e outras perguntas, o catálogo de dados do Azure pode fornecer respostas. As origens de dados central, elevado valor, IT-geridos que são utilizadas em organizações são, muitas vezes, o ponto de partida lógico para povoar o catálogo. Embora a qualquer utilizador pode registar uma origem de dados, ter o catálogo kick-started com as origens de dados que são mais prováveis fornecer o valor para o maior número de utilizadores ajuda promover a adoção e utilização do sistema. 

Se lhe é introdução ao catálogo de dados do Azure, identificar e registar origens de dados fundamentais utilizadas por várias equipas diferentes dos consumidores de dados podem ser o primeiro passo para êxito.

Este cenário também apresenta uma oportunidade para anotar origens de dados de elevado valor para os tornar mais fácil de compreender e aceder. É um aspeto chave deste esforço para incluir informações sobre a forma como os utilizadores podem pedir acesso à origem de dados. Catálogo de dados do Azure, pode fornecer o endereço de e-mail do utilizador ou equipa que é responsável pelo controlo de origem de dados de acesso, as ligações para ferramentas existentes ou documentação ou texto livre que descreve o processo de pedido de acesso. Estas informações ajudam os membros que detetar origens de dados registados, mas que ainda não tem permissões para aceder aos dados facilmente pedir acesso utilizando os processos que são definidos e controlados pelos proprietários de origem de dados.

## <a name="scenario-2-self-service-business-intelligence"></a>Cenário 2: Business intelligence personalizada
Embora as tradicionais soluções de business intelligence empresarias continuarem a ser uma parte valiosas de landscapes de dados de muitas organizações, a alteração esteja a par do negócio fez BI self-service mais importantes. Utilizando BI self-service, os infotrabalhadores e analistas podem criar os seus próprios relatórios, livros e dashboards sem depender de uma equipa de TI central ou a ser restringida por essa equipa de TI agenda e disponibilidade.

Em cenários de BI self-service, os utilizadores normalmente combinam dados de várias origens, muitas das quais podem não anteriormente foram utilizadas para análise e BI. Apesar de já podem ser conhecidas algumas destas origens de dados, este pode ser um desafio para descobrir o que fazer para localizar e avaliar potenciais origens de dados para uma determinada tarefa.

Tradicionalmente, este processo de deteção é um método manual: os analistas de utilizam as suas ligações de rede ponto a ponto para identificar outras pessoas que trabalham com os dados que está a ser sought. Depois de uma origem de dados é localizada e utilizada, o processo repete-se novamente para cada subsequente self-service BI esforço, com utilizadores múltiplos efetuar um processo manual redundante de deteção.

Catálogo de dados do Azure, a organização pode interromper este ciclo de esforço. Após a deteção de uma origem de dados através de meios tradicionais, um analista pode registá-lo para o tornar mais facilmente Detetáveis por outros utilizadores no futuro. Embora o analista pode adicionar mais valor, anotar recursos de dados registados, este anotação não tem de ter ocorrido ao mesmo tempo, como o registo. Podem contribuir os utilizadores ao longo do tempo, como permitir as respetivas agendas, adicionar gradualmente o valor para as origens de dados registadas no catálogo.

Este crescimento orgânico do conteúdo do catálogo é um complemento natural para o registo prévio de origens de dados central. Pré-povoar o catálogo de dados que irão precisar de vários utilizadores pode ser um motivator para utilização inicial e a deteção. Permitir que os utilizadores registar e anotar origens adicionais pode ser uma forma para mantê-los e outros membros da organização parte.

É importante salientar que embora este cenário centra-se especificamente nos BI self-service, o mesmo padrões e desafios aplicam-se em grande escala empresarias BI projetos, bem como. Utilizando o catálogo de dados, a sua organização pode melhorar qualquer esforço de que envolve um processo manual de deteção de origem de dados.

## <a name="scenario-3-capturing-tribal-knowledge"></a>Cenário 3: Capturar dados de conhecimento tribal
Como pode saber quais os dados que precisa de executar a tarefa e onde encontrar dados?

Se tiver sido na sua tarefa de tempo, provavelmente, basta sabe. Já leu uma gradual learning processo e, ao longo do tempo aprendeu sobre as origens de dados que são fundamentais para o seu trabalho diário.

Quando um empregado novo associado a sua equipa, como essa pessoa sabe quais os dados que são necessários para a tarefa e onde pode encontrá-lo?

São odds a nova pessoa integra para estas perguntas.

Esta transferência em curso dos dados de conhecimento tribal faz parte do processo de deteção de origem de dados em organizações de grandes dimensões e em pequenos. Mais sénior e experiente os membros da equipa acumuladas conhecimento ao longo dos anos e os membros da equipa mais recentes tem aprendidas para peça-lhes quando têm perguntas. As informações mais vitais, muitas vezes, existe apenas no cabeças de algumas pessoas essenciais e, quando essas pessoas são de férias ou deixam a equipa, a organização sofrerá.

Especialistas de dados normalmente tornar um esforço documentar os respetivos dados de conhecimento, partilha por e-mail ou em documentos do Word num site do SharePoint equipa. Embora esta abordagem pode ser útil, introduz um novo problema de Deteção: como as pessoas saber que documentação existe e onde pode encontrá-lo?

Catálogo de dados do Azure, a sua organização tem uma localização única central para armazenar e partilhar este conhecimento tribal e para efetuar facilmente Detetáveis. Catálogo de dados, as especialistas de dados podem anotar recursos de dados diretamente em fornecem ligações para documentação existente. Quando os membros da organização utilizam o catálogo para detetar uma origem de dados, irá encontrarem não só a origem de si próprio, mas também os dados de conhecimento que existiam anteriormente apenas em minds de especialistas da sua organização.
