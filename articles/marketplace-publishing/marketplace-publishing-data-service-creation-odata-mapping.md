---
title: "Guia de criação de um serviço de dados para o Marketplace | Microsoft Docs"
description: "Instruções detalhadas sobre como criar, certificar e implementar um serviço de dados para comprar no Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3a632825-db5b-49ec-98bd-887138798bc4
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: a853b4dbd1952ba4ea8ee68ea3ca98f588bb71a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>O mapeamento de um serviço web existente para OData através de CSDL
> [!IMPORTANT]
> **Neste momento, estamos já não integração qualquer nova editores de serviço de dados. Novo dataservices não irá obter aprovada para listagem.** Se tiver uma aplicação de negócio de SaaS que pretende publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço do programador que pretende publicar no Azure Marketplace, pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Este artigo fornece uma descrição geral sobre como utilizar um CSDL para mapear um serviço existente para um serviço compatível de OData. Explica como criar o documento de mapeamento (CSDL) que transforma o pedido de entrada do cliente através de uma chamada de serviço e de saída (dados) de volta para o cliente através de OData compatível feed. Microsoft Azure Marketplace expõe serviços para os utilizadores finais através do protocolo OData. Serviços que são expostos pelos fornecedores de conteúdo (os proprietários de dados) são expostos numa variedade de formulários, tais como REST, SOAP, etc.

## <a name="what-is-a-csdl-and-its-structure"></a>O que é um CSDL e a respetiva estrutura?
Um CSDL (linguagem de definição de esquema Concetual) é uma especificação de definir como descrever o serviço web ou o serviço de base de dados de comuns verbiage XML para o Azure Marketplace.

Descrição geral Simple do **fluxo do pedido:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

O **fluxo de dados** é na direção oposta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figura 1** diagramas como um cliente obter dados de um fornecedor de conteúdo (o serviço) acedendo através do Azure Marketplace.  O CSDL é utilizado pelo componente de mapeamento/transformação para processar o pedido e transmitir dados entre os serviços de ao fornecedor de conteúdos e o cliente efetuou.

*Figura 1: Fluxo detalhado de cliente pedir ao fornecedor de conteúdos através do Azure Marketplace*

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Para em segundo plano em Atom do Atom Pub e o protocolo OData na qual criar as extensões do Azure Marketplace, reveja: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Excerpt from above ligação: *"é o objetivo do protocolo Open Data (hereafter referido como OData) fornecer um protocolo baseado em REST para operações CRUD estilo (criar, ler, atualizar e eliminar) relativamente aos recursos expostas como serviços de dados. "Serviço de dados" é um ponto final onde há dados expostos a partir de uma ou mais "coleções" cada com zero ou mais "entradas", que consistem em escreveu pares nome-valor. OData é publicado pela Microsoft em normas OASIS (organização para o Advancement do Structured Information Standards) para que qualquer pessoa que pretende pode criar servidores, clientes ou ferramentas sem restrições ou royalties."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Três importantes que têm de ser definida pelo CSDL são:
* O **endpoint** do serviço fornecedor do Web endereço (URI) do serviço
* O **parâmetros de dados** que está a ser transmitido como entrada para o fornecedor de serviços as definições de parâmetros que está a ser enviados ao serviço do fornecedor de conteúdos para o tipo de dados.
* **Esquema** dos dados a ser devolvidos para o serviço de pedir o esquema dos dados a ser entregues pelo serviço do fornecedor de conteúdos, incluindo os tipos de contentor, coleções/tabelas, colunas/variáveis e dados.

O diagrama seguinte mostra uma descrição geral do fluxo de onde o cliente introduz a instrução de OData (chamada para o serviço web do fornecedor de conteúdo) para obter os dados/resultados novamente.

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>passos:
1. O cliente envia o pedido através de chamada de serviço concluída com parâmetros de entrada definidos no ficheiro XML para o Azure Marketplace
2. CSDL é utilizado para validar a chamada de serviço.
   * O serviço formatado chamar é enviada para o serviço de fornecedores de conteúdo através do Azure Marketplace
3. O serviço Web executa e preforms a ação do verbo de Http (ou seja, obter) são devolvidos os dados no Azure Marketplace, onde os dados solicitados (se aplicável) são expõe no formato XML para o cliente utilizando o mapeamento definido no CSDL.
4. O cliente é enviado os dados (se existirem) no formato XML ou JSON

## <a name="definitions"></a>Definições
### <a name="odata-atom-pub"></a>Do OData ATOM pub
Definir uma extensão do ATOM pub onde cada entrada representa uma linha de um resultado. A parte do conteúdo da entrada foi melhorada para conter os valores de linha – como pares de valor de chave. Encontrar mais informações aqui: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - linguagem de definição de esquema Concetual
Permite definir funções (SPROCs) e as entidades que estão expostas através de uma base de dados. Obter mais informações, localizadas aqui: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [!TIP]
> Clique em de **outras versões** pendente e selecione uma versão, se não vir o artigo.
> 
> 

### <a name="edm---entry-data-model"></a>EDM - modelo de dados de entrada
* Descrição geral: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink]

[OverviewLink]:http://msdn.microsoft.com/library/vstudio/ee382825(v=vs.100).aspx
* Pré-visualização: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink]

[PreviewLink]:http://msdn.microsoft.com/library/aa697428(v=vs.80).aspx
* Tipos de dados: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink]

[DataTypesLink]:http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx

Fazer uma cópia de seguinte mostra à esquerda de detalhado para a direita fluir de onde o cliente introduz a instrução de OData (chamada para o serviço web do fornecedor de conteúdo) para obter os dados/resultados:

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)

## <a name="csdl-basics"></a>Noções básicas CSDL
Um CSDL (linguagem de definição de esquema Concetual) é uma especificação de definir como descrever o serviço web ou o serviço de base de dados de comuns verbiage XML para o Azure Marketplace. CSDL descreve o críticos pieces que **possibilita a transmissão de dados da origem de dados no Azure Marketplace.** As partes principais são descritas aqui:

* Informações de interface que descrevem todas as funções publicamente disponíveis (FunctionImport nó)
* Informações de tipo de dados para todas as mensagens requests(input) e responses(outputs) de mensagem (EntitySet/EntityContainer/EntityType nós)
* Enlace informações sobre o protocolo de transporte para ser utilizado (cabeçalho nó)
* Informações de endereço para localizar o serviço especificado (BaseURI atributo)

Um nutshell, o CSDL representa um contrato e idiomas-independentes da plataforma entre o requerente de serviço e o fornecedor de serviços. Utilizar o CSDL, um cliente pode localizar um serviço de base de dados/serviço web e invocar qualquer uma das respetivas funções publicamente disponíveis.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Relacionado com um CSDL a uma base de dados ou uma coleção
**A especificação de CSDL**

CSDL é a gramática XML para que descrevem um serviço web. A especificação do próprio está dividida em 4 elementos principais: EntitySet, FunctionImport; Espaço de nomes e EntityType.

Para facilitar esta abstração compreender permite referem-se um CSDL a uma tabela.

Lembre-se;

  CSDL representa um contrato e idiomas-independentes da plataforma entre o **requerente serviço** e **fornecedor de serviços**. Utilizar CSDL, um **cliente** possam localizar um **serviço de base de dados/serviço web** e invocar qualquer um dos respetivos publicamente disponíveis **funções.**

Para um serviço de dados de quatro partes de um CSDL podem considerar em termos de uma base de dados, tabela, coluna e de armazenamento.

Relacionadas com estes da seguinte forma para um serviço de dados:

* EntityContainer ~ = base de dados
* EntitySet ~ = tabela
* EntityType ~ = colunas
* FunctionImport ~ = procedimento armazenado

**Verbos HTTP permitidos**

* GET-devolve valores da base de dados (devolve um conjunto)
* POST – utilizado para transferir dados e valores de retorno opcionais da base de dados (criar uma nova entrada de coleção, id de retorno/URI)
* DELETE-Elimina dados da base de dados (elimina uma coleção)
* COLOCAR – atualizar os dados para uma base de dados (substituir uma coleção ou crie um)

## <a name="metadatamapping-document"></a>Documento de metadados/mapeamento
O documento de metadados/mapeamento é utilizado para mapear os serviços web existente de um fornecedor de conteúdos, de modo a que pode ser exposta como um serviço web OData pelo sistema Azure Marketplace. Se baseia no CSDL e implementa algumas extensões para CSDL para acomodar as necessidades de REST com base em serviços web expostos através do Azure Marketplace. As extensões que se encontram no [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) espaço de nomes.

Segue-se um exemplo do CSDL: (copiar e colar o exemplo CSDL para um editor de XML e altere para corresponder ao seu serviço abaixo.  Em seguida, cole CSDL mapeamento em DataService separador ao criar o serviço no [Portal de publicação do Azure Marketplace](https://publish.windowsazure.com)).

**Termos de licenciamento:** relacionadas com o CSDL termos para o [Portal publicação](https://publish.windowsazure.com) termos de IU (PPUI).

* Oferta relacionada com "Title" o PPUI MyWebOffer
* Aminhaempresa no PPUI está relacionada com **nome a apresentar do publicador** no [Microsoft Developer Center](http://dev.windows.com/registration?accountprogram=azure) IU
* A API está relacionada com um Web ou serviço de dados (um plano de PPUI)

**Hierarquia:** Offer(s) que têm Plan(s) é proprietário de uma empresa (fornecedor de conteúdos), nomeadamente service(s), que linha cópias de segurança com uma API.

### <a name="webservice-csdl-example"></a>Exemplo de WebService CSDL
Liga a um serviço que está a expor um ponto final da aplicação web (por exemplo, uma aplicação c#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID"    d:IsPrimaryKey="True" Type="Int32"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount"    Type="Double"    Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"    Type="String"    Nullable="false" d:Map="./City"/>
        <Property Name="State"    Type="String"    Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime"    Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [!TIP]
> Ver mais exemplos de serviço Web de CSDL no artigo [exemplos de mapeamento existente web do serviço OData através de CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)
> 
> 

### <a name="dataservice-csdl-example"></a>Exemplo de DataService CSDL
Estabelece ligação a um serviço que está a expor uma base de dados tabela ou vista como um ponto final de exemplo abaixo mostra duas APIs para a base de dados com base em CSDL de API (pode utilizar vistas em vez de tabelas).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Veja Também
* Se estiver interessado em aprender e compreender os nós específicos e os respetivos parâmetros, leia este artigo [dados serviço OData mapeamento de nós](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definições e uma explicação, exemplos e contexto de cenários de utilização.
* Se estiver interessado em rever exemplos, leia este artigo [dados serviço OData mapeamento exemplos](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver o código de exemplo e compreender a sintaxe de código e o contexto.
* Para voltar para o caminho previsto para a publicação de um serviço de dados no Azure Marketplace, leia este artigo [dados serviço de publicação guia](marketplace-publishing-data-service-creation.md).

