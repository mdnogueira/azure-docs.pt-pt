---
title: "Guia de criação de um serviço de dados para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implementar um serviço de dados para comprar no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 107baab2-5828-4158-abdf-59a580c80d37
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 8ff76ea21ba684ae2a2afcb74d66b4912d7be053
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Noções sobre o esquema de nós para o mapeamento de um serviço web existente para OData através de CSDL
> [!IMPORTANT]
> **Neste momento, estamos já não integração qualquer nova editores de serviço de dados. Novo dataservices não irá obter aprovada para listagem.** Se tiver uma aplicação de negócio de SaaS que pretende publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço do programador que pretende publicar no Azure Marketplace, pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
>
>

Este documento vai ajudá-lo a clarificar a estrutura do nó para o mapeamento de um protocolo OData para CSDL. É importante ter em atenção que a estrutura do nó está bem formado XML. Por isso, esquema de raiz, principais e subordinados é aplicável ao conceber o mapeamento de OData.

## <a name="ignored-elements"></a>Elementos ignorados
Seguem-se os elementos CSDL (nós XML) alto nível que não vai ser utilizada pelo back-end do Azure Marketplace durante a importação dos metadados do serviço web. Estes podem estar presentes, mas serão ignoradas.

| Elemento | Âmbito |
| --- | --- |
| Utilizando o elemento |O nó, sub nós e todos os atributos |
| Elemento de documentação |O nó, sub nós e todos os atributos |
| ComplexType |O nó, sub nós e todos os atributos |
| Elemento de associação |O nó, sub nós e todos os atributos |
| Propriedade expandida |O nó, sub nós e todos os atributos |
| EntityContainer |Os seguintes atributos são ignorados: *expande* e *AssociationSet* |
| Esquema |Os seguintes atributos são ignorados: *espaço de nomes* |
| FunctionImport |Os seguintes atributos são ignorados: *modo* (o valor predefinido de ln é assumido) |
| EntityType |Os seguintes nós sub são ignorados: *chave* e *PropertyRef* |

O seguinte descreve as alterações (adicionado e ignorado elementos) para os vários nós de CSDL XML em detalhe.

## <a name="functionimport-node"></a>FunctionImport nó
Um nó de FunctionImport representa um URL (ponto de entrada) que expõe um serviço para o utilizador final. O nó permite que descrevem como o URL é resolvido, os parâmetros estão disponíveis para o utilizador final e como estes parâmetros são fornecidos.

Detalhes sobre este nó encontram-se em [aqui][MSDNFunctionImportLink](https://msdn.microsoft.com/library/cc716710.aspx)

Seguem-se os atributos adicionais (ou adições aos atributos) que estão expostos ao nó FunctionImport:

**d:BaseUri** -modelo o URI para o recurso REST está exposto no Marketplace. Marketplace utiliza o modelo para criar consultas contra o serviço web REST. O modelo URI contém os marcadores de posição para os parâmetros no formato {parameterName}, onde o parameterName é o nome do parâmetro. Ex. apiVersion = {apiVersion}.
São permitidos parâmetros a aparecer como parâmetros URI ou como parte do caminho URI. No caso do aspecto do caminho são sempre obrigatórios (não podem ser marcados como anulável). *Exemplo:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nome** -o nome da função importado.  Não pode ser o mesmo que outros nomes definidos no CSDL.  Ex. Nome = "GetModelUsageFile"

**EntitySet** *(opcional)* - se a função devolve uma coleção de tipos de entidade, o valor da **EntitySet** tem de ser a entidade definida para que a coleção pertence. Caso contrário, o **EntitySet** atributo não podem ser utilizado. *Exemplo:*`EntitySet="GetUsageStatisticsEntitySet"`

**ReturnType** *(opcional)* -Especifica o tipo de elementos devolvidos pelo URI.  Não utilize este atributo se a função devolve um valor. Seguem-se os tipos suportados:

* **Coleção (<Entity type name>)**: Especifica uma coleção de tipos de entidade definido. O nome está presente no atributo de nome do nó de EntityType. Um exemplo é coleção (WXC. HourlyResult).
* **Não processado (<mime type>)**: Especifica um documento/blob não processado que é devolvido para o utilizador. Um exemplo é Raw(image/jpeg) outros exemplos:

  * ReturnType="Raw(text/plain)"
  * ReturnType = "coleção (sage. DeleteAllUsageFilesEntity) "*

**d:paging** -Especifica o modo como a paginação é processada pelo recurso REST. Os valores de parâmetros utilizados dentro da Chaveta braches, por exemplo, página = {$page} & itemsperpage = {$size} as opções disponíveis são:

* **Nenhuma:** a paginação não está disponível
* **Ignorar:** paginação é expresso através de uma lógica "Ignorar" e "tomar" (superior). Ignorar salta sobre elementos M e execute, em seguida, devolve os elementos de N seguintes. O valor do parâmetro: $skip
* **Tome:** tirar devolve os elementos de N seguintes. O valor do parâmetro: $take
* **PageSize:** paginação é expresso através de um tamanho (itens por página) e página lógica. Página representa a página atual que é devolvida. O valor do parâmetro: $page
* **Tamanho:** tamanho representa o número de itens devolvidos para cada página. O valor do parâmetro: $size

**d:AllowedHttpMethods** *(opcional)* -Especifica o verbo é processado pelo recurso REST. Além disso, restringe o verbo aceite para o valor especificado.  Predefinição = POST.  *Exemplo:* `d:AllowedHttpMethods="GET"` as opções disponíveis são:

* **GET:** normalmente utilizado para devolver dados
* **POST:** normalmente utilizadas para inserir novos dados
* **PUT:** normalmente utilizadas para atualizar os dados
* **ELIMINAÇÃO:** utilizada para eliminar dados

Nós subordinados adicionais (não abrangidos pela documentação de CSDL) no nó FunctionImport são:

**d:RequestBody** *(opcional)* -corpo do pedido é utilizado para indicar que o pedido espera um corpo para serem enviados. Os parâmetros podem ser especificados no corpo do pedido. Estes são expressas dentro de chavetas, por exemplo, {parameterName}. Estes parâmetros são mapeados da entrada de utilizador para o corpo a que é transferido para o serviço do fornecedor de conteúdos. O elemento de requestBody tem um atributo com o nome httpMethod. O atributo permite que os dois valores:

* **POST:** utilizado se o pedido for um HTTP POST
* **GET:** utilizado se o pedido for um HTTP GET

    Exemplo:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** e **d:Namespace** -este nó descreve os espaços de nomes que estão definidos no XML devolvido pela função de importação (ponto final URI). O XML devolvido pelo serviço de back-end pode conter qualquer número de espaços de nomes para diferenciar o conteúdo que é devolvido. **Todos estes espaços de nomes, se utilizados d:Map ou d:Match consultas XPath têm de estar listada.** O nó de d:Namespaces contém uma conjunto/lista de nós de d:Namespace. Cada uma delas apresenta uma lista de um espaço de nomes utilizado na resposta do serviço de back-end. Seguem-se o atributo do nó d:Namespace:

* **d:prefix:** o prefixo do espaço de nomes, como mostrado nos resultados XML devolvidos pelo serviço, por exemplo, f:FirstName, f:LastName, onde f é o prefixo.
* **d:URI:** URI completo do espaço utilizado no documento de resultado. Representa o valor que o prefixo é resolvido para um em tempo de execução.

**d:ErrorHandling** *(opcional)* -este nó contém as condições para processamento de erros. Cada uma das condições é validada com o resultado devolvido pelo serviço do fornecedor de conteúdos. Se o código de erro HTTP proposto corresponde a uma condição de uma mensagem de erro é devolvida para o utilizador final.

**d:ErrorHandling** *(opcional)* e **d:Condition** *(opcional)* -um nó de condição contém uma condição que é verificada o resultado devolvido pelo serviço do fornecedor de conteúdos. Seguem-se a **necessário** atributos:

* **d:Match:** XPath uma expressão que valida se um determinado nó/valor está presente no fornecedor de conteúdo de saída do XML. O XPath é executado relativamente a saída e deverá devolver VERDADEIRO se a condição for uma correspondência ou false caso contrário.
* **d:HttpStatusCode:** corresponde ao código de estado de HTTP a que deve ser devolvido por Marketplace no caso da condição. Marketplace signalizes erros para o utilizador através de códigos de estado HTTP. Uma lista de códigos de estado HTTP estão disponíveis em http://en.wikipedia.org/wiki/HTTP_status_code
* **d:ErrorMessage:** a mensagem de erro é devolvida – com o código de estado HTTP – para o utilizador final. Deve ser uma mensagem de erro amigável que não contém qualquer segredos.

**d:Title** *(opcional)* -permite que descrevem o título da função. O valor para o título é proveniente de

* O atributo de mapa opcional (um xpath) que especifica onde encontrar o título na resposta devolvida do pedido de serviço.
* - Ou - o título especificado como valor do nó.

**d:Rights** *(opcional)* -os direitos (por exemplo, direitos de autor) associados com a função. O valor para os direitos for proveniente de:

* O atributo de mapa opcional (um xpath) que especifica onde encontrar os direitos na resposta devolvida pelo pedido de serviço.
* - Ou - os direitos especificados como valor do nó.

**d:Description** *(opcional)* - uma descrição para a função de curta. O valor para a descrição é proveniente de

* O atributo de mapa opcional (um xpath) que especifica onde encontrar a descrição na resposta devolvida do pedido de serviço.
* - Ou -descrição especificado como valor do nó.

**d:EmitSelfLink** - *consulte acima exemplo "FunctionImport para paginação através de dados devolvidos"*

**d:EncodeParameterValue** -extensão opcional para OData

**d:QueryResourceCost** -extensão opcional para OData

**d:Map** -extensão opcional para OData

**d:Headers** -extensão opcional para OData

**d:Headers** -extensão opcional para OData

**d:value** -extensão opcional para OData

**d:HttpStatusCode** -extensão opcional para OData

**d:ErrorMessage** -extensão opcional para OData

## <a name="parameter-node"></a>Nó de parâmetro
Este nó representa um parâmetro que é exposto como parte do modelo de URI / corpo que foi especificado no nó FunctionImport do pedido.

Uma página do documento muito úteis detalhes sobre o nó "Elemento de parâmetro" encontra-se em [aqui](http://msdn.microsoft.com/library/ee473431.aspx) (utilize o **versão outras** pendente para selecionar uma versão diferente, se for necessário para ver a documentação). *Exemplo:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Atributo de parâmetro | É necessário | Valor |
| --- | --- | --- |
| Nome |Sim |O nome do parâmetro. Caso confidencial!  Corresponderem do BaseUri. **Exemplo:**`<Property Name="IsDormant" Type="Byte" />` |
| Tipo |Sim |O tipo de parâmetro. O valor tem de ser um **EDMSimpleType** ou um tipo complexo que se encontra no âmbito do modelo. Para obter mais informações, consulte "6 suportado. o parâmetro/propriedade tipos".  (Sensível às maiúsculas e! Primeiro carat letras maiúsculas, rest são minúsculas.)  Consulte também, [Conceptual modelo tipos (CSDL)][MSDNParameterLink](http://msdn.microsoft.com/library/bb399548.aspx). **Exemplo:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Modo |Não |**No**, fora ou InOut, dependendo se o parâmetro é uma entrada, saída ou parâmetro de entrada/saída. (Apenas "em" está disponível no Azure Marketplace.) **Exemplo:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| maxLength |Não |O comprimento máximo permitido do parâmetro. **Exemplo:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| precisão |Não |A precisão do parâmetro. **Exemplo:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Escala |Não |A escala do parâmetro. **Exemplo:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

Seguem-se os atributos que foram adicionados a especificação de CSDL:

| Atributo de parâmetro | Descrição |
| --- | --- |
| **d:RegEx** *(opcional)* |Uma instrução de regex utilizada para validar o valor de entrada para o parâmetro. Se o valor de entrada não corresponde à instrução o valor é rejeitado. Isto permite também especificar um conjunto de valores possíveis, por exemplo, ^ [0-9] +? $ para permitir apenas números. **Exemplo:** ' < nome do parâmetro = "name" modo = "Em" do tipo = "Cadeia" d: Nullable = "false" d:Regex = "^ [a-zA-Z] * $" d:Description = "um nome que não pode conter espaços ou carateres não ingleses de não alfanuméricos" d:SampleValues = "George |
| **d:Enum** *(opcional)* |Lista de valores válidos para o parâmetro de valores separados por um pipe. O tipo de valores tem de corresponder ao tipo do parâmetro definido. Exemplo: ' inglês |
| **d: Nullable** *(opcional)* |Permite definir se um parâmetro pode ser nulo. A predefinição é: true. No entanto, os parâmetros que são expostos como parte do caminho no modelo de URI não podem ser nulos. Quando o atributo é definido como falso para estes parâmetros – a intervenção do utilizador seja substituída. **Exemplo:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(opcional)* |Um valor de exemplo a apresentar como uma nota para o cliente na IU.  É possível adicionar vários valores, utilizando uma lista de pipe separado, ou seja, ' um |

## <a name="entitytype-node"></a>Nó de EntityType
Este nó representa um dos tipos que são devolvidos do Marketplace para o utilizador final. Também contém o mapeamento de saída que é devolvido pelo serviço do fornecedor de conteúdos para os valores que são devolvidos para o utilizador final.

Detalhes sobre este nó encontram-se em [aqui](http://msdn.microsoft.com/library/bb399206.aspx) (utilize o **versão outras** pendente para selecionar uma versão diferente, se for necessário para ver a documentação.)

| Nome do atributo | É necessário | Valor |
| --- | --- | --- |
| Nome |Sim |O nome do tipo de entidade. **Exemplo:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| BaseType |Não |O nome de outro tipo de entidade é o tipo de base do tipo de entidade que está a ser definido. **Exemplo:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Seguem-se os atributos que foram adicionados a especificação de CSDL:

**d:Map** -expressão de XPath um executadas contra o resultado de serviço. Pressuposto aqui é que a saída de serviço contém um conjunto de elementos repetir, como um ATOM feed onde há um conjunto de nós de entrada que repita. Cada um destes repetidos nós contém um registo. O XPath, em seguida, é especificado para apontar para o nó repetido individuais no resultado de serviço ao fornecedor de conteúdos que contém os valores para um registo individual. Exemplo de saída do serviço:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

A expressão XPath seria possível /foo/barra porque cada da barra do nó é o nó repetido na saída e contém o conteúdo propriamente dito que é devolvido para o utilizador final.

**Chave** -este atributo é ignorado por Marketplace. REST baseado em serviços web, em geral, não exponha uma chave primária.

## <a name="property-node"></a>Nó de propriedade
Este nó contém uma propriedade do registo.

Detalhes sobre este nó encontram-se em [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (utilize o **versão outras** pendente para selecionar uma versão diferente, se for necessário para ver a documentação.) *Exemplo:*`<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"     Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| AttributeName | Necessário | Valor |
| --- | --- | --- |
| Nome |Sim |O nome da propriedade. |
| Tipo |Sim |O tipo do valor de propriedade. O tipo de valor de propriedade tem de ser um **EDMSimpleType** ou um tipo complexo (indicado por um nome completamente qualificado) que se encontra no âmbito do modelo. Para obter mais informações, consulte tipos de modelo Conceptual (CSDL). |
| Que pode ser nulo |Não |**Verdadeiro** (o valor predefinido) ou **falso** dependendo se a propriedade pode ter um valor nulo. Nota: na versão CSDL indicado pelo [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) espaço de nomes, uma propriedade de tipo complexo tem de ter Nullable = "False". |
| DefaultValue |Não |O valor predefinido da propriedade. |
| maxLength |Não |O comprimento máximo do valor da propriedade. |
| Multistring |Não |**Verdadeiro** ou **falso** dependendo se o valor da propriedade será armazenado como uma cadeia de comprimento fiexed. |
| precisão |Não |Refere-se ao número máximo de dígitos para manter o valor numérico. |
| Escala |Não |Número máximo de casas decimais a manter no valor numérico. |
| Unicode |Não |**Verdadeiro** ou **falso** dependendo se o valor da propriedade ser armazenado como uma cadeia Unicode. |
| Agrupamento |Não |Uma cadeia que especifica a sequência collating a ser utilizado na origem de dados. |
| ConcurrencyMode |Não |**Nenhum** (o valor predefinido) ou **Fixed**. Se o valor estiver definido como **Fixed**, será utilizado o valor da propriedade no simultaneidade otimista verificações. |

Seguem-se os atributos adicionais que foram adicionados a especificação de CSDL:

**d:Map** -expressão XPath executada contra o serviço de saída e extrai uma propriedade de saída. O XPath especificado é relativo ao nó repetido que foi selecionado XPath o nó de EntityType. Também é possível especificar um XPath absoluto para permitir, incluindo um recurso estático em cada um de nós de saída, como por exemplo uma instrução de direitos de autor apenas encontra-se uma vez no serviço original de saída, mas deve estar presente em cada uma das linhas no resultado de OData. Exemplo do serviço:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

A expressão XPath aqui seria ./bar/baz0 para obter o nó de baz0 do serviço ao fornecedor de conteúdos.

**d:CharMaxLength** -para o tipo de cadeia, pode especificar o comprimento máximo. Consulte o exemplo de DataService CSDL

**d:IsPrimaryKey** -indica se a coluna chave primária na tabela/vista. Consulte DataService CSDL exemplo.

**d:isExposed** -determina se o esquema da tabela está exposto (geralmente true). Consulte o exemplo de DataService CSDL

**d:IsView** *(opcional)* - true se tem como base uma vista em vez de uma tabela.  Consulte o exemplo de DataService CSDL

**d:Tableschema** -consulte o exemplo de DataService CSDL

**d:ColumnName** -é o nome da coluna em tabela/vista.  Consulte o exemplo de DataService CSDL

**d:IsReturned** -é o valor boleano que determina se o serviço expõe este valor para o cliente.  Consulte o exemplo de DataService CSDL

**d:IsQueryable** -é o valor boleano que determina se a coluna pode ser utilizada numa consulta de base de dados.   Consulte o exemplo de DataService CSDL

**d:OrdinalPosition** -está numérica posição a coluna do aspeto, x, na tabela ou vista, onde x é entre 1 e o número de colunas na tabela.  Consulte o exemplo de DataService CSDL

**d:DatabaseDataType** -é o tipo de dados da coluna na base de dados, ou seja, o tipo de dados SQL. Consulte o exemplo de DataService CSDL

## <a name="supported-parametersproperty-types"></a>Tipos de parâmetros suportados/propriedade
Seguem-se os tipos suportados para os parâmetros e as propriedades. (Caso confidencial)

| Tipos primitivos | Descrição |
| --- | --- |
| Valor nulo |Representa a ausência de um valor |
| Valor booleano |Representa o conceito matemática de lógica de valor de binário |
| Bytes |Valor de número inteiro sem sinal de 8 bits |
| DateTime |Representa a data e hora com valores entre 12:00:00 1 de Janeiro a.d. de 1753 através de 11:59:59 P.M, Dezembro de 9999 A.D. |
| Decimal |Representa valores numéricos com fixo precision e scale. Este tipo pode descrevem um valor numérico entre 10 negativo ^ 255 + 1 para 10 positivo ^ 255 -1 |
| duplo |Representa um número com precisão de 15 dígitos que pode representar os valores de intervalo aproximado de ± 2.23e-308 através de ± 1.79e de ponto flutuante +308. **Utilizar Decimal devido a um problema Exel exportação** |
| Único |Representa um número com precisão 7 dígitos que pode representar os valores de intervalo aproximado de ± 1.18e-38 através de ± 3.40e de ponto flutuante +38 |
| GUID |Representa um valor de identificador exclusivo do 16 bytes (128 bits) |
| Int16 |Representa um valor de número inteiro de 16 bits com sinal |
| Int32 |Representa um valor de número inteiro com sinal de 32 bits |
| Int64 |Representa um valor de número inteiro com sinal de 64 bits |
| Cadeia |Representa os dados de carateres ou variável-comprimento fixo |

## <a name="see-also"></a>Veja Também
* Se estiver interessado em compreender o processo de mapeamento de OData e o objetivo geral, leia este artigo [mapeamento de OData do serviço de dados](marketplace-publishing-data-service-creation-odata-mapping.md) para rever as definições, estruturas e instruções.
* Se estiver interessado em rever exemplos, leia este artigo [dados serviço OData mapeamento exemplos](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver o código de exemplo e compreender a sintaxe de código e o contexto.
* Para voltar para o caminho previsto para a publicação de um serviço de dados no Azure Marketplace, leia este artigo [dados serviço de publicação guia](marketplace-publishing-data-service-creation.md).
