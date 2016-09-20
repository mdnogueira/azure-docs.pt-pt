<properties
   pageTitle="Introdução ao Data Lake Store utilizando a interface de linha de comandos entre plataformas | Microsoft Azure"
   description="Utilizar a linha de comandos entre plataformas para criar uma conta de Data Lake Store e executar operações básicas"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/13/2016"
   ms.author="nitinme"/>

# Introdução ao Azure Data Lake Store utilizando a Linha de comandos do Azure

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [SDK .NET](data-lake-store-get-started-net-sdk.md)
- [SDK Java](data-lake-store-get-started-java-sdk.md)
- [API REST](data-lake-store-get-started-rest-api.md)
- [CLI do Azure](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar a interface de linha de comandos do Azure para criar uma conta do Azure Data Lake Store e executar operações básicas, tais como criar pastas, carregar e transferir ficheiros de dados, eliminar a conta, entre outras. Para obter mais informações sobre o Data Lake Store, veja [Descrição geral do Data Lake Store](data-lake-store-overview.md).

A CLI do Azure está implementada no Node.js. Pode ser utilizado em qualquer plataforma que suporte Node.js, incluindo Windows, Mac e Linux. A CLI do Azure é open source. O código fonte é gerido no GitHub em <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Este artigo abrange apenas a utilização da CLI do Azure com o Data Lake Store. Para um guia geral sobre como utilizar a CLI do Azure, veja [Como utilizar a CLI do Azure] [azure-command-line-tools].


##Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte:

- **Uma subscrição do Azure**. Veja [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **CLI do Azure** -Veja [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração. Certifique-se de que reinicia o computador depois de instalar a CLI.

##Iniciar sessão na subscrição do Azure

Siga os passos documentados em [Ligar a uma subscrição do Azure a partir da Interface de Linha de Comandos do Azure (CLI do Azure)](../xplat-cli-connect.md) e ligue à sua subscrição utilizando o método de __início de sessão__.


## Criar uma conta do Azure Data Lake Store

Abra uma linha de comandos, shell ou sessão de terminal e execute os comandos seguintes.

1. Inicie sessão na subscrição do Azure:

        azure login

    Ser-lhe-á pedido para abrir uma página Web e introduzir um código de autenticação. Siga as instruções na página para iniciar sessão na sua subscrição do Azure.

2. Mude para o modo Azure Resource Manager utilizando o seguinte comando:

        azure config mode arm


3. Liste as subscrições do Azure para a sua conta.

        azure account list


4. Se tiver várias subscrições do Azure, utilize o seguinte comando para configurar a subscrição que os comandos da CLI do Azure irão utilizar:

        azure account set <subscriptionname>

5. Crie um novo grupo de recursos. No seguinte comando, forneça os valores de parâmetros que pretende utilizar.

        azure group create <resourceGroup> <location>

    Se o nome da localização contiver espaços, coloque-o entre aspas. Por exemplo, "EUA Leste 2".

5. Crie uma conta do Data Lake Store.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Criar pastas no Data Lake Store

Pode criar pastas na sua conta do Azure Data Lake Store para gerir e armazenar dados. Utilize o seguinte comando para criar uma pasta denominada "mynewfolder" na raiz do Data Lake Store.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Por exemplo:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Carregar dados para o Data Lake Store

Pode carregar os dados para o Data Lake Store diretamente no nível de raiz ou para uma pasta que criou na conta. Os fragmentos abaixo demonstram como carregar alguns dados de exemplo para a pasta (**mynewfolder**) que criou na secção anterior.

Se estiver à procura de alguns dados de exemplo para carregar, pode obter a pasta **Ambulance Data** a partir do [Repositório de Git do Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Transfira o ficheiro e armazene-o num diretório local no seu computador, como C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Por exemplo:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Listar ficheiros no Data Lake Store

Utilize o seguinte comando para listar os ficheiros numa conta do Data Lake Store.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Por exemplo:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

O resultado deve ser semelhante ao seguinte:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## Mudar o nome, transferir e eliminar dados do Data Lake Store

* **Para mudar o nome de um ficheiro**, utilize o seguinte comando:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Por exemplo:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Para transferir um ficheiro**, utilize o seguinte comando: Certifique-se de que o caminho de destino especificado já existe.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Por exemplo:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Para eliminar um ficheiro**, utilize o seguinte comando:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Por exemplo:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    Quando lhe for pedido, introduza **S** para eliminar o item.

## Ver a lista de controlo de acesso para uma pasta no Data Lake Store

Utilize o seguinte comando para ver as ACLs numa pasta do Data Lake Store. Na versão atual, as ACLs apenas podem ser definidos na raiz do Data Lake Store. Assim, o parâmetro de caminho abaixo será sempre a raiz (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Por exemplo:

    azure datalake store permissions show mynewdatalakestore /


## Eliminar a conta do Data Lake Store

Utilize o seguinte comando para eliminar uma conta do Data Lake Store.

    azure datalake store account delete <dataLakeStoreAccountName>

Por exemplo:

    azure datalake store account delete mynewdatalakestore

Quando lhe for pedido, introduza **S** para eliminar a conta.


## Passos seguintes

- [Proteger dados no Data Lake Store](data-lake-store-secure-data.md)
- [Utilizar o Azure Data Lake Analytics com o Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=sep16_HO2-->


