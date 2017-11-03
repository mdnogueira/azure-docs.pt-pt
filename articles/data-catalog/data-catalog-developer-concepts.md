---
title: "Conceitos do Programador de catálogo de dados | Microsoft Docs"
description: "Introdução aos conceitos chaves no modelo conceptual do catálogo de dados do Azure, como expostas através da API de REST do catálogo."
services: data-catalog
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
tags: 
ms.assetid: 89de9137-a0a4-40d1-9f8d-625acad31619
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/03/2017
ms.author: spelluru
ms.openlocfilehash: f48eb610b47820e6d7438520a00a5e6dfe879e01
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-data-catalog-developer-concepts"></a>Conceitos do Programador de catálogo de dados do Azure
Microsoft **catálogo de dados do Azure** é um serviço em nuvem completamente gerido que fornece capacidades de deteção de origem de dados e para fazer crowdsourcing de metadados de origem de dados. Os programadores podem utilizar o serviço através das respetivas APIs REST. Compreender os conceitos implementados no serviço é importante para os programadores integrar com êxito **catálogo de dados do Azure**.

## <a name="key-concepts"></a>Conceitos-chave
O **catálogo de dados do Azure** modelo concetual baseia-se em quatro conceitos chave: O **catálogo**, **utilizadores**, **ativos**, e **anotações**.

![Conceito][1]

*Figura 1 - modelo concetual simplificada de catálogo de dados do Azure*

### <a name="catalog"></a>Catálogo
A **catálogo** é o contentor de nível superior para todos os metadados armazena uma organização. Há um **catálogo** permitido por conta do Azure. Catálogos estão associados a uma subscrição do Azure, mas apenas um **catálogo** podem ser criados para qualquer conta do Azure especificada, apesar de uma conta pode ter várias subscrições.

Contém um catálogo **utilizadores** e **ativos**.

### <a name="users"></a>Utilizadores
Os utilizadores são os principais de segurança que têm permissão para efetuar ações (pesquisar o catálogo, adicionar, editar ou remover itens, etc.) no catálogo.

Existem várias funções diferentes que pode ter um utilizador. Para informações sobre as funções, consulte a secção funções e a autorização.

Utilizadores individuais e grupos de segurança podem ser adicionados.

Catálogo de dados do Azure utiliza o Azure Active Directory para gestão de identidades e acesso. Cada utilizador de catálogo tem de ser um membro do Active Directory para a conta.

### <a name="assets"></a>Elementos
A **catálogo** contém recursos de dados. **Ativos** são a unidade de granularidade gerida pelo catálogo.

A granularidade de um ativo varia de acordo com a origem de dados. Para SQL Server ou base de dados Oracle, um recurso pode ser uma tabela ou numa vista. Para SQL Server Analysis Services, um recurso pode ser uma medida, uma dimensão ou um indicador chave de desempenho (KPI). Para SQL Server Reporting Services, um recurso é um relatório.

Um **Asset** é coisa adicionar ou remover um catálogo. Esta é a unidade de resultado voltar de **pesquisa**.

Um **Asset** é efetuada cópia de segurança do respetivo nome, localização e tipo e anotações mais descrevem-lo.

### <a name="annotations"></a>Anotações
As anotações são itens que representam os metadados sobre recursos.

Exemplos de anotações são descrição, etiquetas, esquema, documentação, etc. Uma lista completa dos tipos de recurso e tipos de anotação estão na secção de modelo de objeto de recurso.

## <a name="crowdsourcing-annotations-and-user-perspective-multiplicity-of-opinion"></a>Crowdsourcing anotações e perspetiva do utilizador (multiplicidade da opinião)
Um aspeto chave do catálogo de dados do Azure é como suporta crowdsourcing de metadados no sistema. Como opposed para uma abordagem de wiki – onde há opinião apenas um e os último wins de escritor – o modelo do catálogo de dados do Azure permite que vários opinions TTL lado no sistema.

Esta abordagem reflete o mundo real dos dados empresariais quais diferentes utilizadores podem ter diferentes perspetivas sobre um determinado recurso:

* Um administrador da base de dados pode fornecer informações sobre contratos de nível de serviço ou a janela de processamento disponíveis para operações de ETL em massa
* Um steward de dados pode fornecer informações sobre os processos de negócio aos quais se aplica o elemento ou as classificações que foi aplicada no negócio
* Um analista do departamento financeiro pode fornecer informações sobre como os dados são utilizados durante o fim do período tarefas relatórios

Para suportar este exemplo, cada utilizador, a DBA, steward os dados e o analista, pode adicionar uma descrição para uma única tabela que foi registada no catálogo. Todas as descrições são mantidas no sistema e, no portal do catálogo de dados do Azure são apresentadas todas as descrições.

Este padrão é aplicado a maioria dos itens de modelo de objetos, pelo que os tipos de objeto no JSON payload são, muitas vezes, matrizes de propriedades onde poderá esperar um singleton.

Por exemplo, sob o elemento raiz é uma matriz de objetos de descrição. A propriedade de matriz com o nome "descrições". Um objeto de descrição tem uma propriedade - descrição. O padrão é que cada utilizador que descrição tipos obtém um objeto de descrição criado para o valor fornecido pelo utilizador.

O UX, em seguida, pode escolher como apresentar a combinação. Existem três padrões diferentes para apresentação.

* O padrão mais simples é "Mostrar tudo". Neste padrão, todos os objetos são apresentados na vista de lista. O portal do catálogo de dados do Azure UX utiliza este padrão para a descrição.
* Padrão de outra é "Merge". Neste padrão, em conjunto, todos os valores de diferentes utilizadores são intercalados com os duplicados removidos. Os exemplos deste padrão no portal do catálogo de dados do Azure UX são as propriedades de etiquetas e especialistas.
* Um terceiro padrão é "último wins de escritor". Neste padrão, apenas o valor mais recente digitado é apresentado. friendlyName é um exemplo deste padrão.

## <a name="asset-object-model"></a>Modelo de objeto de elemento
Conforme apresentado na secção de conceitos chave, o **catálogo de dados do Azure** modelo de objetos incluem itens, o que podem ser ativos ou anotações. Itens têm propriedades, o que podem ser necessária ou opcional. Algumas propriedades aplicam-se a todos os itens. Algumas propriedades se aplicam a todos os recursos. Algumas propriedades aplicam-se apenas aos tipos de recurso específico.

### <a name="system-properties"></a>Propriedades do sistema
<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>carimbo de data/hora</td><td>DateTime</td><td>A última vez em que o item foi modificado. Este campo é gerado pelo servidor quando um item é inserido e sempre que um item é atualizado. O valor desta propriedade é ignorado na entrada de publicar operações.</td></tr><tr><td>ID</td><td>URI</td><td>Url absoluto do item (só de leitura). É o URI endereçável exclusivo para o item.  O valor desta propriedade é ignorado na entrada de publicar operações.</td></tr><tr><td>tipo</td><td>Cadeia</td><td>O tipo de elemento (só de leitura).</td></tr><tr><td>ETag</td><td>Cadeia</td><td>Uma cadeia correspondente para a versão do item que pode ser utilizada para controlo de simultaneidade otimista quando executar operações que atualizam itens no catálogo. "*" pode ser utilizado para corresponder qualquer valor.</td></tr></table>

### <a name="common-properties"></a>Propriedades comuns
Estas propriedades se aplicam a todos os tipos de elemento de raiz e todos os tipos de anotação.

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>fromSourceSystem</td><td>Valor booleano</td><td>Indica se os dados do item são derivados de um sistema de origem (por exemplo, o servidor de base de dados Sql, base de dados Oracle) ou criados por um utilizador.</td></tr>
</table>

### <a name="common-root-properties"></a>Propriedades comuns de raiz
<p>
Estas propriedades aplicam-se a todos os tipos de elemento raiz.

<table><tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr><tr><td>nome</td><td>Cadeia</td><td>Um nome derivado as informações de localização de origem de dados</td></tr><tr><td>DSL</td><td>DataSourceLocation</td><td>Exclusivamente descreve a origem de dados e é um dos identificadores para o elemento. (Consulte a secção identidade dupla).  A estrutura do dsl varia consoante o tipo de origem e de protocolo.</td></tr><tr><td>origem de dados</td><td>DataSourceInfo</td><td>Mais detalhes sobre o tipo de recurso.</td></tr><tr><td>lastRegisteredBy</td><td>SecurityPrincipal</td><td>Descreve o utilizador recentemente registado este recurso.  Contém o id exclusivo para o utilizador (o upn) e um nome a apresentar (lastName e firstName).</td></tr><tr><td>ID do contentor</td><td>Cadeia</td><td>ID do recurso de contentor para a origem de dados. Esta propriedade não é suportada para o tipo de contentor.</td></tr></table>

### <a name="common-non-singleton-annotation-properties"></a>Propriedades comuns de anotação não singleton
Estas propriedades que se aplicam a todos os tipos de anotação não singleton (anotações, o que podem para ser vários por ativo).

<table>
<tr><td><b>Nome da propriedade</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>key</td><td>Cadeia</td><td>Um utilizador especificado chave, que identifica de forma a que a anotação na colecção actual. O comprimento da chave não pode exceder os 256 carateres.</td></tr>
</table>

### <a name="root-asset-types"></a>Tipos de elemento de raiz
Tipos de elemento de raiz são os tipos que representam os vários tipos de recursos de dados que podem ser registados no catálogo. Para cada tipo de raiz, há uma vista, que descreve os ativos e anotações incluídas na vista. Nome da vista deve ser utilizado num segmento de url {view_name} correspondente ao publicar um elemento com a REST API.

<table><tr><td><b>Tipo de recurso (nome de vista)</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Anotações permitidas</b></td><td><b>Comentários</b></td></tr><tr><td>Tabela ("tabelas")</td><td></td><td></td><td>Descrição<p>friendlyName<p>Etiqueta<p>Esquema<p>ColumnDescription<p>ColumnTag<p> disponibilizamos<p>Pré-visualização<p>AccessInstruction<p>TableDataProfile<p>ColumnDataProfile<p>ColumnDataClassification<p>Documentação<p></td><td>Uma tabela representa dados de tabela.  Por exemplo: SQL tabela, vista SQL, tabela em tabela do Analysis Services, Analysis Services Multidimensional de dimensão, Oracle tabela, etc.   </td></tr><tr><td>Medidas ("medidas")</td><td></td><td></td><td>Descrição<p>friendlyName<p>Etiqueta<p>disponibilizamos<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa uma medida de Analysis Services.</td></tr><tr><td></td><td>medidas</td><td>Coluna</td><td></td><td>Metadados que descrevem a medida</td></tr><tr><td></td><td>isCalculated </td><td>Valor booleano</td><td></td><td>Especifica se a medida é calculada ou não.</td></tr><tr><td></td><td>Grupo de medidas</td><td>Cadeia</td><td></td><td>Contentor de físico para a medida</td></tr><td>KPI ("kpis")</td><td></td><td></td><td>Descrição<p>friendlyName<p>Etiqueta<p>disponibilizamos<p>AccessInstruction<p>Documentação</td><td></td></tr><tr><td></td><td>Grupo de medidas</td><td>Cadeia</td><td></td><td>Contentor de físico para a medida</td></tr><tr><td></td><td>goalExpression</td><td>Cadeia</td><td></td><td>Uma expressão numérica MDX ou um cálculo que devolve o valor de destino do KPI.</td></tr><tr><td></td><td>valueExpression</td><td>Cadeia</td><td></td><td>Uma expressão MDX numérica que devolve o valor real do KPI.</td></tr><tr><td></td><td>statusExpression</td><td>Cadeia</td><td></td><td>Uma expressão MDX que representa o estado do KPI num especificado ponto no tempo.</td></tr><tr><td></td><td>trendExpression</td><td>Cadeia</td><td></td><td>Uma expressão MDX que avalia o valor do KPI ao longo do tempo. A tendência pode ser qualquer critério baseados no tempo que é útil num contexto empresariais específicos.</td>
<tr><td>Relatórios "(relatórios)</td><td></td><td></td><td>Descrição<p>friendlyName<p>Etiqueta<p>disponibilizamos<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa um relatório do SQL Server Reporting Services </td></tr><tr><td></td><td>assetCreatedDate</td><td>Cadeia</td><td></td><td></td></tr><tr><td></td><td>assetCreatedBy</td><td>Cadeia</td><td></td><td></td></tr><tr><td></td><td>assetModifiedDate</td><td>Cadeia</td><td></td><td></td></tr><tr><td></td><td>assetModifiedBy</td><td>Cadeia</td><td></td><td></td></tr><tr><td>Contentor ("contentores")</td><td></td><td></td><td>Descrição<p>friendlyName<p>Etiqueta<p>disponibilizamos<p>AccessInstruction<p>Documentação<p></td><td>Este tipo representa um contentor de outros recursos, tais como uma base de dados do SQL Server, um contentor de Blobs do Azure ou um modelo de Analysis Services.</td></tr></table>

### <a name="annotation-types"></a>Tipos de anotação
Tipos de anotação representam os tipos de metadados que podem ser atribuídos para outros tipos no catálogo.

<table>
<tr><td><b>Tipo de anotação (nome de vista aninhadas)</b></td><td><b>Propriedades adicionais</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>Descrição ("descrições")</td><td></td><td></td><td>Esta propriedade contém uma descrição para um recurso. Cada utilizador do sistema pode adicionar as seus próprios descrição.  Apenas esse utilizador pode editar o objeto de descrição.  (Os proprietários de recursos e administradores podem eliminar o objeto de descrição, mas não editá-lo). O sistema mantém as descrições dos utilizadores em separado.  Deste modo, é uma matriz de descrições de cada recurso (um para cada utilizador que tem contribuíram os seus conhecimentos sobre o elemento, para além de possivelmente um que contém informações derivadas da origem de dados).</td></tr>
<tr><td></td><td>descrição</td><td>Cadeia</td><td>Uma descrição breve (linhas 2-3) do elemento</td></tr>

<tr><td>Etiqueta ("etiquetas")</td><td></td><td></td><td>Esta propriedade define uma etiqueta para um recurso. Cada utilizador do sistema pode adicionar várias etiquetas para um recurso.  Apenas o utilizador que criou objetos de etiqueta pode editá-los.  (Os proprietários de recursos e administradores podem eliminar o objeto de etiqueta, mas não editá-lo). O sistema mantém etiquetas dos utilizadores em separado.  Deste modo, é uma matriz de objetos de Tag em cada recurso.</td></tr>
<tr><td></td><td>Etiqueta</td><td>Cadeia</td><td>Uma etiqueta que descrevem o elemento.</td></tr>

<tr><td>FriendlyName ("friendlyName")</td><td></td><td></td><td>Esta propriedade contém um nome amigável para um recurso. FriendlyName é uma anotação singleton - FriendlyName apenas uma pode ser adicionado a um recurso.  Apenas o utilizador que criou FriendlyName objeto pode editá-lo. (Os proprietários de recursos e administradores podem eliminar o objeto de FriendlyName, mas não editá-lo). O sistema mantém nomes amigáveis dos utilizadores em separado.</td></tr>
<tr><td></td><td>friendlyName</td><td>Cadeia</td><td>Um nome amigável do elemento.</td></tr>

<tr><td>Esquema ("schema")</td><td></td><td></td><td>O esquema descreve a estrutura dos dados.  Esta lista os nomes de atributo (coluna, atributo, campo, etc.), tipos, bem como outros metadados.  Esta informação é derivada da origem de dados.  Esquema é uma anotação singleton - apenas um esquema que pode ser adicionada para um recurso.</td></tr>
<tr><td></td><td>colunas</td><td>Coluna []</td><td>Uma matriz de objetos de coluna. Descrevem a coluna com informações derivadas da origem de dados.</td></tr>

<tr><td>ColumnDescription ("columnDescriptions")</td><td></td><td></td><td>Esta propriedade contém uma descrição para uma coluna.  Cada utilizador do sistema pode adicionar as seus próprios descrições para várias colunas (um por coluna). Apenas o utilizador que criou objetos ColumnDescription pode editá-los.  (Os proprietários de recursos e administradores podem eliminar o objeto de ColumnDescription, mas não editá-lo). O sistema mantém descrições das colunas destas utilizador separadamente.  Deste modo, é uma matriz de objetos de ColumnDescription em cada recurso (uma por coluna para cada utilizador que tem contribuíram os seus conhecimentos sobre a coluna para além de possivelmente um que contém informações derivadas da origem de dados).  O ColumnDescription aproximadamente ligado está vinculado ao esquema para poder obter fora de sincronização. O ColumnDescription poderá descrever uma coluna que já não existe no esquema.  Trata-se até o escritor de manter sincronizadas a descrição e o esquema.  A origem de dados pode também ter informações de descrição de colunas e são objetos ColumnDescription adicionais que seriam possível criar quando executar a ferramenta.</td></tr>
<tr><td></td><td>columnName</td><td>Cadeia</td><td>O nome da coluna que esta descrição refere-se a.</td></tr>
<tr><td></td><td>descrição</td><td>Cadeia</td><td>uma descrição breve (2-3 linhas) da coluna.</td></tr>

<tr><td>ColumnTag ("columnTags")</td><td></td><td></td><td>Esta propriedade contém uma tag para uma coluna. Cada utilizador do sistema pode adicionar várias etiquetas para uma determinada coluna e pode adicionar etiquetas de várias colunas. Apenas o utilizador que criou objetos ColumnTag pode editá-los. (Os proprietários de recursos e administradores podem eliminar o objeto de ColumnTag, mas não editá-lo). O sistema mantém as etiquetas de coluna dos utilizadores, estes separadamente.  Deste modo, é uma matriz de objetos de ColumnTag em cada recurso.  O ColumnTag aproximadamente ligado está vinculado ao esquema para poder obter fora de sincronização. O ColumnTag poderá descrever uma coluna que já não existe no esquema.  Trata-se até o escritor para manter a tag de coluna e esquema sincronizadas.</td></tr>
<tr><td></td><td>columnName</td><td>Cadeia</td><td>O nome da coluna que esta etiqueta refere-se a.</td></tr>
<tr><td></td><td>Etiqueta</td><td>Cadeia</td><td>Uma etiqueta que descrevem a coluna.</td></tr>

<tr><td>Especialista ("especialistas")</td><td></td><td></td><td>Esta propriedade contém um utilizador que é considerado um especialista no conjunto de dados. Bolha desenhada opinions(descriptions) dos especialistas na parte superior do UX quando listar as descrições. Cada utilizador pode especificar as seus próprios especialistas. Apenas esse utilizador pode editar o objeto de especialistas. (Os proprietários de recursos e administradores podem eliminar os objetos especialistas, mas não editá-lo).</td></tr>
<tr><td></td><td>disponibilizamos</td><td>SecurityPrincipal</td><td></td></tr>

<tr><td>Pré-visualização ("pré-visualizações")</td><td></td><td></td><td>A pré-visualização contém um instantâneo das principais 20 linhas de dados para o elemento. Pré-visualização só fazem sentido para alguns tipos de recursos (faz sentido para a tabela, mas não para a medida).</td></tr>
<tr><td></td><td>pré-visualização</td><td>[] do objeto</td><td>Matriz de objetos que representam uma coluna.  Cada objeto tem um mapeamento de propriedade para uma coluna com um valor para essa coluna para a linha.</td></tr>

<tr><td>AccessInstruction ("accessInstructions")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>mimeType</td><td>Cadeia</td><td>O tipo de mime do conteúdo.</td></tr>
<tr><td></td><td>Conteúdo</td><td>Cadeia</td><td>As instruções sobre como obter acesso a este recurso de dados. O conteúdo pode ser um URL, um endereço de e-mail ou um conjunto de instruções.</td></tr>

<tr><td>TableDataProfile ("tableDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>numberOfRows</td></td><td>Int</td><td>O número de linhas no conjunto de dados</td></tr>
<tr><td></td><td>Tamanho</td><td>longa</td><td>O tamanho em bytes do conjunto de dados.  </td></tr>
<tr><td></td><td>schemaModifiedTime</td><td>Cadeia</td><td>A hora da última modificação do esquema</td></tr>
<tr><td></td><td>dataModifiedTime</td><td>Cadeia</td><td>A última vez que o conjunto de dados foi modificado (dados foi adicionados, modificados, ou eliminar)</td></tr>

<tr><td>ColumnsDataProfile ("columnsDataProfiles")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>colunas</td></td><td>ColumnDataProfile []</td><td>Uma matriz de perfis de dados da coluna.</td></tr>

<tr><td>ColumnDataClassification ("columnDataClassifications")</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName</td><td>Cadeia</td><td>O nome da coluna que referencia esta classificação.</td></tr>
<tr><td></td><td>Classificação</td><td>Cadeia</td><td>A classificação de dados nesta coluna.</td></tr>

<tr><td>Documentação ("documentação")</td><td></td><td></td><td>Um determinado elemento pode ter apenas uma documentação associada à mesma.</td></tr>
<tr><td></td><td>mimeType</td><td>Cadeia</td><td>O tipo de mime do conteúdo.</td></tr>
<tr><td></td><td>Conteúdo</td><td>Cadeia</td><td>O conteúdo de documentação.</td></tr>

</table>

### <a name="common-types"></a>Tipos comuns
Tipos comuns podem ser utilizados como tipos de propriedades, mas não são itens.

<table>
<tr><td><b>Tipo comum</b></td><td><b>Propriedades</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>
<tr><td>DataSourceInfo</td><td></td><td></td><td></td></tr>
<tr><td></td><td>SourceType</td><td>Cadeia</td><td>Descreve o tipo da origem de dados.  Por exemplo: SQL Server, a base de dados Oracle, etc.  </td></tr>
<tr><td></td><td>objectType</td><td>Cadeia</td><td>Descreve o tipo de objeto da origem de dados. Por exemplo: tabela, ver para o SQL Server.</td></tr>

<tr><td>DataSourceLocation</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Protocolo</td><td>Cadeia</td><td>Necessário. Descreve um protocolo utilizado para comunicar com a origem de dados. Por exemplo: "tds" para o SQl Server, "oracle" para Oracle, etc. Consulte [especificação de referência - DSL estrutura de origem de dados](data-catalog-dsr.md) para obter a lista de protocolos atualmente suportados.</td></tr>
<tr><td></td><td>Endereço</td><td>Dicionário<string, object></td><td>Necessário. O endereço é um conjunto de dados específicos do protocolo que é utilizado para identificar a origem de dados referenciada. Os dados de endereço no âmbito de um protocolo específico, que significa é sem significado sem saberem o protocolo.</td></tr>
<tr><td></td><td>Autenticação</td><td>Cadeia</td><td>Opcional. O esquema de autenticação utilizado para comunicar com a origem de dados. Por exemplo: windows, oauth, etc.</td></tr>
<tr><td></td><td>connectionProperties</td><td>Dicionário<string, object></td><td>Opcional. Obter informações adicionais sobre como ligar a uma origem de dados.</td></tr>

<tr><td>SecurityPrincipal</td><td></td><td></td><td>O back-end não efetua qualquer validação das propriedades fornecidas contra AAD durante a publicação.</td></tr>
<tr><td></td><td>UPN</td><td>Cadeia</td><td>Endereço de e-mail exclusivo do utilizador. Tem de ser especificado se não for fornecido objectId ou no contexto da propriedade "lastRegisteredBy", caso contrário, opcional.</td></tr>
<tr><td></td><td>objectId</td><td>GUID</td><td>Identidade AAD de grupo ou utilizador administrativo. Opcional. Tem de ser especificado se upn não for fornecido, caso contrário, opcional.</td></tr>
<tr><td></td><td>Nome próprio</td><td>Cadeia</td><td>Nome próprio do utilizador (para efeitos de apresentação). Opcional. Só é válida no contexto da propriedade "lastRegisteredBy". Não é possível especificar ao fornecer o principal de segurança para "funções", "permissões" e "especialistas".</td></tr>
<tr><td></td><td>Apelido</td><td>Cadeia</td><td>Apelido do utilizador (para efeitos de apresentação). Opcional. Só é válida no contexto da propriedade "lastRegisteredBy". Não é possível especificar ao fornecer o principal de segurança para "funções", "permissões" e "especialistas".</td></tr>

<tr><td>Coluna</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>Cadeia</td><td>Nome da coluna ou atributo.</td></tr>
<tr><td></td><td>tipo</td><td>Cadeia</td><td>tipo de dados da coluna ou atributo. Os tipos permitidos dependem sourceType de dados do elemento.  Apenas um subconjunto dos tipos é suportado.</td></tr>
<tr><td></td><td>maxLength</td><td>Int</td><td>O comprimento máximo permitido para a coluna ou um atributo. Derivado da origem de dados. Só é aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>precisão</td><td>Bytes</td><td>A precisão da coluna ou um atributo. Derivado da origem de dados. Só é aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>isNullable</td><td>Valor booleano</td><td>Indica se a coluna tem permissão para ter um valor nulo ou não. Derivado da origem de dados. Só é aplicável a alguns tipos de origem.</td></tr>
<tr><td></td><td>expressão</td><td>Cadeia</td><td>Se o valor é uma coluna calculada, este campo contém a expressão que expresse precisa o valor. Derivado da origem de dados. Só é aplicável a alguns tipos de origem.</td></tr>

<tr><td>ColumnDataProfile</td><td></td><td></td><td></td></tr>
<tr><td></td><td>columnName </td><td>Cadeia</td><td>O nome da coluna</td></tr>
<tr><td></td><td>tipo </td><td>Cadeia</td><td>O tipo da coluna</td></tr>
<tr><td></td><td>min. </td><td>Cadeia</td><td>O valor mínimo do conjunto de dados</td></tr>
<tr><td></td><td>Máx. </td><td>Cadeia</td><td>O valor máximo no conjunto de dados</td></tr>
<tr><td></td><td>média </td><td>duplo</td><td>O valor médio no conjunto de dados</td></tr>
<tr><td></td><td>StDev </td><td>duplo</td><td>O desvio padrão para o conjunto de dados</td></tr>
<tr><td></td><td>nullCount </td><td>Int</td><td>A contagem de valores nulos existentes no conjunto de dados</td></tr>
<tr><td></td><td>distinctCount  </td><td>Int</td><td>A contagem de valores distintos no conjunto de dados</td></tr>


</table>

## <a name="asset-identity"></a>Identidade de recurso
Catálogo de dados do Azure utiliza as propriedades de "protocolo" e a identidade da matriz de propriedades "address" da propriedade de "dsl" DataSourceLocation para gerar a identidade do elemento, que é utilizado para resolver o elemento no interior do catálogo.
Por exemplo, o protocolo "tds" tem identidade propriedades "server", "base de dados", "schema" e "objeto". As combinações do protocolo e as propriedades de identidade são utilizadas para gerar a identidade do elemento de tabela do SQL Server.
Catálogo de dados do Azure fornece vários protocolos de origem de dados incorporada, que estão listados em [especificação de referência - DSL estrutura de origem de dados](data-catalog-dsr.md).
O conjunto de protocolos suportados pode ser expandido através de programação (consulte a referência da API de REST do catálogo de dados). Os administradores do catálogo podem registar os protocolos de origem de dados personalizados. A tabela seguinte descreve as propriedades necessárias para registar um protocolo personalizado.

### <a name="custom-data-source-protocol-specification"></a>Especificação de protocolo de origem de dados personalizada
<table>
<tr><td><b>Tipo</b></td><td><b>Propriedades</b></td><td><b>Tipo de dados</b></td><td><b>Comentários</b></td></tr>

<tr><td>DataSourceProtocol</td><td></td><td></td><td></td></tr>
<tr><td></td><td>Espaço de nomes</td><td>Cadeia</td><td>O espaço de nomes do protocolo. Espaço de nomes tem de ser entre 1 e 255 carateres de comprimento, conter uma ou mais partes vazios separadas por ponto (.). Cada parte tem de ter entre 1 e 255 carateres, começar com uma letra e conter apenas letras e números.</td></tr>
<tr><td></td><td>nome</td><td>Cadeia</td><td>O nome do protocolo. Nome tem de ter entre 1 e 255 carateres, começar com uma letra e conter apenas letras, números e o caráter de travessão (-).</td></tr>
<tr><td></td><td>identityProperties</td><td>DataSourceProtocolIdentityProperty []</td><td>Lista de propriedades de identidade, tem de conter pelo menos, mas não existem propriedades mais de 20. Por exemplo: "server", "base de dados", "schema", "objeto" é propriedades de identidade do protocolo "tds".</td></tr>
<tr><td></td><td>identitySets</td><td>DataSourceProtocolIdentitySet []</td><td>Lista de conjuntos de identidade. Define os conjuntos de propriedades de identidade, o que representam a identidade de recurso válido. Tem de conter pelo menos, mas não existem conjuntos mais de 20. Por exemplo: {"server", "base de dados", "schema" e "objeto"} é uma identidade definida para o protocolo "tds", que define a identidade do elemento de tabela do Sql Server.</td></tr>

<tr><td>DataSourceProtocolIdentityProperty</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>Cadeia</td><td>O nome da propriedade. Nome deve ser de 1 a 100 carateres, começar com uma letra e só pode conter letras e números.</td></tr>
<tr><td></td><td>tipo</td><td>Cadeia</td><td>O tipo da propriedade. Valores suportados: "bool" booleano ","byte","guid","int","inteiros","período de tempo","cadeia","url"</td></tr>
<tr><td></td><td>ignoreCase</td><td>bool</td><td>Indica se devem ser ignorada maiúsculas e minúsculas, ao utilizar o valor da propriedade. Só pode ser especificado para propriedades com o tipo "cadeia". Valor predefinido é falso.</td></tr>
<tr><td></td><td>urlPathSegmentsIgnoreCase</td><td>bool []</td><td>Indica se as maiúsculas e minúsculas devem ser ignorada para cada segmento do caminho do url. Só pode ser especificado para propriedades com o tipo de "url". Valor predefinido é [false].</td></tr>

<tr><td>DataSourceProtocolIdentitySet</td><td></td><td></td><td></td></tr>
<tr><td></td><td>nome</td><td>Cadeia</td><td>O nome de identidade do conjunto.</td></tr>
<tr><td></td><td>propriedades</td><td>String]</td><td>A lista de propriedades de identidade incluído nesta identidade de conjunto. Não pode conter duplicados. Cada propriedade referenciada pelo conjunto de identidade tem de ser definida na lista de "identityProperties" do protocolo.</td></tr>

</table>

## <a name="roles-and-authorization"></a>Funções e autorização
Catálogo de dados do Microsoft Azure fornece capacidades de autorização para operações CRUD de elementos e anotações.

## <a name="key-concepts"></a>Conceitos-chave
O catálogo de dados do Azure utiliza dois mecanismos de autorização:

* Autorização baseada em funções
* Autorização baseada em permissão

### <a name="roles"></a>Funções
Existem três funções: **administrador**, **proprietário**, e **contribuinte**.  Cada função tem o âmbito e direitos, que estão resumidos na tabela seguinte.

<table><tr><td><b>Função</b></td><td><b>Âmbito</b></td><td><b>Direitos</b></td></tr><tr><td>Administrador</td><td>Catálogo (todos os recursos/anotações no catálogo de)</td><td>ViewRoles de eliminação de leitura

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Proprietário</td><td>Cada elemento (item de raiz)</td><td>ViewRoles de eliminação de leitura

ChangeOwnership ChangeVisibility ViewPermissions</td></tr><tr><td>Contribuinte</td><td>Cada recurso individual e anotação</td><td>Leitura atualização eliminar ViewRoles Nota: todos os direitos são revogados se leitura direito no item é revogada a partir do contribuinte</td></tr></table>

> [!NOTE]
> **Leitura**, **atualização**, **eliminar**, **ViewRoles** direitos são aplicáveis a qualquer item (anotação ou de recurso) ao **TakeOwnership**, **ChangeOwnership**, **ChangeVisibility**, **ViewPermissions** só são aplicáveis ao elemento de raiz.
> 
> **Eliminar** direito aplica-se a um item e qualquer subitems ou único item abaixo dele. Por exemplo, a eliminação de um recurso também elimina quaisquer anotações para esse recurso.
> 
> 

### <a name="permissions"></a>Permissões
Permissão é como a lista de entradas de controlo de acesso. Cada entrada de controlo de acesso atribui o conjunto de direitos para um principal de segurança. Permissões só podem ser especificadas num recurso (ou seja, o item de raiz) e aplicam-se para o elemento e qualquer subitems.

Durante a **catálogo de dados do Azure** pré-visualizar, apenas **leitura** direita é suportada na lista de permissões para ativar o cenário de restringir a visibilidade de um ativo.

Por predefinição nenhum utilizador autenticado tem **leitura** botão direito do rato para qualquer item no catálogo, a menos que visibilidade restrita para o conjunto de principais as permissões.

## <a name="rest-api"></a>API REST
**COLOCAR** e **POST** item da vista de pedidos podem ser utilizadas para controlar funções e permissões: para além do payload do item, duas propriedades do sistema podem ser especificadas **funções** e **permissões**.

> [!NOTE]
> **permissões** apenas aplicável a um item de raiz.
> 
> **Proprietário** função apenas aplicável a um item de raiz.
> 
> Por predefinição, quando um item é criado no catálogo do respetivo **contribuinte** está definido para o utilizador autenticado atualmente. Se o item deverá ser atualizável por todas as pessoas, **contribuinte** deve ser definido como &lt;todas as pessoas&gt; segurança especial principal no **funções** propriedade quando o item é o primeiro publicados (consulte o exemplo seguinte). **Contribuidor** não pode ser alterada e permanece igual durante o tempo de vida de um item (mesmo **administrador** ou **proprietário** não tem o direito para alterar o **contribuinte**). O único valor suportado para a definição explícita do **contribuinte** é &lt;todas as pessoas&gt;: **contribuinte** só pode ser um utilizador que criou um item ou &lt;todas as pessoas&gt;.
> 
> 

### <a name="examples"></a>Exemplos
**Como contribuinte &lt;todas as pessoas&gt; ao publicar um item.**
O principal de segurança especial &lt;todas as pessoas&gt; tem objectId "00000000-0000-0000-0000-000000000201".
  **POST** https://api.azuredatacatalog.com/catalogs/default/views/tables/?api-version=2016-03-30

> [!NOTE]
> Algumas implementações de cliente HTTP podem emitir automaticamente pedidos em resposta a um 302 do servidor, mas normalmente da faixa cabeçalhos de autorização do pedido. Uma vez que o cabeçalho de autorização é necessário para fazer pedidos ao catálogo de dados do Azure, certifique-se de que cabeçalho de autorização ainda é fornecido quando emitir novamente um pedido para uma localização de redirecionamento especificado pelo catálogo de dados do Azure. O código de exemplo seguinte demonstra-la utilizando o objeto .NET HttpWebRequest.
> 
> 

**Corpo**

    {
        "roles": [
            {
                "role": "Contributor",
                "members": [
                    {
                        "objectId": "00000000-0000-0000-0000-000000000201"
                    }
                ]
            }
        ]
    }

  **Atribua os proprietários e restringir a visibilidade para um item de raiz existente**: **colocar** https://api.azuredatacatalog.com/catalogs/default/views/tables/042297b0...1be45ecd462a?api-version=2016-03-30

    {
        "roles": [
            {
                "role": "Owner",
                "members": [
                    {
                        "objectId": "c4159539-846a-45af-bdfb-58efd3772b43",
                        "upn": "user1@contoso.com"
                    },
                    {
                        "objectId": "fdabd95b-7c56-47d6-a6ba-a7c5f264533f",
                        "upn": "user2@contoso.com"
                    }
                ]
            }
        ],
        "permissions": [
            {
                "principal": {
                    "objectId": "27b9a0eb-bb71-4297-9f1f-c462dab7192a",
                    "upn": "user3@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            },
            {
                "principal": {
                    "objectId": "4c8bc8ce-225c-4fcf-b09a-047030baab31",
                    "upn": "user4@contoso.com"
                },
                "rights": [
                    {
                        "right": "Read"
                    }
                ]
            }
        ]
    }

> [!NOTE]
> No PUT não é necessário especificar um payload de item no corpo: PUT pode ser utilizado para atualizar apenas funções de e/ou permissões.
> 
> 

<!--Image references-->
[1]: ./media/data-catalog-developer-concepts/concept2.png
