---
title: "Idioma de várias de pesquisa do Azure | Microsoft Docs"
description: A pesquisa do Azure suporta 56 idiomas, tirar partido dos analisadores de idiomas da tecnologia de Lucene e processamento de linguagem Natural da Microsoft.
services: search
documentationcenter: 
author: yahnoosh
manager: pablocas
editor: 
ms.assetid: 55a00b44-804d-41bb-9c96-e6ea498616f5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: jlembicz
ms.openlocfilehash: dbbab31bac66ce73dbf9883992713a2c16581e19
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-index-for-documents-in-multiple-languages-in-azure-search"></a>Criar um índice para documentos em vários idiomas na Azure Search
> [!div class="op_single_selector"]
>
> * [Portal](search-language-support.md)
> * [REST](https://msdn.microsoft.com/library/azure/dn879793.aspx)
> * [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.analyzername.aspx)
>
>

Unleashing potência de analisadores de idioma é tão fácil como uma propriedade de definição num campo pesquisável na definição do índice. Agora pode efetuar este passo no portal.

Seguem-se capturas de ecrã dos painéis do Portal do Azure para a pesquisa do Azure que permitem aos utilizadores definir um esquema de índice. A partir deste painel, os utilizadores podem criar todos os campos e defina a propriedade de analisador para cada um deles.

> [!IMPORTANT]
> Só é possível definir um analisador de idioma durante a definição de campo, como no quando criar um novo índice partir do zero cópias de segurança ou ao adicionar um novo campo a um índice existente. Certifique-se de que especificar completamente todos os atributos, incluindo o analisador, ao criar o campo. Não será possível editar os atributos ou alterar o tipo de analisador depois de guardar as alterações.
>
>

## <a name="define-a-new-field-definition"></a>Definir uma nova definição de campo
1. Iniciar sessão para o [portal do Azure](https://portal.azure.com) e abrir o painel de serviço do seu serviço de pesquisa.
2. Clique em **Adicionar índice** na barra de comando na parte superior do dashboard do serviço para iniciar um novo índice ou abra um índice existente para definir um analisador em novos campos estiver a adicionar a um índice existente.
3. É apresentado o painel de campos, dando-lhe opções para definir o esquema de índice, incluindo o separador de analisador utilizado para escolher um analisador de idioma.
4. Nos campos, inicie uma definição de campo, fornecendo um nome, escolher o tipo de dados e definir os atributos para marcar o campo como texto completo nos resultados de pesquisa, utilizáveis em estruturas de navegação de Faceta, recuperável, pesquisável ordenável e assim forth.
5. Antes de passar para o campo seguinte, abra o **analisador** separador.

![][1]
*Para selecionar um analisador, clique no separador de analisador no painel campos*

## <a name="choose-an-analyzer"></a>Escolha um analisador
1. Desloque-se para localizar o campo que está a definir.
2. Se ainda não marcado o campo como pesquisável, clique na caixa de verificação agora para marcá-la como **pesquisável**.
3. Clique na área de analisador para apresentar a lista de analisadores disponíveis.
4. Escolha o analisador de que pretende utilizar.

![][2]
*Selecione uma dos analisadores suportados para cada campo*

Por predefinição, todos os campos pesquisáveis utilizam o [Lucene padrão analisador](http://lucene.apache.org/core/4_10_0/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) que desconhece idioma. Para ver a lista completa dos analisadores suportados, consulte [suporte de idiomas da Azure Search](https://msdn.microsoft.com/library/azure/dn879793.aspx).

Depois do analisador de idioma é selecionado para um campo, será utilizado com cada pedido de indexação e de pesquisa para esse campo. Quando uma consulta é emitida contra vários campos utilizando analisadores diferentes, a consulta será processada independentemente pelos analisadores de direita para cada campo.

Muitas aplicações móveis e web servem os utilizadores à volta de todo o mundo através de vários idiomas. É possível definir um índice para um cenário semelhante através da criação de um campo para cada idioma suportado.

![][3]
*Definição de índice com um campo de descrição para cada idioma suportado*

Se o idioma do agente de emissão de uma consulta é conhecido, um pedido de pesquisa pode ser confinado num campo específico utilizando o **searchFields** parâmetro de consulta. A consulta seguinte será emitida apenas relativamente a descrição na Polaco:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=description_pl&api-version=2016-09-01`

Pode consultar o índice no portal, utilizando **Explorador de pesquisa** colar numa consulta semelhante às mostrado acima. Explorador de pesquisa está disponível a partir da barra de comandos, no painel do serviço. Consulte [consultar o índice da Azure Search no portal do](search-explorer.md) para obter mais detalhes.

Por vezes, não é conhecido o idioma do agente de emissão de uma consulta, caso em que a consulta possível emiti-las em relação a todos os campos em simultâneo. Se for necessário, preferência resulta num determinado idioma pode ser definida utilizando [classificação perfis](https://msdn.microsoft.com/library/azure/dn798928.aspx). No exemplo abaixo, correspondências encontradas na descrição em inglês serão classificar superior relativos ao correspondências em polaco e francês:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2016-09-01`

Se tiver um programador de .NET, tenha em atenção que pode configurar analisadores de idioma a utilizar o [SDK .NET da Azure Search](http://www.nuget.org/packages/Microsoft.Azure.Search). A versão mais recente inclui suporte para os Microsoft dos analisadores de idiomas, bem como.

<!-- Image References -->
[1]: ./media/search-language-support/AnalyzerTab.png
[2]: ./media/search-language-support/SelectAnalyzer.png
[3]: ./media/search-language-support/IndexDefinition.png
