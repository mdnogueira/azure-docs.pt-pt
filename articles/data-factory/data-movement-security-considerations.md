---
title: "Considerações de segurança no Azure Data Factory | Microsoft Docs"
description: "Descreve a infraestrutura de básicos de segurança que utilizam serviços de movimento de dados no Azure Data Factory para proteger os dados."
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/24/2017
ms.author: abnarain
ms.openlocfilehash: a69f3770184d94c481c1b78f23efa9e9c4fb31fa
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="azure-data-factory---security-considerations-for-data-movement"></a>O Azure Data Factory - considerações de segurança para o movimento de dados
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 - GA](v1/data-factory-data-movement-security-considerations.md)
> * [Versão 2 - Pré-visualização](data-movement-security-considerations.md)

Este artigo descreve a infraestrutura de básicos de segurança que utilizam serviços de movimento de dados no Azure Data Factory para proteger os seus dados. Recursos de gestão do Azure Data Factory são criados numa infraestrutura de segurança do Azure e utilizam todas as medidas de segurança possível oferecidas pelo Azure.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [considerações de segurança de movimento de dados do Data Factory versão 1](v1/data-factory-data-movement-security-considerations.md).

Numa solução do Data Factory, pode criar um ou mais [pipelines](concepts-pipelines-activities.md) de dados. Os pipelines são agrupamentos lógicos de atividades que, em conjunto, realizam uma tarefa. Estes pipelines de residir na região onde foi criada a fábrica de dados. 

Apesar de fábrica de dados só está disponível em **EUA Leste**, **EUA Leste 2**, e **Europa Ocidental** regiões (versão 2 pré-visualização), o serviço de movimento de dados está disponível [global em várias regiões](concepts-integration-runtime.md#azure-ir). Se o serviço de movimento de dados ainda não está implementado nessa região, o serviço fábrica de dados garante que os dados deixe uma área geográfica / região, a menos que o serviço para utilizar uma região alternativa instruir explicitamente. 

O Azure Data Factory propriamente dito não armazena quaisquer dados, exceto as credenciais de serviço ligado para os arquivos de dados de nuvem, que são encriptados utilizando certificados. Permite-lhe criar fluxos de trabalho condicionados por dados para orquestrar o movimento dos dados entre [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) e o processamento de dados com [serviços de computação](compute-linked-services.md) noutras regiões ou num ambiente no local. Também permite-lhe monitorizar e gerir fluxos de trabalho utilizando SDKs e Monitor do Azure.

Movimento de dados utilizando o Azure Data Factory foi **certificadas** para:
-   [HIPAA/HITECH](https://www.microsoft.com/en-us/trustcenter/Compliance/HIPAA) 
-   [ISO/IEC 27001](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27001)  
-   [ISO/IEC 27018](https://www.microsoft.com/en-us/trustcenter/Compliance/ISO-IEC-27018)
-   [ESTRELA DE CSA](https://www.microsoft.com/en-us/trustcenter/Compliance/CSA-STAR-Certification)

Se estiver interessado na forma como o Azure protege a própria infraestrutura e de conformidade do Azure, visite o [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx). 

Neste artigo, vamos rever as considerações de segurança nos seguintes cenários de movimento de dados de duas: 

- **Cenário de nuvem**-neste cenário, a origem e de destino estão acessíveis publicamente através da internet. Estes incluem os serviços de armazenamento em nuvem gerido, como o Storage do Azure, Azure SQL Data Warehouse, SQL Database do Azure, Azure Data Lake Store, Amazon S3, Amazon Redshift, serviços SaaS, como o Salesforce e protocolos de web, como o FTP e OData. Pode encontrar uma lista completa das origens de dados suportadas [aqui](copy-activity-overview.md#supported-data-stores-and-formats).
- **Cenário híbrido**- neste cenário, a origem ou de destino é protegido por uma firewall ou dentro de uma rede empresarial no local ou os dados de arquivo está numa rede privada / virtual de rede (frequentemente de origem) e não se encontra acessível publicamente. Servidores de base de dados alojadas em máquinas virtuais também abrangidos por este cenário.

## <a name="cloud-scenarios"></a>Cenários de nuvem
###<a name="securing-data-store-credentials"></a>Proteger credenciais do arquivo de dados
- Armazene credenciais encriptado no store gerida do Azure Data Factory.

   O Azure Data Factory protege as credenciais do arquivo de dados por **encriptar** -los utilizando **certificados geridos pelo Microsoft**. Estes certificados rodam cada **dois anos** (que inclui a renovação do certificado e a migração de credenciais). Estas credenciais encriptados segura são armazenadas num **Storage do Azure é gerida pelos serviços de gestão do Azure Data Factory**. Para obter mais informações sobre a segurança de armazenamento do Azure, consulte [descrição geral de segurança de armazenamento do Azure](../security/security-storage-overview.md).
- Armazenar credenciais no Azure Key Vault 

   Agora, pode escolher armazenar credenciais do arquivo de dados no [Cofre de chaves do Azure](https://azure.microsoft.com/services/key-vault/), em seguida, permitir que o Azure Data Factory para obtê-lo durante a execução de uma atividade. Para obter mais informações, consulte [credencial de arquivo no Cofre de chaves do Azure](store-credentials-in-key-vault.md).

   > [!NOTE]
   > Atualmente, apenas [conector Dynamics](connector-dynamics-crm-office-365.md) suporte esta funcionalidade. 

### <a name="data-encryption-in-transit"></a>Encriptação de dados em trânsito
Se o arquivo de dados na nuvem suporta HTTPS ou TLS, todos os dados transferidos entre os serviços de movimento de dados no Data Factory e um arquivo de dados de nuvem são através de canal seguro, HTTPS ou TLS.

> [!NOTE]
> Todas as ligações ao **SQL Database do Azure** e **Azure SQL Data Warehouse** sempre exigir a encriptação (SSL/TLS) dados enquanto estes se encontram em trânsito para e da base de dados. Ao criar um pipeline com o JSON, adicione o **encriptação** propriedade e defina-o como **verdadeiro** no **cadeia de ligação**. Para **Storage do Azure**, pode utilizar **HTTPS** na cadeia de ligação.

### <a name="data-encryption-at-rest"></a>Encriptação de dados inativos
Alguns dados armazena suporte a encriptação de dados inativos. Sugerimos que ative o mecanismo de encriptação de dados para os arquivos de dados. 

#### <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Encriptação de dados transparente (TDE) no armazém de dados SQL do Azure ajuda-o com a proteger contra a ameaça de atividade maliciosa efetuando em tempo real encriptação e desencriptação de dados inativos. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [proteger uma base de dados no SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md).

#### <a name="azure-sql-database"></a>Base de Dados SQL do Azure
Base de dados SQL do Azure também suporta a encriptação de dados transparente (TDE), que ajuda com a proteger contra a ameaça de atividade maliciosa através de encriptação em tempo real e a desencriptação de dados sem necessidade de alterações à aplicação. Este comportamento é transparente para o cliente. Para obter mais informações, consulte [encriptação transparente de dados com a SQL Database do Azure](/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database). 

#### <a name="azure-data-lake-store"></a>Azure Data Lake Store
Azure Data Lake store também proporciona a encriptação dos dados armazenados na conta. Quando ativada, o arquivo Data Lake é automaticamente encripta os dados antes de a persistência e desencripta antes da obtenção, tornando-a transparente para o cliente de acesso aos dados. Para obter mais informações, consulte [segurança no Azure Data Lake Store](../data-lake-store/data-lake-store-security-overview.md). 

#### <a name="azure-blob-storage-and-azure-table-storage"></a>Armazenamento de Blobs do Azure e o Table Storage do Azure
Storage do Azure de armazenamento de BLOBs e tabelas do Azure suporta armazenamento serviço encriptação (SSE), que encripta os dados antes de a persistência para armazenamento e desencripta antes da obtenção automaticamente. Para obter mais informações, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../storage/common/storage-service-encryption.md).

#### <a name="amazon-s3"></a>Amazon S3
Amazon S3 suporta o cliente e o servidor de encriptação de dados inativos. Para obter mais informações, consulte [proteger utilizando a encriptação de dados](http://docs.aws.amazon.com/AmazonS3/latest/dev/UsingEncryption.html). Atualmente, a fábrica de dados não suporta Amazon S3 dentro de uma nuvem privada virtual (VPC).

#### <a name="amazon-redshift"></a>Amazon Redshift
Amazon Redshift suporta encriptação de cluster para dados inativos. Para obter mais informações, consulte [encriptação de base de dados do Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/mgmt/working-with-db-encryption.html). Atualmente, a fábrica de dados não suporta Amazon Redshift dentro de um VPC. 

#### <a name="salesforce"></a>Salesforce
Salesforce suporta a encriptação de plataforma de proteção que permite que a encriptação de ficheiros, os anexos, campos personalizados. Para obter mais informações, consulte [compreender o fluxo de autenticação do Web Server OAuth](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/intro_understanding_web_server_oauth_flow.htm).  

## <a name="hybrid-scenarios-using-self-hosted-integration-runtime"></a>Cenários híbridos (através da integração personalizada alojada runtime)
Cenários híbridos requerem integração personalizada alojada tempo de execução para ser instalado numa rede no local ou dentro de uma rede virtual (Azure) ou uma nuvem privada virtual (Amazon). O tempo de execução automática alojada integração tem de ser capaz de aceder aos arquivos de dados local. Para obter mais informações sobre o tempo de execução automática alojada integração, consulte [autoalojado integração runtime](create-self-hosted-integration-runtime.md). 

![canais de tempo de execução de integração personalizada alojada](media/data-movement-security-considerations/data-management-gateway-channels.png)

O **canal de comando** permite a comunicação entre os serviços de movimento de dados no Data Factory e integração personalizada alojada tempo de execução. A comunicação contém informações relacionadas com a atividade. O canal de dados é utilizado para transferir dados entre os arquivos de dados no local e arquivos de dados de nuvem.    

### <a name="on-premises-data-store-credentials"></a>Credenciais do arquivo de dados no local
As credenciais para os arquivos de dados no local são sempre encriptadas e armazenadas. -Pode optar por armazenados localmente no computador de tempo de execução integração personalizada alojada ou no Azure Data Factory geridos armazenamento (tal como as credenciais do arquivo de nuvem). 

1. Pode optar por **armazenar credenciais localmente**. Se pretende encriptar e armazenar credenciais localmente no tempo de execução automática alojada integração, siga os passos no [encriptar as credenciais no tempo de execução automática alojada integração](encrypt-credentials-self-hosted-integration-runtime.md). Todos os conectores de suportar esta opção. O tempo de execução de integração personalizada alojada utiliza o Windows [DPAPI](https://msdn.microsoft.com/library/ms995355.aspx) para encriptar os dados confidenciais / informações de credenciais. 

   Utilize **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** cmdlet para encriptar as credenciais do serviço ligado / encriptar confidenciais detalhes no serviço ligado. Em seguida, pode utilizar o JSON devolvido (com **EncryptedCredential** elemento o **connectionString**) para criar um serviço ligado por **Set-AzureRmDataFactoryV2LinkedSevrice**cmdlet.  

2. Se não utilizar **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** cmdlet como descrito no passo anterior e, em vez disso, utilize diretamente **conjunto AzureRmDataFactoryV2LinkedSevrice** cmdlet com a ligação cadeias / credenciais inline no JSON, em seguida, o serviço ligado será **encriptadas e armazenadas no Azure Data Factory geridos armazenamento**. As informações confidenciais ainda são encriptadas pelo certificado e estes certificados são geridos pela Microsoft.



#### <a name="ports-used-during-encrypting-linked-service-on-self-hosted-integration-runtime"></a>Portas utilizadas durante a encriptação do serviço ligado no tempo de execução de integração personalizada alojada
Por predefinição, o PowerShell utiliza a porta **8050** na máquina com o tempo de execução automática alojada integração para comunicação segura. Se necessário, esta porta pode ser alterada.  

![Porta HTTPS para o gateway](media/data-movement-security-considerations/https-port-for-gateway.png)

 


### <a name="encryption-in-transit"></a>Encriptação em trânsito
Todas as transferências de dados são através de canal seguro **HTTPS** e **TLS através de TCP** para impedir ataques man-in-the-middle durante a comunicação com os serviços do Azure.

Também pode utilizar [IPSec VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md) ou [Express Route](../expressroute/expressroute-introduction.md) para proteger ainda mais o canal de comunicação entre a rede no local e o Azure.

Rede virtual é uma representação lógica da rede na nuvem. Pode ligar uma rede no local à sua rede virtual do Azure (VNet) ao configurar a VPN IPSec (site a site) ou uma Expressroute (Peering privado)     

A tabela seguinte resume a rede e recomendações de configuração de tempo de execução automática alojada integração com base em diferentes combinações de origem e de destino localizações para o movimento de dados de híbrida.

| Origem      | Destino                              | Configuração da rede                    | Configuração de tempo de execução de integração                |
| ----------- | ---------------------------------------- | ---------------------------------------- | ---------------------------------------- |
| Local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | VPN IPSec (ponto a site ou site a site) | O tempo de execução automática alojada integração pode ser instalado no local ou num Azure virtual machine (VM) na VNet |
| Local | Máquinas virtuais e serviços em nuvem implementados em redes virtuais | ExpressRoute (Peering privado)           | O tempo de execução automática alojada integração pode ser instalado no local ou numa VM do Azure na VNet |
| Local | Serviços baseados no Azure que tenham um ponto final público | ExpressRoute (Peering público)            | O tempo de execução automática alojada integração tem de ser instalado no local |

As imagens seguintes mostram a utilização de integração personalizada alojada tempo de execução para mover dados entre uma base de dados no local e serviços do Azure com o expressroute e IPSec de VPN (com a rede Virtual):

**Expressroute:**

![Utilizar o Expressroute com o gateway](media/data-movement-security-considerations/express-route-for-gateway.png) 

**IPSec de VPN:**

![IPSec de VPN com o gateway](media/data-movement-security-considerations/ipsec-vpn-for-gateway.png)

### <a name="firewall-configurations-and-whitelisting-ip-address-self-hosted-integration-runtime"></a>Configurações de firewall e o endereço IP a listas brancas (integração personalizada alojada runtime)

#### <a name="firewall-requirements-for-on-premisesprivate-network"></a>Requisitos da firewall da rede no local/privada  
Numa empresa, uma **firewall empresarial** é executado no router central da organização. E, **firewall do Windows** é executado como um daemon no computador local em que o tempo de execução automática alojada integração está instalado. 

A tabela seguinte fornece **porta de saída** e requisitos de domínio para o **firewall empresarial**.

| Nomes de domínio                  | Portas de saída | Descrição                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443, 80        | Necessário para o tempo de execução automática alojada integração para ligar aos serviços de movimento de dados no Data Factory |
| `*.core.windows.net`          | 443            | Utilizada pelo runtime integração personalizada alojada para ligar à conta de armazenamento do Azure quando utiliza o [testado cópia](copy-activity-performance.md#staged-copy) funcionalidade. |
| `*.frontend.clouddatahub.net` | 443            | Necessário pelo tempo de execução automática alojada integração para ligar ao serviço do Azure Data Factory. |
| `*.database.windows.net`      | 1433           | (Opcional) necessárias quando o destino for SQL Database do Azure / do armazém de dados SQL do Azure. Utilize a funcionalidade de teste cópia para copiar dados para o Azure SQL da base de dados/Azure SQL Data Warehouse sem abrir a porta 1433. |
| `*.azuredatalakestore.net`    | 443            | (Opcional) necessárias quando o destino é o Azure Data Lake store |

> [!NOTE] 
> Poderá ter de gerir portas / domínios a listas brancas da firewall da empresa nível conforme exigido pelas origens de dados correspondentes. Esta tabela só utiliza a exemplos de SQL Database do Azure, o Azure SQL Data Warehouse, o Azure Data Lake Store.   

A tabela seguinte fornece **porta de entrada** requisitos para o **firewall do Windows**.

| Portas de entrada | Descrição                              |
| ------------- | ---------------------------------------- |
| 8050 (TCP)    | Necessário para o cmdlet do PowerShell encriptação conforme descrito em [encriptar as credenciais no tempo de execução automática alojada integração](encrypt-credentials-self-hosted-integration-runtime.md)/ credencial manager aplicação em segurança definir credenciais para os arquivos de dados no local no personalizada alojada tempo de execução de integração. |

![Requisitos de portas de gateway](media\data-movement-security-considerations/gateway-port-requirements.png) 

#### <a name="ip-configurationswhitelisting-in-data-store"></a>Configurações de IP/branca no arquivo de dados
Alguns arquivos de dados na nuvem também requerem a listas brancas de endereço IP da máquina aceder às mesmas. Certifique-se de que o endereço IP da máquina de tempo de execução automática alojada integração está na lista de permissões / configuradas na firewall adequadamente.

Arquivos de dados de nuvem seguintes requerem a listas brancas de endereço IP da máquina de tempo de execução automática alojada integração. Alguns destes arquivos de dados, por predefinição, não precisar de adicionar à lista branca do endereço IP. 

- [Base de Dados SQL do Azure](../sql-database/sql-database-firewall-configure.md) 
- [Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-provision.md)
- [Azure Data Lake Store](../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)
- [BD do Cosmos para o Azure](../cosmos-db/firewall-support.md)
- [Amazon Redshift](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Pergunta:** o tempo de execução automática alojada integração pode ser partilhado entre fábricas de dados diferentes?
**Resposta:** não suportam esta funcionalidade ainda. Estamos ativamente a trabalhar no mesmo.

**Pergunta:** quais são os requisitos de porta para o tempo de execução de integração personalizada alojada a funcionar?
**Resposta:** o tempo de execução automática alojada integração permite ligações baseado em HTTP para abrir o internet. O **portas de saída 443 e 80** tem de ser aberto para integração personalizada alojada tempo de execução para que esta ligação. Abra **8050 de porta de entrada** apenas no nível do computador (não a nível de firewall da empresa) para a aplicação Gestor de credenciais. Se a SQL Database do Azure ou do Azure SQL Data Warehouse é utilizado como origem / destino, então poderá ter de abrir **1433** , bem como a porta. Para obter mais informações, consulte [configurações e os endereços IP a listas brancas Firewall](#firewall-configurations-and-whitelisting-ip-address-of gateway) secção. 


## <a name="next-steps"></a>Passos seguintes
Para obter informações sobre o desempenho de atividade de cópia, consulte [copiar guia Otimização e de desempenho de atividade](copy-activity-performance.md).

 
