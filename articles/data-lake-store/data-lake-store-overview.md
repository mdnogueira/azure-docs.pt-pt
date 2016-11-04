---
title: Descrição geral do Azure Data Lake Store | Microsoft Docs
description: Compreender o que é o Azure Data Lake Store e qual a sua importância em relação a outros arquivos de dados
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/02/2016
ms.author: nitinme

---
# Descrição geral do Azure Data Lake Store
O Azure Data Lake Store é um repositório de hiper escala a nível da empresa para cargas de trabalho de análise de macrodados. O Azure Data Lake permite-lhe capturar dados de qualquer tamanho, tipo e velocidade de ingestão num único local para análises exploratórias e operacionais.

> [!TIP]
> Utilize o [percurso de aprendizagem do Data Lake Store](https://azure.microsoft.com/documentation/learning-paths/data-lake-store-self-guided-training/) para começar a explorar o serviço de Azure Data Lake Store.
> 
> 

O Azure Data Lake Store pode ser acedido a partir do Hadoop (disponível com o cluster de HDInsight) utilizando as API REST compatíveis com WebHDFS. Foi concebido especificamente para ativar a análise nos dados armazenados e está otimizado para desempenho para cenários de análise de dados. Fácil de utilizar, inclui todas as funcionalidades de nível empresarial (segurança, capacidade de gestão, escalabilidade, fiabilidade e disponibilidade), essenciais para casos de utilização empresarial reais.

![Azure Data Lake](./media/data-lake-store-overview/data-lake-store-concept.png)

Seguem-se algumas das principais funcionalidades do Azure Data Lake.

### Incorporado para o Hadoop
O Azure Data Lake Store é um sistema de ficheiros Apache Hadoop compatível com o Sistema de Ficheiros Distribuído Hadoop (HDFS) e funciona com o ecossistema do Hadoop.  As aplicações ou serviços do HDInsight que utilizam a API de WebHDFS podem ser facilmente integrados com o Data Lake Store. O Data Lake Store também expõe uma interface REST compatível com WebHDFS para aplicações

Os dados armazenados no Data Lake Store podem ser facilmente analisados utilizando as estruturas de análise de Hadoop, tais como MapReduce ou Hive. Os clusters do Microsoft Azure HDInsight podem ser aprovisionados e configurados para aceder diretamente aos dados armazenados no Data Lake Store.

### Armazenamento ilimitado, ficheiros petabyte
O Azure Data Lake Store fornece armazenamento ilimitado e é adequado para armazenar diversos dados para análise. Não tem quaisquer limites de tamanhos de conta, de ficheiros ou de volume de dados que podem ser armazenados num data lake. Os ficheiros individuais podem variar entre kilobytes e petabytes em termos de tamanho, sendo uma escolha ideal para armazenar qualquer tipo de dados. Os dados são armazenados de maneira duradoura, ao criar várias cópias e não existe limite de tempo para o armazenamento dos dados no data lake.

### Desempenho otimizado para análise de macrodados
O Azure Data Lake Store foi concebido para a execução de sistemas de análise de grande escala que necessitam de débito intenso para consultar e analisar grandes quantidades de dados. O data lake propaga partes de um ficheiro ao longo de um número de servidores de armazenamento individuais. Isto melhora o débito de leitura ao ler o ficheiro em paralelo para efetuar análise de dados.

### Pronto para empresas: elevada disponibilidade e segurança
O Azure Data Lake Store fornece disponibilidade e fiabilidade de norma da indústria. Os recursos de dados são armazenados de maneira duradoura, ao fazer cópias redundantes para proteger contra quaisquer falhas inesperadas. As empresas podem utilizar o Azure Data Lake nas respetivas soluções como uma parte importante da respetiva plataforma de dados.

O Data Lake Store também fornece segurança de nível empresarial para os dados armazenados. Para obter mais informações, consulte o artigo [Proteger dados no Azure Data Lake Store](#DataLakeStoreSecurity).

### Todos os Dados
O Azure Data Lake Store pode armazenar quaisquer dados no formato nativo, tal como está, sem necessidade de qualquer transformações anteriores. Com o Data Lake Store não é necessário um esquema antes de os dados serem carregados, a estrutura de análise individual interpreta os dados e define um esquema durante a análise. A capacidade para armazenar ficheiros de formatos e tamanhos arbitrários permite Data Lake Store processar dados não estruturados, semi-estruturados e estruturados.

Os contentores do Azure Data Lake Store para dados são essencialmente pastas e ficheiros. Utiliza os dados armazenados através do SDK, do Portal do Azure e do Azure Powershell. Desde que coloque os dados num arquivo utilizando estas interfaces e utilize os contentores adequados, pode armazenar qualquer tipo de dados. O Data Lake Store não efetua qualquer processamento especial de dados com base no tipo de dados que armazena.

## <a name="DataLakeStoreSecurity"></a>Proteger dados no Azure Data Lake Store
O Azure Data Lake Store utiliza o Azure Active Directory para autenticação e listas de controlo de acesso (ACL) para gerir o acesso aos seus dados.

| Funcionalidade | Descrição |
| --- | --- |
| Autenticação |O Azure Data Lake Store integra-se com o Azure Active Directory (AAD) para gestão de identidade e de acesso de todos os dados armazenados no Azure Data Lake Store. Como resultado de integração, o Azure Data Lake a partir de todas as funcionalidades do AAD, incluindo autenticação multifator, acesso condicional, controlo de acesso baseado em funções, monitorização de utilização de aplicações, monitorização de segurança e alertas, etc. O Azure Data Lake Store suporta o protocolo de OAuth 2.0 para a autenticação com a interface REST. |
| Controlo de acesso |O Azure Data Lake Store fornece controlo de acesso ao suportar permissões do estilo POSIX expostas pelo protocolo WebHDFS. Na versão atual, os ACLs podem ser ativados na pasta raíz, subpastas, bem como em ficheiros individuais. Os ACLs que aplicar à pasta raiz também serão aplicáveis a todas as pastas/ficheiros subordinados. |

Pretende obter mais informações sobre como proteger dados no Data Lake Store. Siga as ligações abaixo.

* Para obter instruções sobre como proteger os dados no Data Lake Store, consulte o artigo [Proteger dados no Azure Data Lake Store](data-lake-store-secure-data.md).
* Prefere vídeos? [Assista a este vídeo](https://mix.office.com/watch/1q2mgzh9nn5lx) sobre como proteger os dados armazenados no Data Lake Store.

## Aplicações compatíveis com o Azure Data Lake Store
O Azure Data Lake Store é compatível com a maioria dos componentes de origem do ecossistema do Hadoop. Também é integrado corretamente com outros serviços do Azure. Isto faz com que o Data Lake Store seja a opção perfeita para as suas necessidades de armazenamento de dados. Siga as ligações abaixo para obter mais informações sobre como o Data Lake Store pode ser utilizado tanto com componentes de código aberto, bem como outros serviços do Azure.

* Consulte o artigo [Aplicações e serviços compatíveis com o Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md) para obter uma lista de aplicações de código aberto interoperáveis com o Data Lake Store.
* Consulte o artigo [Integração com outros serviços do Azure](data-lake-store-integrate-with-other-services.md) para compreender como o Data Lake Store pode ser utilizado com outros serviços do Azure para ativar uma gama mais vasta de cenários.
* Consulte o artigo [Cenários para utilizar o Data Lake Store](data-lake-store-data-scenarios.md) para saber como utilizar o Data Lake Store em cenários, por exemplo, a ingestão de dados, processamento de dados, transferir dados e visualizar dados.

## O que é o sistema de ficheiros do Azure Data Lake Store (adl: / /)?
O Data Lake Store pode ser acedido através do novo sistema de ficheiros, o AzureDataLakeFilesystem (adl: / /), em ambientes do Hadoop (disponíveis com o cluster de HDInsight). As aplicações e serviços que utilizam adl: / / são capazes de tirar partido da otimização do desempenho que não estão atualmente disponíveis no WebHDFS. Como consequência, o Data Lake Store dá-lhe a flexibilidade de beneficiar do melhor desempenho com a opção recomendada de utilização adl: / / ou manter o código existente ao continuar a utilizar a API WebHDFS diretamente. O Azure HDInsight tira total partido do AzureDataLakeFilesystem para proporcionar o melhor desempenho no Data Lake Store.

Pode aceder aos seus dados no Data Lake Store através de `adl://<data_lake_store_name>.azuredatalakestore.net`. Para obter mais informações sobre como aceder aos dados no Data Lake Store, consulte o artigo [Ver as propriedades dos dados armazenados](data-lake-store-get-started-portal.md#properties)

## Como começo a utilizar o Azure Data Lake Store?
Consulte o artigo [Introdução ao Data Lake Store através do Portal do Azure](data-lake-store-get-started-portal.md), sobre como aprovisionar um Data Lake Store através do Portal do Azure. Depois de ter aprovisionado o Azure Data Lake, pode aprender a utilizar as ofertas de macrodados, tais como o Data Lake Analytics ou o Azure HDInsight com o Data Lake Store. Também pode criar uma aplicação .NET para criar uma conta do Azure Data Lake Store e efetuar operações como carregamento de dados, transferir dados, etc.

* [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Introdução ao Azure Data Lake Store com SDK .NET](data-lake-store-get-started-net-sdk.md)

## Vídeos do Data Lake Store
Se preferir assistir a vídeos para obter mais informações, o Data Lake Store fornece vídeos através de funcionalidades.

* [Criar uma Conta do Azure Data Lake Store](https://mix.office.com/watch/1k1cycy4l4gen)
* [Utilize o Explorador de Dados para Gerir Dados no Azure Data Lake Store](https://mix.office.com/watch/icletrxrh6pc)
* [Ligar o Azure Data Lake Analytics ao Azure Data Lake Store](https://mix.office.com/watch/qwji0dc9rx9k)
* [Aceder ao Azure Data Lake Store através do Data Lake Analytics](https://mix.office.com/watch/1n0s45up381a8)
* [Ligar o Azure HDInsight ao Azure Data Lake Store](https://mix.office.com/watch/l93xri2yhtp2)
* [Aceder ao Azure Data Lake Store através do Hive e do Pig](https://mix.office.com/watch/1n9g5w0fiqv1q)
* [Utilizar o DistCp (Cópia Distribuída do Hadoop) para copiar dados de e para o Azure Data Lake Store](https://mix.office.com/watch/1liuojvdx6sie)
* [Utilizar o Apache Sqoop para mover dados entre origens relacionais e o Azure Data Lake Store](https://mix.office.com/watch/1butcdjxmu114)
* [Orquestração de Dados com o Azure Data Factory para o Azure Data Lake Store](https://mix.office.com/watch/1oa7le7t2u4ka)
* [Proteger Dados no Azure Data Lake Store](https://mix.office.com/watch/1q2mgzh9nn5lx)

<!--HONumber=Sep16_HO3-->


