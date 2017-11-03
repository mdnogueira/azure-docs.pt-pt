---
title: Como anotar origens de dados | Microsoft Docs
description: "Artigo procedimentos realce como anotar recursos de dados no catálogo de dados do Azure, incluindo nomes amigáveis, etiquetas, descrições e especialistas."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 5a7e6bb2-863c-4eca-b614-1c814920d9ed
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: 4518fc126c717cc79ca7950c0b1ddcd9f1d8c7d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-annotate-data-sources"></a>Como anotar origens de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço em nuvem completamente gerido que funciona como um sistema de registo e de deteção de origens de dados empresariais. Por outras palavras, o catálogo de dados é tudo sobre ajudando as pessoas detetar, compreender e utilizar origens de dados e organizações de ajuda para rentabilizar o valor a partir dos respetivos dados existentes. Quando uma origem de dados é registada no catálogo de dados, os metadados é copiado e o serviço indexa, mas o bloco não termina não existe. Catálogo de dados permite que os utilizadores forneçam os seus próprios metadados descritivos – como etiquetas – para complementar os metadados extraídos da origem de dados e para a origem de dados mais compreensível para as pessoas e descrições.

## <a name="annotation-and-crowdsourcing"></a>Anotação e crowdsourcing
Todos têm um opinião. E é uma boa coisa.
Catálogo de dados reconhece que os utilizadores diferentes têm diferentes perspetivas em origens de dados empresariais e cada um destes perspetivas que pode ser útil. Considere o seguinte cenário:

* O administrador de sistema sabe o contrato de nível de serviço para os servidores ou serviços que alojam a origem de dados.
* O administrador da base de dados sabe o agendamento de cópia de segurança para cada base de dados e o processamento de ETL permitido do windows.
* O proprietário do sistema sabe o processo para os utilizadores pedir acesso à origem de dados.
* O steward dados sabe como mapeiam os atributos da origem de dados e recursos para o modelo de dados da empresa.
* O analista sabe como os dados são utilizados no contexto dos processos de negócio que suporta.

Cada um destes perspetivas é útil e o catálogo de dados utiliza uma abordagem de crowdsourcing aos metadados permite que cada um deles ser capturadas e utilizadas para fornecer uma visão geral de origens de dados registados. Utilizar o portal do catálogo de dados, cada utilizador pode adicionar e editar as suas própria anotações, enquanto a capacidade de ver anotações fornecidas por outros utilizadores.

## <a name="different-types-of-annotations"></a>Diferentes tipos de anotações
Catálogo de dados suporta os seguintes tipos de anotações:

| Anotação | Notas |
| --- | --- |
| Nome amigável |Nomes amigáveis podem ser fornecidos ao nível do recurso de dados, para tornar mais facilmente compreendidos os recursos de dados. Nomes amigáveis são mais úteis quando o nome do objeto subjacente é crípticas, abreviado ou caso contrário não significativo para os utilizadores. |
| Descrição |Descrições podem ser fornecidas no recurso de dados e o atributo / níveis de coluna. Descrições são anotações de texto abreviado de forma gratuita que descrevem o utilizador perspetiva no recurso de dados ou a sua utilização. |
| Etiquetas (etiquetas de utilizador) |As etiquetas podem ser fornecidas no recurso de dados e o atributo / níveis de coluna. Etiquetas de utilizador são definidos pelo utilizador etiquetas que podem ser utilizadas para categorizar os recursos de dados ou atributos. |
| Etiquetas (etiquetas de glossário) |As etiquetas podem ser fornecidas no recurso de dados e o atributo / níveis de coluna. Etiquetas de glossário são os termos do glossário definidas centralmente que podem ser utilizados para categorizar os recursos de dados ou atributos utilizando uma taxonomia comercial comuns. Para mais informações, veja [Como configurar o Glossário Comercial para Etiquetagem Regida](data-catalog-how-to-business-glossary.md) |
| Especialistas |Especialistas podem ser fornecidas ao nível do elemento de dados. Especialistas identificar utilizadores ou grupos com perspetivas especialistas nos dados e podem servir como pontos de contacto para utilizadores detetar as origens de dados registados e tiver questões que não são respondidos pelas anotações existentes. |
| Pedir acesso |Informações de pedidos de acesso podem ser fornecidas ao nível do elemento de dados. Esta informação é para os utilizadores que detetar uma origem de dados que ainda não ter permissões para aceder. Os utilizadores podem introduzir o endereço de e-mail do utilizador ou grupo que concede acesso, o URL do processo ou ferramenta que os utilizadores têm de ter acesso, ou podem introduzir o processo de si próprio como texto. |
| Documentação |Documentação pode ser fornecida ao nível do elemento de dados. Documentação do recurso é informações de texto formatado, que podem incluir ligações e imagens e que pode fornecer as informações que não é transmitidas através de etiquetas e descrições. |

## <a name="annotating-multiple-assets"></a>Anotar vários recursos
Ao selecionar vários recursos de dados no portal do catálogo de dados, os utilizadores podem anotar todos os recursos selecionados numa única operação. Anotações serão aplicada a todos os elementos selecionados, facilitando a selecionar e fornecer uma descrição consistente e conjuntos de etiquetas e especialistas para recursos de dados relacionados.

> [!NOTE]
> As etiquetas e especialistas também podem ser fornecidas quando a ferramenta de registo de origem ao registar recursos de dados com os dados de catálogo de dados.
>
>

Quando selecionar várias tabelas e vistas, as colunas que todos os dados ativos têm em comum de selecionado será apresentado no portal do catálogo de dados. Isto permite que os utilizadores forneçam etiquetas e descrições para todas as colunas com o mesmo nome para todos os elementos selecionados.

## <a name="annotations-and-discovery"></a>Deteção e anotações
Tal como os metadados extraídos da origem de dados durante o registo é adicionado para o índice de pesquisa do catálogo de dados, fornecido pelo utilizador metadados também são indexados. Isto significa que não só anotações tornam mais fácil para os utilizadores a compreender os dados que estes detetar, anotações também o tornam mais fácil para os utilizadores detetar os recursos de dados anotado pesquisando com os termos de licenciamento que façam sentido aos mesmos.

## <a name="summary"></a>Resumo
Registar uma origem de dados com o catálogo de dados faz com que os dados detetável por copiar os metadados estruturais e descritivo da origem de dados para o serviço de catálogo. Depois de uma origem de dados foi registada, os utilizadores podem fornecer anotações para facilitar a detetar e compreender a partir de dentro do portal do catálogo de dados.

## <a name="see-also"></a>Consultar também
* [Introdução ao catálogo de dados do Azure](data-catalog-get-started.md) tutorial para obter detalhes passo a passo sobre como anotar origens de dados.
