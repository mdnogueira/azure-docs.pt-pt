---
title: "Ferramenta de avaliação do Cortana Intelligence solução | Microsoft Docs"
description: "Como Partner Microsoft, seguem-se todos os passos que tem de seguir para publicar a sua solução do Cortana Intelligence AppSource."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: 8340ae1231b7f40f739d7bbb63cee1bf0f095ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Ferramenta de avaliação da solução do Cortana Intelligence
## <a name="overview"></a>Descrição geral
Pode utilizar a ferramenta de avaliação do Cortana Intelligence solução para avaliar as soluções de análise avançadas para compatibilidade com recomendadas pela Microsoft as melhores práticas. Microsoft é entusiasmado para trabalhar com os nossos parceiros (ISVs / SIs) para fornecer soluções de alta qualidade para os clientes, de revendedores e de implementação. Este guia irá guiá-lo durante o processo de utilizar a ferramenta de avaliação de solução com a sua solução e descrevem as melhores práticas específicas no verifica a existência.

## <a name="getting-started"></a>Introdução
. [Transferir](https://aka.ms/aa-evaluation-tool-download) e instalar a ferramenta de avaliação de solução do Cortana Intelligence.

Pré-requisitos:
- Windows 10: [oficial site para o Windows 10](https://www.microsoft.com/en-us/windows)
- O Azure Powershell: [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).

## <a name="identifying-your-app"></a>Identificar a sua aplicação
Depois de concluída a instalação, abra a ferramenta e iniciar a sua avaliação primeiro.

![Ferramenta de avaliação aberta](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Fornece informações de identificação sobre a sua solução.

![Ligar a subscrição do Azure](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Ligar à sua subscrição do Azure e forneça o grupo de recursos que contém a aplicação.

![Selecione recursos](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

Assim que o grupo de recursos terem sido carregado, selecione os recursos que estão incluídos na sua solução e identificam a acessibilidade de quaisquer recursos de dados como:
- Ingestão
- Consumo
- Interno

Estas informações são utilizadas para compreender melhor a sua solução é a utilização de vários componentes e para garantir que são consistentes com as melhores práticas componentes destinada ao utilizador.

### <a name="ingestion"></a>Ingestão
Ingestão neste caso, significa que quaisquer origens de dados que são utilizados para solicitar dados de fora da solução ou quaisquer serviços fora da solução utilizam para enviar dados para a mesma.

### <a name="consumption"></a>Consumo
Consumo neste caso, significa que os conjuntos de dados que são utilizados para enviar dados para os utilizadores finais, direta ou indiretamente. Por exemplo:
- Conjuntos de dados utilizados numa consulta direta do Power BI.
- Conjuntos de dados consultados num WebApp.

>[!NOTE]
Se for utilizado um recurso específico para ingestão e consumo, escolha **consumo**.

### <a name="internal"></a>Interno
Utilize o parâmetro interno quaisquer recursos de dados que são utilizadas apenas no processamento de aplicação interna.

Em seguida, será solicitado para fornecer credenciais válidas para quaisquer bases de dados especificados no passo anterior:

![Pré-requisitos do conjunto de teste](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Casos de teste de solução
A ferramenta de solução irá efetuar uma coleção de testes automatizadas na sua solução.

![Execução do conjunto de teste](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

Após a conclusão dos testes, será pedido para fornecer uma explicação ou justificação para por que razão a solução não cumprir o requisito.

![Indicar uma justificação de negócio](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Por exemplo, se a sua solução publica para o armazém de dados do Azure SQL, os testes de avaliação exigem também publicar Azure Analysis Services. 

A solução pode utilizar máquinas virtuais de IaaS com o Sql Server Analysis Services em vez do Azure Analysis Services. Isto seria um motivo da falha do teste aceitável.
## <a name="packaging-your-evaluation-results"></a>Empacotar os resultados da avaliação
Depois de concluir os casos de teste, o pacote de avaliação será exportado para um ficheiro zip e será pedido para fornecer comentários sobre a ferramenta de avaliação. 

Terá de partilhar este ficheiro de zip de resultados de teste com a Microsoft para a sua solução para avaliar antes de obter aprovação para ser adicionado a AppSource

![Ferramenta de avaliação do nível](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

Acima secção deste artigo abrange várias funcionalidades da ferramenta, agora reveja informe-nos tipos de melhores práticas que avalia esta ferramenta.

## <a name="security-evaluation-considerations"></a>Considerações de avaliação de segurança
### <a name="databases-should-use-azure-active-directory-authentication"></a>Bases de dados devem utilizar a autenticação do Azure Active Directory
Quaisquer recursos de SQL do Azure ou do armazém de dados do Azure SQL o sloution devem ser ativados com a autenticação do Azure Active Directory (AAD). AAD fornece um único local para gerir todos os seus identidades e funções.

| Para obter mais informações sobre | Consulte este artigo |
| --- | --- |
| AAD com a base de dados SQL e do armazém de dados do SQL Server | [Utilizar a autenticação do Active Directory do Azure para a autenticação com base de dados SQL ou SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) |
| Configurar e gerir AAD | [Configurar e gerir a autenticação do Azure Active Directory com a base de dados SQL ou SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) |
| Autenticação de WebApps do Azure | [Autenticação e autorização no serviço de aplicações do Azure](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) |
| Configurar WebApps no AAD | [Como configurar a sua aplicação de serviço de aplicações para utilizar o início de sessão do Azure Active Directory](https://docs.microsoft.com/en-us/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Conjuntos de dados acessíveis aos utilizadores finais devem suportar o controlo de acesso baseado em funções
Ao executar a ferramenta de avaliação, será pedido para especificar quaisquer relatórios ou publicação de recursos. Presume-se que estes recursos destinam-se para acesso por utilizadores finais, não os programadores. Estes recursos deve fornecer controlo de acesso baseado em funções (RBAC) para garantir que os utilizadores finais só são capazes de aceder a dados autorizados.

Mais concretamente, podem ser configuradas com RBAC qualquer um dos seguintes recursos do Azure sendo considerados aceitável:
- Proteger o HDInsight, consulte [uma introdução ao Hadoop segurança com clusters do HDInsight associados a um domínio](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)
- SQL do Azure, consulte [autenticações AAD com SQL do Azure]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)
- Serviços de análise do Azure, consulte [gerir utilizadores e funções de base de dados para o Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users)
- O Azure SQL Data Warehouse (Lembre-se de que porque o armazém de dados do SQL Server suporta o RBAC, não é recomendado para acesso de utilizador final diretas.)

Se estiver a utilizar outro tipo de recurso que suporta o RBAC, especifique que no justificação caso de teste.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>O Azure Data Lake Store deve utilizar a encriptação em rest
O Azure Data Lake Store (ADLS) suporta a encriptação de em rest por predefinição utilizando chaves de encriptação ADLS geridos. Também pode configurar a encriptação a utilizar o Cofre de chaves do Azure.

Para obter informações sobre como especificar definições de encriptação de ADLS [criar uma conta do Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>SQL do Azure e o Azure SQL Data Warehouse devem utilizar a encriptação
Azure SQL tanto o armazém de dados do Azure SQL suportam transparente dados encriptação (TDE), que fornece a encriptação em tempo real e a desencriptação de ficheiros de registo e dados.

| Para obter mais informações sobre | Consulte este artigo |
| --- | --- |
| Encriptação de dados transparente (TDE) | [Encriptação transparente de dados](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse com TDE | [Encrption TDE TSQL do armazém de dados SQL](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Configurar o SQL do Azure com TDE | [Encriptação transparente de dados com base de dados SQL do Azure](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Configurar o SQL do Azure com sempre encriptado | [Base de dados do SQL sempre encriptados Cofre de chaves do Azure](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Para além de TDE, SQL do Azure também suporta sempre encriptados, uma nova tecnologia de encriptação de dados garante que os dados são encriptados não só em rest e durante movimento entre cliente e servidor, mas também ao dados está a ser utilizada ao executar comandos no servidor.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Quaisquer máquinas virtuais tem de ser implementado no Azure Marketplace
Para fornecer um nível de segurança consistente entre AppSource, é necessário que quaisquer máquinas virtuais implementadas como parte de uma solução do Cortana Intelligence ser certificadas e publicadas no Azure Marketplace.

Para procurar a lista atual de imagens do Azure Marketplace, consulte [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Para obter informações sobre como publicar uma imagem de máquina virtual para o Azure Marketplace, consulte [guia para criar uma imagem de máquina virtual para o Azure Marketplace](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Considerações sobre escalabilidade de avaliação
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Soluções da Cortana Intelligence devem incluir uma plataforma de macrodados dimensionável
Soluções da Cortana Intelligence devem dimensionado para tamanhos de dados muito grandes. No Azure, isto significa que devem incluir uma das duas plataformas de dados de escala Petabyte:
- Azure Data Lake Store
- Azure SQL Data Warehouse

Se a sua solução não necessita de suporte para estes tamanhos de dados ou se estiver a utilizar uma plataforma de dados alternativos,. explicam isto no justificação caso de teste.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Soluções da Cortana Intelligence devem incluir a ambientes de dados de ingestão dedicado
Soluções da Cortana Intelligence, geralmente, devem evitar diretamente inserir dados de origens de dados relacionais. Em vez disso, os dados não processados devem ser armazenados num ambiente não estruturado, com idempotent inserções/atualizações para qualquer arquivos relacionais utilizando o Azure Data Factory.

Para obter mais informações sobre a cópia de dados com o Azure Data Factory, [Tutorial: criar um pipeline com atividade de cópia com o Visual Studio](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>O Azure SQL Data Warehouse deve utilizar o PolyBase para ingestão de dados
Armazém de dados SQL do Azure suporta o PolyBase para ingestão de dados altamente dimensionável e paralelas. O PolyBase permite-lhe utilizar o armazém de dados do Azure SQL Server para consultas de problema em relação a conjuntos de dados externos armazenados no Blob Storage do Azure ou do Azure Data Lake Store. Isto proporciona um desempenho superior para métodos alternativos de atualizações em massa.

Para obter instruções sobre como começar a PolyBase e armazém de dados do Azure SQL, consulte [carregar dados com o PolyBase no SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Para obter mais informações sobre as melhores práticas com o PolyBase e o armazém de dados do Azure SQL, consulte [guia para utilizar o PolyBase no SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Considerações de avaliação de disponibilidade

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>Conjuntos de dados acessíveis aos utilizadores finais devem suportar um grande volume de utilizadores em simultâneo
Ao executar a ferramenta de avaliação, será pedido para especificar quaisquer relatórios ou publicação de recursos. Presume-se que estes recursos destinam-se para acesso por utilizadores finais, não os programadores. Estes recursos devem suportar média-grande número de utilizadores em simultâneo.

Especificamente, o Azure SQL Data Warehouse não deve ser a origem de dados único disponível aos utilizadores finais. Se o armazém de dados do Azure SQL é fornecido como um recurso para utilizadores avançados, Azure Analysis Services devem ser efetuadas disponíveis aos utilizadores típicos.

Para obter mais informações sobre limites de simultaneidade de armazém de dados do Azure SQL, consulte [gestão simultaneidade e carga de trabalho no SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Para obter mais informações sobre os serviços de análise do Azure, consulte [descrição geral dos serviços de análise](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Recursos SQL do Azure devem ter uma réplica só de leitura para a ativação pós-falha
Bases de dados SQL do Azure suportam georreplicação para uma instância secundária. Esta instância, em seguida, pode ser utilizada como uma instância de ativação pós-falha para fornecer aplicações de elevada disponibilidade.

Para obter mais informações sobre a georreplicação para bases de dados SQL do Azure, consulte [descrição geral do SQL Server da base de dados de Georreplicação](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview).

Para obter instruções sobre como configurar a georreplicação de SQL do Azure, consulte [configurar georreplicação ativa para a base de dados do Azure SQL com o Transact-SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>O Azure SQL Data Warehouse deve ter cópias de segurança georredundante ativadas
Armazém de dados SQL do Azure suporta cópias de segurança diárias para armazenamento georredundante. Este georreplicação garante que pode restaurar o armazém de dados, mesmo em situações onde não é possível aceder aos instantâneos armazenados na sua região primária. Esta funcionalidade está ativada por predefinição e não deve ser desativar para soluções da Cortana Intelligence.

Para obter mais informações sobre cópias de segurança do armazém de dados do Azure SQL Server e o restauro, consulte o artigo aqui [cópias de segurança do SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Máquinas virtuais devem ser configuradas com conjuntos de disponibilidade
Máquinas virtuais do Azure devem ser configuradas em conjuntos de disponibilidade para minimizar o impacto de eventos de manutenção planeado e imprevisto.

Para obter mais informações sobre a disponibilidade de máquina virtual do Azure, consulte [gerir a disponibilidade das virtual machines Windows no Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Outras considerações de avaliação
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Aplicações da Cortana Intelligence devem utilizar uma ferramenta centralizada de orquestração de dados
Utilizar uma ferramenta única para gerir e agendamento de movimento de dados e a transformação garante a consistência à volta de dados fundamentais. Fornece uma lógica simples em torno da lógica de repetição, gestão de dependência, / registo de alerta, etc. Recomendamos a utilização de [do Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-introduction) de orquestração de dados no Azure.

Se estiver a utilizar uma ferramenta que não sejam do Azure Data Factory para orquestração de dados, descreve qual a ferramenta ou ferramentas que está a utilizar.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Devem ser retrained modelos de Machine Learning do Azure utilizando o Azure Data Factory
Azure Machine Learning (AzureML) fornece fáceis de utilizar ferramentas para a criação e implementação de modelação preditiva e do machine learning pipelines. No entanto, é importante que as implementações de produção destes modelos do AzureML não é baseado num único conjunto de dados fixo, mas em vez disso, é feita para o dynamics shifting do mundo real phenomena.

Para obter mais informações sobre a criação de serviços web de reparametrização no AzureML, consulte [Machine Learning reparametrização dos modelos programáticos](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-retrain-models-programmatically).

Para obter mais informações sobre automatizar o processo de preparação do modelo utilizando o Azure Data Factory, consulte [modelos de atualizar o Azure Machine Learning utilizando a atividade do recurso da atualização](https://docs.microsoft.com/en-us/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Documentação existente
[Microsoft Azure certificadas para aumentar a sua empresa de nuvem](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Certificado para Cortana Intellignece do Microsoft Azure](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)

