---
title: "Novidades no catálogo de dados do Azure | Microsoft Docs"
description: "Este artigo fornece uma descrição geral das novas funcionalidades adicionadas ao catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 1201f8d4-6f26-4182-af3f-91e758a12303
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 09/06/2017
ms.author: maroche
ms.openlocfilehash: 7259505b68202f3741e64e86555d2e3f968b8951
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="whats-new-in-azure-data-catalog"></a>Novidades no catálogo de dados do Azure
Atualizações **catálogo de dados do Azure** são lançadas regularmente. Não cada versão inclui novas funcionalidades de destinada ao utilizador, como algumas versões são concentra-se nas capacidades do serviço de back-end. Esta página realça as novas capacidades destinada ao utilizador adicionadas para o serviço de catálogo de dados do Azure.

## <a name="whats-new-for-september-2017"></a>Novidades de Setembro de 2017 
A partir de Setembro de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para extrair associar metadados de relação de origens de dados DB2 ao registar as tabelas relacionadas com a ferramenta de registo de origem de dados.
* Suporte para registar origens de dados 3.4 de versão de MongoDB utilizando a ferramenta de registo da origem de dados.
* Suporte para eliminar todos os metadados de objetos contidos numa única operação ao remover uma base de dados ou outro contentor a partir do catálogo de dados.
* Suporte para visualização até 1000 etiquetas, os termos do glossário comercial ou outra pesquisa facetas, quando limitar uma pesquisa no portal do catálogo de dados.


## <a name="whats-new-for-august-2017"></a>Novidades de Agosto de 2017 
A partir de Agosto de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

*   Uma nova amostra de programador está disponível para criar e gerir metadados de relação utilizando a API de REST do catálogo de dados. O *importar informações de relação para o catálogo de dados* exemplo está disponível na [página de exemplos de código do catálogo de dados](https://azure.microsoft.com/resources/samples/?service=data-catalog&sort=0). 
* Suporte para extrair associar metadados de relação de origens de dados Teradata ao registar as tabelas relacionadas com a ferramenta de registo de origem de dados.
* Suporte para a função de valor de tabela do SQL Server objetos (TVF) quando registar origens de dados do SQL Server utilizando a ferramenta de registo da origem de dados.
* Várias atualizações e refinamentos para aumentar o desempenho e a facilidade de utilização do portal do catálogo de dados.

## <a name="whats-new-for-july-2017"></a>Novidades de Julho de 2017 
A partir de Julho de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Suporte para um controlo mais granular sobre operações de metadados de permitido, incluindo:
    - Os administradores do catálogo podem restringir a capacidade dos próprios utilizadores contribuir etiquetas e os metadados relacionados para o catálogo, ativar o acesso só de leitura para o catálogo.
    - Os administradores do catálogo podem restringir a capacidade dos próprios utilizadores para registar origens de dados de novo no catálogo.
    - Os administradores do catálogo podem restringir a capacidade dos próprios utilizadores para assumir a propriedade de metadados do recurso de dados no catálogo.
    - Podem ser concedidas permissões para grupos de segurança do Azure Active Directory e os utilizadores para facilitar a gestão de permissões.
* Suporte para as relações entre os recursos de dados registados e deteção de dados relacionados de recursos no portal do catálogo de dados, incluindo:
    - Extração de metadados de relação do SQL Server (incluindo a SQL Database do Azure), Oracle e MySQL origens de dados ao utilizar a ferramenta de registo de origem de dados do catálogo de dados.
    - Deteção de recursos de dados relacionados ao visualizar metadados recurso no portal do catálogo de dados.
    - Operações para definir, detetar e gerir relações entre os recursos de dados utilizando a API de REST do catálogo de dados.

Para obter detalhes adicionais sobre a gestão de permissões no catálogo de dados, consulte [como proteger o acesso ao catálogo de dados e recursos de dados](data-catalog-how-to-secure-catalog.md).
Para obter detalhes adicionais sobre relações no catálogo de dados, consulte [como ver dados relacionados ativos no catálogo de dados do Azure](data-catalog-how-to-view-related-data-assets.md).

## <a name="whats-new-for-june-2017"></a>Novidades para Junho de 2017 
A partir de Junho de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Suporte para origens de dados Sybase, Apache Cassandra e MongoDB. Os utilizadores podem agora registar e detetar Cassandra e MongoDB bases de dados e tabelas e bases de dados Sybase, tabelas e vistas.

> [!NOTE]
> Ao registar o MongoDB e Cassandra tabelas que inclua colunas com tipos de dados complexas, como registos e matrizes, estas colunas não serão incluídas nos metadados adicionados ao catálogo de dados.

## <a name="whats-new-for-may-2017"></a>Novidades de Maio de 2017 
A partir de Maio de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Uma nova amostra de programador está disponível para a importação em massa do glossário de termos de negócio. O exemplo de importação em volume de glossário está disponível na [página do catálogo de dados para programadores amostras](https://docs.microsoft.com/azure/data-catalog/data-catalog-samples). 
*   Suporte para editar informações de ligação de ODBC no portal do catálogo de dados do Azure. Os proprietários de recursos de dados e os administradores do catálogo de dados agora podem editar as informações de ligação para origens de dados ODBC registadas sem ser necessário voltar a registar as origens de dados.
*   Suporte para URLs clicáveis nas definições de glossário comercial e descrições. Quando os URLs estão incluídos nas propriedades de descrição e a definição para os termos do glossário comercial, os URLs serão apresentados como hiperligações no portal do catálogo de dados.
*   Suporte para apresentar nomes especialistas para além dos endereços de e-mail especialista. Ao visualizar especialistas nas propriedades de um recurso de dados no portal do catálogo de dados, nome completo do especialista do Azure Active Directory será apresentado na descrição.

## <a name="whats-new-for-april-2017"></a>Novidades de Abril de 2017 
A partir de Abril de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Suporte para as origens de dados ODBC. Os utilizadores podem agora registar e detetar bases de dados ODBC, tabelas e vistas utilizando a ferramenta de registo de origem de dados do catálogo de dados.

## <a name="whats-new-for-march-2017"></a>Novidades de Março de 2017 
A partir de Março de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Suporte para utilizar grupos de segurança do AAD para que os administradores do catálogo de dados.
*   Catálogo de dados do Azure está agora disponível numa nova região do Azure. Os clientes podem aprovisionar o catálogo de dados do Azure na Central EUA oeste região, para além dos EUA leste, EUA oeste, Europa Ocidental e da Austrália leste, Europa do Norte e Sudeste asiático. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

## <a name="whats-new-for-february-2017"></a>Novidades de Fevereiro de 2017 
A partir de Fevereiro de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Suporte para definições avançadas na ferramenta de registo de origem de dados do catálogo de dados. Os utilizadores podem especificar valores de tempo limite do comando ao registar origens de dados de grandes dimensões.
*   Suporte para as origens de dados do FTP e PostgreSQL. Os utilizadores podem agora registar e detetar FTP ficheiros e diretórios e as bases de dados PostgreSQL, tabelas e vistas.

## <a name="whats-new-for-january-2017"></a>Novidades de Janeiro de 2017 
A partir de Janeiro de 2017, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Catálogo de dados do Azure está agora [CSA ESTRELA](https://www.microsoft.com/trustcenter/compliance/csa-star-certification) em conformidade.
*   Integração com [obter & transformar no Excel 2016 e o Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605). Os utilizadores do Excel podem partilhar consultas e detetar consultas utilizando o catálogo de dados do Azure de dentro de Excel. Esta funcionalidade está disponível para os utilizadores com licenças Power BI Pro.

## <a name="whats-new-for-december-2016"></a>Novidades de Dezembro de 2016
A partir de Dezembro de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Catálogo de dados do Azure está agora [HIPAA](https://www.microsoft.com/trustcenter/Compliance/HIPAA) e [EU modelo cláusulas](https://www.microsoft.com/TrustCenter/Compliance/EU-Model-Clauses) em conformidade.
*   Suporte para editar informações de ligação da origem de dados. Os proprietários de recursos de dados e os administradores do catálogo de dados agora podem editar as informações de ligação para origens de dados registados sem ser necessário voltar a registar as origens de dados.
*   Suporte para as origens de dados em Salesforce.com. Os utilizadores podem agora registar e detete objetos do Salesforce.


## <a name="whats-new-for-november-2016"></a>Novidades de Novembro de 2016
A partir de Novembro de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:
*   Catálogo de dados do Azure está agora [ISO/IEC 27001](https://www.microsoft.com/trustcenter/compliance/iso-iec-27001) e [ISO/IEC 27018](https://www.microsoft.com/TrustCenter/Compliance/iso-iec-27018) em conformidade.
*   Suporte para o registo manual das origens de dados ODBC a utilizar o portal do catálogo de dados e a REST API.

## <a name="whats-new-for-september-2016"></a>Novidades de Setembro de 2016
A partir de Setembro de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para as origens de dados IBM DB2. Os utilizadores podem agora registar e detetar bases de dados DB2, tabelas e vistas.
* Suporte para as origens de dados de base de dados do Azure Cosmos. Os utilizadores podem agora registar e detetar bases de dados de base de dados do Cosmos e coleções.
* Suporte para personalizar o nome do catálogo no portal do catálogo de dados. Os administradores do catálogo podem fornecer texto que é apresentado no portal título, por exemplo, o nome da organização.

## <a name="whats-new-for-august-2016"></a>Novidades de Agosto de 2016
A partir de Agosto de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Melhoramentos para o registo de origens de dados do SQL Server Master Data Services (MDS). Os utilizadores agora podem incluir perfis de pré-visualizações e os dados ao registar utilizando a ferramenta de registo de origem de dados do catálogo de dados de entidades MDS.
* Suporte para pesquisas guardadas organizacionais definida pelo administrador. Quando guardar uma pesquisa no portal do catálogo de dados, os administradores do catálogo de dados podem agora optar por guardar pesquisas para utilização pessoal ou para todos os utilizadores do catálogo. Organizacionais procuras guardadas são partilhadas com todos os utilizadores do catálogo e podem fornecer padronizados pontos de partida para a deteção de origem de dados.
* Atualizações para a vista de propriedades no portal do catálogo de dados. Todas as propriedades de recurso de dados são agora apresentadas e geridas num painel único, redimensionável para fornecer mais consistente e detetável experiência.

## <a name="whats-new-for-july-2016"></a>Novidades de Julho de 2016
A partir de Julho de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para as origens de dados do SQL Server Master Data Services (MDS). Os utilizadores podem agora registar e detetar MDS modelos e entidades.
* Suporte para procedimentos armazenados do SQL Server. Os utilizadores podem agora registar e detete objetos de procedimento armazenado em origens de dados do SQL Server.
* Suporte para idiomas adicionais no portal do catálogo de dados do Azure e a ferramenta de registo de origem de dados, para um total de 18 idiomas suportados. A experiência de utilizador do catálogo de dados do Azure está localizada com base nas preferências de idioma no Windows ou no browser.
* As atualizações e refinement para o catálogo de dados home page do portal, incluindo uma experiência de utilizador simplificada e melhoramentos de desempenho.

## <a name="whats-new-for-june-2016"></a>Novidades para Junho de 2016
A partir de Junho de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para redimensionar colunas na vista de lista quando detetar recursos de dados no portal do catálogo de dados. Os utilizadores podem agora redimensionar colunas individuais para ler mais facilmente os metadados de recurso demorado, tais como as etiquetas e descrições.
* Power Query para Excel foi adicionada ao menu "Abrir em" no portal do catálogo de dados. Os utilizadores podem abrir agora origens de dados suportadas no Excel 2016 ou no Excel 2010 e o Excel 2013 com o [Power Query para Excel](https://support.office.com/article/Introduction-to-Microsoft-Power-Query-for-Excel-6E92E2F4-2079-4E1F-BAD5-89F6269CD605) suplemento instalado.
* Suporte para as origens de dados do Table Storage do Azure. Os utilizadores podem agora registar e detete objetos de tabela em origens de dados do Storage do Azure.

## <a name="whats-new-for-may-2016"></a>Novidades de Maio de 2016
A partir de Maio de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Um glossário comercial que permite aos administradores do catálogo definir termos de negócio e hierarquias para criar um vocabulário de negócio comuns. Os utilizadores podem Etiquetar recursos de dados registados com os termos do glossário para o facilitar a detetar e compreender o conteúdo do catálogo. Para obter mais informações, veja [How to set up the Business Glossary for Governed Tagging (Como configurar o Glossário Comercial para Etiquetagem Regida)](data-catalog-how-to-business-glossary.md).  
* Melhoramentos para o glossário comercial catálogo de dados que permite aos utilizadores atualizar os termos do glossário vários numa única operação. Os utilizadores podem selecionar vários termos para editar os campos seguintes:
  * Termo do principal: O utilizador pode selecionar um novo termo principal e todos os termos de licenciamento selecionados são atualizados para ser subordinados do termo principal selecionado. Se selecionado de termos de todas as ter a mesma actividade principal, em seguida, esse principal é apresentado na caixa de texto, caso contrário, o termo principal campo está definido para em branco.   
  * As etiquetas e intervenientes: os utilizadores podem adicionar e remover as etiquetas e intervenientes para os termos do glossário vários utilizando a mesma experiência como marcação de vários recursos de dados.

> [!NOTE]
> O glossário comercial está disponível apenas a edição Standard do Azure dados de catálogo. A edição gratuita não fornece capacidades para etiquetagem regida ou um glossário comercial.

## <a name="whats-new-for-march-2016"></a>Novidades de Março de 2016
A partir de Março de 2016, foram adicionadas as seguintes funcionalidades para o Azure dados catálogo: g:

* Um consolidado REST API ponto final para aceder programaticamente as capacidades de pesquisa e capacidades de gestão de ativos de catálogo do serviço catálogo de dados do Azure. Este ponto final de API de ponto final e de catálogo de API de pesquisa foi preterido e descontinuada em 21 de Março de 2016. Não foram efetuadas alterações para a semântica de API. Apenas o ponto final de URI alterado. Para obter mais informações, consulte o [referência de API de REST de catálogo de dados do Azure](https://msdn.microsoft.com/library/azure/mt267595.aspx). Para exemplos de API, consulte [amostras de programador do catálogo de dados do Azure](data-catalog-samples.md).

## <a name="whats-new-for-february-2016"></a>Novidades de Fevereiro de 2016
A partir de Fevereiro de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Uma seleção de origem de dados reestruturada recentemente experiência na ferramenta de registo de origem de dados do catálogo de dados do Azure. A ferramenta de registo da origem de dados foi atualizada para tornar mais fácil de localizar e selecione as origens de dados suportados pelo catálogo de dados do Azure.
* Suporte para 10 idiomas adicionais no portal do catálogo de dados do Azure e a ferramenta de registo da origem de dados. Além do inglês, a experiência de catálogo de dados do Azure está agora disponível em alemão, espanhol, francês, italiano, japonês, coreano, Português-Brasil, russo, chinês simplificado e chinês tradicional. A experiência de utilizador do catálogo de dados do Azure está localizada com base nas preferências de idioma no Windows ou no browser do utilizador.
* Suporte para a georreplicação de dados do catálogo de dados do Azure para a recuperação de continuidade e desastre de negócio. Todo o conteúdo do catálogo de dados do Azure, incluindo dados origem metadados e crowdsourcing as anotações, agora é replicado entre duas regiões do Azure sem custos adicionais aos clientes. Regiões do Azure previamente estão emparelhadas, pelo menos de 500 quilómetros, à excepção e siga o mapeamento, conforme descrito em [recuperação de continuidade e desastre de negócio (BCDR): Azure emparelhado regiões](../best-practices-availability-paired-regions.md).
* Suporte para alterar a subscrição do Azure utilizada pelo catálogo de dados do Azure. Administradores do catálogo de dados do Azure podem utilizar a página de definições no portal do catálogo de dados do Azure para selecionar outra subscrição do Azure para fins de faturação.

## <a name="whats-new-for-january-2016"></a>Novidades de Janeiro de 2016
A partir de Janeiro de 2016, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para registar manualmente origens de dados adicionais. Pode agora utilizar "Criar Manual entrada" no portal do catálogo de dados do Azure ou utilizar a API de REST do catálogo de dados do Azure para registar as origens de dados seguintes:
  * OData - função, o conjunto de entidades e o contentor de entidades
  * HTTP - ficheiro, o ponto final, o relatório e o Site
  * Sistema de ficheiros - ficheiro
  * SharePoint - lista
  * FTP - de ficheiros e diretórios
  * Em Salesforce.com - objeto
  * DB2 - de tabela, vista e base de dados
  * PostgreSQL - de tabela, vista e base de dados
* Suporte para "Abrir no SQL Server Data Tools" para origens de dados do SQL Server (incluindo BD SQL do Azure e Azure SQL Data Warehouse).  
* Suporte para registar e deteção de vistas de SAP HANA e pacotes. Pode registar origens de dados SAP HANA utilizando o catálogo de dados do Azure origem de dados ferramenta de registo e podem anotar e detetar origens de dados SAP HANA registadas através do portal do catálogo de dados do Azure.
* A capacidade de pin e remover recursos de dados no portal do catálogo de dados do Azure. Pode optar por afixar a recursos de dados para que fiquem mais fáceis de detetar e reutilizar.
* Uma página inicial recentemente reestruturada no portal do catálogo de dados do Azure. A nova home page inclui informações sobre a atividade atual de utilizadores - incluindo recursos publicados recentemente, afixados ativos e pesquisas guardadas - e informações sobre a atividade no catálogo como um todo.
* Suporte para definições de utilizador persistentes no portal do catálogo de dados do Azure. Definições de experiência de utilizador, incluindo a vista de grelha ou mosaico, o número de resultados por página e acessos realce ou desativar, são mantidas entre sessões de utilizador.
* Catálogo de dados do Azure está agora disponível em duas regiões do Azure de novo. Os clientes podem aprovisionar o catálogo de dados do Azure em regiões Europa do Norte e Sudeste asiático, para além dos EUA leste, EUA oeste, Europa Ocidental e leste da Austrália. Para obter mais informações, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

> [!NOTE]
> "Abrir no SQL Server Data Tools" requer o Visual Studio 2013 com a Update 4 e ferramentas do SQL Server para ser instalada. Para instalar a versão mais recente do SQL Server Data Tools, visite [transferir SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx).


## <a name="whats-new-for-december-2015"></a>Novidades de Dezembro de 2015
A partir de Dezembro de 2015, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para perfis de dados para origens de dados do Azure SQL Data Warehouse. Ao registar o Azure SQL Data Warehouse tabelas e vistas, os utilizadores podem optar por incluir métricas de perfil de dados com os metadados extraídos da origem de dados.
* Suporte para registar e deteção de objetos de MySQL e bases de dados. Os utilizadores podem registar origens de dados MySQL utilizando o catálogo de dados do Azure origem de dados ferramenta de registo e podem anotar e detetar origens de dados MySQL registadas através do portal do catálogo de dados do Azure.
* Suporte para a autenticação SPNEGO e o Windows para origens de dados Teradata. Ao registar Teradata tabelas e vistas, os utilizadores podem optar por ligar para Teradata através da autenticação SPNEGO e Windows e LDAP e TD2.
* Suporte para as origens de dados do Azure Data Lake Store. Os utilizadores podem agora registar e detetar origens de dados do Azure Data Lake Store utilizando o catálogo de dados do Azure.
* Suporte para especificar manualmente as definições de proxy de rede na ferramenta de registo de origem de dados do catálogo de dados do Azure. Podem selecionar "Modificar as definições de proxy" a partir da página de boas-vindas a ferramenta utilizadores e podem especificar o endereço de proxy e a porta a ser utilizada pela ferramenta.

## <a name="whats-new-for-november-2015"></a>Novidades de Novembro de 2015
A partir de Novembro de 2015, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* A capacidade de ver e copiar as cadeias de ligação no portal do catálogo de dados do Azure para SQL Server (incluindo a SQL Database do Azure) e origens de dados Oracle. Os utilizadores podem clicar "Ver cadeias de ligação" ligação as informações de ligação para uma tabela do SQL Server ou Oracle, a vista ou a base de dados, para ver as cadeias de ligação utilizadas para ligar à origem de dados. Cadeias de ligação do ADO.NET, ODBC, OLEDB e JDBC são fornecidas para origens de dados do SQL Server. ODBC e cadeias de ligação de OLEDB são fornecidas para origens de dados Oracle.
* Suporte para incluindo perfis de dados ao registar Teradata tabelas e vistas.
* Suporte para "Abrir no Power BI Desktop" para o SQL Server (incluindo BD SQL do Azure e Azure SQL Data Warehouse), SQL Server Analysis Services, o Storage do Azure e o origens HDFS.  
* Suporte para autenticação LDAP para origens de dados Teradata. Ao registar Teradata tabelas e vistas, os utilizadores podem optar por ligar para Teradata através de LDAP e TD2 autenticação.
* Suporte para "Abrir em Excel" para origens de dados Teradata.
* Suporte para os termos da procura recente no portal do catálogo de dados do Azure. Quando pesquisar no portal, os utilizadores podem selecionar dos termos de pesquisa utilizados recentemente para acelerar a experiência de deteção.
* Suporte para a pré-visualização para origens de dados Teradata. Ao registar Teradata tabelas e vistas, os utilizadores podem optar por incluir registos de instantâneo com os metadados extraídos da origem de dados.
* Suporte para "Abrir em Excel" para origens de dados do Azure SQL Data Warehouse.
* Suporte para definir e editar os esquemas de nível de coluna para recursos de dados registados manualmente. Depois de criar manualmente um recurso de dados através do portal do catálogo de dados do Azure, os utilizadores podem adicionar definições da coluna nas propriedades de recurso de dados.
* Suporte para "tem" consultas ao pesquisar o catálogo de dados do Azure, para ativar a deteção de recursos de dados registados que possui os metadados específicos. Sintaxe de consulta de catálogo de dados do Azure inclui agora:

| Sintaxe de consulta | Objetivo |
| --- | --- |
| `has:previews` |Localiza recursos de dados que incluem uma pré-visualização |
| `has:documentation` |Localiza recursos de dados para o qual foi fornecida documentação |
| `has:tableDataProfiles` |Localiza recursos de dados com informações de perfil de dados de nível da tabela |
| `has:columnsDataProfiles` |Localiza recursos de dados com informações de perfil de dados ao nível da coluna |


> [!NOTE]
> "Abrir no Power BI Desktop" necessita de uma versão atual da aplicação Power BI Desktop para ser instalada. Se ocorrerem problemas ou de erros com esta funcionalidade, certifique-se de que tem a versão mais recente do Power BI Desktop de [PowerBI.com](https://powerbi.com).


## <a name="whats-new-for-october-2015"></a>Novidades de Outubro de 2015
A partir de Outubro de 2015, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para encriptação de inativos de dados em perfis de pré-visualizações e os dados para origens de dados registados. Origens de dados registadas com o serviço, sem ser necessário para a gestão de chaves pelos administradores do catálogo de perfis de dados e o catálogo de dados do Azure encripta transparente quaisquer registos de pré-visualização.
* Suporte para as origens de dados Teradata. Os utilizadores podem agora registar e detetar Teradata tabelas e vistas.
* Suporte para as origens de dados do Hive no local. Os utilizadores podem agora registar e detetar as tabelas do Hive para Apache Hive em origens de dados do Hadoop no local.
* Suporte para pesquisas guardadas no portal do catálogo de dados do Azure. Os utilizadores podem guardar termos de pesquisa e filtrar seleções facilmente repetir pesquisas anteriores e definir vistas útil do conteúdo do catálogo. Utilizador também pode marcar uma procura guardada como as respetivas pesquisa predefinida. Quando um utilizador clica no ícone de pesquisa "Lupa" do catálogo de dados do Azure home page do portal ou a partir da página "Introdução", o utilizador é direcionado diretamente para a pesquisa guardada como predefinição.
* Suporte para documentação de texto formatado para recursos de dados registados e contentores no portal do catálogo de dados do Azure. Os utilizadores agora podem fornecer documentação para os recursos de dados, como tabelas, vistas e relatórios e para contentores, tais como bases de dados e os modelos, para cenários em que as etiquetas e descrições não são suficientes.
* Suporte para registar manualmente os tipos de origens de dados conhecido. Os utilizadores podem introduzir manualmente as informações da origem de dados através do portal do catálogo de dados do Azure para todos os tipos de origem de dados suportados pelo catálogo de dados do Azure.
* Suporte para autorizar os grupos de segurança do Azure Active Directory. Os administradores do catálogo podem ativar o acesso de catálogo para grupos de segurança, contas de utilizador, tornando mais fácil gerir o acesso ao catálogo de dados do Azure.
* Suporte para abrir as origens de dados do Hive no Excel a partir do portal do catálogo de dados do Azure.

> [!NOTE]
> A versão atual, é suportada apenas Teradata TD2 autenticação. Versões de autenticação adicional mecanismos são suportados no futuro.

> [!NOTE]
> Para utilizar a funcionalidade de "Abrir em Excel" para origens de dados do Hive, os utilizadores têm instalaram o controlador ODBC do Hive.

## <a name="whats-new-for-september-2015"></a>Novidades de Setembro de 2015
A partir de Setembro de 2015, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para incluindo perfis de dados ao registar origens de dados do Hive.
* Suporte para a deteção através de programação a API do catálogo, tornando mais fácil para aplicações integrar com o catálogo de dados do Azure.
* Novos dados "Introdução" origem experiência de deteção no portal do catálogo de dados do Azure. Quando os utilizadores introduzem a página "detetar" do portal do catálogo de dados do Azure sem introduzir um termo de pesquisa, são apresentados com uma descrição geral do conteúdo de catálogo, incluindo as etiquetas mais frequentemente utilizadas, especialistas, tipos de origens de dados e os tipos de objeto.
* Suporte para registar e deteção de objetos do armazém de dados do SQL do Azure e bases de dados. Para obter informações adicionais sobre o Azure SQL Data Warehouse, consulte [SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
* Suporte para registar e deteção de servidores do SQL Server Reporting Services como contentores e modelos de SQL Server Analysis Services. Quando registar objetos SSAS e SSRS, o catálogo de dados do Azure cria uma entrada para o modelo SSAS e o servidor SSRS e para os relatórios e outros objetos. Os contentores podem ser detetados e anotado com o portal do catálogo de dados do Azure. Os utilizadores também podem procurar e filtrar o conteúdo de um modelo ou do servidor para além de pesquisar e filtrar o conteúdo do catálogo.
* Suporte para registar e deteção de objetos de SQL Server Analysis Services através de HTTP/HTTPS. Os utilizadores agora podem ligar aos servidores SSAS utilizando um URL (por exemplo, https://servername/olap/msmdpump.dll) em vez de um nome de servidor e podem utilizar a autenticação básica e ligações anónimas para além de autenticação do Windows. Para obter informações adicionais sobre as ligações HTTP/HTTPS ao SSAS, consulte [configurar o acesso HTTP ao Analysis Services](https://msdn.microsoft.com/library/gg492140.aspx).
* Suporte para as origens de dados do Hive no HDInsight. Os utilizadores podem agora registar e detetar as tabelas do Hive para Apache Hive no Hadoop no HDInsight origens de dados. Para obter informações adicionais sobre o Hive no HDInsight, consulte o [Centro de documentação do HDInsight](../hdinsight/hdinsight-use-hive.md).
* Suporte para registar e deteção de bases de dados Oracle e clusters do HDFS como contentores. Quando registar tabelas Oracle e vistas ou HDFS, o catálogo de dados do Azure cria uma entrada para a base de dados, tabelas e vistas. A base de dados pode ser detetado e anotado com o portal do catálogo de dados do Azure. Os utilizadores também podem procurar e filtrar o conteúdo da base de dados ou num cluster, além de pesquisar e filtrar o conteúdo do catálogo.
* Suporte para registar manualmente os tipos de origens de dados desconhecido. Os utilizadores podem introduzir manualmente as informações da origem de dados através do portal do catálogo de dados do Azure, para que possam ser anotadas e detetar origens de dados explicitamente não suportadas pela ferramenta de registo da origem de dados.
* Suporte para registar e deteção de bases de dados do SQL Server como contentores. Ao registar e vistas de tabelas do SQL Server, o catálogo de dados do Azure cria uma entrada para a base de dados, tabelas e vistas. A base de dados pode ser detetado e anotado com o portal do catálogo de dados do Azure. Os utilizadores também podem procurar e filtrar o conteúdo de uma base de dados, além de pesquisar e filtrar o conteúdo do catálogo.

> [!NOTE]
> SSAS e SSRS objetos que foram registados antes da versão 18 de Setembro tem de estar registados novamente utilizando a ferramenta de registo da origem de dados antes da entrada de modelo ou do servidor é adicionada ao catálogo. Volte a registar uma origem de dados não afeta as anotações que foram adicionadas pelos utilizadores no portal do catálogo de dados do Azure.

> [!NOTE]
> Tabelas de Oracle e vistas e ficheiros do HDFS e diretórios que foram registados antes da versão 11 de Setembro tem de ser novamente registados utilizando a ferramenta de registo da origem de dados antes da entrada de base de dados ou o cluster é adicionada ao catálogo. Volte a registar uma origem de dados não afeta as anotações que foram adicionadas pelos utilizadores no portal do catálogo de dados do Azure.

> [!NOTE]
> Tabelas de SQL Server e vistas que foram registados antes da versão 4 de Setembro tem de ser novamente registadas utilizando a ferramenta de registo da origem de dados antes da entrada de base de dados é adicionada ao catálogo. Volte a registar uma origem de dados não afeta as anotações que foram adicionadas pelos utilizadores no portal do catálogo de dados do Azure.

## <a name="whats-new-for-august-2015"></a>Novidades de Agosto de 2015
A partir de Agosto de 2015, foram adicionadas as seguintes capacidades do Azure no catálogo de dados:

* Suporte para a criação de perfis de dados de origens de dados do SQL Server e Oracle. Ao registar o SQL Server e Oracle tabelas e vistas, os utilizadores podem optar por incluir informações de perfil de dados para os objetos que está a ser registado. O perfil de dados inclui estatísticas ao nível do objeto e ao nível da coluna.
* Suporte para as origens de dados do Hadoop HDFS. Os utilizadores podem agora registar e detetar HDFS ficheiros e diretórios.
* Suporte para fornecer informações de pedido de acesso para origens de dados registados. Para qualquer recurso de dados registados, os utilizadores agora podem fornecer instruções para pedir acesso, incluindo ligações de e-mail ou URLs, fácil integração com ferramentas existentes e processos.
* Sugestões de ferramenta para as etiquetas e especialistas, para facilitar a detetar que os utilizadores forneceu os metadados para recursos de dados registados.
* Adicionámos um novo botão de "Utilizador" e o menu à nossa barra de navegação superior. Este menu permite que o utilizador veja a conta utilizada para iniciar sessão no catálogo de dados do Azure e terminar sessão, se assim o desejar. Este menu também apresenta o nome do catálogo, que é útil para programadores utilizando a API de REST do catálogo de dados do Azure.
* Standard Edition: Ao adicionar os proprietários de recursos de dados, o catálogo de dados do Azure agora suporta contas de utilizador e grupos de segurança como proprietários. Para adicionar um grupo de segurança como proprietário de recursos de dados selecionada, pode introduzir o nome a apresentar do grupo ou endereço de correio eletrónico UPN do grupo, se tiver um.
* Suporte para as origens de dados do Blob Storage do Azure. Os utilizadores podem agora registar e detetar o blobs Storage do Azure e diretórios.

