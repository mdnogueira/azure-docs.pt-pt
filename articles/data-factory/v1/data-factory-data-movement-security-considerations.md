---
title: "Considerações de segurança para o movimento de dados no Azure Data Factory | Microsoft Docs"
description: Saiba mais sobre como proteger o movimento de dados no Azure Data Factory.
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 02bb0dd2927c552809e2903092939a479d69bb9e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>O Azure Data Factory - considerações de segurança para o movimento de dados

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory, que está geralmente disponível (GA). Se estiver a utilizar a versão 2 do serviço do Data Factory, o que está em pré-visualização, consulte [considerações de segurança de movimento de dados para a versão 2 do Data Factory](../data-movement-security-considerations.md).

## <a name="introduction"></a>Introdução
Este artigo descreve a infraestrutura de básicos de segurança que utilizam serviços de movimento de dados no Azure Data Factory para proteger os seus dados. Recursos de gestão do Azure Data Factory são criados numa infraestrutura de segurança do Azure e utilizam todas as medidas de segurança possível oferecidas pelo Azure.

Numa solução do Data Factory, pode criar um ou mais [pipelines](data-factory-create-pipelines.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Estes pipelines de residir na região onde foi criada a fábrica de dados. 

Apesar de fábrica de dados só está disponível em **EUA oeste**, **EUA Leste**, e **Europa do Norte** regiões, o serviço de movimento de dados está disponível [global em várias regiões](data-factory-data-movement-activities.md#global). Serviço de fábrica de dados garante que os dados deixe uma área geográfica / região, a menos que explicitamente a instruir o serviço para utilizar uma região alternativa, se o serviço de movimento de dados ainda não foi implementado nessa região. 

O Azure Data Factory propriamente dito não armazena quaisquer dados, exceto as credenciais de serviço ligado para os arquivos de dados de nuvem, que são encriptados utilizando certificados. Permite-lhe criar fluxos de trabalho condicionados por dados para orquestrar o movimento dos dados entre [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) e o processamento de dados com [serviços de computação](data-factory-compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite [monitorizar e gerir fluxos de trabalho](data-factory-monitor-manage-pipelines.md) com mecanismos programáticos e de IU.

Movimento de dados utilizando o Azure Data Factory foi **certificadas** para:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA)  
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018) 
-   [ESTRELA DE CSA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)
     
Se estiver interessado na forma como o Azure protege a própria infraestrutura e de conformidade do Azure, visite o [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx). 

Neste artigo, vamos rever as considerações de segurança nos seguintes cenários de movimento de dados de duas: 

- **Cenário de nuvem**-neste cenário, a origem e de destino estão acessíveis publicamente através da internet. Estes incluem os serviços de armazenamento em nuvem gerido, como o Storage do Azure, Azure SQL Data Warehouse, SQL Database do Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS, como o Salesforce e protocolos de web, como o FTP e OData. Pode encontrar uma lista completa das origens de dados suportadas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- **Cenário híbrido**- neste cenário, a origem ou de destino é protegido por uma firewall ou dentro de uma rede empresarial no local ou os dados de arquivo está numa rede privada / virtual de rede (frequentemente de origem) e não se encontra acessível publicamente. Servidores de base de dados alojadas em máquinas virtuais também abrangidos por este cenário.

## <a name="cloud-scenarios"></a>Cenários de nuvem
###<a name="securing-data-store-credentials"></a>Proteger credenciais do arquivo de dados
O Azure Data Factory protege as credenciais do arquivo de dados por **encriptar** -los utilizando **certificados geridos pelo Microsoft**. Estes certificados rodam cada **dois anos** (que inclui a renovação do certificado e a migração de credenciais). Estas credenciais encriptados segura são armazenadas num **Storage do Azure é gerida pelos serviços de gestão do Azure Data Factory**. Para obter mais informações sobre a segurança de armazenamento do Azure, consulte [descrição geral de segurança de armazenamento do Azure](../../security/security-storage-overview.md).

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se o arquivo de dados na nuvem suporta HTTPS ou TLS, todos os dados transferidos entre os serviços de movimento de dados no Data Factory e um arquivo de dados de nuvem são através de canal seguro, HTTPS ou TLS.

> [!NOTE]
> Todas as ligações ao **SQL Database do Azure** e **Azure SQL Data Warehouse** sempre exigir a encriptação (SSL/TLS) dados enquanto estes se encontram em trânsito para e da base de dados. Ao criar um pipeline com um editor de JSON, adicione o **encriptação** propriedade e defina-o como **verdadeiro** no **cadeia de ligação**. Quando utiliza o [Assistente para copiar](data-factory-azure-copy-wizard.md), o assistente define esta propriedade, por predefinição. Para **Storage do Azure**, pode utilizar **HTTPS** na cadeia de ligação.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Alguns dados armazena suporte a encriptação de dados inativos. Sugerimos que ative o mecanismo de encriptação de dados para os arquivos de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Encriptação de dados transparente (TDE) no armazém de dados SQL do Azure ajuda-o com a proteger contra a ameaça de atividade maliciosa efetuando em tempo real encriptação e desencriptação de dados inativos. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [proteger uma base de dados no SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Base de dados SQL do Azure também suporta a encriptação de dados transparente (TDE), que ajuda com a proteger contra a ameaça de atividade maliciosa através de encriptação em tempo real e a desencriptação de dados sem necessidade de alterações à aplicação. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [encriptação transparente de dados com a SQL Database do Azure](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store também proporciona a encriptação dos dados armazenados na conta. Quando ativada, o arquivo Data Lake é automaticamente encripta os dados antes de a persistência e desencripta antes da obtenção, tornando-a transparente para o cliente de acesso aos dados. Para obter mais informações, consulte [segurança no Azure Data Lake Store](../../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e o Table Storage do Azure
Storage do Azure de armazenamento de BLOBs e tabelas do Azure suporta armazenamento serviço encriptação (SSE), que encripta os dados antes de a persistência para armazenamento e desencripta antes da obtenção automaticamente. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 suporta o cliente e o servidor de encriptação de dados inativos. Para obter mais informações, consulte [proteger utilizando a encriptação de dados](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, a fábrica de dados não suporta Amazon S3 dentro de uma nuvem privada virtual (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift suporta encriptação de cluster para dados inativos. Para obter mais informações, consulte [encriptação de base de dados do Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, a fábrica de dados não suporta Amazon Redshift dentro de um VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce suporta a encriptação de plataforma de proteção que permite que a encriptação de ficheiros, os anexos, campos personalizados. Para obter mais informações, consulte [compreender o fluxo de autenticação do Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-data-management-gateway"></a>Cenários híbridos (utilizando o Data Management Gateway)
Cenários híbridos requerem Data Management Gateway para ser instalado numa rede no local ou dentro de uma rede virtual (Azure) ou uma nuvem privada virtual (Amazon). O gateway tem de ser capaz de aceder aos arquivos de dados local. Para obter mais informações sobre o gateway, consulte [Data Management Gateway](data-factory-data-management-gateway.md). 

![Canais de Gateway de gestão de dados](media/data-factory-data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimento de dados no Data Factory e Data Management Gateway. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para transferir dados entre os arquivos de dados no local e arquivos de dados de nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do arquivo de dados no local
As credenciais para os arquivos de dados no local são armazenadas localmente (não na nuvem). Estes podem ser definidas de três formas diferentes. 

- Utilizar **texto simples** (menos seguro) através de HTTPS a partir do Portal do Azure / Assistente para copiar. As credenciais são transmitidas em texto simples para o gateway no local.
- Utilizar **bibliotecas de criptografia de JavaScript do Assistente para copiar**.
- Utilizar **clique-uma vez com base na aplicação Gestor de credenciais**. Clique em-assim que executa a aplicação na máquina no local que tenha acesso ao gateway e define as credenciais do arquivo de dados. Esta opção e a outra seguinte são as opções mais seguras. A aplicação do Gestor de credenciais, por predefinição, utiliza a porta 8050 na máquina com o gateway para comunicação segura.  
- Utilize [New-AzureRmDataFactoryEncryptValue](/powershell/module/azurerm.datafactories/New-AzureRmDataFactoryEncryptValue) cmdlet do PowerShell para encriptar as credenciais. O cmdlet utiliza o certificado que gateway está configurado para utilizar para encriptar as credenciais. Pode utilizar as credenciais encriptadas devolvidas por este cmdlet e adicione-o a **EncryptedCredential** elemento do **connectionString** no ficheiro JSON que utiliza com o [New-AzureRmDataFactoryLinkedService](/powershell/module/azurerm.datafactories/new-azurermdatafactorylinkedservice) cmdlet ou no fragmento JSON no Editor do Data Factory no portal. Esta opção e clique em-assim que a aplicação são as opções mais seguras. 

#### <a name="javascript-cryptography-library-based-encryption"></a>Encriptação de baseada em bibliotecas de criptografia de JavaScript
Pode encriptar as credenciais do arquivo de dados utilizando [bibliotecas de criptografia de JavaScript](https://www.microsoft.com/download/details.aspx?id=52439) do [Assistente para copiar](data-factory-copy-wizard.md). Quando seleciona esta opção, o Assistente para copiar obtém a chave pública do gateway e utiliza-o para encriptar as credenciais do arquivo de dados. As credenciais são desencriptadas pelo computador gateway e protegidas pelo Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx).

**Browsers suportados:** IE8 IE9, IE10, IE11, Microsoft Edge e mais recente Firefox, Chrome, Opera, Safari browsers. 

#### <a name="click-once-credentials-manager-app"></a>Clique em-uma vez na aplicação Gestor de credenciais
Pode iniciar o clique-depois com a base de aplicação do portal do Azure/copiar Assistente do Gestor de credenciais durante a criação de pipelines. Esta aplicação assegura que as credenciais não são transferidas em texto simples através da transmissão. Por predefinição, utiliza a porta **8050** na máquina com o gateway para comunicação segura. Se necessário, esta porta pode ser alterada.  
  
![Porta HTTPS para o gateway](media/data-factory-data-movement-security-considerations/https-port-for-gateway.png)

Atualmente, o Data Management Gateway utiliza um único **certificado**. Este certificado é criado durante a instalação do gateway (aplica-se a Data Management Gateway, se for criado depois de Novembro de 2016 e versão 2.4.xxxx.x ou posterior). Pode substituir este certificado com o seu próprio certificado de SSL/TLS. Este certificado é utilizado pela, clique em-Gestor de aplicação para ligar em segurança para a máquina do gateway para definir as credenciais do arquivo de dados de credenciais uma vez. Armazena os dados credenciais do arquivo de forma segura no local utilizando o Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) na máquina com o gateway. 

> [!NOTE]
> Gateways anteriores que foram instalados antes de Novembro de 2016 ou a versão 2.3.xxxx.x continuam a utilizar as credenciais encriptadas e armazenadas na nuvem. Mesmo se atualizar o gateway para a versão mais recente, as credenciais não são migradas para uma máquina no local    
  
| Versão do gateway (durante a criação de) | Credenciais armazenadas | Encriptação de credenciais / segurança | 
| --------------------------------- | ------------------ | --------- |  
| < = 2.3.xxxx.x | Na nuvem | Encriptado utilizando o certificado (diferente da utilizada pela aplicação de Gestor de credenciais) | 
| > = 2.4.xxxx.x | No local | Protegido por DPAPI | 
  

### <a name="encryption-in-transit"></a>Encriptação em trânsito
Todas as transferências de dados são através de canal seguro **HTTPS** e **TLS através de TCP** para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.
 
Também pode utilizar [IPSec VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Express Route](../../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a rede no local e o Azure.

Rede virtual é uma representação lógica da rede na nuvem. Pode ligar uma rede no local à sua rede virtual do Azure (VNet) ao configurar a VPN IPSec (site a site) ou uma Expressroute (Peering privado)     

A tabela seguinte resume as recomendações de configuração de rede e gateway com base em diferentes combinações de localizações de origem e destino para o movimento de dados de híbrida.

| Origem | Destino | Configuração da rede | Configuração do gateway |
| ------ | ----------- | --------------------- | ------------- | 
| Local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | VPN IPSec (ponto a site ou site a site) | Gateway pode ser instalado no local ou num Azure virtual machine (VM) na VNet | 
| Local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | ExpressRoute (Peering privado) | Gateway pode ser instalado no local ou numa VM do Azure na VNet | 
| Local | Serviços baseados no Azure que tenham um ponto final público | ExpressRoute (Peering público) | Gateway tem de ser instalado no local | 

As imagens seguintes mostram a utilização do Data Management Gateway para mover dados entre uma base de dados no local e serviços do Azure com o expressroute e IPSec de VPN (com a rede Virtual):

**Expressroute:**
 
![Utilizar o Expressroute com o gateway](media/data-factory-data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec de VPN:**

![IPSec de VPN com o gateway](media/data-factory-data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-of-gateway"></a>Configurações de firewall e a listas brancas endereço IP do gateway

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos da firewall da rede no local/privada  
Numa empresa, uma **firewall empresarial** é executado no router central da organização. E, **firewall do Windows** é executado como um daemon no computador local no qual o gateway está instalado. 

A tabela seguinte fornece **porta de saída** e requisitos de domínio para o **firewall empresarial**.

| Nomes de domínio | Portas de saída | Descrição |
| ------------ | -------------- | ----------- | 
| `*.servicebus.windows.net` | 443, 80 | Necessário para o gateway para ligar aos serviços de movimento de dados no Data Factory |
| `*.core.windows.net` | 443 | Utilizada pelo gateway para ligar à conta de armazenamento do Azure ao utilizar o [testado cópia](data-factory-copy-activity-performance.md#staged-copy) funcionalidade. | 
| `*.frontend.clouddatahub.net` | 443 | Necessária para o gateway para ligar ao serviço do Azure Data Factory. | 
| `*.database.windows.net` | 1433   | (Opcional) necessárias quando o destino for SQL Database do Azure / do armazém de dados SQL do Azure. Utilize a funcionalidade de teste cópia para copiar dados para o Azure SQL da base de dados/Azure SQL Data Warehouse sem abrir a porta 1433. | 
| `*.azuredatalakestore.net` | 443 | (Opcional) necessárias quando o destino é o Azure Data Lake store | 

> [!NOTE] 
> Poderá ter de gerir portas / domínios a listas brancas da firewall da empresa nível conforme exigido pelas origens de dados correspondentes. Esta tabela só utiliza a exemplos de SQL Database do Azure, o Azure SQL Data Warehouse, o Azure Data Lake Store.   

A tabela seguinte fornece **porta de entrada** requisitos para o **firewall do windows**.

| Portas de entrada | Descrição | 
| ------------- | ----------- | 
| 8050 (TCP) | Necessárias para a aplicação de Gestor de credenciais para definir em segurança credenciais para os arquivos de dados no local no gateway. | 

![Requisitos de portas de gateway](media\data-factory-data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurations-whitelisting-in-data-store"></a>Configurações de IP / armazenar a listas brancas de dados
Alguns arquivos de dados na nuvem também requerem a listas brancas de endereço IP da máquina aceder às mesmas. Certifique-se de que o endereço IP do computador gateway está na lista de permissões / configuradas na firewall adequadamente.

Arquivos de dados de nuvem seguintes requerem a listas brancas de endereço IP da máquina do gateway. Alguns destes arquivos de dados, por predefinição, não precisar de adicionar à lista branca do endereço IP. 

- [Base de Dados SQL do Azure](../../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md#create-a-server-level-firewall-rule-in-the-azure-portal)
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta:** o Gateway pode ser partilhado entre fábricas de dados diferentes?
**Resposta:** não suportam esta funcionalidade ainda. Estamos ativamente a trabalhar no mesmo.

**Pergunta:** quais são os requisitos de porta para o gateway funcionar?
**Resposta:** baseado em HTTP ligações para abrir o internet faz com que o Gateway. O **portas de saída 443 e 80** tem de ser aberto para o gateway tornar esta ligação. Abra **8050 de porta de entrada** apenas no nível do computador (não a nível de firewall da empresa) para a aplicação Gestor de credenciais. Se a SQL Database do Azure ou do Azure SQL Data Warehouse é utilizado como origem / destino, então poderá ter de abrir **1433** , bem como a porta. Para obter mais informações, consulte [configurações e os endereços IP a listas brancas Firewall](#firewall-configurations-and-whitelisting-ip-address-of gateway) secção. 

**Pergunta:** quais são os requisitos de certificado para o Gateway?
**Resposta:** gateway atual requer um certificado que é utilizado pela aplicação de Gestor de credenciais para definir em segurança as credenciais do arquivo de dados. Este certificado é um certificado autoassinado criado e configurado, o programa de configuração do gateway. Pode utilizar o seu próprio TLS / SSL em vez disso, o certificado. Para obter mais informações, consulte [clique-a aplicação de Gestor de credenciais uma vez](#click-once-credentials-manager-app) secção. 

## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o desempenho de atividade de cópia, consulte [copiar guia Otimização e de desempenho de atividade](data-factory-copy-activity-performance.md).

 
