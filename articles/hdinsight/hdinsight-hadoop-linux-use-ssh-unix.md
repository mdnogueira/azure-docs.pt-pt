---
title: Utilizar SSH com Hadoop - Azure HDInsight | Microsoft Docs
description: "Pode aceder ao HDInsight através do Secure Shell (SSH). Este documento fornece informações sobre como ligar ao HDInsight através dos comandos ssh e scp dos clientes Windows, Linux, Unix ou macOS."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: hadoop commands in linux,hadoop linux commands,hadoop macos,ssh hadoop,ssh hadoop cluster
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.openlocfilehash: 23621c418663ee5b4ed83ab989663a882e7000bd
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="connect-to-hdinsight-hadoop-using-ssh"></a>Ligar ao HDInsight (Hadoop) através de SSH

Saiba como utilizar [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) para ligar de forma segura ao Hadoop no Azure HDInsight. 

O HDInsight pode utilizar o Linux (Ubuntu) como o sistema operativo dos nós dentro do cluster do Hadoop. A tabela seguinte contém as informações de portas e de endereços necessárias quando liga ao HDInsight baseado em Linux através de um cliente SSH:

| Endereço | Porta | Liga-se a... |
| ----- | ----- | ----- |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | Nó de extremidade (R Server no HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | Nó de extremidade (qualquer outro tipo de cluster, se existir um nó de extremidade) |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nó principal primário |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nó principal secundário |

> [!NOTE]
> Substitua `<edgenodename>` pelo nome do nó de extremidade.
>
> Substitua `<clustername>` pelo nome do cluster.
>
> Se o cluster contém um nó de extremidade, recomendamos __ligar sempre ao nó de extremidade__ através de SSH. Os nós principais alojam serviços que são fundamentais para o estado de funcionamento do Hadoop. O nó de extremidade executa apenas o que colocar no mesmo.
>
> Para obter mais informações sobre a utilização de nós de extremidade, veja [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight).

> [!TIP]
> Quando liga pela primeira vez ao HDInsight, o cliente SSH pode apresentar um aviso a indicar que não é possível estabelecer a autenticidade do anfitrião. Quando lhe for pedido selecione "sim" para adicionar o anfitrião à lista de servidores fidedignos do seu cliente SSH.
>
> Se anteriormente tiver ligado a um servidor com o mesmo nome, poderá receber um aviso a indicar que a chave de anfitrião armazenado não corresponde à chave de anfitrião do servidor. Veja a documentação para o cliente SSH sobre como remover a entrada existente para o nome do servidor.

## <a name="ssh-clients"></a>Clientes SSH

Os sistemas Linux, Unix e macOS fornecem os comandos `ssh` e `scp`. O cliente `ssh` é usado normalmente para criar uma sessão de linha de comandos remota com um sistema baseado em Unix ou Linux. O cliente `scp` é usado para copiar com segurança ficheiros entre o cliente e o sistema remoto.

O Microsoft Windows não instala nenhum cliente SSH por predefinição. Os clientes `ssh` e `scp` estão disponíveis para o Windows através dos seguintes pacotes:

* Cliente OpenSSH (Beta): na Fall Creators Update, aceda a __Settings__ (Definições) > __Apps & features__ (Aplicações e funcionalidades) > __Manage optional features__ (Gerir funcionalidades opcionais) > __Add a feature__ (Adicionar uma funcionalidade) e selecione __OpenSSH Client__ (Cliente SSH). 

    > [!NOTE]
    > Se os comandos `ssh` e `scp` não estiverem disponíveis no PowerShell depois de ativar esta funcionalidade, termine sessão e reinicie-a.

* [Bash no Ubuntu no Windows 10](https://msdn.microsoft.com/commandline/wsl/about): os comandos `ssh` e `scp` estão disponíveis através do Bash na linha de comandos do Windows.

* [Azure Cloud Shell](../cloud-shell/quickstart.md): o Cloud Shell fornece um ambiente de Bash no seu navegador e fornece `ssh`, `scp` e outros comandos comuns do Linux.

* [Git (https://git-scm.com/)](https://git-scm.com/): os comandos `ssh` e `scp` estão disponíveis através da linha de comandos GitBash.

Também existem vários clientes SSH gráficos, tais como [PuTTY (http://www.chiark.greenend.org.uk/~sgtatham/putty/)](http://www.chiark.greenend.org.uk/~sgtatham/putty/) e [MobaXterm (http://mobaxterm.mobatek.net/)](http://mobaxterm.mobatek.net/). Embora estes clientes possam ser utilizados para ligar ao HDInsight, o processo de ligação é diferente relativamente à utilização do utilitário `ssh`. Para obter mais informações, veja a documentação do cliente gráfico que está a utilizar.

## <a id="sshkey"></a>Autenticação: chaves SSH

As chaves SSH utilizam a [encriptação de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) para autenticar sessões SSH. As chaves SSH são mais seguras do que as palavras-passe e proporcionam uma forma fácil de proteger o acesso ao seu cluster do Hadoop.

Se a sua conta SSH for protegida com uma chave, o cliente tem de fornecer a chave privada correspondente quando se ligar:

* A maioria dos clientes podem ser configurados para utilizar uma __chave predefinida__. Por exemplo, o cliente `ssh` procura uma chave privada em `~/.ssh/id_rsa` nos ambientes Linux e Unix.

* Pode especificar o __caminho para uma chave privada__. Com o cliente `ssh`, o parâmetro `-i` é utilizado para especificar o caminho para a chave privada. Por exemplo, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se tiver __múltiplas chaves privadas__ para utilização com diferentes servidores, considere utilizar um utilitário como [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). O utilitário `ssh-agent` pode ser usado para selecionar automaticamente a chave a utilizar ao estabelecer uma sessão SSH.

> [!IMPORTANT]
>
> Se proteger a chave privada com uma frase de acesso, tem de introduzi-la quando utilizar a chave. Utilitários como `ssh-agent` podem colocar em cache a palavra-passe, para sua comodidade.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Utilize o comando `ssh-keygen` para criar ficheiros de chaves públicas e privadas. O comando seguinte gera um par de chaves RSA de 2048 bits que pode ser utilizado com o HDInsight:

    ssh-keygen -t rsa -b 2048

São-lhe pedidas informações durante o processo de criação de chaves. Por exemplo, onde estão armazenadas as chaves ou se pretende utilizar uma frase de acesso. Após a conclusão do processo, são criados dois ficheiros: uma chave pública e uma chave privada.

* A __chave pública__ é utilizada para criar um cluster do HDInsight. A chave pública tem a extensão `.pub`.

* A __chave privada__ é utilizada para autenticar o cliente no cluster do HDInsight.

> [!IMPORTANT]
> Pode utilizar uma frase de acesso para proteger as chaves. Uma frase de acesso é efetivamente uma palavra-passe na sua chave privada. Mesmo se alguém obtiver a sua chave privada, essa pessoa tem de ter a frase de acesso para poder utilizá-la.

### <a name="create-hdinsight-using-the-public-key"></a>Utilizar a chave pública para criar o HDInsight

| Método de criação | Como utilizar a chave pública |
| ------- | ------- |
| **Portal do Azure** | Desmarque __Utilizar a mesma palavra-passe de início de sessão do cluster__ e selecione __Chave Pública__ como o tipo de autenticação SSH. Por fim, selecione o ficheiro de chave pública ou cole os conteúdos de texto do ficheiro no campo __Chave pública SSH__.</br>![Caixa de diálogo Chave pública SSH na criação do cluster do HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| **Azure PowerShell** | Utilize o parâmetro `-SshPublicKey` do cmdlet `New-AzureRmHdinsightCluster` e transmita os conteúdos da chave pública como uma cadeia.|
| **CLI do Azure 1.0** | Utilize o parâmetro `--sshPublicKey` do comando `azure hdinsight cluster create` e transmita os conteúdos da chave pública como uma cadeia. |
| **Modelo do Resource Manager** | Para obter um exemplo de como utilizar chaves SSH com um modelo, veja [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Implementar o HDInsight no Linux com uma chave SSH). O elemento `publicKeys` no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) é utilizado para transmitir as chaves ao Azure ao criar o cluster. |

## <a id="sshpassword"></a>Autenticação: palavra-passe

As contas SSH podem ser protegidas através de palavra-passe. Quando utiliza SSH para se ligar ao HDInsight, é-lhe pedido que introduza a palavra-passe.

> [!WARNING]
> A Microsoft não recomenda utilizar a autenticação por palavra-passe para SSH. As palavras-passe podem ser descobertas e são vulneráveis a ataques de força bruta. Em vez disso, recomendamos que utilize [chaves SSH para a autenticação](#sshkey).

### <a name="create-hdinsight-using-a-password"></a>Utilizar uma palavra-passe para criar o HDInsight

| Método de criação | Como especificar a palavra-passe |
| --------------- | ---------------- |
| **Portal do Azure** | Por predefinição, a conta de utilizador SSH tem a mesma palavra-passe da conta de início de sessão do cluster. Para utilizar outra palavra-passe, desmarque __Utilizar a mesma palavra-passe de início de sessão do cluster__ e introduza a palavra-passe no campo __Palavra-passe SSH__.</br>![Caixa de diálogo Palavra-passe SSH na criação do cluster do HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| **Azure PowerShell** | Utilize o parâmetro `--SshCredential` do cmdlet `New-AzureRmHdinsightCluster` e transmita um objeto `PSCredential` que contenha o nome e a palavra-passe da conta de utilizador SSH. |
| **CLI do Azure 1.0** | Utilize o parâmetro `--sshPassword` do comando `azure hdinsight cluster create` e indique o valor da palavra-passe. |
| **Modelo do Resource Manager** | Para obter um exemplo de como utilizar a palavra-passe com um modelo, veja [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Implementar o HDInsight no Linux com uma palavra-passe SSH). O elemento `linuxOperatingSystemProfile` no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) é utilizado para transmitir o nome e a palavra-passe da conta SSH ao Azure ao criar o cluster.|

### <a name="change-the-ssh-password"></a>Alterar a palavra-passe SSH

Para obter informações sobre como alterar a palavra-passe da conta do utilizador SSH, veja a secção __Change passwords__ (Alterar palavras-passe) do documento [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) (Gerir o HDInsight).

## <a id="domainjoined"></a>Autenticação: HDInsight associado a um domínio

Se estiver a utilizar um __cluster do HDInsight associado a um domínio__, tem de utilizar o comando `kinit` depois de se ligar com SSH. Este comando pede-lhe um utilizador e uma palavra-passe de domínio e autentica a sua sessão com o domínio do Azure Active Directory associado ao cluster.

Para obter mais informações, veja [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure.md) (Configurar o HDInsight associado a um domínio).

## <a name="connect-to-nodes"></a>Ligar a nós

Os nós principais e o nó de extremidade (caso exista um) podem ser acedidos através da Internet nas portas 22 e 23.

* Ao estabelecer ligação com os __nós principais__, utilize a porta __22__ ligar ao nó principal primário e a porta __23__ para ligar ao nó principal secundário. O nome de domínio completamente qualificado a utilizar é `clustername-ssh.azurehdinsight.net`, em que `clustername` é o nome do cluster.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```
    
* Quando ligar ao __nó de extremidade__, utilize a porta 22. O nome de domínio completamente qualificado é `edgenodename.clustername-ssh.azurehdinsight.net`, em que `edgenodename` é um nome que forneceu quando criou o nó de extremidade. `clustername` é o nome do cluster.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]
> Os exemplos anteriores pressupõem que está a utilizar a autenticação por palavra-passe ou a autenticação de certificados está a ocorrer automaticamente. Se utilizar um par de chaves SSH para a autenticação e o certificado não for utilizado automaticamente, utilize o parâmetro `-i` para especificar a chave privada. Por exemplo, `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Assim que estiver ligado, a linha de comandos muda para indicar o nome de utilizador SSH e o nó a que está ligado. Por exemplo, quando estiver ligado ao nó principal primário como `sshuser`, a linha de comandos é `sshuser@hn0-clustername:~$`.

### <a name="connect-to-worker-and-zookeeper-nodes"></a>Ligar a nós de trabalho e Zookeeper

Os nós de trabalho e Zookeeper não são acessíveis diretamente pela Internet. Podem ser acedidos a partir dos nós principais ou nós de extremidade do cluster. Seguem-se os passos gerais necessários para ligar a outros nós:

1. Utilizar o SSH para ligar a um nó principal ou de extremidade:

        ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net

2. A partir da ligação SSH ao nó principal ou de extremidade, utilize o comando `ssh` para ligar a um nó de trabalho no cluster:

        ssh sshuser@wn0-myhdi

    Para obter uma lista dos nomes dos nós, veja o documento [Manage HDInsight by using the Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes) (Gerir o HDInsight com a API REST Ambari).

Se a conta SSH for protegida por __palavra-passe__, introduza-a ao ligar.

Se a conta SSH for protegida por __chaves SSH__, certifique-se de que o encaminhamento SSH está ativado no cliente.

> [!NOTE]
> Outra forma de aceder diretamente a todos os nós do cluster é instalar HDInsight numa Rede Virtual do Azure. Depois, pode associar o computador remoto à mesma rede virtual e aceder diretamente a todos os nós no cluster.
>
> Para obter mais informações, veja [Use a virtual network with HDInsight](hdinsight-extend-hadoop-virtual-network.md) (Utilizar uma rede virtual com o HDInsight).

### <a name="configure-ssh-agent-forwarding"></a>Configurar o reencaminhamento de agentes SSH

> [!IMPORTANT]
> Os passos seguintes pressupõem um sistema baseado em Linux ou UNIX e funcionam com o Bash no Windows 10. Se não funcionarem no seu sistema, poderá ter de ver a documentação do seu cliente SSH.

1. Utilizando um editor de texto, abra `~/.ssh/config`. Se este ficheiro não existir, pode criá-lo ao introduzir `touch ~/.ssh/config` na linha de comandos.

2. Adicione o texto seguinte ao ficheiro `config`.

        Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
          ForwardAgent yes

    Substitua as informações do __Anfitrião__ pelo endereço do nó ao qual se liga com SSH. O exemplo anterior utiliza o nó de extremidade. Esta entrada configura o reencaminhamento de agentes SSH para o nó especificado.

3. Teste o reencaminhamento de agente SSH ao utilizar o comando seguinte no terminal:

        echo "$SSH_AUTH_SOCK"

    Este comando devolve informações semelhantes ao texto seguinte:

        /tmp/ssh-rfSUL1ldCldQ/agent.1792

    Se não forem devolvidas informações, então, `ssh-agent` não está em execução. Para obter mais informações, veja as informações de scripts de arranque do agente em [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) ou consulte a documentação do cliente SSH.

4. Depois de verificar que o **ssh-agent** está em execução, utilize o seguinte para adicionar a chave privada SSH ao agente:

        ssh-add ~/.ssh/id_rsa

    Se a chave privada estiver armazenada num ficheiro diferente, substitua `~/.ssh/id_rsa` pelo caminho para o ficheiro.

5. Utilize SSH para ligar aos nós de extremidade ou principais do cluster. Em seguida, utilize o comando SSH para ligar a um nó de trabalho ou zookeeper. A ligação é estabelecida com a chave reencaminhada.

## <a name="copy-files"></a>Copiar ficheiros

O utilitário `scp` pode ser utilizado para copiar ficheiros de e para os nós individuais do cluster. Por exemplo, o comando seguinte copia o diretório `test.txt` do sistema local para o nó principal primário:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Uma vez que não foi especificado nenhum caminho após `:`, o ficheiro é colocado no diretório raiz `sshuser`.

O exemplo seguinte copia o ficheiro `test.txt` do diretório raiz `sshuser` no nó principal primário para o sistema local:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]
> `scp` apenas pode aceder ao sistema de ficheiros de nós individuais dentro do cluster. Não pode ser utilizado para aceder a dados no armazenamento compatível com HDFS do cluster.
>
> Utilize `scp` quando tiver de carregar um recurso para utilização a partir de uma sessão SSH. Por exemplo, carregue um script de Python e, em seguida, execute o script a partir de uma sessão SSH.
>
> Para obter informações sobre o carregamento de dados diretamente para o armazenamento compatível com HDFS, consulte os seguintes documentos:
>
> * [HDInsight com o Armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md).
>
> * [HDInsight com o Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Passos seguintes

* [Use SSH tunneling with HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) (Utilizar túnel SSH com o HDInsight)
* [Use a virtual network with HDInsight](hdinsight-extend-hadoop-virtual-network.md) (Utilizar uma rede virtual com o HDInsight)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight)