---
title: Resolver problemas de HBase utilizando o Azure HDInsight | Microsoft Docs
description: "Obtenha respostas a questões recorrentes sobre como trabalhar com o HBase e o Azure HDInsight."
services: hdinsight
documentationcenter: 
author: nitinver
manager: ashitg
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 7/7/2017
ms.author: nitinver
ms.openlocfilehash: cd6315c192ad3c33d43406993b1a3e6bd6ec7e4d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-hbase-by-using-azure-hdinsight"></a>Resolver problemas de HBase utilizando o Azure HDInsight

Saiba mais sobre os principais problemas e as resoluções ao trabalhar com múltiplos payloads Apache HBase no Apache Ambari.

## <a name="how-do-i-run-hbck-command-reports-with-multiple-unassigned-regions"></a>Como executar relatórios de comando hbck a com várias regiões não atribuídas?

Uma mensagem de erro comuns que poderá ver quando executa o `hbase hbck` comando é "várias regiões a ser não atribuídas ou holes na cadeia de regiões."

Na IU do mestre de HBase, pode ver o número de regiões que são desequilibrada em todos os servidores de região. Em seguida, pode executar `hbase hbck` comando para ver holes na cadeia de região.

Poderá ser holes, por isso, causado pelas regiões offline, corrigir primeiro as atribuições. 

Para colocar as regiões não atribuídas um estado normal, conclua os seguintes passos:

1. Inicie sessão no cluster HBase do HDInsight através de SSH.
2. Para estabelecer ligação com a shell de ZooKeeper, execute o `hbase zkcli` comando.
3. Execute o `rmr /hbase/regions-in-transition` comando ou o `rmr /hbase-unsecure/regions-in-transition` comando.
4. Para sair do `hbase zkcli` shell, utilize o `exit` comando.
5. Abra a IU do Apache Ambari e, em seguida, reinicie o serviço de mestre de HBase do Active Directory.
6. Execute o `hbase hbck` comando novamente (sem quaisquer opções). Verifique o resultado deste comando para se certificar de que todas as regiões estão a ser atribuídas.


## <a name="how-do-i-fix-timeout-issues-with-hbck-commands-for-region-assignments"></a>Como corrigir problemas de limite de tempo quando utilizar comandos de hbck a existência de atribuições de região?

### <a name="issue"></a>Problema

Uma causa potencial de problemas de tempo limite ao utilizar o `hbck` comando pode ser que várias regiões estão no estado "em transição" durante muito tempo. Pode ver essas regiões como offline na IU do mestre de HBase. Porque um número elevado de regiões está a tentar efetuar a transição, mestre HBase poderá tempo limite e ser incapaz de colocar essas regiões novamente online.

### <a name="resolution-steps"></a>Passos de resolução

1. Inicie sessão no cluster HBase do HDInsight através de SSH.
2. Para estabelecer ligação com a shell de ZooKeeper, execute o `hbase zkcli` comando.
3. Execute o `rmr /hbase/regions-in-transition` ou `rmr /hbase-unsecure/regions-in-transition` comando.
4. Para sair do `hbase zkcli` shell, utilize o `exit` comando.
5. Na IU do Ambari, reinicie o serviço de mestre de HBase do Active Directory.
6. Execute o `hbase hbck -fixAssignments` novamente o comando.

## <a name="how-do-i-force-disable-hdfs-safe-mode-in-a-cluster"></a>Como posso force-desativar modo de segurança do HDFS num cluster?

### <a name="issue"></a>Problema

O local distribuídas ficheiro sistema Hadoop (HDFS) está encravada no modo de segurança no cluster do HDInsight.

### <a name="detailed-description"></a>Descrição detalhada

Este erro poderá ser causado por uma falha ao executar o seguinte comando do HDFS:

```apache
hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
```

O erro que poderá ver quando tentar executar o comando tem o seguinte aspeto:

```apache
hdiuser@hn0-spark2:~$ hdfs dfs -D "fs.default.name=hdfs://mycluster/" -mkdir /temp
17/04/05 16:20:52 WARN retry.RetryInvocationHandler: Exception while invoking ClientNamenodeProtocolTranslatorPB.mkdirs over hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net/10.0.0.22:8020. Not retrying because try once and fail.
org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.hdfs.server.namenode.SafeModeException): Cannot create directory /temp. Name node is in safe mode.
It was turned on manually. Use "hdfs dfsadmin -safemode leave" to turn safe mode off.
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.checkNameNodeSafeMode(FSNamesystem.java:1359)
        at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.mkdirs(FSNamesystem.java:4010)
        at org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.mkdirs(NameNodeRpcServer.java:1102)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolServerSideTranslatorPB.mkdirs(ClientNamenodeProtocolServerSideTranslatorPB.java:630)
        at org.apache.hadoop.hdfs.protocol.proto.ClientNamenodeProtocolProtos$ClientNamenodeProtocol$2.callBlockingMethod(ClientNamenodeProtocolProtos.java)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Server$ProtoBufRpcInvoker.call(ProtobufRpcEngine.java:640)
        at org.apache.hadoop.ipc.RPC$Server.call(RPC.java:982)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2313)
        at org.apache.hadoop.ipc.Server$Handler$1.run(Server.java:2309)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:422)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1724)
        at org.apache.hadoop.ipc.Server$Handler.run(Server.java:2307)
        at org.apache.hadoop.ipc.Client.getRpcResponse(Client.java:1552)
        at org.apache.hadoop.ipc.Client.call(Client.java:1496)
        at org.apache.hadoop.ipc.Client.call(Client.java:1396)
        at org.apache.hadoop.ipc.ProtobufRpcEngine$Invoker.invoke(ProtobufRpcEngine.java:233)
        at com.sun.proxy.$Proxy10.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.protocolPB.ClientNamenodeProtocolTranslatorPB.mkdirs(ClientNamenodeProtocolTranslatorPB.java:603)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invokeMethod(RetryInvocationHandler.java:278)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:194)
        at org.apache.hadoop.io.retry.RetryInvocationHandler.invoke(RetryInvocationHandler.java:176)
        at com.sun.proxy.$Proxy11.mkdirs(Unknown Source)
        at org.apache.hadoop.hdfs.DFSClient.primitiveMkdir(DFSClient.java:3061)
        at org.apache.hadoop.hdfs.DFSClient.mkdirs(DFSClient.java:3031)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1162)
        at org.apache.hadoop.hdfs.DistributedFileSystem$24.doCall(DistributedFileSystem.java:1158)
        at org.apache.hadoop.fs.FileSystemLinkResolver.resolve(FileSystemLinkResolver.java:81)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirsInternal(DistributedFileSystem.java:1158)
        at org.apache.hadoop.hdfs.DistributedFileSystem.mkdirs(DistributedFileSystem.java:1150)
        at org.apache.hadoop.fs.FileSystem.mkdirs(FileSystem.java:1898)
        at org.apache.hadoop.fs.shell.Mkdir.processNonexistentPath(Mkdir.java:76)
        at org.apache.hadoop.fs.shell.Command.processArgument(Command.java:273)
        at org.apache.hadoop.fs.shell.Command.processArguments(Command.java:255)
        at org.apache.hadoop.fs.shell.FsCommand.processRawArguments(FsCommand.java:119)
        at org.apache.hadoop.fs.shell.Command.run(Command.java:165)
        at org.apache.hadoop.fs.FsShell.run(FsShell.java:297)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
        at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:90)
        at org.apache.hadoop.fs.FsShell.main(FsShell.java:350)
mkdir: Cannot create directory /temp. Name node is in safe mode.
```

### <a name="probable-cause"></a>Causa provável

O cluster do HDInsight tem foi escalado para baixo até um muito alguns nós. O número de nós é inferior ou perto o fator de replicação do HDFS.

### <a name="resolution-steps"></a>Passos de resolução 

1. Obter o estado do HDFS no cluster do HDInsight, executando os seguintes comandos:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   ```

   ```apache
   hdiuser@hn0-spark2:~$ hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -report
   Safe mode is ON
   Configured Capacity: 3372381241344 (3.07 TB)
   Present Capacity: 3138625077248 (2.85 TB)
   DFS Remaining: 3102710317056 (2.82 TB)
   DFS Used: 35914760192 (33.45 GB)
   DFS Used%: 1.14%
   Under replicated blocks: 0
   Blocks with corrupt replicas: 0
   Missing blocks: 0
   Missing blocks (with replication factor 1): 0

   -------------------------------------------------
   Live datanodes (8):

   Name: 10.0.0.17:30010 (10.0.0.17)
   Hostname: 10.0.0.17
   Decommission Status : Normal
   Configured Capacity: 421547655168 (392.60 GB)
   DFS Used: 5288128512 (4.92 GB)
   Non DFS Used: 29087272960 (27.09 GB)
   DFS Remaining: 387172253696 (360.58 GB)
   DFS Used%: 1.25%
   DFS Remaining%: 91.85%
   Configured Cache Capacity: 0 (0 B)
   Cache Used: 0 (0 B)
   Cache Remaining: 0 (0 B)
   Cache Used%: 100.00%
   Cache Remaining%: 0.00%
   Xceivers: 2
   Last contact: Wed Apr 05 16:22:00 UTC 2017
   ...

   ```
2. Também pode verificar a integridade das HDFS no cluster do HDInsight utilizando os seguintes comandos:

   ```apache
   hdiuser@hn0-spark2:~$ hdfs fsck -D "fs.default.name=hdfs://mycluster/" /
   ```

   ```apache
   Connecting to namenode via http://hn0-spark2.2oyzcdm4sfjuzjmj5dnmvscjpg.dx.internal.cloudapp.net:30070/fsck?ugi=hdiuser&path=%2F
   FSCK started by hdiuser (auth:SIMPLE) from /10.0.0.22 for path / at Wed Apr 05 16:40:28 UTC 2017
   ....................................................................................................

   ....................................................................................................
   ..................Status: HEALTHY
   Total size:    9330539472 B
   Total dirs:    37
   Total files:   2618
   Total symlinks:                0 (Files currently being written: 2)
   Total blocks (validated):      2535 (avg. block size 3680686 B)
   Minimally replicated blocks:   2535 (100.0 %)
   Over-replicated blocks:        0 (0.0 %)
   Under-replicated blocks:       0 (0.0 %)
   Mis-replicated blocks:         0 (0.0 %)
   Default replication factor:    3
   Average block replication:     3.0
   Corrupt blocks:                0
   Missing replicas:              0 (0.0 %)
   Number of data-nodes:          8
   Number of racks:               1
   FSCK ended at Wed Apr 05 16:40:28 UTC 2017 in 187 milliseconds

   The filesystem under path '/' is HEALTHY
   ```

3. Se determinar que existem não existem em falta, danificado, ou blocos under-replicados ou que podem ser ignorados esses blocos, execute o seguinte comando para tirar o nó nome fora do modo de segurança:

   ```apache
   hdfs dfsadmin -D "fs.default.name=hdfs://mycluster/" -safemode leave
   ```


## <a name="how-do-i-fix-jdbc-or-sqlline-connectivity-issues-with-apache-phoenix"></a>Como corrija a conectividade de JDBC ou SQLLine problemas com o Apache Phoenix?

### <a name="resolution-steps"></a>Passos de resolução

Para estabelecer ligação com o Phoenix, tem de fornecer o endereço IP de um nó de ZooKeeper ativo. Certifique-se de que o serviço de ZooKeeper que sqlline.py está a tentar ligar-se está a funcionar.
1. Inicie sessão no cluster do HDInsight através de SSH.
2. Introduza o seguinte comando:
                
   ```apache
           "/usr/hdp/current/phoenix-client/bin/sqlline.py <IP of machine where Active Zookeeper is running"
   ```

   > [!Note] 
   > Pode obter o endereço IP do nó ZooKeeper Active Directory a partir da IU do Ambari. Aceda a **HBase** > **ligações rápidas** > **ZK\* (ativo)** > **Zookeeper informações**. 

3. Se o sqlline.py liga ao Phoenix e não não tempo limite, execute o seguinte comando para validar a disponibilidade e o estado de funcionamento de Phoenix:

   ```apache
           !tables
           !quit
   ```      
4. Se este comando funciona, não há nenhum problema. O endereço IP fornecido pelo utilizador poderá estar incorreto. No entanto, se o comando interrompe para um período de tempo alargado e, em seguida, apresenta o seguinte erro, continue para o passo 5.

   ```apache
           Error while connecting to sqlline.py (Hbase - phoenix) Setting property: [isolation, TRANSACTION_READ_COMMITTED] issuing: !connect jdbc:phoenix:10.2.0.7 none none org.apache.phoenix.jdbc.PhoenixDriver Connecting to jdbc:phoenix:10.2.0.7 SLF4J: Class path contains multiple SLF4J bindings. 
   ```

5. Execute os seguintes comandos a partir do nó principal (hn0) para diagnosticar a condição do sistema Phoenix. Tabela de catálogo:

   ```apache
            hbase shell
                
           count 'SYSTEM.CATALOG'
   ```

   O comando deverá devolver um erro semelhante ao seguinte: 

   ```apache
           ERROR: org.apache.hadoop.hbase.NotServingRegionException: Region SYSTEM.CATALOG,,1485464083256.c0568c94033870c517ed36c45da98129. is not online on 10.2.0.5,16020,1489466172189) 
   ```
6. Na IU do Ambari, conclua os seguintes passos para reiniciar o serviço de HMaster em todos os nós de ZooKeeper:

    1. No **resumo** secção do HBase, aceda a **HBase** > **mestre do Active Directory HBase**. 
    2. No **componentes** secção, reinicie o serviço de mestre de HBase.
    3. Repita estes passos para todas as restantes **mestre do modo de espera HBase** serviços. 

Pode demorar até cinco minutos para que o serviço de mestre de HBase para stabilize e concluir o processo de recuperação. Após alguns minutos, repita os comandos de sqlline.py para confirmar que o sistema. Tabela de catálogo está operacional e que as TI podem ser consultada. 

Quando o sistema. Tabela de catálogo para normal, o problema de conectividade para Phoenix deve ser resolvido automaticamente.


## <a name="what-causes-a-master-server-to-fail-to-start"></a>O que faz com que um servidor principal falhar ao iniciar?

### <a name="error"></a>Erro 

Ocorre uma falha de mudança de nome atomic.

### <a name="detailed-description"></a>Descrição detalhada

Durante o processo de arranque, HMaster concluir vários passos de inicialização. Estes incluem mover dados a partir da pasta zero (.tmp) para a pasta de dados. HMaster também analisa a pasta de registos de escrita antecipada (WALs) para ver se existem quaisquer servidores de região de responder, e assim sucessivamente. 

Durante o arranque, HMaster num nível básico `list` comando estas pastas. Se em qualquer altura, HMaster vê um ficheiro inesperado em nenhum destas pastas, emite uma exceção e não comece.  

### <a name="probable-cause"></a>Causa provável

Nos registos do servidor de região, tente identificar a linha cronológica da criação do ficheiro e, em seguida, ver se Ocorreu uma falha de processo perto da hora que de ficheiro foi criado. (Contacte o suporte de HBase para ajudá-lo a fazê-lo.) Este ajuda-na fornecer mais robustos mecanismos, para que possa evitar atingir este erro e certifique-se encerramentos processo correto.

### <a name="resolution-steps"></a>Passos de resolução

Verifique a pilha de chamadas e tente determinar a pasta qual poderá estar a causar o problema (por exemplo, poderá ser a pasta de WALs ou a pasta de .tmp). Em seguida, no Explorador de nuvem ou através da utilização de comandos HDFS, tente localizar o ficheiro de problema. Normalmente, este é um \*-renamePending.json ficheiro. (O \*-renamePending.json ficheiro é um ficheiro de diário de alterações que é utilizado para implementar a operação de mudança de nome atómico no controlador WASB. Devido a erros nesta implementação, estes ficheiros podem ser deixados ao longo depois de falhas de processos e etc.) Força-eliminar este ficheiro no Explorador de nuvem ou através da utilização de comandos HDFS. 

Por vezes, também poderá haver um ficheiro temporário denominado algo semelhante ao seguinte *$$$. $$$* nesta localização. Tem de utilizar o HDFS `ls` comando para ver este ficheiro; não é possível ver o ficheiro no Cloud Explorer. Para eliminar este ficheiro, utilize o comando HDFS `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`.  

Após executar estes comandos, HMaster deve começar imediatamente. 

### <a name="error"></a>Erro

O endereço do servidor não está listado no *hbase: meta* para xxx de região.

### <a name="detailed-description"></a>Descrição detalhada

Poderá ver uma mensagem no cluster do Linux que indica que o *hbase: meta* tabela não se encontra online. Executar `hbck` pode reportar que "hbase: meta tabela replicaId 0 não se encontra em qualquer região." O problema poderá ser que HMaster não foi possível inicializar depois de reiniciado o HBase. Nos registos HMaster, poderá ver a mensagem: "nenhum endereço do servidor listados no hbase: meta para região hbase: cópia de segurança \<nome de região\>".  

### <a name="resolution-steps"></a>Passos de resolução

1. Na shell do HBase, introduza os comandos seguintes (alteração real valores, conforme aplicável):  

   ```apache
   > scan 'hbase:meta'  
   ```

   ```apache
   > delete 'hbase:meta','hbase:backup <region name>','<column name>'  
   ```

2. Eliminar o *hbase: espaço de nomes* entrada. Esta entrada pode ser o mesmo erro que está a ser reportados quando o *hbase: espaço de nomes* tabela é procurada.

3. Trazer para primeiro o HBase no estado de execução, na IU do Ambari, reinicie o serviço de HMaster Active Directory.  

4. Na shell do HBase, para trazer todas as tabelas offline, execute o seguinte comando:

   ```apache 
   hbase hbck -ignorePreCheckPermission -fixAssignments 
   ```

### <a name="additional-reading"></a>Leitura adicional

[Não é possível processar a tabela HBase](http://stackoverflow.com/questions/4794092/unable-to-access-hbase-table)


### <a name="error"></a>Erro

HMaster exceder o tempo limite com uma exceção fatal semelhante a "java.io.IOException: ServiceHost 300000ms a aguardar para a tabela de espaço de nomes a ser atribuída."

### <a name="detailed-description"></a>Descrição detalhada

Podem ocorrer este problema se tiver várias tabelas e regiões que não foram descarregadas quando reiniciar os serviços de HMaster. Reinício poderá falhar e verá a mensagem de erro anterior.  

### <a name="probable-cause"></a>Causa provável

Este é um problema conhecido com o serviço de HMaster. Tarefas de arranque do cluster geral podem demorar muito tempo. HMaster encerrado porque a tabela de espaço de nomes ainda não está atribuída. Isto ocorre apenas em situações em que a grande quantidade de dados unflushed existe e de um tempo limite de cinco minutos não é suficiente.
  
### <a name="resolution-steps"></a>Passos de resolução

1. Na IU do Ambari, aceda a **HBase** > **folhas**. No ficheiro de hbase-site.xml personalizada, adicione a seguinte definição: 

   ```apache
   Key: hbase.master.namespace.init.timeout Value: 2400000  
   ```

2. Reinicie os serviços necessários (HMaster e possivelmente outros serviços do HBase).  


## <a name="what-causes-a-restart-failure-on-a-region-server"></a>O que causa uma falha de reinício num servidor região?

### <a name="issue"></a>Problema

Uma falha de reinício num servidor região pode ser impedida ao seguintes melhores práticas. Recomendamos que coloque em pausa atividade pesada carga de trabalho quando estiver a planear reiniciar os servidores de região de HBase. Se uma aplicação continua a estabelecer ligação com os servidores de região quando shutdown está em curso, a operação de reinício do servidor de região será mais lenta por vários minutos. Além disso, é boa ideia primeiro esvaziar todas as tabelas. Para obter uma referência sobre como remover da cache tabelas, consulte [HBase do HDInsight: como melhorar a hora de reinício de cluster HBase ao limpar as tabelas](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

Se iniciar a operação de reinício nos servidores de região de HBase da IU do Ambari, poderá ver de imediato que os servidores de região correu para baixo, mas não reiniciem imediato. 

Eis o que acontece em segundo plano: 

1. O agente do Ambari envia um pedido de paragem para o servidor de região.
2. O agente do Ambari aguarda 30 segundos para o servidor de região encerrar corretamente. 
3. Se a aplicação continua a estabelecer ligação com o servidor de região, o servidor não irá encerrar imediatamente. O tempo limite de 30 segundo expira antes de ocorre o encerramento. 
4. Depois de 30 segundos, o agente do Ambari envia um kill de imposição (`kill -9`) comando para o servidor de região. Pode ver este no registo do agente do ambari (no /var/registo/diretório do nó de trabalho respetivo):

   ```apache
           2017-03-21 13:22:09,171 - Execute['/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/current/hbase-regionserver/conf stop regionserver'] {'only_if': 'ambari-sudo.sh  -H -E t
           est -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1', 'on_timeout': '! ( ambari-sudo.sh  -H -E test -
           f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >/dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H 
           -E cat /var/run/hbase/hbase-hbase-regionserver.pid`', 'timeout': 30, 'user': 'hbase'}
           2017-03-21 13:22:40,268 - Executing '! ( ambari-sudo.sh  -H -E test -f /var/run/hbase/hbase-hbase-regionserver.pid && ps -p `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid` >
           /dev/null 2>&1 ) || ambari-sudo.sh -H -E kill -9 `ambari-sudo.sh  -H -E cat /var/run/hbase/hbase-hbase-regionserver.pid`'. Reason: Execution of 'ambari-sudo.sh su hbase -l -s /bin/bash -c 'export  
           PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/var/lib/ambari-agent ; /usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh --config /usr/hdp/curre
           nt/hbase-regionserver/conf stop regionserver was killed due timeout after 30 seconds
           2017-03-21 13:22:40,285 - File['/var/run/hbase/hbase-hbase-regionserver.pid'] {'action': ['delete']}
           2017-03-21 13:22:40,285 - Deleting File['/var/run/hbase/hbase-hbase-regionserver.pid']
   ```
Devido um encerramento abrupto, a porta associada ao processo poderá não ser libertada, apesar do processo de servidor de região está parado. Esta situação pode levar a uma AddressBindException quando está a iniciar o servidor de região, conforme mostrado nos seguintes registos. Pode verificar isto na região-server.log no diretório /var/log/hbase em nós de trabalho onde o servidor de região conseguir iniciar. 

   ```apache

   2017-03-21 13:25:47,061 ERROR [main] regionserver.HRegionServerCommandLine: Region server exiting
   java.lang.RuntimeException: Failed construction of Regionserver: class org.apache.hadoop.hbase.regionserver.HRegionServer
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2636)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.start(HRegionServerCommandLine.java:64)
   at org.apache.hadoop.hbase.regionserver.HRegionServerCommandLine.run(HRegionServerCommandLine.java:87)
   at org.apache.hadoop.util.ToolRunner.run(ToolRunner.java:76)
   at org.apache.hadoop.hbase.util.ServerCommandLine.doMain(ServerCommandLine.java:126)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.main(HRegionServer.java:2651)
        
   Caused by: java.lang.reflect.InvocationTargetException
   at sun.reflect.NativeConstructorAccessorImpl.newInstance0(Native Method)
   at sun.reflect.NativeConstructorAccessorImpl.newInstance(NativeConstructorAccessorImpl.java:57)
   at sun.reflect.DelegatingConstructorAccessorImpl.newInstance(DelegatingConstructorAccessorImpl.java:45)
   at java.lang.reflect.Constructor.newInstance(Constructor.java:526)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.constructRegionServer(HRegionServer.java:2634)
   ... 5 more
        
   Caused by: java.net.BindException: Problem binding to /10.2.0.4:16020 : Address already in use
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2497)
   at org.apache.hadoop.hbase.ipc.RpcServer$Listener.<init>(RpcServer.java:580)
   at org.apache.hadoop.hbase.ipc.RpcServer.<init>(RpcServer.java:1982)
   at org.apache.hadoop.hbase.regionserver.RSRpcServices.<init>(RSRpcServices.java:863)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.createRpcServices(HRegionServer.java:632)
   at org.apache.hadoop.hbase.regionserver.HRegionServer.<init>(HRegionServer.java:532)
   ... 10 more
        
   Caused by: java.net.BindException: Address already in use
   at sun.nio.ch.Net.bind0(Native Method)
   at sun.nio.ch.Net.bind(Net.java:463)
   at sun.nio.ch.Net.bind(Net.java:455)
   at sun.nio.ch.ServerSocketChannelImpl.bind(ServerSocketChannelImpl.java:223)
   at sun.nio.ch.ServerSocketAdaptor.bind(ServerSocketAdaptor.java:74)
   at org.apache.hadoop.hbase.ipc.RpcServer.bind(RpcServer.java:2495)
   ... 15 more
   ```

### <a name="resolution-steps"></a>Passos de resolução

1. Tente reduzir a carga nos servidores de região de HBase antes de iniciar um reinício. 
2. Em alternativa (se o passo 1 não ajuda), tente reiniciar manualmente os servidores de região em nós de trabalho, utilizando os seguintes comandos:

   ```apache
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh stop regionserver"
   sudo su - hbase -c "/usr/hdp/current/hbase-regionserver/bin/hbase-daemon.sh start regionserver"   
   ```

### <a name="see-also"></a>Veja Também
[Resolver problemas utilizando o Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)