---
title: "Utilizar o SSH túnel para aceder ao Azure HDInsight | Microsoft Docs"
description: "Saiba como utilizar um túnel SSH para procurar em segurança a recursos da web alojados em nós do HDInsight baseado em Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 879834a4-52d0-499c-a3ae-8d28863abf65
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/10/2017
ms.author: larryfr
ms.openlocfilehash: 7f55abc84a8afea398cf0e95761d922b77e1c248
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="use-ssh-tunneling-to-access-ambari-web-ui-jobhistory-namenode-oozie-and-other-web-uis"></a>Utilizar o túnel SSH para aceder à IU da web do Ambari, JobHistory, NameNode, Oozie e outras web UIs

Clusters do HDInsight baseado em Linux fornecem acesso à IU da web do Ambari através da Internet, mas algumas funcionalidades da IU não estão. Por exemplo, a IU da web para outros serviços que estão anexados através do Ambari. Para uma funcionalidade completa da IU da web do Ambari, tem de utilizar um túnel SSH para o cabeçalho de cluster.

## <a name="why-use-an-ssh-tunnel"></a>Porquê utilizar um túnel SSH

Várias de menus na Ambari funcionam apenas através de um túnel SSH. Estes menus dependem de web sites e serviços em execução em outros tipos de nó, tais como nós de trabalho. Muitas vezes, estes web sites não são protegidos, pelo que não é seguro expô-los diretamente na internet.

A seguinte UIs Web necessitam de um túnel SSH:

* JobHistory
* NameNode
* Pilhas de thread
* IU da web do Oozie
* IU do modelo global de HBase e registos

Se utilizar ações de Script ao personalizar o seu cluster, quaisquer serviços ou utilitários que instala e expõem uma IU da web requerem um túnel SSH. Por exemplo, se instalar Hue utilizando uma ação de Script, tem de utilizar um túnel SSH para aceder a IU da web de Hue.

> [!IMPORTANT]
> Se tiver acesso direto ao HDInsight através de uma rede virtual, não terá da utilizar túneis SSH. Para obter um exemplo de aceder diretamente a HDInsight através de uma rede virtual, consulte o [HDInsight ligar à sua rede no local](connect-on-premises-network.md) documento.

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

[Secure Shell (SSH) túnel](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) encaminha o tráfego enviado para uma porta na sua estação de trabalho local. O tráfego é encaminhado através de uma ligação SSH para o nó principal do cluster de HDInsight. O pedido foi resolvido como se o ponto de origem no nó principal. A resposta, em seguida, é encaminhada através do túnel na estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. A maioria dos sistemas de operativos fornecem um cliente SSH através de `ssh` comando. Para obter mais informações, veja [Utilizar SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um browser que pode ser configurado para utilizar um proxy SOCKS5.

    > [!WARNING]
    > O suporte de proxy de SOCKS incorporado no Windows não suporta SOCKS5 e não funciona com os passos neste documento. Os browsers seguintes dependem de definições de proxy do Windows e trabalhar com os passos neste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome depende também as definições de proxy do Windows. No entanto, pode instalar as extensões que suportam SOCKS5. Recomendamos [FoxyProxy padrão](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Criar um túnel utilizando o comando SSH

Utilize o seguinte comando para criar um SSH túnel utilizando o `ssh` comando. Substitua **USERNAME** com um utilizador SSH para o cluster do HDInsight e substitua **CLUSTERNAME** com o nome do cluster do HDInsight:

```bash
ssh -C2qTnNf -D 9876 USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
```

Este comando cria uma ligação que encaminha o tráfego para a porta local 9876 ao cluster através de SSH. As opções são:

* **D 9876** -a porta local que encaminha o tráfego através do túnel.
* **C** -comprimir todos os dados, porque o tráfego web é principalmente texto.
* **2** -force SSH para experimentar apenas a versão 2 do protocolo.
* **q** -modo silencioso.
* **T** -desativar alocação pseudo-tty, uma vez que está a reencaminhar apenas uma porta.
* **n**-Impedi a leitura de STDIN, uma vez que está a reencaminhar apenas uma porta.
* **N** -não é executar um comando remoto, uma vez que está a reencaminhar apenas uma porta.
* **f** -executar em segundo plano.

Depois do comando for concluído, o tráfego enviado para a porta 9876 no computador local é encaminhado para o nó principal do cluster.

## <a name="useputty"></a>Criar um túnel através do PuTTY

[PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty) for um cliente SSH gráfico para Windows. Utilize os seguintes passos para criar um túnel SSH PuTTY a utilizar:

1. Abra o PuTTY e introduza as suas informações de ligação. Se não estiver familiarizado com o PuTTY, consulte o [documentação (http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html) do PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html).

2. No **categoria** secção para a esquerda da caixa de diálogo, expanda **ligação**, expanda **SSH**e, em seguida, selecione **túneis**.

3. Fornecem as seguintes informações sobre o **opções controlar SSH porta reencaminhamento** formulário:
   
   * **Porta de origem** - A porta no cliente que pretende reencaminhar. Por exemplo, **9876**.

   * **Destino** -o SSH endereço para o cluster do HDInsight baseado em Linux. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

   * **Dinâmico** - Ativa encaminhamento proxy de SOCKS dinâmico.
     
     ![Imagem das opções de túnel](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

4. Clique em **adicionar** para adicionar as definições e, em seguida, clique em **abrir** para abrir uma ligação SSH.

5. Quando lhe for solicitado, inicie sessão no servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Utilizar o túnel do seu browser

> [!IMPORTANT]
> Os passos nesta secção utilizam o browser Mozilla FireFox, que fornece as mesmas definições de proxy em todas as plataformas. Outros browsers modernos, tais como o Google Chrome, podem necessitar de uma extensão, como FoxyProxy para trabalhar com o túnel.

1. Configurar o browser utilize **localhost** e a porta que utilizou quando criar o túnel como um **SOCKS v5** proxy. Eis o aspeto as definições de Firefox. Se utilizou uma porta diferente que 9876, altere a porta para que utilizou:
   
    ![imagem de definições de Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]
   > Selecionar **DNS remoto** resolve pedidos de sistema de nomes de domínio (DNS), utilizando o cluster do HDInsight. Esta definição resolve DNS utilizando o nó principal do cluster.

2. Certifique-se de que o túnel funciona, visitando o site, tais como [http://www.whatismyip.com/](http://www.whatismyip.com/). O IP devolvido deve ser utilizada pelo centro de dados do Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Certifique-se com a IU da web do Ambari

Quando o cluster for estabelecido, utilize os seguintes passos para verificar se pode aceder ao service web UIs da Web do Ambari:

1. No seu browser, aceda a http://headnodehost:8080. O `headnodehost` endereço é enviado através do túnel para o cluster e a resolver para o headnode Ambari está em execução. Quando lhe for pedido, introduza o nome de utilizador de admin (administrador) e a palavra-passe para o cluster. Poderá ser solicitado uma segunda vez pela IU da web do Ambari. Se assim for, reintroduza as informações.

   > [!NOTE]
   > Quando utiliza o endereço de http://headnodehost:8080 para ligar ao cluster, está a ligar através do túnel. Comunicação é protegida através do túnel SSH em vez de HTTPS. Para ligar através da internet através de HTTPS, utilize https://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do cluster.

2. Na IU da Web do Ambari, selecione o HDFS na lista à esquerda da página.

    ![Imagem com o HDFS selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Quando as informações do serviço HDFS for apresentadas, selecione **ligações rápidas**. É apresentada uma lista de nós principais do cluster. Selecione um de nós principais e, em seguida, selecione **NameNode IU**.

    ![Imagem com o menu de QuickLinks expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

   > [!NOTE]
   > Quando seleciona __ligações rápidas__, poderá receber um indicador de espera. Esta condição pode ocorrer se tiver uma ligação lenta da internet. Aguarde um minuto ou dois para que os dados a ser recebido do servidor e tente novamente a lista.
   >
   > Algumas entradas no **ligações rápidas** menu poderá cortar pelo lado direito do ecrã. Se assim for, expanda o menu utilizando o rato e utilizar a tecla de seta para a direita para deslocar o ecrã para a direita para ver o resto do menu.

4. É apresentada uma página semelhante ao seguinte imagem:

    ![Imagem da IU NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

   > [!NOTE]
   > Tenha em atenção o URL para esta página; deve ser semelhante **http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster**. Este URI está a utilizar o nome interno de domínio completamente qualificado (FQDN) do nó e só é acessível ao utilizar um túnel SSH.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu como criar e utilizar um túnel SSH, consulte o documento seguinte para outras formas de utilizar Ambari:

* [Gerir clusters do HDInsight utilizando Ambari](hdinsight-hadoop-manage-ambari.md)

Para obter mais informações sobre como utilizar o SSH com o HDInsight, consulte [utilizar o SSH com o HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

