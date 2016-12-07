---
title: Utilizar chaves SSH com o Hadoop baseado em Linux a partir do Linux, Unix ou OS X | Microsoft Docs
description: " Pode aceder ao HDInsight baseado em Linux através do Secure Shell (SSH). Este documento fornece informações sobre a utilização do SSH com o HDInsight a partir de clientes Linux, Unix ou OS X."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a6a16405-a4a7-4151-9bbf-ab26972216c5
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 3c3944118ca986009711aee032b45c302b63e63b
ms.openlocfilehash: 93bf35edd2173c147f48512d92bc8e4734cd1dbd


---
# <a name="use-ssh-with-linux-based-hadoop-on-hdinsight-from-linux-unix-or-os-x"></a>Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X

> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

O [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite-lhe iniciar sessão num cluster do HDInsight baseado em Linux e executar comandos através de uma interface de linha de comandos. Este documento disponibiliza informações básicas sobre SSH e informações específicas sobre a utilização de SSH com o HDInsight.

## <a name="what-is-ssh"></a>O que é o SSH?

O SSH é um protocolo de rede criptográfico que lhe permite comunicar em segurança com um servidor remoto através de uma rede não segura. É utilizado para fornecer um início de sessão de linha de comandos a um servidor remoto. Neste caso, os nós principais ou os nós de extremidade de um cluster do HDInsight. 

Também pode utilizar o SSH para fazer o túnel do tráfego de rede do cliente para o cluster do HDInsight. A utilização de um túnel permite-lhe aceder a recursos no cluster do HDInsight que não estão expostos diretamente à Internet. Para obter mais informações sobre como utilizar o túnel de SSH, veja [Use SSH tunneling with HDInsight (Utilizar túnel de SSH com o HDInsight)](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ssh-clients"></a>Clientes SSH

Muitos sistemas operativos fornecem funcionalidades de cliente SSH através dos utilitários da linha de comandos `ssh` e `scp`.

* __ssh__: um cliente SSH geral que pode ser utilizado para estabelecer uma sessão de linha de comandos remota e criar túneis.
* __scp__: um utilitário que copia ficheiros entre sistemas locais e remotos com o protocolo SSH.

Historicamente, o Windows nunca disponibilizou um cliente SSH até ao Windows 10 Anniversary Edition. Esta versão do Windows inclui a funcionalidade Bash on Windows 10 para programadores, que proporciona comandos `ssh`, `scp`, entre outros. Para obter mais informações sobre como utilizar Bash on Windows 10, veja [Bash on Ubuntu on Windows (Bash no Ubuntu no Windows)](https://msdn.microsoft.com/commandline/wsl/about).

Se utiliza o Windows e não tem acesso ao Bash on Windows 10, recomendamos os clientes SSH seguintes:

* [Git For Windows](https://git-for-windows.github.io/): disponibiliza os utilitários de linha de comandos `ssh` e `scp`.
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/): disponibiliza um cliente SSH gráfico.
* [MobaXterm](http://mobaxterm.mobatek.net/): disponibiliza um cliente SSH gráfico.
* [Cygwin](https://cygwin.com/): disponibiliza os utilitários de linha de comandos `ssh` e `scp`.

> [!NOTE]
> Os passos neste documento partem do princípio de que tem acesso ao comando `ssh`. Se estiver a utilizar um cliente como o puTTY ou o MobaXterm, veja a documentação desse produto para obter o comando e os parâmetros equivalentes.

## <a name="ssh-authentication"></a>Autenticação SSH

As ligações SSH podem ser autenticadas através de uma palavra-passe ou da [criptografia de chave pública (https://en.wikipedia.org/wiki/Public-key_cryptography)](https://en.wikipedia.org/wiki/Public-key_cryptography) A utilização de uma chave é a opção mais segura, porque as chaves não estão sujeitas a muitos dos ataques de que as palavras-passe são alvo. No entanto, é mais complicado criar e gerir chaves do que utilizar palavras-passe.

A utilização da criptografia de chave pública envolve a criação de um par de chaves _pública_ e _privada_.

* A **chave pública** é carregada para os nós do cluster do HDInsight ou de qualquer outro serviço que queira utilizar com a criptografia de chave pública.

* A **chave privada** é o que apresenta ao cluster do HDInsight quando inicia sessão com um cliente SSH, para confirmar a sua identidade. Proteja esta chave privada. Não a partilhe.

    Pode criar uma frase de acesso para a chave privada para acrescentar mais segurança. Tem de indicar esta frase de acesso antes de a chave poder ser utilizada.

### <a name="create-a-public-and-private-key"></a>Criar uma chave pública e privada

O utilitário `ssh-keygen` é a forma mais fácil de criar um par de chaves pública e privada para utilização com o HDInsight. Numa linha de comandos, utilize o comando seguinte para criar um par de chaves novo para utilizar com o HDInsight:

> [!NOTE]
> Se estiver a utilizar um cliente GUI SSH, como MobaXTerm ou puTTY, veja a documentação desse cliente para saber como gerar as chaves.

    ssh-keygen -t rsa -b 2048
   
São-lhe pedidas as informações seguintes:

* A localização do ficheiro. Por predefinição, a localização é `~/.ssh/id_rsa`.

* Uma frase de acesos opcional. Se introduzir uma frase de acesso, tem de reintroduzi-la quando se autenticar no cluster do HDInsight.

> [!IMPORTANT]
> A frase de acesso é uma palavra-passe para a chave privada. Sempre que utilizar a chave privada para autenticação, tem de indicar a frase de acesso antes de a chave poder ser utilizada. Se alguém obtiver a sua chave privada, essa pessoa não conseguirá utilizá-la sem a frase de acesso.
>
> Se se esquecer da frase de acesso, não há forma de a repor ou recuperar.

Depois de concluído o comando, tem dois ficheiros novos:

* __id\_rsa__: este ficheiro contém a chave privada.
    
    > [!WARNING]
    > Tem de restringir o acesso a este ficheiro para impedir acessos não autorizados a serviços protegidos pela chave pública.

* __id\_rsa.pub__: este ficheiro contém a chave pública. É utilizado quando cria um cluster do HDInsight.

    > [!NOTE]
    > Não importa quem tem acesso à chave _pública_. Por si só, a única coisa que a chave pública faz é confirmar a chave privada. Determinados serviços, como o servidor SSH, utilizam a chave pública para confirmar a sua identidade quando se autentica com a chave privada.

## <a name="configure-ssh-on-hdinsight"></a>Configurar o SSH no HDInsight

Quando cria um cluster do HDInsight baseado em Linux, tem de indicar um _nome de utilizador de SSH_ e uma _palavra-passe_ ou uma _chave pública_. Durante a criação do cluster, estas informações são utilizadas para criar um início de sessão nos nós do cluster do HDInsight. A palavra-passe ou a chave pública são utilizadas para proteger a conta de utilizador.

Para obter mais informações sobre como configurar o SSH durante a criação do cluster, veja um dos documentos seguintes:

* [Create HDInsight using the Azure portal (Criar o HDInsight com o portal do Azure)](hdinsight-hadoop-create-linux-clusters-portal.md)
* [Create HDInsight using the Azure CLI (Criar o HDInsight com a CLI do Azure)](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
* [Create HDInsight using Azure PowerShell (Criar o HDInsight com o Azure PowerShell)](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [Create HDInsight using Azure Resource Manager templates (Criar o HDInsight com modelos do Azure Resource Manager)](hdinsight-hadoop-create-linux-clusters-arm-templates.md)
* [Create HDInsight using the .NET SDK (Criar o HDInsight com o .NET SDK)](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [Create HDInsight using REST (Criar o HDInsight com REST)](hdinsight-hadoop-create-linux-clusters-curl-rest.md)

### <a name="additional-ssh-users"></a>Utilizadores de SSH adicionais

Embora possam ser acrescentados utilizadores de SSH adicionais ao cluster depois de este ter sido criado, não o recomendamos.

* Os utilizadores de SSH novos têm de ser adicionados manualmente a cada nó do cluster.

* Os utilizadores de SSH novos têm o mesmo acesso ao HDInsight que o utilizador predefinido. Não existe forma de restringir o acesso a dados ou trabalhos no HDInsight com base na conta de utilizador de SSH.

Para restringir o acesso individualmente por utilizador, tem de utilizar um cluster do HDInsight associado a um domínio. O HDInsight associado a um domínio utiliza o Active Directory para controlar o acesso aos recursos do cluster.

Ao utilizar um cluster do HDInsight associado a um domínio, pode utilizar o Active Directory para autenticar depois de se ligar com o SSH. Vários utilizadores podem ligar-se com o SSH e, depois de se terem ligado, autenticar-se nas respetivas contas do Active Directory. Veja a secção [HDInsight associado a um domínio](#domainjoined) para obter mais informações.

##<a name="a-idconnecta-connect-to-hdinsight"></a><a id="connect"></a> Ligar ao HDInsight

Embora todos os nós dos clusters do HDInsight executem o servidor SSH, só se pode ligar aos nós principais ou de extremidade através da Internet pública.

* Para ligar aos _nós principais_, utilize `CLUSTERNAME-ssh.azurehdinsight.net`, em que __CLUSTERNAME__ é o nome do cluster do HDInsight. Ligar na porta 22 (a porta predefinida do SSH) liga ao nó principal primário. A Porta 23 liga ao nó principal secundário.

* Para ligar a um _nó de extremidade_, utilize `EDGENAME.CLUSTERNAME-ssh.azurehdinsight.net`, em que __EDGENAME__ é o nome do nó de extremidade e __CLUSTERNAME__ é o nome do cluster do HDInsight. Utilize a porta 22 quando se ligar ao nó de extremidade.

Os exemplos seguintes demonstram como se ligar aos nós principais e de extremidade de um cluster chamado __myhdi__ com o nome de utilizador de SSH __sshuser__. O nome do nó de extremidade é __myedge__.

| Para... | Utilize... |
| ----- | ----- |
| Ligar ao nó principal primário | `ssh sshuser@myhdi-ssh.azurehdinsight.net` |
| Ligar ao nó principal secundário | `ssh -p 23 sshuser@myhdi-ssh.azurehdinsight.net` |
| Ligar ao nó de extremidade | `ssh sshuser@edge.myhdi-ssh.azurehdinsight.net` |

Se utilizar uma palavra-passe para proteger a conta SSH, é-lhe pedido que a introduza.

Se utilizar uma chave pública para proteger a conta SSH, poderá ter de indicar o caminho para a chave privada correspondente ao utilizar o comutador `-i`. O exemplo seguinte demonstra como utilizar o comutador `-i`:

    ssh -i /path/to/public.key sshuser@myhdi-ssh.azurehdinsight.net

### <a name="connect-to-other-nodes"></a>Ligar a outros nós

Os nós de trabalho e os nós Zookeeper não estão diretamente acessíveis de fora do cluster, mas é possível aceder-lhes a partir dos nós principais ou de extremidade do cluster. Seguem-se os passos gerais necessários para o fazer:

1. Utilizar o SSH para ligar a um nó principal ou de extremidade:

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. A partir da ligação SSH ao nó principal ou de extremidade, utilize o comando `ssh` para ligar a um nó de trabalho no cluster:

        ssh sshuser@wn0-myhdi

    Para obter uma lista dos nós de trabalho no cluster, veja o exemplo de como obter o nome de domínio completamente qualificado dos nós do cluster no documento [Manage HDInsight by using the Ambari REST API (Gerir o HDInsight com a API REST Ambari)](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes).

Se a conta SSH estiver protegida por uma palavra-passe, é-lhe pedido que a introduza e a ligação é, então, estabelecida.

Se autenticar a sua conta de utilizador com uma chave SSH, tem de confirmar que o seu ambiente local está configurado para o reencaminhamento de agentes SSH.

> [!IMPORTANT]
> Os passos abaixo pressupõem uma sistema baseado em Linux/UNIX e funcionam com o Bash on Windows 10. Se não funcionarem no seu sistema, poderá ter de ver a documentação do seu cliente SSH.

1. Utilizando um editor de texto, abra `~/.ssh/config`. Se este ficheiro não existir, pode criá-lo ao introduzir `touch ~/.ssh/config` na linha de comandos.

2. Adicione o seguinte ao ficheiro. Substitua *CLUSTERNAME* pelo nome do cluster do HDInsight.
   
        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes
   
    Esta entrada configura o reencaminhamento de agente SSH para o cluster do HDInsight.

3. Teste o reencaminhamento de agente SSH ao utilizar o comando seguinte no terminal:
   
        echo "$SSH_AUTH_SOCK"
   
    Este comando devolve informações semelhantes ao texto seguinte:
   
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
   
    Se não forem devolvidas informações, tal indica que `ssh-agent` não está em execução. Veja as informações de script de arranque do agente em [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) ou consulte a documentação do seu cliente SSH para obter os passos específicos relativos à instalação e configuração de `ssh-agent`.

4. Depois de verificar que o **ssh-agent** está em execução, utilize o seguinte para adicionar a chave privada SSH ao agente:
   
        ssh-add ~/.ssh/id_rsa
   
    Se a chave privada estiver armazenada num ficheiro diferente, substitua `~/.ssh/id_rsa` pelo caminho para o ficheiro.

###<a name="a-iddomainjoineda-domain-joined-hdinsight"></a><a id="domainjoined"></a> HDInsight associado a um domínio

O [HDInsight associado a um domínio](hdinsight-domain-joined-introduction.md) integra o Kerberos no Hadoop no HDInsight. Uma vez que o utilizador de SSH não é um utilizador de domínio do Active Directory, não pode executar comandos do Hadoop enquanto não se autenticar com o Active Diretory. Utilize os passos seguintes para autenticar a sua sessão SSH com o Active Directory:

1. Ligue a um cluster do HDInsight associado a um domínio através do SSH, conforme mencionado na secção [Ligar ao HDInsight](#connect). Por exemplo, o comando seguinte liga-se a um cluster do HDInsight chamado __myhdi__ com uma conta SSH com o nome __sshuser__.

        ssh sshuser@myhdi-ssh.azurehdinsight.net

2. Utilize o seguinte para autenticar com um utilizador de domínio e uma palavra-passe:

        kinit

     Quando lhe for pedido, introduza um nome de utilizador de domínio e a palavra-passe do utilizador de domínio.

    Para obter mais informações sobre como configurar utilizadores de domínio para clusters do HDInsight associados a um domínio, veja [Configurar clusters do HDInsight associados a um domínio](hdinsight-domain-joined-configure.md).

Depois de autenticar com o comando `kinit`, já pode utilizar os comandos do Hadoop, como `hdfs dfs -ls /` ou `hive`.

## <a name="a-idtunnelassh-tunneling"></a><a id="tunnel"></a>Túnel SSH

É possível utilizar o SSH para criar um túnel entre os pedidos locais, tais como pedidos Web, e o cluster do HDInsight. O pedido será encaminhado para o recurso solicitado como se tivesse tido origem no nó principal do cluster do HDInsight.

> [!IMPORTANT]
> Um túnel SSH é um requisito para aceder à IU da Web de alguns serviços do Hadoop. Por exemplo, só é possível aceder à IU do Histórico de Tarefas ou à IU do Resource Manager através de um túnel SSH.

Para obter mais informações sobre como criar e utilizar um túnel SSH, veja [Use SSH Tunneling to access Ambari web UI, JobHistory, NameNode, Oozie, and other web UI's (Utilizar o Túnel SSH para aceder à IU Web de Ambari, a JobHistory, a NameNode, a Oozie e a outras IUs Web)](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="next-steps"></a>Passos seguintes

Agora que sabe como efetuar a autenticação com uma chave SSH, saiba como utilizar o MapReduce com o Hadoop no HDInsight.

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Nov16_HO3-->


