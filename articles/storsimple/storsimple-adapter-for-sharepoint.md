---
title: Instalar o adaptador do StorSimple para o SharePoint | Microsoft Docs
description: Descreve como instalar e configurar ou remova o adaptador do StorSimple para SharePoint num farm de servidores do SharePoint.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 36c20b75-f2e5-4184-a6b5-9c5e618f79b2
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/06/2017
ms.author: v-sharos
ms.openlocfilehash: 8910471e09b9ecc797005818538ccfc6a91c68a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-the-storsimple-adapter-for-sharepoint"></a>Instale e configure o adaptador do StorSimple para SharePoint
## <a name="overview"></a>Descrição geral
O adaptador do StorSimple para SharePoint é um componente que lhe permite fornecer proteção de dados para farms do SharePoint server e flexíveis de armazenamento do Microsoft Azure StorSimple. Pode utilizar a placa para mover o conteúdo de objeto grande binário (BLOB) de bases de dados de conteúdo do SQL Server para o dispositivo de armazenamento de nuvem do Microsoft Azure StorSimple híbrida.

O adaptador do StorSimple para SharePoint funciona como um fornecedor de armazenamento de BLOBS remoto (RBS) e utiliza a funcionalidade de armazenamento de BLOBS do SQL Server remoto para armazenar o conteúdo do SharePoint não estruturado (sob a forma de BLOBs) num servidor de ficheiros que é copiado por um dispositivo StorSimple.

> [!NOTE]
> O adaptador do StorSimple para o SharePoint suporta o SharePoint Server 2010 remoto BLOB de armazenamento (RBS). Não suporta a SharePoint Server 2010 externo BLOB de armazenamento (EBS).


* Para transferir o adaptador do StorSimple para o SharePoint, vá para [StorSimple adaptador para o SharePoint] [ 1] no Microsoft Download Center.
* Para informações sobre o planeamento de RBS e RBS limitações, aceda a [decidir utilizar RBS no SharePoint 2013] [ 2] ou [planear RBS (SharePoint Server 2010)] [ 3].

O resto desta descrição geral descreve brevemente a função do StorSimple adaptador para o SharePoint e os limites de desempenho e capacidade de SharePoint que deve ter conhecimento antes de instalar e configurar o adaptador. Depois de rever estas informações, aceda ao [adaptador StorSimple para a instalação do SharePoint](#storsimple-adapter-for-sharepoint-installation) para começar a configurar o adaptador.

### <a name="storsimple-adapter-for-sharepoint-benefits"></a>Placa StorSimple para os benefícios do SharePoint
Num site do SharePoint, conteúdo é armazenado como dados de BLOBS não estruturados num ou mais bases de dados conteúdos. Por predefinição, estas bases de dados estão alojados em computadores que executam o SQL Server e estão localizados no farm de servidor do SharePoint. Os bLOBs rapidamente podem aumentar de tamanho, consumir grandes quantidades de armazenamento no local. Por este motivo, pode querer localizar soluções de outro armazenamento menos dispendioso. O SQL Server proporciona uma tecnologia chamada remoto Blob de armazenamento (RBS) que lhe permite armazenar o conteúdo do BLOB no sistema de ficheiros, fora da base de dados do SQL Server. RBS, BLOBs podem residir no sistema de ficheiros no computador que está a executar o SQL Server ou podem ser armazenados no sistema de ficheiros no computador de outro servidor.

RBS requer que utilize um fornecedor RBS, por exemplo, o adaptador do StorSimple para o SharePoint, para ativar RBS no SharePoint. O adaptador do StorSimple para SharePoint funciona com RBS, permitindo-lhe mover BLOBs para um servidor de cópia de segurança do sistema do Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os dados de BLOBS localmente ou na nuvem, com base na utilização. Os bLOBs que são muito ativos (normalmente denominados de camada 1 ou os dados) residirem localmente. Menos dados do Active Directory e os dados de arquivo residem na nuvem. Depois de ativar RBS numa base de dados de conteúdos, qualquer novo conteúdo BLOB criado no SharePoint é armazenado no dispositivo StorSimple e não se encontra na base de dados do conteúdo.

A implementação do Microsoft Azure StorSimple de RBS fornece as seguintes vantagens:

* Ao mover conteúdo BLOB para um servidor separado, pode reduzir a carga de consulta no SQL Server, que pode melhorar a capacidade de resposta do SQL Server. 
* Azure StorSimple utiliza e compressão de eliminação de duplicados para reduzir o tamanho dos dados.
* Azure StorSimple fornece proteção de dados na forma de locais e instantâneos de nuvem. Além disso, se colocar a base de dados no dispositivo StorSimple, pode criar a base de dados de conteúdo e os BLOBs em conjunto de forma consistente com a falha. (Mover a base de dados de conteúdo para o dispositivo só é suportada para o dispositivo de série 8000 do StorSimple. Esta funcionalidade não é suportada para a série de 5000 ou 7000.)
* Azure StorSimple inclui funcionalidades de recuperação de desastre, incluindo a ativação pós-falha, a recuperação de ficheiros e de volume (incluindo a recuperação de teste) e restauro rápido de dados.
* Pode utilizar o software de recuperação de dados, tais como Kroll Ontrack PowerControls, com StorSimple instantâneos de dados de BLOBS para efetuar a recuperação ao nível do item de conteúdo do SharePoint. (Este software de recuperação de dados é uma compra separada).
* O adaptador do StorSimple para SharePoint plugs para o portal de Administração Central do SharePoint, permitindo-lhe gerir a solução completa do SharePoint a partir de uma localização central.

Mover o conteúdo do BLOB para o sistema de ficheiros pode fornecer outras reduções de custos e benefícios. Por exemplo, utilizar RBS pode reduzir a necessidade de armazenamento de camada 1 dispendioso e porque diminui-lo a base de dados de conteúdos, RBS pode reduzir o número de bases de dados necessários no farm de servidor do SharePoint. No entanto, outros fatores, tais como os limites de tamanho de base de dados e a quantidade de conteúdo não RBS, pode também afetar os requisitos de armazenamento. Para obter mais informações sobre os custos e as vantagens da utilização RBS, consulte [planear RBS (SharePoint Foundation 2010)] [ 4] e [decidir utilizar RBS no SharePoint 2013] [5].

### <a name="capacity-and-performance-limits"></a>Limites de capacidade e o desempenho
Antes de considerar que utilizar RBS na sua solução do SharePoint, deve ter conhecimento do desempenho testado e limites de capacidade do SharePoint Server 2010 e o SharePoint Server 2013 e como estes limites relacionados com um desempenho aceitável. Para obter mais informações, consulte [limites de Software e os limites para SharePoint 2013](https://technet.microsoft.com/library/cc262787.aspx).

Reveja o seguinte antes de configurar RBS:

* Certifique-se de que o tamanho total do conteúdo (o tamanho de uma base de dados de conteúdo) mais o tamanho dos BLOBs externalized associados não excede o limite de tamanho RBS suportado pelo SharePoint. Este limite é de 200 GB. 
  
    **Base de dados de conteúdo de medidas e o tamanho do BLOB**
  
  1. Execute esta consulta no WFE de Administração Central. Iniciar a Shell de gestão do SharePoint e, em seguida, introduza o seguinte comando do Windows PowerShell para obter o tamanho das bases de dados de conteúdo:
     
     `Get-SPContentDatabase | Select-Object -ExpandProperty DiskSizeRequired`
     
      Este passo obtém o tamanho da base de dados de conteúdo no disco.
  2. Execute um dos seguintes consultas SQL no SQL Server Management Studio na caixa de servidor SQL em cada base de dados de conteúdos e adicione o resultado para o número de obtido no passo 1.
     
     No SharePoint 2013 conteúdos as bases de dados, introduza:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[DocStreams] WHERE [Content] IS NULL`
     
     No SharePoint 2010 conteúdos as bases de dados, introduza:
     
     `SELECT SUM([Size]) FROM [ContentDatabaseName].[dbo].[AllDocs] WHERE [Content] IS NULL`
     
     Este passo obtém o tamanho dos BLOBs que tenham sido externalized.
* Recomendamos que armazene todo o conteúdo BLOB e a base de dados localmente no dispositivo StorSimple. O dispositivo StorSimple é um cluster de dois nós para elevada disponibilidade. Colocar as bases de dados de conteúdo e os BLOBs no dispositivo StorSimple fornece elevada disponibilidade.
  
    Utilize tradicionais do SQL Server migração melhores práticas para mover a base de dados de conteúdo para o dispositivo StorSimple. Mova a base de dados apenas depois de todo o conteúdo BLOB da base de dados foi movido para a partilha de ficheiros através de RBS. Se optar por mover a base de dados de conteúdo para o dispositivo StorSimple, recomendamos que configure o armazenamento de conteúdo da base de dados no dispositivo como um volume primário.
* No Microsoft Azure StorSimple, se utilizar volumes em camadas, não há nenhuma forma de garantir esse conteúdo armazenado localmente na StorSimple dispositivo não irá ser colocado em camadas para o armazenamento de nuvem do Microsoft Azure. Por conseguinte, recomendamos a utilização de volumes do StorSimple afixado localmente em conjunto com RBS do SharePoint. Isto irá garantir que todo o conteúdo BLOB localmente permanece no dispositivo StorSimple e não é movido para o Microsoft Azure.
* Se não armazenar as bases de dados de conteúdo no dispositivo StorSimple, utilize tradicionais do SQL Server elevada disponibilidade melhores práticas que suportam RBS. Clustering do SQL Server suporta RBS, ao SQL Server espelhamento não. 

> [!WARNING]
> Se não tiver ativado RBS, não recomendamos a mover a base de dados de conteúdo para o dispositivo StorSimple. Esta é uma configuração untested.

## <a name="storsimple-adapter-for-sharepoint-installation"></a>Placa StorSimple para a instalação do SharePoint
Antes de poder instalar o adaptador do StorSimple para o SharePoint, tem de configurar o dispositivo StorSimple e certifique-se de que o farm do SharePoint server e as instâncias do SQL Server cumprem todos os pré-requisitos. Este tutorial descreve os requisitos de configuração, bem como dos procedimentos para instalar e atualizar o adaptador do StorSimple para SharePoint.

## <a name="configure-prerequisites"></a>Configurar os pré-requisitos
Antes de poder instalar o adaptador do StorSimple para o SharePoint, certifique-se de que o dispositivo StorSimple, farm do SharePoint server e as instâncias do SQL Server cumprem os seguintes pré-requisitos.

### <a name="system-requirements"></a>Requisitos de sistema
O adaptador do StorSimple para SharePoint funciona com o seguinte hardware e software:

* Sistema de operativo suportado – Windows Server 2008 R2 SP1, Windows Server 2012 ou Windows Server 2012 R2
* Versões suportadas do SharePoint – SharePoint Server 2010 ou SharePoint Server 2013
* Versões suportadas do SQL Server – SQL Server 2008 Enterprise Edition, SQL Server 2008 R2 Enterprise Edition ou SQL Server 2012 Enterprise Edition
* Suportado dispositivos StorSimple série 8000 do StorSimple, séries StorSimple 7000 ou série de StorSimple 5000.

### <a name="storsimple-device-configuration-prerequisites"></a>Pré-requisitos de configuração do dispositivo StorSimple
O dispositivo StorSimple é um dispositivo de bloco e como tal requer um servidor de ficheiros no qual os dados podem ser alojados. Recomendamos que utilize um servidor separado, em vez de um servidor existente do farm do SharePoint. Este servidor de ficheiros tem de ser na mesma rede de área local (LAN) como o computador do SQL Server que aloja as bases de dados de conteúdo.

> [!TIP]
> * Se configurar o farm do SharePoint para elevada disponibilidade, deve implementar o servidor de ficheiros de elevada disponibilidade também.
> * Se armazenar o conteúdo da base de dados no dispositivo StorSimple, utilize as melhores práticas de elevada disponibilidade tradicional que suportam RBS. Clustering do SQL Server suporta RBS, ao SQL Server espelhamento não. 


Certifique-se de que o dispositivo StorSimple está configurado corretamente e que os volumes adequados para suportar a implementação de SharePoint estão configurados e acessível a partir do computador do SQL Server. Aceda a [implementar o dispositivo StorSimple no local](storsimple-8000-deployment-walkthrough-u2.md) se ainda não tiver implementado e configurado o dispositivo StorSimple. Tenha em atenção o endereço IP do dispositivo StorSimple; precisará dele durante adaptador StorSimple para a instalação do SharePoint.

Além disso, certifique-se de que o volume a ser utilizada para BLOB externalization cumpre os seguintes requisitos:

* O volume tem de ser formatado com um tamanho de unidade de alocação de 64 KB.
* O front-end (WFE) da web e servidores de aplicações tem de ser capazes de aceder ao volume através de um caminho de convenção de Nomenclatura Universal (UNC).
* Farm de servidor do SharePoint tem de ser configurado para escrever o volume.

> [!NOTE]
> Depois de instalar e configurar o adaptador, todos os externalization de BLOB fique através do dispositivo StorSimple (o dispositivo irá apresentar os volumes ao SQL Server e gerir as camadas de armazenamento). Não é possível utilizar quaisquer outros elementos para externalization de BLOB.


Se planear utilizar o Snapshot Manager do StorSimple para criar instantâneos dos dados de BLOBS e de base de dados, lembre-se de que instale o Snapshot Manager do StorSimple no servidor de base de dados para que possa utilizar o serviço de escritor do SQL Server para implementar o serviço cópia do Windows Volume sombra de volumes (VSS).

> [!IMPORTANT]
> Snapshot Manager do StorSimple não suporta o escritor de VSS do SharePoint e não pode assumir instantâneos consistentes com aplicações dos dados do SharePoint. Num cenário de SharePoint, o Snapshot Manager do StorSimple fornece apenas cópias de segurança consistentes com falhas.


## <a name="sharepoint-farm-configuration-prerequisites"></a>Pré-requisitos de configuração de farm do SharePoint
Certifique-se de que o farm de servidores do SharePoint está corretamente configurado, da seguinte forma:

* Verifique se o farm do SharePoint server está em bom estado de funcionamento e verifique o seguinte:
* Todos os SharePoint WFE e servidores de aplicação, registados no farm estão em execução e podem ser executar o ping do servidor no qual irá instalar o adaptador do StorSimple para SharePoint.
* O temporizador do SharePoint (SPTimerV3 ou SPTimerV4) está em execução em cada servidor WFE e o servidor de aplicações.
* O serviço de temporizador do SharePoint e o conjunto aplicacional do IIS em que o site de Administração Central do SharePoint está em execução tem privilégios administrativos.
* Certifique-se de que o Internet Explorer avançada contexto de segurança (IE ESC) está desativado. Siga estes passos para desativar IE ESC:
  
  1. Feche todas as instâncias do Internet Explorer.
  2. Inicie o Gestor de servidor.
  3. No painel esquerdo, clique em **servidor Local**.
  4. No painel direito, junto a **configuração de segurança avançada do IE**, clique em **no**.
  5. Em **administradores**, clique em **desativar**.
  6. Clique em **OK**.

## <a name="remote-blob-storage-rbs-prerequisites"></a>Remoto pré-requisitos de armazenamento de BLOBS (RBS)
Certifique-se de que está a utilizar uma versão suportada do SQL Server. As seguintes versões são capazes de utilizar RBS e suportados:

* SQL Server 2008 Enterprise Edition
* Edição do SQL Server 2008 R2 Enterprise
* SQL Server 2012 Enterprise Edition

Os bLOBs podem externalized apenas os volumes que apresenta o dispositivo StorSimple ao SQL Server. Não existem outros destinos para externalization BLOBS são suportados.

Quando tiver terminado de todos os passos de configuração de pré-requisitos, aceda a [instalar o adaptador do StorSimple para SharePoint](#install-the-storsimple-adapter-for-sharepoint).

## <a name="install-the-storsimple-adapter-for-sharepoint"></a>Instalar o adaptador do StorSimple para SharePoint
Utilize os seguintes passos para instalar o adaptador do StorSimple para SharePoint. Se estiver a reinstalar o software, consulte o artigo [atualizar ou reinstalar o adaptador do StorSimple para SharePoint](#upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint). O tempo necessário para a instalação depende o número total de bases de dados do SharePoint no farm do SharePoint server.

[!INCLUDE [storsimple-install-sharepoint-adapter](../../includes/storsimple-install-sharepoint-adapter.md)]

## <a name="configure-rbs"></a>Configurar RBS
Depois de instalar o adaptador do StorSimple para o SharePoint, configure RBS conforme descrito no procedimento seguinte.

> [!TIP]
> O adaptador do StorSimple para SharePoint plugs à página de Administração Central do SharePoint, permitindo RBS ser ativado ou desativado em cada base de dados de conteúdo no farm do SharePoint. No entanto, ativando ou desativando RBS na base de dados de conteúdos faz com que uma reposição do IIS, que, consoante a configuração do farm, momentaneamente pode interromper a disponibilidade do SharePoint web front-end (WFE). (Fatores como a utilização de um balanceador de carga de front-end, a atual carga de trabalho do servidor e assim sucessivamente, podem limitar ou eliminar este interrupção) Para proteger os utilizadores de uma interrupção, é recomendável que ativar ou desativar RBS apenas durante uma janela de manutenção planeada.


[!INCLUDE [storsimple-sharepoint-adapter-configure-rbs](../../includes/storsimple-sharepoint-adapter-configure-rbs.md)]

## <a name="configure-garbage-collection"></a>Configurar a recolha de lixo
Quando os objetos são eliminados do site do SharePoint, estes não são automaticamente eliminados do volume de arquivo RBS. Em vez disso, um assíncrono, o programa de manutenção em segundo plano elimina BLOBs órfãos do arquivo de ficheiros. Os administradores de sistema podem agendar este processo para executar periodicamente ou podem iniciá-lo, sempre que necessário.

Este programa de manutenção (Microsoft.Data.SqlRemoteBlobs.Maintainer.exe) é instalado automaticamente em todos os servidores SharePoint WFE e servidores de aplicações quando ativar RBS. O programa é instalado na seguinte localização: *unidade de arranque*: \Programas\Microsoft SQL remoto Blob Storage 10.50\Maintainer\

Para obter informações sobre como configurar e utilizar o programa de manutenção, consulte [manter RBS no SharePoint Server 2013][8].

> [!IMPORTANT]
> O programa de responsável pela manutenção RBS é intensiva de recursos. Deve agendar que seja executado apenas em períodos de atividade ligeira no farm do SharePoint.


### <a name="delete-orphaned-blobs-immediately"></a>Eliminar BLOBs órfãos imediatamente
Se precisar eliminar BLOBs órfãos imediatamente, pode utilizar as instruções seguintes. Tenha em atenção que estas instruções são um exemplo de como isto pode ser feito um ambiente do SharePoint 2013 com os seguintes componentes:

* O nome de base de dados de conteúdos é WSS_Content.
* O nome do SQL Server é SHRPT13 SQL12\SHRPT13.
* O nome da aplicação web é SharePoint – 80.

[!INCLUDE [storsimple-sharepoint-adapter-garbage-collection](../../includes/storsimple-sharepoint-adapter-garbage-collection.md)]

## <a name="upgrade-or-reinstall-the-storsimple-adapter-for-sharepoint"></a>Atualizar ou reinstalar o adaptador do StorSimple para SharePoint
Utilize o procedimento seguinte para atualizar o servidor do SharePoint e, em seguida, reinstalar StorSimple adaptador para o SharePoint ou para simplesmente atualizar ou reinstalar o adaptador de um farm de servidores do SharePoint existente.

> [!IMPORTANT]
> Reveja as seguintes informações antes de tentar atualizar o software do SharePoint e/ou a atualização ou reinstale o adaptador do StorSimple para o SharePoint:
> 
> * Todos os ficheiros que anteriormente foram movidos para armazenamento externo através de RBS não estarão disponíveis até que a reinstalação estiver concluída e a funcionalidade RBS seja ativada de novo. Para limitar o impacto de utilizador, execute qualquer atualização ou a reinstalação durante uma janela de manutenção planeada.
> * O tempo necessário para a atualização/reinstalação pode variar, dependendo do número total de bases de dados do SharePoint no farm de servidor do SharePoint.
> * Após a reinstalação/atualização estiver concluída, terá de ativar RBS para as bases de dados de conteúdo. Consulte [configurar RBS](#configure-rbs) para obter mais informações.
> * Se estiver a configurar RBS para um farm do SharePoint que tem um número muito elevado de bases de dados (superior a 200), o **Administração Central do SharePoint** página poderá tempo limite. Se isso ocorrer, atualize a página. Isto não afeta o processo de configuração.


[!INCLUDE [storsimple-upgrade-sharepoint-adapter](../../includes/storsimple-upgrade-sharepoint-adapter.md)]

## <a name="storsimple-adapter-for-sharepoint-removal"></a>Placa StorSimple para remoção do SharePoint
Os procedimentos seguintes descrevem como mover os BLOBs de volta para as bases de dados de conteúdo do SQL Server e, em seguida, desinstalar o adaptador do StorSimple para SharePoint. 

> [!IMPORTANT]
> Tem de mover os BLOBs de volta para as bases de dados de conteúdo antes de desinstalar o software de adaptador.


### <a name="before-you-begin"></a>Antes de começar
Recolha as seguintes informações antes de mover os dados de volta para os conteúdo bases de dados e iniciar o processo de remoção do adaptador:

* Os nomes de todas as bases de dados para o qual RBS está ativado
* O caminho UNC do arquivo BLOB configurado

### <a name="move-the-blobs-back-to-the-content-databases"></a>Mover os BLOBs de volta para as bases de dados de conteúdo
Antes de desinstalar o adaptador do StorSimple para software do SharePoint, tem de migrar todos os BLOBs que foram externalized novamente para as bases de dados de conteúdo do SQL Server. Se tentar desinstalar o adaptador do StorSimple para SharePoint antes de mover todos os BLOBs novamente para as bases de dados de conteúdos, verá a seguinte mensagem de aviso.

![Mensagem de aviso](./media/storsimple-adapter-for-sharepoint/sasp1.png)

#### <a name="to-move-the-blobs-back-to-the-content-databases"></a>Para mover os BLOBs novamente para as bases de dados de conteúdo
1. Transferir a cada um dos objetos externalized.
2. Abra o **Administração Central do SharePoint** e navegue até à página **definições do sistema**.
3. Em **Azure StorSimple**, clique em **configurar adaptador do StorSimple**.
4. No **configurar adaptador do StorSimple** página, clique em de **desativar** no botão abaixo cada uma das bases de dados conteúdos que pretende remover a partir do BLOB storage externo. 
5. Elimine os objetos do SharePoint e, em seguida, volte a carregá-los.

Em alternativa, pode utilizar o Microsoft` RBS Migrate()` cmdlet do PowerShell incluído com o SharePoint. Para obter mais informações, consulte [migrar conteúdo ou a sair RBS](https://technet.microsoft.com/library/ff628255.aspx).

Depois de mover os BLOBs de volta para a base de dados de conteúdo, avance para o próximo passo: [desinstalará o adaptador](#uninstall-the-adapter).

### <a name="uninstall-the-adapter"></a>Desinstalar o adaptador
Depois de mover os BLOBs novamente para as bases de dados de conteúdo do SQL Server, utilize uma das seguintes opções para desinstalar o adaptador do StorSimple para SharePoint.

#### <a name="to-use-the-installation-program-to-uninstall-the-adapter"></a>Utilizar o programa de instalação para desinstalar o adaptador
1. Utilize uma conta com privilégios de administrador para iniciar sessão no servidor web front-end (WFE).
2. Faça duplo clique o adaptador do StorSimple para o installer do SharePoint. O Assistente de configuração é iniciado.
   
    ![Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp2.png)
3. Clique em **Seguinte**. É apresentada a página seguinte.
   
    ![Página do Assistente para remover o programa de configuração](./media/storsimple-adapter-for-sharepoint/sasp3.png)
4. Clique em **remover** para selecionar o processo de remoção. É apresentada a página seguinte.
   
    ![Página de confirmação do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp4.png)
5. Clique em **remover** para confirmar a remoção. A seguinte página de progresso é apresentado.
   
    ![Página de progresso do Assistente de configuração](./media/storsimple-adapter-for-sharepoint/sasp5.png)
6. Quando a remoção estiver concluída, é apresentada a página de conclusão. Clique em **concluir** para fechar o Assistente de configuração.

#### <a name="to-use-the-control-panel-to-uninstall-the-adapter"></a>Utilizar o painel de controlo para desinstalar o adaptador
1. Abra o painel de controlo e, em seguida, clique em **programas e funcionalidades**.
2. Selecione **StorSimple adaptador para o SharePoint**e, em seguida, clique em **desinstalação**.

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre StorSimple](storsimple-overview.md).

<!--Reference links-->
[1]: https://www.microsoft.com/download/details.aspx?id=44073
[2]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[3]: https://technet.microsoft.com/library/ff628583(v=office.14).aspx
[4]: https://technet.microsoft.com/library/ff628569(v=office.14).aspx
[5]: https://technet.microsoft.com/library/ff628583(v=office.15).aspx
[8]: https://technet.microsoft.com/en-us/library/ff943565.aspx
