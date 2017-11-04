---
title: Utilizar o Apache Phoenix e o SQuirreL com baseado no Windows Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar o Apache Phoenix no HDInsight e como instalar e configurar o SQuirreL na estação de trabalho para ligar a um cluster de HBase no HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1a756e98-75c1-44cd-a178-c5119683b7b7
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 64a4c5b158ebe0119f2f0133587a743fd2dbf0ff
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-phoenix-and-squirrel-with-windows-based-hbase-clusters-in-hdinsight"></a>Utilizar o Apache Phoenix e o SQuirreL com clusters baseados em Windows HBase no HDInsight
Saiba como utilizar [Apache Phoenix](http://phoenix.apache.org/) no HDInsight e como instalar e configurar o SQuirreL na estação de trabalho para ligar a um cluster de HBase no HDInsight. Para obter mais informações sobre o Phoenix, consulte [Phoenix em 15 minutos ou menos](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Para a gramática Phoenix, consulte [Phoenix gramática](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Para informações de versão Phoenix no HDInsight, consulte [Novidades nas versões de cluster do Hadoop fornecidas pelo HDInsight?](hdinsight-component-versioning.md).
>

> [!IMPORTANT]
> Os passos neste documento só funcionam para os clusters do HDInsight baseados em Windows. HDInsight só está disponível no Windows para as versões inferiores ao HDInsight 3.4. O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). Para obter informações sobre como utilizar o Phoenix no HDInsight baseado em Linux, consulte [clusters de utilizar o Apache Phoenix com HBase baseado em Linux no HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md).
>



## <a name="use-sqlline"></a>Utilizar SQLLine
[SQLLine](http://sqlline.sourceforge.net/) é um utilitário de linha de comandos para executar o SQL Server.

### <a name="prerequisites"></a>Pré-requisitos
Antes de poder utilizar SQLLine, tem de ter o seguinte:

* **Um cluster de HBase no HDInsight**. Para obter informações sobre aprovisionar HBase do cluster, consulte [introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started].
* **Ligar ao cluster HBase através do protocolo de ambiente de trabalho remoto**. Para obter instruções, consulte [clusters do Hadoop gerir no HDInsight ao utilizar o Portal clássico do Azure][hdinsight-manage-portal].

**Para saber o nome de anfitrião**

1. Abra **linha de comandos do Hadoop** no ambiente de trabalho.
2. Execute o seguinte comando para obter o sufixo DNS:

        ipconfig

    Anote **sufixo DNS específico da ligação**. Por exemplo, *myhbasecluster.f5.internal.cloudapp.net*. Quando liga a um cluster HBase, terá de ligar a uma das Zookeepers utilizando o FQDN. Cada cluster de HDInsight tem 3 Zookeepers. São *zookeeper0*, *zookeeper1*, e *zookeeper2*. O FQDN será semelhante ao seguinte *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Para utilizar SQLLine**

1. Abra **linha de comandos do Hadoop** no ambiente de trabalho.
2. Execute os seguintes comandos para abrir SQLLine:

        cd %phoenix_home%\bin
        sqlline.py [The FQDN of one of the Zookeepers]

    ![HDInsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

    Os comandos utilizados no exemplo:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

Para obter mais informações, consulte [SQLLine manual](http://sqlline.sourceforge.net/#manual) e [Phoenix gramática](http://phoenix.apache.org/language/index.html).

## <a name="use-squirrel"></a>Utilize o SQuirreL
[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) é um programa gráfico do Java que irão permitir-lhe visualizar a estrutura de uma base de dados de conformidade de JDBC, procurar os dados nas tabelas, emitir comandos SQL etc. Pode ser utilizado para ligar ao Apache Phoenix no HDInsight.

Esta secção mostra como instalar e configurar o SQuirreL na estação de trabalho para ligar a um cluster de HBase no HDInsight através de VPN.

### <a name="prerequisites"></a>Pré-requisitos
Antes de seguir os procedimentos, tem de ter o seguinte:

* Um cluster HBase implementado a uma rede virtual do Azure com uma máquina virtual DNS.  Para obter instruções, consulte [HBase criar clusters na Azure Virtual Network][hdinsight-hbase-provision-vnet].

* Obter o sufixo DNS específico da ligação do HBase cluster cluster. Para obtê-lo, RDP num cluster e, em seguida, executar IPConfig.  O sufixo DNS é semelhante a:

        myhbase.b7.internal.cloudapp.net
* Transfira e instale [Microsoft Visual Studio Express para Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) na estação de trabalho. Terá makecert no pacote para criar o certificado.  
* Transfira e instale [ambiente de tempo de execução Java](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) na estação de trabalho.  SQuirreL SQL versão de cliente 3.0 e superior requer JRE versão 1.6 ou superior.  

### <a name="configure-a-point-to-site-vpn-connection-to-the-azure-virtual-network"></a>Configurar uma ligação VPN ponto a Site para a Azure virtual network
Existem 3 passos envolvidos configurar uma ligação de VPN ponto a site:

1. [Configurar uma rede virtual e um gateway de encaminhamento dinâmico](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Criar os certificados](#Create-your-certificates)
3. [Configurar o cliente VPN](#Configure-your-VPN-client)

Consulte [configurar uma ligação VPN ponto a Site a uma rede Virtual do Azure](../vpn-gateway/vpn-gateway-point-to-site-create.md) para obter mais informações.

#### <a name="configure-a-virtual-network-and-a-dynamic-routing-gateway"></a>Configurar uma rede virtual e um gateway de encaminhamento dinâmico
Assegurar que aprovisionou um cluster HBase numa rede virtual do Azure (consulte os pré-requisitos para esta secção). O passo seguinte consiste em configurar uma ligação ponto a site.

**Para configurar a conetividade ponto a site**

1. Iniciar sessão para o [Portal clássico do Azure][azure-portal].
2. No lado esquerdo, clique em **redes**.
3. Clique na rede virtual que criou (consulte [HBase aprovisionar clusters na Azure Virtual Network][hdinsight-hbase-provision-vnet]).
4. Clique em **configurar** do nível superior.
5. No **conetividade ponto a site** secção, selecione **configurar conetividade ponto a site**.
6. Configurar **IP inicial** e **CIDR** para especificar o intervalo de endereços IP partir da qual os clientes VPN receberão um endereço IP quando forem ligados. O intervalo não pode sobrepor-se com qualquer um dos intervalos localizados na sua rede no local e a rede virtual do Azure que irá ser ligar. Por exemplo. Se tiver selecionado 10.0.0.0/20 para a rede virtual, pode selecionar 10.1.0.0/24 para o espaço de endereço do cliente. Consulte o [ConetividadepontoaSitede] [ vnet-point-to-site-connectivity] página para obter mais informações.
7. Na secção de espaços de endereços de rede virtual, clique em **adicionar sub-rede do gateway**.
8. Clique em **guardar** na parte inferior da página.
9. Clique em **Sim** para confirmar a alteração. Aguarde até que o sistema concluiu a efetuar a alteração antes de avançar para o procedimento seguinte.

**Para criar um gateway de encaminhamento dinâmico**

1. No Portal clássico do Azure, clique em **DASHBOARD** da parte superior da página.
2. Clique em **criar GATEWAY** na parte inferior da página.
3. Clique em **Sim** para confirmar. Aguarde até que o gateway ser criado.
4. Clique em **DASHBOARD** do nível superior.  Irá ver um diagrama visual da rede virtual:

    ![Diagrama de virtual de ponto a site de rede virtual do Azure][img-vnet-diagram]

    O diagrama mostra 0 ligações de cliente. Depois de efetuar uma ligação à rede virtual, o número será atualizado para um.

#### <a name="create-your-certificates"></a>Criar os certificados
É uma forma de criar um certificado x. 509 utilizando a ferramenta de criação de certificados (makecert.exe) que vem com [Microsoft Visual Studio Express para Windows Desktop](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).

**Para criar um certificado de raiz autoassinado**

1. A partir da sua estação de trabalho, abra uma janela de linha de comandos.
2. Navegue para a pasta de ferramentas do Visual Studio.
3. O seguinte comando no exemplo abaixo irá criar e instale um certificado de raiz no arquivo de certificados pessoais na estação de trabalho e também criar um ficheiro. cer correspondente que mais tarde deverá carregar para o Portal clássico do Azure.

        makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

    Mude para o diretório que pretende que o ficheiro. cer esteja localizada, onde HBaseVnetVPNRootCertificate é o nome que pretende utilizar para o certificado.

    Não feche a linha de comandos.  Precisará no procedimento seguinte.

   > [!NOTE]
   > Uma vez que criou um certificado de raiz a partir do qual serão gerados certificados de cliente, deverá exportar este certificado, juntamente com a respetiva chave privada, e guardá-lo numa localização segura, onde possa ser recuperado.
   >
   >

**Para criar um certificado de cliente**

* Na mesma linha de comandos (tem de estar no mesmo computador onde criou o certificado de raiz. O certificado de cliente tem de ser gerado do certificado de raiz), execute o seguinte comando:

          makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

    HBaseVnetVPNRootCertificate é o nome do certificado de raiz.  Tem de corresponder ao nome do certificado de raiz.  

    O certificado de raiz e o certificado de cliente são armazenadas no arquivo de certificados pessoais no seu computador. Utilize certmgr.msc para verificar.

    ![Certificado VPN de ponto a site de rede virtual do Azure][img-certificate]

    É necessário instalar um certificado de cliente em cada computador que pretende ligar à rede virtual. Recomendamos que crie certificados de cliente exclusivos para cada computador que pretende ligar à rede virtual. Para exportar os certificados de cliente, utilize certmgr.msc.

**Para carregar o certificado de raiz para o Portal clássico do Azure**

1. No Portal clássico do Azure, clique em **rede** à esquerda.
2. Clique na rede virtual em que o cluster HBase é implementado.
3. Clique em **certificados** do nível superior.
4. Clique em **carregar** na parte inferior e especifique o ficheiro de certificado de raiz que criou no procedimento anteriores à última. Aguarde até que o certificado foi importado.
5. Clique em **DASHBOARD** na parte superior.  O diagrama virtual mostra o estado.

#### <a name="configure-your-vpn-client"></a>Configurar o cliente VPN
**Para transferir e instalar o pacote de VPN cliente**

1. Na página do DASHBOARD da rede virtual, na secção de leitura rápida, clique em **transfira o pacote de VPN do cliente de 64 bits** ou **transfira o pacote de VPN do cliente de 32 bits** com base na estação de trabalho da SO versão.
2. Clique em **executar** para instalar o pacote.
3. Na linha de segurança, clique em **obter mais informações**e, em seguida, clique em **executar mesmo assim**.
4. Clique em **Sim** duas vezes.

**Para ligar a VPN**

1. No ambiente de trabalho da sua estação de trabalho, clique no ícone de redes na barra de tarefas. Deverá ver uma ligação VPN com o nome de rede virtual.
2. Clique no nome de ligação VPN.
3. Clique em **Ligar**.

**Testar a resolução de nome de domínio e de ligação de VPN**

* A partir da estação de trabalho, abra uma linha de comandos, não sendo ping um dos seguintes nomes dado o sufixo DNS do cluster HBase myhbase.b7.internal.cloudapp.net:

        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        headnode0.myhbase.b7.internal.cloudapp.net
        headnode1.myhbase.b7.internal.cloudapp.net
        workernode0.myhbase.b7.internal.cloudapp.net

### <a name="install-and-configure-squirrel-on-your-workstation"></a>Instalar e configurar o SQuirreL na estação de trabalho
**Para instalar o SQuirreL**

1. Transfira o SQuirreL SQL cliente jar ficheiro [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Abra/executar o ficheiro jar. Requer o [ambiente de tempo de execução Java](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Clique em **seguinte** duas vezes.
4. Especifique um caminho de tenha a permissão de escrita e, em seguida, clique em **seguinte**.

  > [!NOTE]
  > A pasta de instalação predefinida é a pasta C:\Program Files\squirrel-sql-3.6.  Para poder escrever este caminho, o instalador tem de ser concedido o privilégio de administrador. Pode abrir uma linha de comandos como administrador, navegue até à pasta de reciclagem do Java e, em seguida, execute:
  >
  >     Java.exe-jar [o caminho do ficheiro jar SQuirreL]
5. Clique em **OK** para confirmar a criar o diretório de destino.
6. A predefinição é instalar o Base e os pacotes padrão.  Clique em **Seguinte**.
7. Clique em **seguinte** duas vezes e, em seguida, clique em **feito**.

**Para instalar o controlador de Phoenix**

O ficheiro jar do phoenix controlador está localizado no cluster de HBase. O caminho é semelhante ao seguinte com base nas versões:

    C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
É necessário copiá-lo para a estação de trabalho em [pasta de instalação SQuirreL] / lib caminho.  A forma mais fácil para RDP para o cluster e, em seguida, utilize o ficheiro de copiar/colar (CTRL + C e CTRL + V) para a copiar para a estação de trabalho.

**Para adicionar um controlador de Phoenix SQuirreL**

1. Abra o SQuirreL SQL Client na sua estação de trabalho.
2. Clique em de **controlador** separador no lado esquerdo.
3. Do **controladores** menu, clique em **novo controlador**.
4. Introduza as seguintes informações:

   * **Nome**: Phoenix
   * **URL do exemplo**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
   * **Nome de classe**: org.apache.phoenix.jdbc.PhoenixDriver

     > [!WARNING]
     > Utilizador todas as minúsculas no URL do exemplo. Pode utilizar estes quórum zookeeper completa no caso de uma delas está inativo.  Os nomes de anfitriões são zookeeper0, zookeeper1 e zookeeper2.
     >
     >

     ![Controlador SQuirreL de Phoenix de HBase do HDInsight][img-squirrel-driver]
5. Clique em **OK**.

**Para criar um alias para o cluster de HBase**

1. No SQuirreL, clique o **Aliases** separador no lado esquerdo.
2. Do **Aliases** menu, clique em **Novo Alias**.
3. Introduza as seguintes informações:

   * **Nome**: O nome do HBase cluster ou qualquer nome que preferir.
   * **Controlador**: Phoenix.  Isto deve corresponder ao nome do controlador que criou no último procedimento.
   * **URL**: O URL é copiado da sua configuração de controlador. Certifique-se ao utilizador todas as letras maiúsculas e minúsculas.
   * **Nome de utilizador**: pode ser qualquer texto.  Porque utilizar conectividade VPN aqui, o nome de utilizador não é utilizado de todo.
   * **Palavra-passe**: pode ser qualquer texto.

     ![Controlador SQuirreL de Phoenix de HBase do HDInsight][img-squirrel-alias]
4. Clique em **teste**.
5. Clique em **Ligar**. Quando faz a ligação, SQuirreL aspeto:

    ![HBase Phoenix SQuirreL][img-squirrel]

**Para executar um teste**

1. Clique em de **SQL** separador direito seguinte para o **objetos** separador.
2. Copie e cole o seguinte código:

        CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Clique no botão de execução.

    ![HBase Phoenix SQuirreL][img-squirrel-sql]
4. Voltar ao painel de **objetos** separador.
5. Expanda o nome de alias e, em seguida, expanda **tabela**.  Deverá ver a nova tabela listada na.

## <a name="next-steps"></a>Passos seguintes
Neste artigo, aprendeu a utilizar o Apache Phoenix no HDInsight.  Para obter mais informações, consulte

* [Descrição geral do HBase do HDInsight ][hdinsight-hbase-overview]: O HBase é uma base de dados NoSQL open source do Apache, baseada no Hadoop, que fornece acesso aleatório e uma sólida consistência para grandes quantidades de dados não estruturados e semi-estruturados.
* [Aprovisionar clusters do HBase no Azure Virtual Network][hdinsight-hbase-provision-vnet]: com a integração de rede virtual, clusters de HBase podem ser implementados para a mesma rede virtual que as suas aplicações para que as aplicações podem comunicar com o HBase diretamente.
* [Configurar a replicação do HBase no HDInsight](hbase/apache-hbase-replication.md): Saiba como configurar a replicação do HBase entre dois centros de dados do Azure.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:hbase/apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:hbase/apache-hbase-overview.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png
