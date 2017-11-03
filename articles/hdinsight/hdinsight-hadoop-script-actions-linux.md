---
title: "Script de desenvolvimento de ações com o HDInsight baseado em Linux - Azure | Microsoft Docs"
description: "Saiba como utilizar os scripts de Bash para personalizar clusters do HDInsight baseado em Linux. A funcionalidade de ação de script do HDInsight permite-lhe executar scripts durante ou após a criação do cluster. Scripts podem ser utilizados para alterar as definições de configuração de cluster ou instalar software adicional."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: larryfr
ms.openlocfilehash: 0cef360de3b7a9be01536b0ebe90769c89e7c432
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="script-action-development-with-hdinsight"></a>Desenvolvimento de ações de script com o HDInsight

Saiba como personalizar o cluster do HDInsight utilizando scripts de Bash. Ações de script são uma forma de personalizar HDInsight durante ou após a criação do cluster.

> [!IMPORTANT]
> Os passos neste documento exigem um cluster do HDInsight que utiliza o Linux. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows).

## <a name="what-are-script-actions"></a>Quais são as ações de script

Ações de script são scripts de Bash Azure é executado em nós de cluster para disponibilizar as alterações de configuração ou instalar software. Uma ação de script é executada como raiz e fornece direitos de acesso total a nós do cluster.

Ações de script podem ser aplicadas através os seguintes métodos:

| Utilize este método para aplicar um script... | Durante a criação de cluster... | Num cluster em execução... |
| --- |:---:|:---:|
| Portal do Azure |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| CLI do Azure |&nbsp; |✓ |
| SDK de .NET do HDInsight |✓ |✓ |
| Modelo Azure Resource Manager |✓ |&nbsp; |

Para obter mais informações sobre como utilizar estes métodos para aplicar ações de script, consulte [HDInsight personalizar clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Melhores práticas para o desenvolvimento do script

Quando desenvolver um script personalizado para um cluster do HDInsight, existem vários procedimentos recomendados para tenha em atenção:

* [A versão do Hadoop de destino](#bPS1)
* [A versão do SO de destino](#bps10)
* [Fornecer estáveis ligações para recursos de script](#bPS2)
* [Utilizar recursos previamente compilados](#bPS4)
* [Certifique-se de que o script de personalização do cluster idempotent](#bPS3)
* [Certifique-se de elevada disponibilidade da arquitetura do cluster](#bPS5)
* [Configurar as componentes personalizadas para utilizar o Blob storage do Azure](#bPS6)
* [Escrever informações de STDOUT e STDERR](#bPS7)
* [Guardar ficheiros como ASCII com endings de linha de LF](#bps8)
* [Utilize a lógica de repetição para recuperar de erros transitórios](#bps9)

> [!IMPORTANT]
> Ações de script tem de concluir dentro de 60 minutos ou o processo de falha. Durante o aprovisionamento de nó, o script é executado simultaneamente com outros processos de instalação e configuração. Disputa pelos recursos, tais como a largura de banda de CPU, tempo ou de rede pode fazer com que o script a demorar mais tempo a concluir do que-lo no seu ambiente de desenvolvimento.

### <a name="bPS1"></a>A versão do Hadoop de destino

Versões diferentes do HDInsight têm versões diferentes dos serviços de Hadoop e componentes instalados. Se o script espera uma versão específica de um serviço ou componente, só deve utilizar o script com a versão do HDInsight que inclui os componentes necessários. Pode encontrar informações sobre versões de componentes incluídos com o HDInsight com o [controlo de versões do HDInsight componente](hdinsight-component-versioning.md) documento.

### <a name="bps10"></a>A versão do SO de destino

HDInsight baseado em Linux baseia-se a distribuição de Ubuntu Linux. Versões diferentes do HDInsight baseiam-se em diferentes versões do Ubuntu, que pode alterar a forma como se comporta seu script. Por exemplo, o HDInsight 3.4 e anterior baseiam-se em versões do Ubuntu que utilizam Upstart. Ubuntu 16.04, que utiliza Systemd baseiam versões 3.5 e superiores. Systemd e Upstart dependem comandos diferentes, pelo que o seu script deve ser escrito para trabalhar com ambos.

Outra diferença importante entre HDInsight 3.4 e 3.5 é que `JAVA_HOME` agora aponta para Java 8.

Pode verificar a versão do SO utilizando `lsb_release`. O código seguinte demonstra como determinar se o script está em execução no Ubuntu 14 ou 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Pode encontrar o script completo que contém estes fragmentos em https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Para a versão do Ubuntu que é utilizado pelo HDInsight, consulte o [versão do componente de HDInsight](hdinsight-component-versioning.md) documento.

Para compreender as diferenças entre Systemd e Upstart, consulte [Systemd para utilizadores Upstart](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Fornecer estáveis ligações para recursos de script

O script e de recursos associados têm de permanecer disponíveis ao longo da duração do cluster. Estes recursos são necessários se são adicionados novos nós ao cluster durante operações de dimensionamento.

A melhor prática é transferir e arquivar tudo numa conta do Storage do Azure na sua subscrição.

> [!IMPORTANT]
> A conta de armazenamento utilizada tem de ser a conta de armazenamento predefinido para o cluster ou um contentor público só de leitura em qualquer outra conta de armazenamento.

Por exemplo, os exemplos fornecidos pela Microsoft são armazenados no [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) conta de armazenamento. Este é um contentor público só de leitura mantido pela equipa do HDInsight.

### <a name="bPS4"></a>Utilizar recursos previamente compilados

Para reduzir o tempo que demora a executar o script, evite operações compilam recursos a partir do código de origem. Por exemplo, a pré-compilar recursos e armazená-las num blob Storage do Azure de conta no mesmo centro de dados do HDInsight.

### <a name="bPS3"></a>Certifique-se de que o script de personalização do cluster idempotent

Scripts tem de ser idempotent. Se o script é executado múltiplas vezes, este deverá devolver o cluster para o mesmo estado sempre.

Por exemplo, um script que modifica os ficheiros de configuração deve não adicionar as entradas duplicadas se executada várias vezes.

### <a name="bPS5"></a>Certifique-se de elevada disponibilidade da arquitetura do cluster

Clusters do HDInsight baseado em Linux fornecem dois nós principais que estão ativos do cluster e executam ações de script em ambos os nós. Se os componentes a que instalar esperam apenas um nó principal, não instale os componentes em ambos os nós principais.

> [!IMPORTANT]
> Serviços fornecidos como parte do HDInsight são concebidos para a ativação pós-falha entre os dois nós principais, conforme necessário. Esta funcionalidade não é expandida para componentes personalizados instalados através de ações de script. Se necessitar de elevada disponibilidade de componentes personalizados, tem de implementar o seus próprios mecanismo de ativação pós-falha.

### <a name="bPS6"></a>Configurar as componentes personalizadas para utilizar o Blob storage do Azure

Os componentes que instalar num cluster poderão ter uma configuração predefinida, que utiliza armazenamento distribuído ficheiro sistema Hadoop (HDFS). O HDInsight utiliza o armazenamento do Azure ou para o Data Lake Store como armazenamento de predefinido. Ambos fornecem um sistema de ficheiros compatíveis do HDFS que mantém os dados, mesmo se o cluster é eliminado. Terá de configurar os componentes a instalar utilizar WASB ou ADL em vez do HDFS.

Na maioria das operações, não é necessário especificar o sistema de ficheiros. Por exemplo, o seguinte copia o ficheiro giraph examples.jar do sistema de ficheiros local para o armazenamento de cluster:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

Neste exemplo, o `hdfs` comando transparente utiliza o armazenamento de cluster predefinido. Para algumas operações, poderá ter de especificar o URI. Por exemplo, `adl:///example/jars` para o Data Lake Store ou `wasb:///example/jars` para armazenamento do Azure.

### <a name="bPS7"></a>Escrever informações de STDOUT e STDERR

HDInsight regista a saída do script que é escrita STDOUT e STDERR. Pode ver estas informações utilizando a IU da web do Ambari.

> [!NOTE]
> Ambari só está disponível se o cluster é criado com êxito. Se utilizar uma ação de script durante a criação do cluster e a criação falhar, consulte a secção de resolução de problemas [clusters do HDInsight de personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) para outras formas de aceder a informações com sessão iniciada.

A maioria dos utilitários e pacotes de instalação já escrevem informações de STDOUT e STDERR, no entanto, pode querer adicionar registo adicional. Para enviar texto para STDOUT, utilize `echo`. Por exemplo:

```bash
echo "Getting ready to install Foo"
```

Por predefinição, `echo` envia a cadeia para STDOUT. Para direcionar o mesmo em STDERR, adicione `>&2` antes `echo`. Por exemplo:

```bash
>&2 echo "An error occurred installing Foo"
```

Isto redireciona informações escritas STDOUT em STDERR (2). Para obter mais informações sobre o redirecionamento de e/s, consulte [http://www.tldp.org/LDP/abs/html/io-redirection.html](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Para obter mais informações sobre a visualização de informações registadas pelas ações de script, consulte [clusters do HDInsight de personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a>Guardar ficheiros como ASCII com endings de linha de LF

Scripts de bash devem ser armazenados em formato ASCII, com linhas terminadas por LF. Os ficheiros que são armazenados como UTF-8 ou utilizam CRLF como o fim da linha poderão falhar com o erro seguinte:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a>Utilize a lógica de repetição para recuperar de erros transitórios

Quando a transferência de ficheiros, instalando pacotes com apt get ou outras ações que transmitem dados através da internet, a ação poderão falhar devido a erros de rede transitórios. Por exemplo, o recurso remoto que estão a comunicar com pode estar no processo de falhar ao longo de um nó de cópia de segurança.

Para tornar o seu script resiliente para erros transitórios, pode implementar a lógica de repetição. A função seguinte demonstra como implementar a lógica de repetição. Repetir a operação de três vezes antes de falhar.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Os exemplos seguintes demonstram como utilizar esta função.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helpermethods"></a>Métodos de programa auxiliar para scripts personalizados

Métodos de programa auxiliar de ação de script são utilitários que pode utilizar ao escrever scripts personalizados. Estes métodos estão contidos no[https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) script. Utilize o seguinte procedimento para transferir e utilizá-los como parte do seu script:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Os seguintes programas de ajuda disponíveis para utilização no seu script:

| Utilização de programa auxiliar | Descrição |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Transfere um ficheiro da origem de URI para o caminho de ficheiro especificado. Por predefinição, este não substituir ficheiro existente. |
| `untar_file TARFILE DESTDIR` |Extrai um ficheiro de tar (utilizando `-xf`) para o diretório de destino. |
| `test_is_headnode` |Se executou num nó principal do cluster, 1 retorno; caso contrário, 0. |
| `test_is_datanode` |Se o nó atual é um nó de dados (trabalho), devolver um 1; caso contrário, 0. |
| `test_is_first_datanode` |Se o nó atual é os primeiro dados (trabalho) nó (denominado workernode0) devolver um 1; caso contrário, 0. |
| `get_headnodes` |Devolva o nome de domínio completamente qualificado do headnodes no cluster. Os nomes são delimitados por vírgulas. Uma cadeia vazia, é devolvida com o erro. |
| `get_primary_headnode` |Obtém o nome de domínio completamente qualificado do headnode primário. Uma cadeia vazia, é devolvida com o erro. |
| `get_secondary_headnode` |Obtém o nome de domínio completamente qualificado do headnode secundário. Uma cadeia vazia, é devolvida com o erro. |
| `get_primary_headnode_number` |Obtém o sufixo numérico do headnode primário. Uma cadeia vazia, é devolvida com o erro. |
| `get_secondary_headnode_number` |Obtém o sufixo numérico do headnode secundário. Uma cadeia vazia, é devolvida com o erro. |

## <a name="commonusage"></a>Padrões de utilização comuns

Esta secção fornece orientação na implementação de algumas das comuns padrões de utilização que possam ser executadas ao escrever o seu próprio script personalizado.

### <a name="passing-parameters-to-a-script"></a>Os parâmetros a transmitir para um script

Em alguns casos, o script pode necessitar de parâmetros. Por exemplo, poderá ter a palavra-passe de administrador para o cluster, ao utilizar a API de REST do Ambari.

Os parâmetros transmitidos para o script são conhecidos como *parâmetros posicionais*e são atribuídos ao `$1` para o primeiro parâmetro, `$2` para o segundo e, de modo-no. `$0`contém o nome do próprio script.

Os valores transmitidos para o script como parâmetros devem ser colocadas entre por plicas ('). Se o fizer, assegura que o valor transmitido é tratado como um literal.

### <a name="setting-environment-variables"></a>Definir variáveis de ambiente

Definir uma variável de ambiente é executada a seguinte instrução:

    VARIABLENAME=value

Onde VARIABLENAME é o nome da variável. Para aceder à variável, utilize `$VARIABLENAME`. Por exemplo, para atribuir um valor fornecido por um parâmetro posicional como uma variável de ambiente com o nome de palavra-passe, teria de utilizar a seguinte instrução:

    PASSWORD=$1

Em seguida, utilizar subsequente acesso às informações `$PASSWORD`.

Predefinir o script de variáveis de ambiente só existem no âmbito do script. Em alguns casos, poderá ter de adicionar as variáveis de ambiente em todo o sistema que serão mantidas após terminar o script. Para adicionar as variáveis de ambiente em todo o sistema, adicionar a variável `/etc/environment`. Por exemplo, a seguinte instrução adiciona `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Acesso a localizações onde estão armazenados os scripts personalizados

Scripts utilizados para personalizar um cluster tem de ser armazenadas de uma das seguintes localizações:

* Um __conta de armazenamento do Azure__ que está associado ao cluster.

* Um __conta de armazenamento adicional__ associados com o cluster.

* A __URI legível publicamente__. Por exemplo, um URL para dados armazenados no OneDrive, Dropbox ou outros ficheiros que aloja o serviço.

* Um __conta do Azure Data Lake Store__ que está associado ao cluster do HDInsight. Para obter mais informações sobre como utilizar o Azure Data Lake Store com o HDInsight, consulte [criar um cluster do HDInsight com o Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > O HDInsight utiliza para aceder ao arquivo Data Lake do principal de serviço tem de ter acesso de leitura para o script.

Recursos utilizados pelo script também tem de ser publicamente disponíveis.

Armazenar os ficheiros numa conta do Storage do Azure ou do Azure Data Lake Store fornece acesso rápido, como ambos dentro da rede do Azure.

> [!NOTE]
> O formato URI utilizado para referenciar o script difere consoante o serviço que está a ser utilizado. Para contas do storage associadas com o cluster do HDInsight, utilize `wasb://` ou `wasbs://`. Para os URIs legíveis publicamente, utilize `http://` ou `https://`. Para o Data Lake Store, utilize `adl://`.

### <a name="checking-the-operating-system-version"></a>A verificar a versão do sistema operativo

Versões diferentes do HDInsight dependem de versões específicas do Ubuntu. Poderão existir diferenças entre versões de SO que tem de verificar no script. Por exemplo, terá de instalar um binário que está associado à versão do Ubuntu.

Para verificar a versão do SO, utilize `lsb_release`. Por exemplo, o script seguinte demonstra como fazer referência um ficheiro de tar específico, dependendo da versão do SO:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Lista de verificação para implementar uma ação de script

Eis os passos que pegámos quando preparar para implementar estes scripts:

* Colocar os ficheiros que contêm os scripts personalizados num local que esteja acessível a nós do cluster durante a implementação. Por exemplo, o armazenamento de predefinido para o cluster. Ficheiros também podem ser armazenados nos serviços de alojamento legíveis publicamente.
* Certifique-se de que o script é impotent. Se o fizer, permite que o script ser executada várias vezes no mesmo nó.
* Utilize um diretório de ficheiro temporário /tmp para manter os ficheiros transferidos utilizados pelos scripts e, em seguida, limpe-los depois de tem executado scripts.
* Se as definições de nível de SO ou ficheiros de configuração de serviço do Hadoop são alterados, pode pretender reinicie os serviços do HDInsight.

## <a name="runScriptAction"></a>Como executar uma ação de script

Pode utilizar ações de script ao personalizar clusters do HDInsight utilizando os seguintes métodos:

* Portal do Azure
* Azure PowerShell
* Modelos do Azure Resource Manager
* O SDK .NET do HDInsight.

Para obter mais informações sobre como utilizar cada método, consulte [como utilizar a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Exemplos de script personalizado

A Microsoft fornece scripts de exemplo para instalar componentes de um cluster do HDInsight. Consulte as ligações seguintes para obter mais ações de script de exemplo.

* [Instalar e utilizar Hue nos clusters do HDInsight](hdinsight-hadoop-hue-linux.md)
* [Instalar e utilizar Solr nos clusters do HDInsight](hdinsight-hadoop-solr-install-linux.md)
* [Instalar e utilizar Giraph nos clusters do HDInsight](hdinsight-hadoop-giraph-install-linux.md)
* [Instalar ou atualizar Mono nos clusters do HDInsight](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Resolução de problemas

Seguem-se os erros que poderá encontrar ao utilizar scripts que criou:

**Erro**: `$'\r': command not found`. Por vezes, seguido de `syntax error: unexpected end of file`.

*Causa*: Este erro é causado quando terminar de linhas de um script com CRLF. Sistemas UNIX esperam apenas LF como o fim de linha.

Este problema ocorre normalmente quando o script foi criado no ambiente do Windows, como CRLF é uma linha comuns final para muitos editores de texto no Windows.

*Resolução*: se for uma opção no seu editor de texto, selecione o formato Unix ou LF para terminar a linha. Também pode utilizar os seguintes comandos num sistema Unix para alterar o CRLF para um LF:

> [!NOTE]
> Os comandos seguintes são quase equivalentes em que o se deve ser alterado endings de linha CRLF para LF. Selecione um com base no utilitários disponíveis no seu sistema.

| Comando | Notas |
| --- | --- |
| `unix2dos -b INFILE` |O ficheiro original é feito com uma. Extensão BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE contém uma versão com apenas os endings LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modifica o ficheiro diretamente |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE contém uma versão com apenas os endings LF. |

**Erro**: `line 1: #!/usr/bin/env: No such file or directory`.

*Causa*: Este erro ocorre quando o script foi guardado como UTF-8 com uma marca de ordem de bytes (LM).

*Resolução*: guardar o ficheiro ou como ASCII ou UTF-8 sem um LM. Também pode utilizar o seguinte comando num sistema Linux ou Unix para criar um ficheiro sem a LM:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Substitua `INFILE` com o ficheiro que contém o LM. `OUTFILE`deve ser um novo nome de ficheiro, que contém o script do LM.

## <a name="seeAlso"></a>Passos seguintes

* Saiba como [clusters do HDInsight de personalizar através da ação de script](hdinsight-hadoop-customize-cluster-linux.md)
* Utilize o [referência do SDK .NET do HDInsight](https://msdn.microsoft.com/library/mt271028.aspx) para obter mais informações sobre como criar aplicações de .NET que gerem o HDInsight
* Utilize o [API de REST do HDInsight](https://msdn.microsoft.com/library/azure/mt622197.aspx) para aprender a utilizar REST para efetuar ações de gestão nos clusters do HDInsight.
