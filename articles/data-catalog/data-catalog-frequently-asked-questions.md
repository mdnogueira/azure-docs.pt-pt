---
title: "Catálogo de dados do Azure perguntas mais frequentes | Microsoft Docs"
description: "Perguntas mais frequentes sobre o catálogo de dados do Azure, incluindo as capacidades de deteção de origem de dados, anotação e gestão."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5c7e209a-458c-4bb4-96bb-7ed178f9528a
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 11/13/2017
ms.author: maroche
ms.openlocfilehash: d1fd6b0bd47188570553e0e3ad1de527a35e98f6
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-data-catalog-frequently-asked-questions"></a>Perguntas mais frequentes sobre o catálogo de dados do Azure
Este artigo fornece respostas às perguntas mais frequentes relacionados com o serviço de catálogo de dados do Azure.

## <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
Catálogo de dados é um serviço completamente gerido, alojado no Microsoft Azure, o que funciona como um sistema de registo e a deteção de origens de dados empresariais. Catálogo de dados, qualquer utilizador dos analistas a cientistas de dados e os programadores, pode registar, detetar, compreender e consumir origens de dados.

## <a name="what-customer-challenges-does-it-solve"></a>O cliente desafia o does resolver?
Catálogo de dados aborda os desafios de deteção de origem de dados e de "dados escuros", para que os utilizadores podem detetar e compreender origens de dados empresariais.

## <a name="what-are-its-target-audiences"></a>Quais são o público-alvo?
Catálogo de dados foi concebido para utilizadores técnicos e não técnicos, incluindo:

* Os programadores de dados e profissionais de BI e a análise: pessoas que são responsáveis pela produzir dados e análise de conteúdo para outros utilizadores consumir.
* Stewards de dados: pessoas que têm o conhecimento sobre os dados, o que significa e como se destina a ser utilizado.
* Os consumidores de dados: às pessoas que necessitam de ser capaz de detetar facilmente, compreender e ligar aos dados que precisam para o trabalho e utilizando a ferramenta à escolha.
* Central IT: as pessoas que precisam para centenas de origens de dados detetável por utilizadores empresariais e que precisam manter a supervisão como dados está a ser utilizados e por quem.

## <a name="what-is-its-availability-by-region"></a>O que é a disponibilidade do mesmo por região?
Serviços de catálogo de dados estão atualmente disponíveis nos seguintes centros de dados:

* EUA Oeste
* EUA Leste
* Europa Ocidental
* Europa do Norte
* Leste da Austrália
* Sudeste Asiático

## <a name="what-are-its-limits-on-the-number-of-data-assets"></a>Quais são os limites no número de recursos de dados?
Edição gratuita do catálogo de dados está limitado a recursos de dados registados 5000.

Edição Standard do catálogo de dados suporta até 100.000 recursos de dados registados.

Qualquer objeto registado no catálogo de dados, tais como tabelas, vistas, ficheiros e relatórios, contagens como um recurso de dados.

## <a name="what-are-its-supported-data-source-and-asset-types"></a>Quais são os respetivos tipos de recursos e de origem de dados suportados?
Para obter uma lista de origens de dados atualmente suportados, consulte [DSR do catálogo de dados](data-catalog-dsr.md).

## <a name="how-do-i-request-support-for-another-data-source"></a>O pedido de suporte para outra origem de dados?
Para submeter pedidos de funcionalidades e outros comentários, vá para o [catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog/category/320788-data-sources).

## <a name="how-do-i-get-started-with-data-catalog"></a>Como começar a utilizar com o catálogo de dados?
É a melhor forma de começar, acedendo a [introdução ao catálogo de dados](data-catalog-get-started.md). Este artigo é uma descrição geral ponto a ponto sobre as capacidades de serviço.

## <a name="how-do-i-register-my-data"></a>Como registar a meus dados?
Para registar os seus dados no catálogo de dados:
1. No portal do catálogo de dados do Azure, no **publicar** área, iniciar a ferramenta de registo do catálogo de dados do Azure. 
2. Na ferramenta registo de origem de dados do catálogo de dados, inicie sessão com as mesmas credenciais que utiliza para aceder ao portal do catálogo de dados.
3. Selecione a origem de dados e dos recursos específicos que pretende registar.

## <a name="what-properties-does-it-extract-for-data-assets-that-are-registered"></a>As propriedades que se extrair para recursos de dados que estão registados?
As propriedades específicas diferem da origem de dados à origem de dados, mas, em geral, o serviço de publicação do catálogo de dados extrai as seguintes informações:

* Nome do recurso
* Tipo de recurso
* Descrição de recurso
* Nomes de atributo/coluna
* Tipos de dados de atributo/coluna
* Descrição de atributo/coluna

> [!IMPORTANT]
> Registar recursos de dados com o catálogo de dados não mover ou copiar os dados para a nuvem. Registar recursos de uma origem de dados copia metadados dos recursos no Azure, mas os dados permanecem na localização de origem de dados existente. A exceção a esta regra é se optar por carregar os registos de pré-visualização ou um perfil de dados ao registar os ativos. Quando inclui uma versão de pré-visualização, até 20 registos são copiados de cada recurso e armazenadas como instantâneo no catálogo de dados. Ao incluir um perfil de dados, agregam informações são calculadas e incluídas nos metadados que é armazenado no catálogo. Agregadas informações podem incluir o tamanho de tabelas, a percentagem de valores nulos por colunas ou valores mínimos, máximo e médio para colunas. 
>
>

> [!NOTE]
> Para origens de dados, tais como o SQL Server Analysis Services que tenha uma primeira classe **Descrição** propriedade, a ferramenta de registo de origem de dados do catálogo de dados extrai esse valor de propriedade. Para bases de dados relacionais de SQL Server, que não dispõem de uma primeira classe **Descrição** propriedade, a ferramenta de registo de origem de dados do catálogo de dados extrai o valor da **ms_description** propriedade para expandida objetos e colunas. Para obter mais informações, consulte [utilizando propriedades expandidas em objetos de base de dados](https://technet.microsoft.com/library/ms190243%28v=sql.105%29.aspx).
>
>

## <a name="how-long-should-it-take-for-newly-registered-assets-to-appear-in-the-catalog"></a>Quanto deve necessário para recursos recentemente registados a aparecer no catálogo?
Depois de registar recursos de catálogo de dados, poderão ocorrer durante um período de 5 a 10 segundos antes de serem apresentados no portal do catálogo de dados.

## <a name="how-do-i-annotate-and-enrich-the-metadata-for-my-registered-data-assets"></a>Como anotar e enriqueça os metadados para os meus recursos de dados registados?
É a forma mais simples de fornecer os metadados para recursos registados para selecionar o elemento no portal do catálogo de dados e, em seguida, introduza os valores no painel de propriedades ou painel de esquema para o objeto selecionado.

Também pode fornecer alguns metadados, como especialistas e etiquetas, durante o processo de registo. Os valores a fornece o serviço de publicação do catálogo de dados que se aplicam a todos os recursos a ser registados nessa altura. Para ver os objetos recentemente registados no portal de anotação adicionais, selecione o **ver Portal** botão no ecrã final da ferramenta de registo de origem de dados do catálogo de dados.

## <a name="how-do-i-delete-my-registered-data-objects"></a>Como eliminar o meu objetos de dados registados
Pode eliminar um objeto do catálogo de dados, selecionando o objeto no portal e, em seguida, clicando a **eliminar** botão. Remover o objeto remove os seus metadados do catálogo de dados, mas não afeta a origem de dados subjacente.

## <a name="what-is-an-expert"></a>O que é um especialista?
Um especialista é uma pessoa que tenha uma perspetiva-se informado sobre um objeto de dados. Um objeto pode ter vários especialistas. Um especialista não precisa de ser o "proprietário" de um objeto, mas é simplesmente a alguém que sabe como os dados podem e devem ser utilizados.

## <a name="how-do-i-share-information-with-the-data-catalog-team-if-i-encounter-problems"></a>Como posso partilharem informações com a equipa do catálogo de dados se posso encontrar problemas?
Para comunicar problemas, partilham informações e fazer perguntas, avance para o [fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).

## <a name="does-the-catalog-work-with-another-data-source-that-im-interested-in"></a>O catálogo funciona com outra origem de dados que estou interessado em?
Ativamente estamos a trabalhar adicionar mais origens de dados no catálogo de dados. Se pretender ver uma origem de dados específicos suportada, sugerimos (ou o suporte de voz, se já tiver sido sugerido) acedendo ao [catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog).

## <a name="how-is-azure-data-catalog-related-to-the-data-catalog-in-power-bi-for-office-365"></a>Como é o catálogo de dados do Azure relacionado para o catálogo de dados no Power BI para Office 365?
Pode considerar do catálogo de dados do Azure como uma evolução do catálogo de dados no Power BI. A partir de mola 2017, o catálogo de dados do Azure é utilizado para ativar a partilha e a deteção de consultas no Excel 2016 e o Power Query para Excel. Capacidades do catálogo de dados no Excel estão disponíveis para os utilizadores com licenças Power BI Pro.

## <a name="what-permissions-do-i-need-to-register-assets-with-data-catalog"></a>Que permissões é necessário registar recursos de catálogo de dados?
Para executar a ferramenta de registo do catálogo de dados, necessita de permissões na origem de dados que lhe permite ler os metadados da origem. Para incluir também uma versão de pré-visualização, tem de ter as permissões que permitem-lhe ler os dados de objetos que está a ser registados.

Catálogo de dados também permite aos administradores do catálogo restringir quais os utilizadores e grupos que podem adicionar metadados para o catálogo. Para obter mais informações, consulte [como proteger o acesso ao catálogo de dados e recursos de dados](data-catalog-how-to-secure-catalog.md).

## <a name="will-data-catalog-be-made-available-for-on-premises-deployment-as-well"></a>Será catálogo de dados seja disponibilizado para, bem como a implementação no local?
Catálogo de dados é um serviço em nuvem que pode trabalhar com as origens de dados na nuvem e no local para fornecer uma solução de deteção de origem de dados híbrida. Não existem atualmente não planos para uma versão do serviço catálogo de dados que é executado no local.

## <a name="can-i-extract-more-or-richer-metadata-from-the-data-sources-i-register"></a>Pode extrair metadados mais rico ou mais das origens de dados, que o registo?
Ativamente estamos a trabalhar para expandir as capacidades do catálogo de dados. Se pretender ter adicionais metadados extraídos da origem de dados durante o registo, sugerimos (ou votos para o mesmo, se já tiver sido sugerido) no [catálogo de dados nos fóruns de comentários do Azure](https://feedback.azure.com/forums/906052-data-catalog). 

Se gostaria de incluir metadados da coluna/esquema, pré-visualizações ou perfis de dados, as origens de dados onde estes metadados não é extraído pela ferramenta de registo de origem de dados, pode utilizar a API do catálogo de dados para adicionar estes metadados. Para obter mais informações, consulte [API de REST do catálogo de dados do Azure](https://docs.microsoft.com/rest/api/datacatalog/).

## <a name="how-do-i-restrict-the-visibility-of-registered-data-assets-so-that-only-certain-people-can-discover-them"></a>Como restringir a visibilidade dos recursos de dados registados, para que apenas determinadas pessoas podem detetá-los?
Selecione os recursos de dados no catálogo de dados e, em seguida, clique o **obter propriedade** botão. Os proprietários de recursos de dados no catálogo de dados podem alterar as definições de visibilidade para optar por permitir que todos os utilizadores detetar os recursos pertencentes à empresa ou restringir a visibilidade para utilizadores específicos. Para obter mais informações, consulte [gerir recursos de dados no catálogo de dados do Azure](data-catalog-how-to-manage.md).

## <a name="how-do-i-update-the-registration-for-a-data-asset-so-that-changes-in-the-data-source-are-reflected-in-the-catalog"></a>Como atualizar o registo para um recurso de dados para que as alterações da origem de dados são refletidas no catálogo?
Para atualizar os metadados para recursos de dados que já estão registados no catálogo, basta volte a registar a origem de dados que contém os recursos. As alterações da origem de dados, tais como colunas a ser adicionadas ou removidas de tabelas ou vistas, são atualizadas no catálogo, mas qualquer anotações fornecidas pelos utilizadores são retidas.

## <a name="my-question-isnt-answered-here-where-can-i-go-for-answers"></a>A minha pergunta não é atendida aqui. Onde ir para respostas
Vá para o [fórum do catálogo de dados do Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409). Perguntas mais frequentes existe irão orientar os respetivos aqui.
