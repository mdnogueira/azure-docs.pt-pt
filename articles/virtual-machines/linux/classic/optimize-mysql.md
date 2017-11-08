---
title: Otimizar o desempenho de MySQL no Linux | Microsoft Docs
description: "Saiba como otimizar o MySQL em execução numa máquina virtual do Azure (VM) com Linux."
services: virtual-machines-linux
documentationcenter: 
author: NingKuang
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 0c1c7fc5-a528-4d84-b65d-2df225f2233f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/31/2017
ms.author: ningk
ms.openlocfilehash: 7e7582a31cb3e74fd8c3cd0dd54961392d9c53bb
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2017
---
# <a name="optimize-mysql-performance-on-azure-linux-vms"></a>Otimizar o desempenho de MySQL em VMs do Linux do Azure
Existem muitos fatores que afetam o desempenho de MySQL no Azure, tanto na seleção de virtual hardware e a configuração de software. Este artigo incida no otimizar o desempenho através de armazenamento, sistema e configurações de base de dados.

> [!IMPORTANT]
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [do Azure Resource Manager](../../../resource-manager-deployment-model.md) e clássico. Este artigo cobre a utilização do modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager. Para obter informações sobre as otimizações de VM com Linux com o modelo do Resource Manager, consulte [otimizar a VM com Linux no Azure](../optimization.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="utilize-raid-on-an-azure-virtual-machine"></a>Utilizar RAID numa máquina virtual do Azure
O armazenamento é o fator chave que afeta o desempenho de base de dados em ambientes de nuvem. Em comparação com um único disco, RAID pode fornecer um acesso mais rápido através de concorrência. Para obter mais informações, consulte [níveis de RAID padrão](http://en.wikipedia.org/wiki/Standard_RAID_levels).   

Débito de e/s de disco e tempo de resposta de e/s no Azure podem ser melhoradas através de RAID. Os nossos testes de laboratório mostram que o débito de e/s de disco pode ser duplicado e tempo de resposta de e/s pode ser reduzido por meio em média quando o número de discos RAID está duplicado (a partir de dois a quatro, quatro para oito, etc.). Consulte [anexo A](#AppendixA) para obter mais detalhes.  

Para além de e/s de disco, o MySQL desempenho melhora quando aumenta o nível RAID.  Consulte [apêndice B](#AppendixB) para obter mais detalhes.  

Também poderá considerar o tamanho do segmento. Em geral, quando tem um tamanho de segmento maior, pode obter inferior dos custos gerais, especialmente para escritas grandes. No entanto, quando o tamanho do segmento é demasiado grande, poderá adicionar overhead adicional que impede a tirar partido do RAID. O tamanho predefinido da atual é 512 KB, que é provada para ser o ideal para ambientes de produção mais gerais. Consulte [apêndice C](#AppendixC) para obter mais detalhes.   

Existem limites no quantos discos pode adicionar para tipos de outra máquina virtual. Estes limites passo são detalhadas no [tamanhos do serviço de Máquina Virtual e em nuvem do Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx). Precisa de quatro discos de dados anexados a seguir o exemplo RAID neste artigo, embora possa escolher configurar RAID com menos de discos.  

Este artigo pressupõe que já criou uma máquina virtual Linux e ter o MYSQL instalado e configurado. Para obter mais informações sobre começar a utilizar, consulte como instalar o MySQL no Azure.  

### <a name="set-up-raid-on-azure"></a>Configurar RAID no Azure
Os passos seguintes mostram como criar RAID no Azure utilizando o portal do Azure. Também pode configurar RAID utilizando scripts do Windows PowerShell.
Neste exemplo, iremos irá configurar RAID 0 com quatro discos.  

#### <a name="add-a-data-disk-to-your-virtual-machine"></a>Adicionar um disco de dados à máquina virtual
No portal do Azure, aceda ao dashboard e selecione a máquina virtual para o qual pretende adicionar um disco de dados. Neste exemplo, a máquina virtual é mysqlnode1.  

<!--![Virtual machines][1]-->

Clique em **discos** e, em seguida, clique em **anexar novo**.

![Máquinas virtuais adicionar disco](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-Disks-option.png)

Crie um novo disco de 500 GB. Certifique-se de que **preferência de Cache do anfitrião** está definido como **nenhum**.  Quando tiver terminado, clique em **OK**.

![Anexar o disco em branco](media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-attach-empty-disk.png)


Esta ação adiciona um disco vazio para a máquina virtual. Repita este passo três vezes mais para tem quatro discos de dados para o RAID.  

Pode ver as unidades na máquina virtual foi adicionadas ao observar o registo de mensagens do kernel. Por exemplo, para ver isto em Ubuntu, utilize o seguinte comando:  

    sudo grep SCSI /var/log/dmesg

#### <a name="create-raid-with-the-additional-disks"></a>Criar RAID com os discos adicionais
Os passos seguintes descrevem como [configurar software RAID no Linux](../configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

> [!NOTE]
> Se estiver a utilizar o sistema de ficheiros XFS, execute os passos seguintes depois de ter criado o RAID.
>
>

Para instalar XFS em Debian, Ubuntu ou Linux Mint, utilize o seguinte comando:  

    apt-get -y install xfsprogs  

Instalar XFS no Fedora, CentOS ou RHEL, utilize o seguinte comando:  

    yum -y install xfsprogs  xfsdump


#### <a name="set-up-a-new-storage-path"></a>Configurar um novo caminho de armazenamento
Utilize o seguinte comando para configurar um novo caminho de armazenamento:  

    root@mysqlnode1:~# mkdir -p /RAID0/mysql

#### <a name="copy-the-original-data-to-the-new-storage-path"></a>Copiar os dados originais para o novo caminho de armazenamento
Utilize o seguinte comando para copiar dados para o novo caminho de armazenamento:  

    root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

#### <a name="modify-permissions-so-mysql-can-access-read-and-write-the-data-disk"></a>Modificar permissões para MySQL possa aceder (leitura e escrita) o disco de dados
Utilize o comando seguinte para modificar as permissões:  

    root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


## <a name="adjust-the-disk-io-scheduling-algorithm"></a>Ajustar o algoritmo de agendamento de e/s de disco
Linux implementa quatro tipos de algoritmos de agendamento de e/s:  

* Algoritmo NOOP (operação não)
* Algoritmo de prazo (prazo)
* Algoritmo de colocação completamente justa (CFQ)
* Algoritmo de período de orçamento (Anticipatory)  

Pode selecionar diferentes e/s os Agendadores em diferentes cenários para otimizar o desempenho. Num ambiente de acesso aleatório completamente, não há uma diferença significativa entre os algoritmos CFQ e o prazo de desempenho. Recomendamos que defina o ambiente de base de dados MySQL para prazo para a estabilidade. Se existir uma grande quantidade de e/s sequenciais, CFQ poderá reduzir o desempenho de e/s de disco.   

Para SSD e outro equipamento, NOOP ou prazo pode alcançar melhor desempenho do que o Programador de predefinição.   

Antes do kernel 2.5, o algoritmo de agendamento de e/s predefinido é prazo. Começando com o kernel 2.6.18, CFQ ficou o algoritmo de agendamento de e/s de predefinido.  Pode especificar esta definição no momento do arranque de kernel ou dinamicamente modificar esta definição quando o sistema está em execução.  

O exemplo seguinte demonstra como verificar e definir o Programador de predefinido para o algoritmo NOOP na família Debian de distribuição.  

### <a name="view-the-current-io-scheduler"></a>Ver o Programador de e/s atual
Para ver o programador, execute o seguinte comando:  

    root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler

Verá a seguinte saída, que indica o programador atual:  

    noop [deadline] cfq


### <a name="change-the-current-device-devsda-of-the-io-scheduling-algorithm"></a>Alterar o dispositivo atual (dev/sda) do algoritmo de agendamento e/s
Execute os seguintes comandos para alterar o dispositivo atual:  

    azureuser@mysqlnode1:~$ sudo su -
    root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
    root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
    root@mysqlnode1:~# update-grub

> [!NOTE]
> Não é útil definir este para /dev/sda individualmente. Tem de ser definido em todos os discos de dados onde reside a base de dados.  
>
>

Deverá ver o resultado seguinte, que indica que grub.cfg foi reconstruída com êxito e que o Programador de predefinição foi atualizado para NOOP:  

    Generating grub configuration file ...
    Found linux image: /boot/vmlinuz-3.13.0-34-generic
    Found initrd image: /boot/initrd.img-3.13.0-34-generic
    Found linux image: /boot/vmlinuz-3.13.0-32-generic
    Found initrd image: /boot/initrd.img-3.13.0-32-generic
    Found memtest86+ image: /memtest86+.elf
    Found memtest86+ image: /memtest86+.bin
    done

Para a família de distribuição do Red Hat, é necessário o seguinte comando:

    echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

## <a name="configure-system-file-operations-settings"></a>Configurar definições de operações de ficheiros de sistema
É uma melhor prática para desativar o *atime* funcionalidade de registo no sistema de ficheiros. Atime é a hora do último acesso de ficheiro. Sempre que aceder a um ficheiro, o sistema de ficheiros regista o timestamp no registo. No entanto, esta informação é raramente utilizada. Pode desativá-lo se não precisa de, que irá reduzir o tempo de acesso global do disco.  

Para desativar o registo de atime, terá de modificar a fstab de /etc/ de ficheiro de configuração de sistema de ficheiros e adicionar o **noatime** opção.  

Por exemplo, edite o ficheiro de /etc/fstab de vim, adicionar noatime conforme mostrado no seguinte exemplo:  

    # CLOUD_IMG: This file was created/modified by the Cloud Image build process
    UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
    #Add the “noatime” option below to disable atime logging
    UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
    /dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

Em seguida, remontar o sistema de ficheiros com o seguinte comando:  

    mount -o remount /RAID0

Teste o resultado modificado. Quando modificar o ficheiro de teste, o tempo de acesso não está atualizado. Os exemplos seguintes mostram o código de aspeto antes e após a modificação.

Antes:        

![Antes da modificação de acesso de código][5]

Depois:

![Após a modificação de acesso de código][6]

## <a name="increase-the-maximum-number-of-system-handles-for-high-concurrency"></a>Aumentar o número máximo de identificadores de sistema de concorrência elevada
MySQL é uma base de dados de concorrência elevada. O número predefinido de identificadores simultâneos é 1024 para Linux, que nem sempre é suficiente. Utilize os seguintes passos para aumentar os identificadores simultâneos máximos do sistema para suportar a simultaneidade elevada de MySQL.

### <a name="modify-the-limitsconf-file"></a>Modificar o ficheiro limits.conf
Para aumentar os máximos identificadores simultâneos permitidos, adicione as seguintes linhas de quatro no ficheiro /etc/security/limits.conf. Tenha em atenção que 65536 é o número máximo que o sistema pode suportar.   

    * recuperável nofile 65536
    * disco rígido nofile 65536
    * recuperável nproc 65536
    * disco rígido nproc 65536

### <a name="update-the-system-for-the-new-limits"></a>Atualize o sistema para os limites de novo
Para atualizar o sistema, execute os seguintes comandos:  

    ulimit -SHn 65536
    ulimit -SHu 65536

### <a name="ensure-that-the-limits-are-updated-at-boot-time"></a>Certifique-se de que os limites são atualizados no momento do arranque
Colocar os seguintes comandos de arranque no ficheiro /etc/rc.local para entrarão em vigor no momento do arranque.  

    echo “ulimit -SHn 65536” >>/etc/rc.local
    echo “ulimit -SHu 65536” >>/etc/rc.local

## <a name="mysql-database-optimization"></a>Otimização de base de dados MySQL
Para configurar o MySQL no Azure, pode utilizar a estratégia de otimização de desempenho mesma que utilizar numa máquina no local.  

As regras de otimização de e/s principais são:   

* Aumente o tamanho da cache.
* Reduza o tempo de resposta de e/s.  

Para otimizar as definições do servidor MySQL, pode atualizar o ficheiro de my.cnf, que é o ficheiro de configuração predefinida do servidor e computadores cliente.  

Os seguintes itens de configuração são os principais fatores que afetam o desempenho de MySQL:  

* **innodb_buffer_pool_size**: conjunto de memória intermédia contém dados colocados em memória intermédia e o índice. Normalmente, isto é definido para 70 por cento da memória física.
* **innodb_log_file_size**: Este é o tamanho do registo de Refazer. Utilize registos de Refazer para garantir que as operações de escrita estão rápida, fiável e recuperáveis após uma falha. Isto está definido para 512 MB, que lhe proporcionará muitos de espaço de registo de operações de escrita.
* **max_connections**:, por vezes, as aplicações não fechar as ligações corretamente. Um valor maior irá conceder-o servidor mais tempo para reciclar idled ligações. O número máximo de ligações é 10.000, mas o máximo recomendado é 5 000.
* **Innodb_file_per_table**: esta definição ativa ou desativa a capacidade de InnoDB para armazenar as tabelas nos ficheiros separados. Ative a opção para garantir que a várias operações de administração avançadas podem ser aplicadas de forma eficiente. A partir de um desempenho ponto de vista-lo pode acelerar a transmissão de espaço da tabela e otimizar o desempenho de gestão debris. A definição recomendada para esta opção está ON.</br></br>
De MySQL 5.6, a predefinição é em, pelo que é necessária nenhuma ação. Para versões anteriores, a predefinição é OFF. A definição deve ser alterada antes dos dados são carregados, porque são afetadas apenas tabelas recentemente criadas.
* **innodb_flush_log_at_trx_commit**: O valor predefinido é 1, com o âmbito definido para 0 ~ 2. O valor predefinido é a opção mais adequada autónomo, base de dados MySQL. A definição de 2 permite que a maioria dos integridade de dados e é adequada para mestre no MySQL Cluster. A definição de 0 permite perda de dados, que pode afetar a fiabilidade (em alguns casos com um melhor desempenho) e é adequado para o serviço secundário no MySQL Cluster.
* **Innodb_log_buffer_size**: A memória intermédia de registo permite transações executar sem ser necessário descarregar o registo no disco antes de consolidar as transações. No entanto, se não houver objetos binário de grandes dimensões ou o campo de texto, a cache será consumida rapidamente e e/s de disco frequentes será acionada. É melhor aumentar o tamanho de memória intermédia se Innodb_log_waits Estado variável não é 0.
* **query_cache_size**: A melhor opção é desativá-los proposto. Defina query_cache_size como 0 (esta é a predefinição na MySQL 5.6) e utilizar outros métodos para acelerar a consultas.  

Consulte [apêndice a](#AppendixD) para uma comparação dos antes e após a otimização de desempenho.

## <a name="turn-on-the-mysql-slow-query-log-for-analyzing-the-performance-bottleneck"></a>Ative o registo de consultas lenta de MySQL para analisar o estrangulamento do desempenho
O registo de consultas lenta de MySQL pode ajudar a identificar as consultas lentas para MySQL. Depois de ativar o registo de consultas lenta de MySQL, pode utilizar ferramentas de MySQL como **mysqldumpslow** para identificar o estrangulamento do desempenho.  

Por predefinição, este não está ativada. Ativar o registo de consultas lenta pode consumir alguns recursos de CPU. Recomendamos que ative este temporariamente para resolução de problemas de congestionamentos de desempenho. Para ativar o registo de consultas lenta:

1. Modificar o ficheiro de my.cnf adicionando as seguintes linhas ao fim:

        long_query_time = 2
        slow_query_log = 1
        slow_query_log_file = /RAID0/mysql/mysql-slow.log

2. Reinicie o servidor de MySQL.

        service  mysql  restart

3. Verifique se a definição está a demorar efeito utilizando o **mostrar** comando.

![NO lenta--registo de consultas][7]   

![Resultados de lenta--registo de consultas][8]

Neste exemplo, pode ver que a funcionalidade de consulta lenta foi ativada. Em seguida, pode utilizar o **mysqldumpslow** ferramenta para determinar os congestionamentos de desempenho e otimizar o desempenho, como adicionar os índices.

## <a name="appendices"></a>Appendices
Seguem-se dados de teste de desempenho de exemplo produzidos num ambiente de laboratório de destino. Fornecem em segundo plano geral sobre a tendência de dados de desempenho com diferentes abordagens de otimização do desempenho. Os resultados podem variar em diferentes versões de produto ou de ambiente.

### <a name="AppendixA"></a>Anexo A  
**Desempenho de disco (IOPS) com diferentes níveis RAID**

![Disco IOPS com diferentes níveis RAID][9]

**Comandos de teste**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

> [!NOTE]
> A carga de trabalho deste teste utiliza 64 threads, a tentar contactar o limite superior de RAID.
>
>

### <a name="AppendixB"></a>Apêndice B  
**Comparação de desempenho (débito) MySQL com diferentes níveis RAID**   
(Sistema de ficheiros XFS)

![Comparação de desempenho de MySQL com diferentes níveis RAID][10]  
![Comparação de desempenho de MySQL com diferentes níveis RAID][11]

**Comandos de teste**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**Comparação de desempenho (OLTP) MySQL com diferentes níveis RAID**  
![Comparação de desempenho (OLTP) MySQL com diferentes níveis RAID][12]

**Comandos de teste**

    time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

### <a name="AppendixC"></a>Apêndice C   
**Comparação de desempenho (IOPS) de disco para tamanhos de segmentos diferentes**  
(Sistema de ficheiros XFS)

![][13]

**Comandos de teste**  

    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
    fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

Os tamanhos de ficheiro utilizados para este teste 30 GB e 1 GB, respetivamente, com RAID 0 (4 discos) XFS sistema de ficheiros.

### <a name="AppendixD"></a>Apêndice D  
**Comparação de desempenho (débito) MySQL antes e depois de otimização**  
(Sistema de ficheiros XFS)

![Comparação de desempenho (débito) MySQL antes e depois de otimização][14]

**Comandos de teste**

    mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**A definição de configuração para a predefinição e a otimização é o seguinte:**

| Parâmetros | Predefinição | Otimização |
| --- | --- | --- |
| **innodb_buffer_pool_size** |Nenhuma |7 GB |
| **innodb_log_file_size** |5 MB |512MB |
| **max_connections** |100 |5000 |
| **innodb_file_per_table** |0 |1 |
| **innodb_flush_log_at_trx_commit** |1 |2 |
| **innodb_log_buffer_size** |8 MB |128 MB |
| **query_cache_size** |16 MB |0 |

Para mais detalhadas [parâmetros de configuração da otimização](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html), consulte o [instruções oficiais MySQL](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method).  

  **Ambiente de teste**  

| Hardware | Detalhes |
| --- | --- |
| CPU |AMD Opteron processador 4171 Putador / 4 núcleos |
| Memória |14 GB |
| Disco |10 GB/disco |
| SO |Ubuntu 14.04.1 LTS |

[1]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-01.png
[2]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-02.png
[3]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-03.png
[4]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-04.png
[5]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-05.png
[6]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-06.png
[7]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-07.png
[8]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-08.png
[9]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-09.png
[10]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-10.png
[11]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-11.png
[12]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-12.png
[13]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-13.png
[14]:media/optimize-mysql/virtual-machines-linux-optimize-mysql-perf-14.png

