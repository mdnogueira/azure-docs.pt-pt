---
title: Utilizar chaves SSH com o Hadoop baseado em Linux a partir do Linux, Unix ou OS X | Microsoft Docs
description: " Pode aceder ao HDInsight baseado em Linux através do Secure Shell (SSH). Este documento fornece informações sobre a utilização do SSH com o HDInsight a partir de clientes Linux, Unix ou OS X."
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/13/2016
ms.author: larryfr

---
# Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Linux, Unix ou OS X
> [!div class="op_single_selector"]
> * [Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
> * [Linux, Unix, OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
> 
> 

O [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) permite-lhe efetuar operações remotamente nos seus clusters do HDInsight baseado em Linux utilizando uma interface de linha de comandos. Este documento fornece informações sobre a utilização do SSH com o HDInsight a partir de clientes Linux, Unix ou OS X.

> [!NOTE]
> Os passos deste artigo partem do princípio de que está a utilizar um cliente Linux, Unix ou OS X. Estes passos podem ser feitos num cliente baseado no Windows, se tiver instalado um pacote que forneça `ssh` e `ssh-keygen`, como [Bash on Ubuntu on Windows](https://msdn.microsoft.com/commandline/wsl/about).
> 
> Se não tiver o SSH instalado no cliente baseado no Windows, utilize os passos em [Utilizar o SSH com o Hadoop baseado em Linux no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md) para obter informações sobre como instalar e utilizar PuTTY.
> 
> 

## Pré-requisitos
* **ssh-keygen** e **ssh** para clientes Linux, Unix e OS X. Normalmente, estes utilitários são fornecidos com o sistema operativo ou estão disponíveis através do sistema de gestão do pacote.
* Um browser moderno que suporte HTML5.

OU

* [CLI do Azure](../xplat-cli-install.md).
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

## O que é o SSH?
O SSH é um utilitário para iniciar sessão e executar remotamente comandos num servidor remoto. Com o HDInsight baseado em Linux, o SSH estabelece uma ligação encriptada ao nó principal do cluster e disponibiliza uma linha de comandos utilizada para introduzir os comandos. Em seguida, os comandos são executados diretamente no servidor.

### Nome de utilizador SSH
Um nome de utilizador SSH é o nome que utiliza para se autenticar no cluster do HDInsight. Quando especifica um nome de utilizador SSH durante a criação do cluster, este utilizador é criado em todos os nós do cluster. Assim que o cluster for criado, pode utilizar este nome de utilizador para se ligar aos nós principais do cluster do HDInsight. Depois, a partir dos nós principais, pode ligar-se aos nós de trabalho individuais.

### Palavra-passe ou Chave pública SSH
Um utilizador do SSH pode utilizar uma palavra-passe ou uma chave pública para a autenticação. Uma palavra-passe é simplesmente uma cadeia de texto criada por si, enquanto uma chave pública faz parte de um par de chaves criptográficas gerado para o identificar de forma exclusiva.

Uma chave é mais segura do que uma palavra-passe. No entanto, são necessários passos adicionais para gerar a chave e tem de manter os ficheiros que a contêm numa localização segura. Se alguém conseguir obter acesso aos ficheiros de chave, também obtém acesso à sua conta. Além disso, se perder os ficheiros de chave, não conseguirá iniciar sessão na sua conta.

Um par de chaves é constituído por uma chave pública (que é enviada para o servidor do HDInsight) e uma chave privada (que é guardada no seu computador cliente). Quando se liga ao servidor do HDInsight através do SSH, o cliente SSH utiliza a chave privada no seu computador para se autenticar junto do servidor.

## Criar uma chave SSH
Utilize as seguintes informações caso pretenda utilizar chaves SSH com o cluster. Se pretender utilizar uma palavra-passe, pode ignorar esta secção.

1. Abra uma sessão de terminal e utilize o seguinte comando para verificar se tem quaisquer chaves SSH existentes:
   
        ls -al ~/.ssh
   
    Procure os ficheiros seguintes na listagem de diretórios. Estes são nomes comuns para as chaves SSH públicas.
   
   * id\_dsa.pub
   * id\_ecdsa.pub
   * id\_ed25519.pub
   * id\_rsa.pub
2. Se não pretender utilizar um ficheiro existente ou não se tiver qualquer chave SSH existente, utilize o seguinte comando para gerar um novo ficheiro:
   
        ssh-keygen -t rsa
   
    Ser-lhe-á pedido que forneça as seguintes informações:
   
   * A localização do ficheiro – por predefinição, a localização é ~/.ssh/id\_rsa.
   * Uma frase de acesso – é-lhe pedido que introduza esta informação.
     
     > [!NOTE]
     > Recomendamos vivamente que utilize uma frase de acesso segura para a chave. Contudo, caso se esqueça da frase de acesso, não tem como a recuperar.
     > 
     > 
     
     Quando o comando terminar, terão sido criados dois novos ficheiros, a chave privada (por exemplo, **id\_rsa**) e a chave pública (por exemplo, **id\_rsa.pub**).

## Criar um cluster do HDInsight baseado em Linux
Ao criar um cluster do HDInsight baseado em Linux, tem de fornecer a chave pública criada anteriormente. Existem duas formas de criar um cluster do HDInsight a partir de clientes Linux, Unix ou OS X:

* **Portal do Azure** – utiliza um portal baseado na Web para criar o cluster.
* **CLI do Azure para Mac, Linux e Windows** – utiliza comandos da linha de comandos para criar o cluster.

Cada um destes métodos requer a utilização de uma palavra-passe ou de uma chave pública. Para obter informações completas sobre a criação de um cluster do HDInsight baseado em Linux, consulte [Aprovisionar clusters do HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md).

### Portal do Azure
Ao utilizar o [Portal do Azure][preview-portal] para criar um cluster do HDInsight baseado em Linux, tem de introduzir um **NOME DE UTILIZADOR SSH** e selecionar a opção para introduzir uma **PALAVRA-PASSE** ou uma **CHAVE PÚBLICA SSH**.

Se selecionar **CHAVE PÚBLICA SSH**, pode colar a chave pública (contida no ficheiro com a extensão **.pub**) no campo **Chave Pública SSH** ou selecionar a opção **Selecionar um ficheiro** para procurar e selecionar o ficheiro de chave pública.

![Imagem de um formulário que solicita a chave pública](./media/hdinsight-hadoop-linux-use-ssh-unix/ssh-key.png)

> [!NOTE]
> O ficheiro de chave é simplesmente um ficheiro de texto. O conteúdo deverá ser semelhante ao seguinte:
> 
> ```
> ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCelfkjrpYHYiks4TM+r1LVsTYQ4jAXXGeOAF9Vv/KGz90pgMk3VRJk4PEUSELfXKxP3NtsVwLVPN1l09utI/tKHQ6WL3qy89WVVVLiwzL7tfJ2B08Gmcw8mC/YoieT/YG+4I4oAgPEmim+6/F9S0lU2I2CuFBX9JzauX8n1Y9kWzTARST+ERx2hysyA5ObLv97Xe4C2CQvGE01LGAXkw2ffP9vI+emUM+VeYrf0q3w/b1o/COKbFVZ2IpEcJ8G2SLlNsHWXofWhOKQRi64TMxT7LLoohD61q2aWNKdaE4oQdiuo8TGnt4zWLEPjzjIYIEIZGk00HiQD+KCB5pxoVtp user@system
> ```
> 
> 

Deste modo, é criado um início de sessão para o utilizador especificado, utilizando a palavra-passe ou a chave pública fornecida por si.

### Interface de Linha de Comandos do Azure para Mac, Linux e Windows
Pode utilizar a [CLI do Azure para Mac, Linux e Windows](../xplat-cli-install.md) para criar um novo cluster através do comando `azure hdinsight cluster create`.

Para obter mais informações sobre a utilização deste comando, consulte [Aprovisionar clusters do Hadoop em Linux no HDInsight através de opções personalizadas](hdinsight-hadoop-provision-linux-clusters.md).

## Ligar a um cluster do HDInsight baseado em Linux
A partir de uma sessão de terminal, utilize o comando SSH para ligar ao nó principal do cluster ao fornecer o endereço e o nome de utilizador:

* **Endereço SSH** – existem dois endereços que podem ser utilizados para ligar a um cluster com o SSH:
  
  * **Ligar ao nó principal**: o nome do cluster, seguido de **-ssh.azurehdinsight.net**. Por exemplo, **mycluster-ssh.azurehdinsight.net**.
  * **Ligar ao nó de extremidade**: se o cluster for o Servidor R no HDInsight, o cluster contém também um nó de extremidade ao qual é possível aceder ao utilizar **RServer.CLUSTERNAME.ssh.azurehdinsight.net**, em que **CLUSTERNAME** é o nome do cluster.
* **Nome de utilizador** – o nome de utilizador SSH que forneceu quando criou o cluster.

O exemplo seguinte irá ligar ao nó principal primário de **mycluster** como o utilizador **me**:

    ssh me@mycluster-ssh.azurehdinsight.net

Caso tenha utilizado uma palavra-passe para a conta de utilizador, é-lhe pedido que introduza a palavra-passe.

Caso tenha utilizado uma chave SSH protegida por uma frase de acesso, é-lhe pedido que introduza a frase de acesso. Caso contrário, o SSH tentará efetuar automaticamente a autenticação utilizando um das chaves privadas locais no cliente.

> [!NOTE]
> Se o SSH não efetuar automaticamente a autenticação com a chave privada correta, utilize o parâmetro **-i** e especifique o caminho para a chave privada. O exemplo seguinte carregará a chave privada a partir de `~/.ssh/id_rsa`:
> 
> `ssh -i ~/.ssh/id_rsa me@mycluster-ssh.azurehdinsight.net`
> 
> 

Se estiver a estabelecer ligação com o endereço do nó principal e não for especificada qualquer porta, o SSH utilizará a porta 22 como predefinição, que ligará ao nó principal primário no cluster do HDInsight. Se utilizar a porta 23, liga-se ao secundário. Para obter mais informações sobre os nós principais, consulte [Disponibilidade e fiabilidade de clusters do Hadoop no HDInsight](hdinsight-high-availability-linux.md).

### Ligar a nós de trabalho
Os nós de trabalho não estão diretamente acessíveis a partir de fora do datacenter do Azure, mas é possível aceder-lhes a partir do nó principal do cluster através do SSH.

Se utilizar uma chave SSH para autenticar a sua conta de utilizador, tem de concluir os passos seguintes no cliente:

1. Utilizando um editor de texto, abra `~/.ssh/config`. Se este ficheiro não existir, pode criá-lo ao introduzir `touch ~/.ssh/config` no terminal.
2. Adicione o seguinte ao ficheiro. Substitua *CLUSTERNAME* pelo nome do cluster do HDInsight.
   
        Host CLUSTERNAME-ssh.azurehdinsight.net
          ForwardAgent yes
   
    Esta ação configura o reencaminhamento de agente SSH para o cluster do HDInsight.
3. Teste o reencaminhamento de agente SSH ao utilizar o comando seguinte no terminal:
   
        echo "$SSH_AUTH_SOCK"
   
    Este comando deverá devolver informações semelhantes a estas:
   
        /tmp/ssh-rfSUL1ldCldQ/agent.1792
   
    Se não forem devolvidas informações, tal indica que o **ssh-agent** não está em execução. Consulte a documentação do sistema operativo para ver os passos específicos de instalação e configuração do **ssh-agent** ou consulte [Utilizar ssh-agent com ssh](http://mah.everybody.org/docs/ssh).
4. Depois de verificar que o **ssh-agent** está em execução, utilize o seguinte para adicionar a chave privada SSH ao agente:
   
        ssh-add ~/.ssh/id_rsa
   
    Se a chave privada estiver armazenada num ficheiro diferente, substitua `~/.ssh/id_rsa` pelo caminho para o ficheiro.

Utilize os seguintes passos para ligar aos nós de trabalho do cluster.

> [!IMPORTANT]
> Se utilizar uma chave SSH para autenticar a sua conta, tem de concluir os passos anteriores para confirmar que o reencaminhamento de agente está a funcionar.
> 
> 

1. Ligue ao cluster do HDInsight utilizando o SSH, tal como descrito anteriormente.
2. Assim que estiver ligado, utilize o seguinte para obter uma lista de nós do cluster. Substitua *ADMINPASSWORD* pela palavra-passe da conta de administrador do cluster. Substitua *CLUSTERNAME* pelo nome do cluster.
   
        curl --user admin:ADMINPASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/hosts
   
    Esta ação irá devolver informações no formato JSON para os nós do cluster, incluindo `host_name`, que contém o nome de domínio completamente qualificado (FQDN) de cada nó. Segue-se um exemplo de uma entrada `host_name` devolvida pelo comando **curl**:
   
        "host_name" : "workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net"
3. Assim que tiver uma lista dos nós de trabalho aos quais se pretende ligar, utilize o seguinte comando da sessão SSH para o servidor para abrir uma ligação a um nó de trabalho:
   
        ssh USERNAME@FQDN
   
    Substitua *USERNAME* pelo nome de utilizador SSH e *FQDN* pelo FQDN do nó de trabalho. Por exemplo, `workernode0.workernode-0-e2f35e63355b4f15a31c460b6d4e1230.j1.internal.cloudapp.net`.
   
   > [!NOTE]
   > Se utilizar uma palavra-passe para autenticar a sessão SSH, é-lhe pedido que introduza novamente a palavra-passe. Se utilizar uma chave SSH, a ligação deverá ser concluída sem serem apresentados quaisquer pedidos.
   > 
   > 
4. Quando a sessão for estabelecida, o prompt do terminal mudará de `username@hn#-clustername` para `username@wk#-clustername` para indicar que está ligado ao nó de trabalho. Todos os comandos que executar neste momento serão executados no nó de trabalho.
5. Quando terminar de realizar ações no nó de trabalho, utilize o comando `exit` para fechar a sessão no nó de trabalho. Desta forma, regressa ao prompt `username@hn#-clustername`.

## Adicionar mais contas
1. Gere uma nova chave pública e uma chave privada para a nova conta de utilizador, conforme descrito na secção [Criar uma chave SSH](#create-an-ssh-key-optional).
   
   > [!NOTE]
   > A chave privada deve ser gerada num cliente que o utilizador irá utilizar para ligar ao cluster ou deve ser transferida em segurança para esse cliente depois de ser criada.
   > 
   > 
2. Numa sessão SSH para o cluster, adicione o novo utilizador com o seguinte comando:
   
        sudo adduser --disabled-password <username>
   
    Esta ação irá criar uma nova conta de utilizador, mas irá desativar a autenticação por palavra-passe.
3. Crie o diretório e os ficheiros para armazenar a chave ao utilizar os seguintes comandos:
   
        sudo mkdir -p /home/<username>/.ssh
        sudo touch /home/<username>/.ssh/authorized_keys
        sudo nano /home/<username>/.ssh/authorized_keys
4. Quando o editor nano for apresentado, copie e cole o conteúdo da chave pública da nova conta de utilizador. Por último, utilize **Ctrl-X** para guardar o ficheiro e sair do editor.
   
    ![imagem do editor nano com chave de exemplo](./media/hdinsight-hadoop-linux-use-ssh-unix/nano.png)
5. Utilize o seguinte comando para alterar a propriedade da pasta .ssh e o conteúdo para a nova conta de utilizador:
   
        sudo chown -hR <username>:<username> /home/<username>/.ssh
6. Agora, deverá conseguir autenticar-se junto do servidor com a nova conta de utilizador e chave privada.

## <a id="tunnel"></a>Túnel SSH
É possível utilizar o SSH para criar um túnel entre os pedidos locais, tais como pedidos Web, e o cluster do HDInsight. O pedido será encaminhado para o recurso solicitado como se tivesse tido origem no nó principal do cluster do HDInsight.

> [!IMPORTANT]
> Um túnel SSH é um requisito para aceder à IU da Web de alguns serviços do Hadoop. Por exemplo, só é possível aceder à IU do Histórico de Tarefas ou à IU do Resource Manager através de um túnel SSH.
> 
> 

Para obter mais informações sobre como criar e utilizar um túnel SSH, consulte [Utilizar um túnel SSH para aceder à IU da Web do Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md).

## Passos seguintes
Agora que sabe como efetuar a autenticação com uma chave SSH, saiba como utilizar o MapReduce com o Hadoop no HDInsight.

* [Utilizar o Hive com o HDInsight](hdinsight-use-hive.md)
* [Utilizar o Pig com o HDInsight](hdinsight-use-pig.md)
* [Utilizar tarefas de MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

[preview-portal]: https://portal.azure.com/



<!--HONumber=Sep16_HO3-->


