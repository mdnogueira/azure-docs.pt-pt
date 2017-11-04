---
title: Perguntas mais frequentes sobre os ficheiros do Azure | Microsoft Docs
description: Encontrar respostas a perguntas mais frequentes sobre os ficheiros do Azure.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/13/2017
ms.author: renash
ms.openlocfilehash: 91c46ea0897f83811cfa5c1a8b67677caff14569
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Perguntas mais frequentes sobre os ficheiros do Azure
[Ficheiros do Azure](storage-files-introduction.md) oferece completamente geridos partilhas de ficheiros na nuvem que estão acessíveis através da norma da indústria [protocolo Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (também conhecido como sistema de ficheiros Internet comum ou CIFS). As partilhas de Ficheiros do Azure podem ser montadas em simultâneo por implementações na cloud ou no local do Windows, Linux e macOS. Além disso, as partilhas de ficheiros do Azure podem ser colocadas em cache em servidores Windows com sincronização de ficheiros do Azure (pré-visualização) para acesso rápido quase onde os dados está a ser utilizados.

Este artigo irá cobrir algumas das perguntas mais comuns sobre ficheiros do Azure e funcionalidades, incluindo a sincronização de ficheiros do Azure. Se não vir a resposta à sua pergunta aqui, não hesitam em entrar-nos através dos canais seguintes (em constante crescendo ordem):

1. Na secção de comentários deste artigo.
2. [Fórum de armazenamento do Azure](https://social.msdn.microsoft.com/Forums/home?forum=windowsazuredata)
3. [Ficheiros do Azure UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Support da Microsoft: Para criar um novo incidente de suporte, navegue até à "ajuda + suporte" separador no portal do Azure e clique em "Novo suporte de pedido".

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>**Por que motivo é útil ficheiros do Azure?**  
   Ficheiros do Azure permite-lhe criar partilhas de ficheiros na nuvem sem ter de gerir a sobrecarga de um servidor físico ou de dispositivo/aplicação. Isto significa pode gaste menos tempo a aplicar atualizações do SO e substituir discos incorretos - podermos fazer tudo isto monotonous por si. Para saber mais sobre os cenários que pode ajudar a ficheiros do Azure, consulte [motivo pelo qual os ficheiros do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>**O que são formas diferentes de acesso aos ficheiros nos ficheiros do Azure?**  
    É possível montar a partilha de ficheiros no seu computador local utilizando o protocolo SMB 3.0 ou utilizar ferramentas como [Explorador de armazenamento](http://storageexplorer.com/) para aceder a ficheiros na partilha de ficheiros. Da sua aplicação, pode utilizar bibliotecas de cliente de armazenamento, REST APIs, PowerShell ou o CLI para aceder aos seus ficheiros numa partilha de ficheiros do Azure.

* <a id="what-is-afs"></a>**O que é a sincronização de ficheiros do Azure?**  
    Sincronização de ficheiros do Azure permite-lhe centralizar partilhas de ficheiros da sua organização nos ficheiros de Azure sem fornecer a flexibilidade, o desempenho e a compatibilidade de um servidor de ficheiros no local. Isto é feito ao transformar os Servidores do Windows numa cache rápida da partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

* <a id="files-versus-blobs"></a>**Por que motivo utilizaria uma partilha de ficheiros do Azure versus Blob storage do Azure com os meus dados?**  
    Ficheiros do Azure e BLOBs do Azure de armazenamento de fornecer uma forma de armazenar grandes quantidades de dados na nuvem, mas são úteis para fins de ligeiramente diferentes. Armazenamento de Blobs do Azure é útil para aplicações de grande escala, nativa na nuvem que precisam para armazenar dados não estruturados. Para maximizar o desempenho e dimensionamento, o Blob storage do Azure é uma abstração de armazenamento mais simples do que um sistema de ficheiros for VERDADEIRO. Além disso, Blob storage do Azure só pode ser acedido através de bibliotecas de cliente baseado em REST (ou diretamente através do protocolo baseado em REST).

    Ficheiros do Azure por outro lado especificamente procura a um sistema de ficheiros, com todas as abstracts de ficheiro que conhece e adoram de anos de sistemas de operativos no local. Como um armazenamento de Blobs do Azure, ficheiros do Azure oferece uma interface REST e bibliotecas de cliente baseado em REST mas, ao contrário do armazenamento de Blobs do Azure, ficheiros do Azure também oferece acesso SMB para partilhas de ficheiros do Azure. Isto significa que pode diretamente montar uma partilha de ficheiros do Azure no Windows, Linux ou macOS, no local ou na nuvem VMs, sem ter de escrever qualquer código ou quaisquer controladores especiais ao sistema de ficheiros. Além disso, as partilhas de ficheiros do Azure podem ser colocadas em cache em servidores de ficheiros no local utilizando a sincronização de ficheiros do Azure para um acesso rápido quase onde os dados está a ser utilizados. 
   
    Para um debate mais aprofundado sobre as diferenças entre os ficheiros do Azure e o armazenamento de Blobs do Azure, consulte [decidir quando deve utilizar o Blob storage do Azure, ficheiros do Azure ou do Azure discos](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais sobre armazenamento de Blobs do Azure, consulte o artigo [introdução ao Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que motivo utilizaria uma partilha de ficheiros de Azure face a discos do Azure?**  
    Um disco do Azure são apenas que, um disco. Um disco de autónomo por si só, não é muito útil - para obter o valor fora de um disco do Azure, terá de anexar a máquina virtual em execução no Azure. Discos do Azure podem ser utilizados para qualquer coisa e tudo teria de utilizar um disco para um servidor no local: como um disco de sistema do SO, espaço de comutação de sistema operativo, ou como armazenamento dedicado para uma aplicação. Uma utilização interessante para discos do Azure é para criar um servidor de ficheiros na nuvem para utilização em exatamente da mesma coloca poderá utilizar uma partilha de ficheiros do Azure. Implementar um servidor de ficheiros em VMs do Azure é uma forma de fantastic e de elevado desempenho para obter o armazenamento de ficheiros no Azure quando necessitar de opções de implementação não suportadas atualmente pelos ficheiros do Azure (por exemplo, o NFS protocolo suporte ou premium armazenamento). 

    Por outro lado, com um servidor de ficheiros de discos do Azure como back-end armazenamento normalmente, será muito mais dispendioso do que utilizar uma partilha de ficheiros do Azure por vários motivos. Em primeiro lugar, para além de pagar para armazenamento em disco, tem também paga para o custo de um ou mais VMs do Azure em execução. Segundo, tem de gerir também as VMs utilizadas para executar o servidor de ficheiros, tais como a ser responsável por atualizações do SO, etc. Por fim, se precisa, em última análise de dados colocados em cache no local, é cópias de segurança para que possa configurar e gerir tecnologias de replicação (por exemplo, a replicação de sistema de ficheiros distribuído) para se certificar de que ocorrem.

    Uma abordagem interessante para obter o melhor dos ficheiros do Azure e um servidor de ficheiros alojados em VMs do Azure com discos do Azure como armazenamento de back-end, é instalar a sincronização de ficheiros do Azure no servidor de ficheiros alojado de VM de nuvem. Se a partilha de ficheiros do Azure está na mesma região que o servidor de ficheiros, pode ativar a criação de camadas de nuvem e definir a percentagem de espaço livre de volume para máximo (99%). Isto garante mínima duplicação de dados ao permitir-lhe utilizar suportam qualquer aplicações pretende contra os servidores de ficheiros, essa nada necessidade de protocolo NFS.

    Para obter informações sobre uma forma de configurar um elevado desempenho e o servidor de ficheiros altamente disponíveis no Azure, consulte [implementar IaaS VM Clusters de convidados no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para um debate mais aprofundado sobre as diferenças entre os ficheiros do Azure e os discos do Azure, consulte [decidir quando deve utilizar o Blob storage do Azure, ficheiros do Azure ou do Azure discos](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais acerca dos discos do Azure, consulte [descrição geral do discos geridos Azure](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**Como começar a utilizar a utilizar ficheiros do Azure?**  
    Introdução aos ficheiros do Azure é fácil: simplesmente [criar uma partilha de ficheiros](storage-how-to-create-file-share.md) e montagem no seu sistema de operativo preferencial: 

    - [Montar no Windows](storage-how-to-use-files-windows.md)
    - [Montar no Linux](storage-how-to-use-files-linux.md)
    - [Montar no macOS](storage-how-to-use-files-mac.md)

    Para obter um guia mais aprofundado sobre como implementar uma partilha de ficheiros do Azure substituir a produção de partilhas de ficheiros na sua organização, consulte [planear uma implementação de ficheiros do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>**Que opções de redundância de armazenamento são suportadas pelo Azure ficheiros?**  
    Ficheiros do Azure atualmente suporta apenas armazenamento localmente redundante (LRS) e armazenamento georredundante (GRS) neste momento. Iremos planeia suportar o armazenamento com redundância de zona (ZRS) e armazenamento georredundante com acesso de leitura (RA-GRS) no futuro, mas não tem linhas cronológicas partilhar neste momento.

* <a id="tier-options"></a>**As camadas de armazenamento são atualmente suportadas pelo Azure ficheiros?**  
    Ficheiros do Azure atualmente suporta apenas a camada de armazenamento standard. Não existe atualmente linhas cronológicas partilhar para suporte de frio e premium. Tenha em atenção que não é possível criar partilhas de ficheiros do Azure de contas do storage apenas de BLOBs ou de contas de armazenamento Premium.

* <a id="give-us-feedback"></a>**Posso certeza de que pretende ver *x* funcionalidade adicionada ao ficheiros do Azure, pode, adicioná-la?**  
    Absolutamente, a equipa de ficheiros do Azure está interessada em hearing todos os comentários que tem sobre o nosso serviço. Votar em pedidos de funcionalidades no [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Iremos procura reencaminhar para delighting com muitas funcionalidades novas.

## <a name="azure-file-sync"></a>Sincronização de ficheiros do Azure
* <a id="afs-region-availability"></a>**As regiões são atualmente suportadas para a sincronização de ficheiros do Azure (pré-visualização)?**  
    Sincronização de ficheiros do Azure está atualmente disponível nos EUA oeste, Europa Ocidental, leste da Austrália e Sudeste asiático. Iremos adicionar suporte para regiões adicionais à medida que trabalha para disponibilidade geral. Para obter mais informações, consulte [disponibilidade de região](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**Posso ter associado a um domínio e servidores associados a um domínio no mesmo grupo de sincronização?**  
    Sim, um grupo de sincronização pode conter pontos finais do servidor que tenham diferentes associação do Active Directory, que inclui não estão a ser associado a um domínio. Enquanto a configuração funciona tecnicamente, não recomendamos esta como um configuration normal como ACLs são definidas para os ficheiros/pastas num servidor não poderá ser impostas por outros servidores no grupo de sincronização. Para obter os melhores resultados, recomendamos que a sincronização entre qualquer um dos servidores no mesmo do Active Directory floresta, servidores em diferentes florestas do Active Directory com relações de fidedignidade estabelecida ou servidores não de um domínio, mas não uma mistura de todos os aspetos anteriores.

* <a id="afs-change-detection"></a>**Criar um ficheiro diretamente na minha partilha de ficheiros do Azure através de SMB ou através do portal. Quanto tempo até que o ficheiro está sincronizado com os servidores no grupo de sincronização?** 
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Quando o mesmo ficheiro é alterado em dois servidores aproximadamente à mesma hora, o que acontece?**  
    Sincronização de ficheiros do Azure utiliza uma estratégia de resolução de conflito simples: vamos manter as alterações. Mais recentemente gravadas mantém o nome de ficheiro original. O ficheiro mais antigo tem esta '' e o número de conflito acrescentado ao nome com este Taxonomia: 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    Por exemplo, o conflito de primeiro `CompanyReport.docx` iria ficar `CompanyReport-CentralServer.docx` se `CentralServer` é onde ocorreu a operação de escrita mais antiga. O segundo conflito deverá ser identificado como `CompanyReport-CentralServer-1.docx`.

* <a id="afs-storage-redundancy"></a>**Armazenamento georredundante (GRS) é suportado para a sincronização de ficheiros do Azure?**  
    Sim, o armazenamento localmente redundante (LRS) e o armazenamento georredundante (GRS) são suportados pela sincronização de ficheiros do Azure. No caso de uma ativação pós-falha GRS entre regiões emparelhadas, recomendamos que encará-los a região novo como uma cópia de segurança de dados apenas. Sincronização do Azure do ficheiro não será automaticamente iniciado a sincronizar com a região primária novo. 

* <a id="sizeondisk-versus-size"></a>**Por que motivo não o *tamanho do disco* propriedade para uma correspondência de ficheiro a *tamanho* propriedade depois de utilizar a sincronização de ficheiros do Azure?**  
    Explorador de ficheiros Windows expõe duas propriedades, **tamanho** e **tamanho do disco** para representar o tamanho de um ficheiro. Estas propriedades subtly diferem no significado; **Tamanho** representa o tamanho completo do ficheiro, enquanto **tamanho do disco** representa o tamanho do fluxo de ficheiros, na verdade, armazenado no disco. Estes podem diferir por diversas razões, tais como compressão, utilize da eliminação de duplicados de dados, ou no nosso caso, na nuvem em camadas com sincronização de ficheiros do Azure. Se um ficheiro está em camadas para uma partilha de ficheiros do Azure, o tamanho do disco será zero porque o fluxo do ficheiro está armazenado numa partilha de ficheiros do Azure, não no disco. Também é possível para um ficheiro a ser parcialmente em camadas (ou parcialmente resgatar os), que significa que parte do ficheiro está no disco. Isto pode acontecer quando os ficheiros parcialmente são lidos por aplicações, tais como jogadores multimedia ou utilitários zipping. 

* <a id="is-my-file-tiered"></a>**Como posso saber se um ficheiro foi camado?**  
    Existem várias formas para verificar se tem sido camado um ficheiro para o ficheiro do Azure partilham:
    
    1. **Verifique os atributos de ficheiro no ficheiro.** Para tal, faça duplo clique num ficheiro, navegue para **detalhes** e desloque para baixo até o **atributos** propriedade. Um ficheiro em camadas a ter os seguintes atributos definir:     
        
        | Letra de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o ficheiro deve ser feito pelo software de cópia de segurança. Este atributo é sempre definido independentemente se o ficheiro está em camadas ou totalmente armazenado no disco. |
        | P | Ficheiro disperso | Indica que o ficheiro é um ficheiro disperso, que é um tipo especializado de ficheiros que NTFS oferece para uma utilização eficiente quando o ficheiro no fluxo de disco principalmente está vazio. Sincronização de ficheiros do Azure utiliza ficheiros dispersos porque um ficheiro é totalmente camadas, que significa que a sequência de ficheiros apenas é armazenada na nuvem, ou parcialmente resgatar os, o que significa que parte dos ficheiros já se encontra no disco. Se um ficheiro é totalmente resgatar os para o disco, sincronização de ficheiros do Azure irá convertê-lo a partir de um ficheiro disperso para um ficheiro regular. |
        | L | Ponto de reanálise | Indica que o ficheiro tem um ponto de reanálise, que é um ponteiro especial para utilização por um filtro de sistema de ficheiros. Sincronização de ficheiros do Azure utiliza pontos de nova análise para definir o filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys) onde na nuvem está armazenado o ficheiro. Isto permite o acesso totalmente integrado sem o utilizador final mesmo necessidade de conhecer a que sincronização de ficheiros do Azure está a ser utilizada ou como obter acesso ao ficheiro na partilha de ficheiros do Azure. Quando um ficheiro é totalmente resgatar os, Azure ficheiro Sync remove o ponto de reanálise do ficheiro. |
        | NÃ | Offline | Indica que alguns ou todos os conteúdos do ficheiro não estão armazenados num disco. Quando um ficheiro é totalmente resgatar os, Azure ficheiro Sync remove este atributo. |

        ![A caixa de diálogo de propriedades para um ficheiro com o separador de detalhes, selecionado](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Também é possível ver os atributos para todos os ficheiros numa pasta adicionando o **atributos** campo para a visualização de tabela de Exporer de ficheiro. Para tal, faça duplo clique numa coluna existente (por exemplo, tamanho), selecione **mais** e selecione **atributos** na lista pendente.
        
    2. **Utilize `fsutil` para procurar pontos de reanálise num ficheiro.** Tal como mencionado na opção anterior, um ficheiro em camadas terão sempre um ponto de reanálise, ou um apontador especial para definir o filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys). Pode verificar se um ficheiro tem uma reanálise ponto com o `fsutil` utilitário numa linha de comandos elevada ou sessão do PowerShell:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o ficheiro tem um ponto de reanálise, deverá ver **reanálise valor de etiqueta: 0x8000001e**. Este valor hexadecimal é o valor do ponto de reanálise pertencente a sincronização de ficheiros do Azure. A saída também irá conter a reanálise partilham dados, que representa o caminho para o ficheiro no seu ficheiro do Azure.

        > [!Warning]  
        > O `fsutil reparsepoint` comando utilitário também contém a capacidade de eliminar um ponto de reanálise. Não execute este comando, a menos que instruiu pela equipa de engenharia do Azure ficheiro Sync - fazê-lo poderá causar perda de dados. 

* <a id="afs-recall-file"></a>**Um ficheiro que pretendo utilizar tenha sido em camadas... como posso recuperá-la no disco para utilização localmente?**  
    É a forma mais fácil para recuperar um ficheiro de disco para abri-lo. O filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys) perfeitamente irá transferir o ficheiro da partilha de ficheiros do Azure sem precisar de fazer qualquer trabalho. Para tipos de ficheiro que podem ser parcialmente ficheiros de zip, abertura de um ficheiro ou de leitura, por exemplo, o suporte não resultará na transferência de todo o ficheiro.

    Também é possível forçar um ficheiro para possível resgatar os através do PowerShell. Por exemplo, isto poderá ser útil se pretender recuperar muitos ficheiros de uma só vez (por exemplo, todos os ficheiros numa pasta). Abra uma sessão do PowerShell para o nó de servidor em que a sincronização de ficheiros do Azure está instalada e execute os seguintes comandos do PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**Como posso impor a um ficheiro ou diretório para ser colocado em camadas?**  
    Quando ativada, a funcionalidade de camadas na nuvem será automaticamente ficheiros com base na última acesso de camada e modificar vezes para alcançar a percentagem de espaço livre no volume especificada no ponto final da nuvem, no entanto, por vezes, poderá pretender forçar um ficheiro para a camada manualmente. Por exemplo, isto poderá ser útil se guardar um ficheiro grande, que não pretender utilizar novamente durante muito tempo e pretende que o espaço livre no seu volume agora para outras ficheiros/pastas. Pode forçar a criação de camadas com os seguintes comandos do PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Os ficheiros ou pastas são excluídas automaticamente por uma sincronização de ficheiros do Azure?**
    Por predefinição, a sincronização de ficheiros do Azure exclui os seguintes ficheiros:
    
    - Desktop.ini
    - thumbs.dB
    - ehthumbs.dB
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    As seguintes pastas também são excluídas por predefinição:

    - Informações de volume \System
    - \$RECICLAGEM. RECICLAGEM
    - \SyncShareState

* <a id="afs-os-support"></a>**Gostaria de utilizar a sincronização de ficheiros do Azure com o Windows Server 2008 R2, com o Linux, ou com o meu dispositivo NAS - sincronização de ficheiros do Azure suporta que?**
    Hoje em dia, sincronização de ficheiros do Azure só suporta o Windows Server 2016 e Windows Server 2012 R2. Neste momento, não temos planos de que pode partilhar, mas estamos aberta e interessado suportar plataformas adicionais com base no pedido do cliente. Indique as plataformas que pretende-nos para suportar no [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

## <a name="security-authentication-and-access-control"></a>Segurança, a autenticação e controlo de acesso
* <a id="ad-support"></a>**É suportada pelo Azure ficheiros de controlo de acesso e a autenticação baseada no Active Directory?**  
    Ficheiros do Azure, pode gerir o controlo de acesso de duas formas:

    - Através da SAS, pode gerar tokens com permissões específicas que são válidos por um intervalo de tempo especificado. Por exemplo, pode gerar um token com acesso só de leitura para um determinado ficheiro com a expiração de 10 minutos. Quem tiver este token enquanto for válido tem acesso só de leitura a esse ficheiro para os 10 minutos. Chaves SAS só são suportadas através das bibliotecas de cliente de REST API ou hoje em dia, tem de montar a partilha de ficheiros do Azure através de SMB com as chaves de conta de armazenamento.

    - Sincronização de ficheiros do Azure irá preservar e replicar todas as ACLs (baseado no AD ou locais) para todos os pontos finais de servidor que sincroniza-se para. Porque o Windows Server já pode autenticar com o Active Directory, sincronização de ficheiros do Azure, pode fornecer uma medida de intervalo de paragem excelente até suporte total para autenticação baseada em AD e suporte da ACL são recebidos.

    Ficheiros do Azure não suporta o Active Directory diretamente neste momento.

* <a id="encryption-at-rest"></a>**Como garantir que meu partilha de ficheiros do Azure é encriptado em rest?**  
    Encriptação em rest está a ser ativado por predefinição em todas as regiões. Para estas regiões, não terá de fazer nada para ativar a encriptação. Para outras regiões, consulte [encriptação do lado do servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**Como podem a fornecer acesso a um ficheiro específico, utilizando um browser?**  
    Através da SAS, pode gerar tokens com permissões específicas que são válidos por um intervalo de tempo especificado. Por exemplo, pode gerar um token de acesso só de leitura a um determinado ficheiro por um período de tempo específico. Quem tiver este url pode aceder ao ficheiro diretamente a partir de qualquer browser enquanto é válido. As chaves SAS podem ser facilmente geradas a partir da IU, como o Explorador de Armazenamento.

* <a id="file-level-permissions"></a>**É possível especificar só de leitura ou permissões só de escrita em pastas na partilha de?**  
    Não tem este nível de controlo sobre as permissões se montar a partilha de ficheiros através do SMB. No entanto, pode conseguir isto ao criar uma assinatura de acesso partilhado (SAS) através da API REST ou das bibliotecas de cliente.

* <a id="ip-restrictions"></a>**Pode implementar restrições de IP para uma partilha de ficheiros do Azure?**  
    Sim, o acesso à partilha de ficheiros do Azure pode ser restringido a um nível de conta de armazenamento. Para obter mais informações, consulte ver [configurar Firewalls de armazenamento do Azure e as redes virtuais](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**Quais são as políticas de conformidade de dados suportadas para ficheiros do Azure?**  
   Ficheiros do Azure é executada sobre a mesma arquitetura de armazenamento como outros serviços de armazenamento no armazenamento do Azure e aplica-se as mesmas políticas de conformidade de dados. Obter mais informações sobre a compatibilidade de dados do Storage do Azure, pode transferir e consulte [documento de proteção de dados do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409) e [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Acesso no local
* <a id="expressroute-not-required"></a>**É necessário utilizar o Azure ExpressRoute para ligar aos ficheiros do Azure ou utilizar a sincronização de ficheiros do Azure no local?**  
    Não, ExpressRoute não é necessário para aceder a uma partilha de ficheiros do Azure. Se estiver a montar uma partilha de ficheiros do Azure diretamente no local, todos os que não é necessária é que tenha a porta 445 (saída de TCP) aberta para acesso à Internet (esta é a porta SMB comunica). Se estiver a utilizar a sincronização de ficheiros do Azure, todos os que requer é a porta 443 (saída de TCP) para acesso HTTPS (não SMB necessário). No entanto, ExpressRoute pode ser utilizado com qualquer uma das opções de acesso, se assim o desejar.

* <a id="mount-locally"></a>**Como montar uma partilha de ficheiros do Azure no meu computador local?**  
    É possível montar a partilha de ficheiros através do protocolo SMB, desde que a porta 445 (saída de TCP) esteja aberta e o cliente suportar o protocolo SMB 3.0 (por exemplo, estiver a utilizar Windows 10 ou Windows Server 2016). Se a porta 445 estiver bloqueada pela política da sua organização ou pelo seu ISP, pode utilizar a sincronização de ficheiros do Azure para aceder à partilha de ficheiros do Azure.

## <a name="backup"></a>Cópia de segurança
* <a id="backup-share"></a>**Como posso fazer cópia de segurança os meus ficheiros do Azure partilhar?**  
    Pode utilizar periódica [partilhar instantâneos (pré-visualização)](storage-how-to-use-files-snapshots.md) para proteção contra eliminações acidentais. Pode utilizar o AzCopy, RoboCopy, ou um 3rd ferramenta de cópia de segurança que pode uma partilha de ficheiros montados de cópia de segurança de terceiros. 

## <a name="share-snapshots"></a>Partilhar instantâneos
### <a name="share-snapshots---general"></a>Partilhar instantâneos - gerais
* <a id="what-are-snaphots"></a>**O que é o instantâneo de partilha de ficheiros?**  
    Instantâneos de partilha de ficheiros do Azure permite-lhe criar um versões só de leitura das suas partilhas de ficheiros. Também permite-lhe copiar uma versão antiga do seu back conteúdo para a mesma partilha ou uma localização alternativa no Azure ou no local para que as modificações adicionais. Para saber mais sobre o instantâneo de partilha, consulte o artigo em [partilhar descrição geral de instantâneo](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**Onde estão armazenados os meus instantâneos partilha?**  
    Instantâneos de partilha são armazenados na mesma conta de armazenamento como a partilha de ficheiros.

* <a id="snapshot-perf-impact"></a>**Existem as implicações de desempenho?**  
    Instantâneos de partilha não têm qualquer overhead de desempenho.

* <a id="snapshot-consistency"></a>**Aplicação de instantâneos de partilha são consistentes?**  
    Não. Os instantâneos de partilha não são aplicação consistente. Utilizador terá de remover da cache as escritas da aplicação para a partilha antes de tirar o instantâneo de partilha.

* <a id="snapshot-limits"></a>**Existem limites no número de instantâneos de partilha?**  
    Não há um limite de 200 instantâneos de partilha que pode manter os ficheiros do Azure. Partilha de instantâneos não contam para a quota de partilha pelo que não existe nenhum por limite de partilha no total de espaço utilizado por todos os instantâneos de partilha. Limites de conta de armazenamento ainda são aplicáveis. Depois de instantâneos de partilha de 200, instantâneos mais antigos terá de ser eliminado para criar a partilha dos novos instantâneos.

### <a name="create-share-snapshots"></a>Criar partilha de instantâneos
* <a id="file-snaphsots"></a>**Pode criar instantâneos de partilha de ficheiros individuais?**  
    Instantâneos de partilha são criados ao nível da partilha de ficheiros. Pode restaurar ficheiros individuais a partir do instantâneo de partilha de ficheiros, mas não é possível criar instantâneos de partilha ao nível dos ficheiros. No entanto, se tiver criado um instantâneo de partilha de nível de partilha e pretende para listar os instantâneos de partilha em que um determinado ficheiro foi alterado, pode fazê-da experiência de "Versões anteriores" numa partilha de Windows montada. Indique se tiver necessidade de uma funcionalidade de instantâneos de ficheiros [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>**Pode criar instantâneos de partilha da partilha de ficheiros encriptado?**  
    Pode tirar um instantâneo de partilha de partilhas de ficheiros do Azure com a encriptação de Inativos ativada. Pode restaurar ficheiros a partir de um instantâneo de partilha para uma partilha de ficheiros encriptados. Se a partilha for encriptada, o instantâneo de partilha também será encriptado.

* <a id="geo-redundant-snaphsots"></a>**A minha instantâneos de partilha são georredundante?**
    Instantâneo de partilha terão a mesma redundância como a partilha de ficheiros do Azure para a qual foram executadas. Se tiver selecionado o armazenamento georredundante (GRS) para a sua conta também será armazenado redundantly o instantâneo de partilha na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerir a partilha de instantâneos
* <a id="browse-snapshots-linux"></a>**Pode procurar meu instantâneos de partilha do Linux?**  
    Pode utilizar o Azure CLI 2.0 para criar, listar, procurar e restaurar no Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>**Pode copiar os instantâneos de partilha para uma conta de armazenamento diferente?**  
    Pode copiar ficheiros de instantâneos de partilha para outra localização, mas não os instantâneos de partilha próprios.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados de instantâneos de partilha
* <a id="promote-share-snapshot"></a>**Pode promover um instantâneo de partilha para a partilha de base?**  
    Só pode copiar dados a partir de um instantâneo de partilha para qualquer destino pretendido. No entanto, não é possível promover um instantâneo de partilha para a partilha de base.

* <a id="restore-snapshotted-file-to-other-share"></a>**Posso restaure os dados a partir do meu instantâneo de partilha para uma conta de armazenamento diferente?**  
    Sim. Podem ser copiados os ficheiros a partir de um instantâneo de partilha original ou uma localização alternativa que inclui a conta de armazenamento mesmo/diferente em regiões idêntica ou diferentes. Também pode copiar ficheiros para qualquer outra nuvem ou no local.    
  
### <a name="cleanup-share-snapshot"></a>Instantâneo de partilha de limpeza
* <a id="delete-share-keep-snapshots"></a>**Pode eliminar a minha partilha mas não partilhar instantâneos?**
    Não poderá eliminar a partilha se tiver instantâneos de partilha de Active Directory na sua partilha. Existe uma API disponível para a eliminação de instantâneos de partilha, juntamente com a partilha e pode conseguir o mesmo do portal do Azure, bem como.

* <a id="delete-share-with-snapshots"></a>**O que acontece a minha instantâneos de partilha de eliminar a minha conta de armazenamento?**
    Se eliminar a conta de armazenamento, os instantâneos de partilha também serão eliminados.

## <a name="billing-and-pricing"></a>Faturação e preços
* <a id="vm-file-share-network-traffic"></a>**O tráfego de rede entre uma VM do Azure e uma partilha de ficheiros do Azure contam como largura de banda externa que é cobrada na subscrição?**  
    Se a partilha de ficheiros e VM estiverem na mesma região do Azure, o tráfego entre eles é gratuito. Se estiverem em regiões diferentes, o tráfego entre os mesmos será cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>**Quanto deve partilhar o custo de instantâneos?**
     Durante a partilha de pré-visualização capacidade de instantâneos não lhe será cobrada. Standard armazenamento saída e a transação custos sill aplicam-se. Depois de disponibilidade geral, serão cobradas capacidade e transações de instantâneo de partilha.
     
     Os instantâneos de partilha são incrementais natureza. O instantâneo de base de partilha é a partilha de si próprio. Todos os instantâneos de partilha subsequentes são incrementais e apenas armazenará a diferença de instantâneo de partilha anterior. É-lhe faturado apenas para o conteúdo alterado. Se tiver uma partilha com GiB 100 de dados, mas apenas 5 GiB foi alterado desde o último instantâneo de partilha, será de instantâneo partilha consome apenas 5 GiB adicionais e será faturado apenas 105 GiB. Consulte [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais informações sobre transações e encargos associados à saída padrão.

## <a name="scale-and-performance"></a>Dimensionamento e desempenho
* <a id="files-scale-limits"></a>**Quais são os limites de escala de ficheiros do Azure?**  
    Para obter informações sobre metas de escalabilidade e desempenho dos ficheiros do Azure, consulte [metas de desempenho e escalabilidade de ficheiros do Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>**Preciso de uma partilha de ficheiros maior do que os ficheiros do Azure atualmente oferece … posso aumentar o tamanho das minha partilha de ficheiros do Azure?**  
    Não, o tamanho máximo de uma partilha de ficheiros do Azure é 5 TiB. Esta é atualmente um limite rígido que iremos não é possível ajustar. Estamos a trabalhar para uma solução para aumentar o tamanho da partilha 100 TiBs, mas não tem linhas cronológicas partilhar neste momento.

* <a id="open-handles-quota"></a>**Quantos clientes podem aceder em simultâneo ao mesmo ficheiro?**  
    Há uma quota de identificadores abertos 2000 num único ficheiro. Assim que tiver 2000 identificadores abertos, obterá um erro que for atingida a quota.

* <a id="zip-slow-performance"></a>**O meu desempenho foi lento ao tentar deszipar ficheiros para ficheiros do Azure. O que devo fazer?**  
    Para transferir um grande número de ficheiros para ficheiros do Azure, recomendamos que utilize AzCopy (Windows, pré-visualização do Linux/Unix) ou do Azure Powershell, estas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>**Posso ter montada meu partilha de ficheiros do Azure no Windows Server 2012 R2 ou Windows 8.1 e o meu desempenho for lento - por que motivo?**  
    Se um problema conhecido montar uma partilha de ficheiros do Azure no Windows Server 2012 R2 e Windows 8.1 que foi aplicado na atualização cumulativa Abril de 2014 para Windows 8.1 e Windows Server 2012 R2. Certifique-se de que todas as instâncias do Windows Server 2012 R2 e Windows 8.1 têm este patch aplicada para um ótimo desempenho (como é óbvio, sempre Recomendamos colocar todos os patches do Windows através do Windows Update). Para obter mais informações, consulte o artigo KB associado, [desempenho lento ao aceder a ficheiros do Azure a partir do Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funcionalidades e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>**Posso utilizar o meu partilha de ficheiros do Azure como um *testemunho de partilha de ficheiros* para os meus Cluster de ativação pós-falha do Windows Server?**  
    Isto não é atualmente suportado para uma partilha de ficheiros do Azure. Para obter mais informações sobre como definir esta opção para o Blob storage do Azure, consulte [implementar um testemunho de nuvem para um Cluster de ativação pós-falha](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>**Podem montar uma partilha de ficheiros do Azure numa instância de contentor do Azure?** ?  
    Sim, as partilhas de ficheiros do Azure são fantastic opção para manter as informações para além da duração de uma instância de contentor. Para obter mais informações, consulte [montar uma partilha de ficheiros do Azure com instâncias de contentor do Azure](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**Existe uma operação de mudança de nome na REST API?**  
    Neste momento, não.

* <a id="nested-shares"></a>**Pode possível ter partilhas aninhadas, por outras palavras, uma partilha numa partilha?**  
    Não. A partilha de ficheiros é o controlador virtual que é possível montar, pelo que não são suportadas partilhas aninhadas.

* <a id="ibm-mq"></a>**Utilizar os ficheiros do Azure com o IBM MQ**  
    A IBM lançou um documento para guiar os clientes do IBM MQ quando configurar os ficheiros do Azure com o respetivo serviço. Para mais informações, consulte [Como configurar o gestor de filas de várias instâncias do IBM MQ com o Serviço do Ficheiro do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consultar também
* [Resolução de problemas de ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Resolução de problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas de sincronização de ficheiros do Azure (pré-visualização)](storage-sync-files-troubleshoot.md)