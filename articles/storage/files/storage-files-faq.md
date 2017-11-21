---
title: Perguntas mais frequentes sobre ficheiros do Azure | Microsoft Docs
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
ms.openlocfilehash: da8ccf35dcc873a5c31842c6eb7bdf72879854c2
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>Perguntas mais frequentes sobre os ficheiros do Azure
[Ficheiros do Azure](storage-files-introduction.md) oferece completamente geridos partilhas de ficheiros na nuvem que estão acessíveis através da norma da indústria [protocolo Server Message Block (SMB)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (também conhecido como sistema de ficheiros Internet comum ou CIFS). É possível montar partilhas de ficheiros do Azure em simultâneo em implementações de nuvem ou no local do Windows, Linux e macOS. Também pode colocar em cache as partilhas de ficheiros do Azure nas máquinas do Windows Server utilizando a sincronização de ficheiros do Azure (pré-visualização) para acesso rápido próximo de onde os dados são utilizados.

Este artigo responde a questões recorrentes sobre ficheiros do Azure e funcionalidades, incluindo a utilização de sincronização de ficheiros do Azure com ficheiros do Azure. Se não vir a resposta à sua pergunta, pode contactar-nos através dos seguintes canais (pela ordem em constante crescendo):

1. A secção de comentários deste artigo.
2. [Fórum de armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=windowsazuredata).
3. [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Suporte da Microsoft. Para criar um novo pedido de suporte, no portal do Azure, o **ajudar** separador, selecione o **ajuda + suporte** botão e, em seguida, selecione **novo pedido de suporte**.

## <a name="general"></a>Geral
* <a id="why-files-useful"></a>**Como ficheiros do Azure é útil?**  
   Pode utilizar os ficheiros do Azure para criar partilhas de ficheiros na nuvem, sem a ser responsável por gerir a sobrecarga de um servidor físico, o dispositivo ou aplicação. Fazemos o trabalho monotonous para si, incluindo aplicar atualizações do SO e substituir discos incorretos. Para saber mais sobre os cenários que ficheiros do Azure pode ajudar com, consulte [motivo pelo qual os ficheiros do Azure é útil](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>**O que são formas diferentes de acesso aos ficheiros nos ficheiros do Azure?**  
    É possível montar a partilha de ficheiros no seu computador local utilizando o protocolo SMB 3.0, ou pode utilizar ferramentas como [Explorador de armazenamento](http://storageexplorer.com/) para aceder a ficheiros na partilha de ficheiros. Da sua aplicação, pode utilizar bibliotecas de cliente de armazenamento, REST APIs, PowerShell ou o CLI do Azure para aceder aos seus ficheiros na partilha de ficheiros do Azure.

* <a id="what-is-afs"></a>**O que é a sincronização de ficheiros do Azure?**  
    Pode utilizar a sincronização de ficheiros do Azure para centralizar partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo o flexibilidade, o desempenho e a compatibilidade de um servidor de ficheiros no local. Sincronização de ficheiros do Azure transforma as máquinas do Windows Server para uma cache rápida da Azure da partilha de ficheiros. Pode utilizar qualquer protocolo de que está disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, o sistema de ficheiros de rede (NFS) e o ficheiro de transferência de protocolo de serviço (FTPS). Pode ter caches tantos conforme necessário por todo o mundo.

* <a id="files-versus-blobs"></a>**Por que motivo utilizaria uma partilha de ficheiros do Azure versus Blob storage do Azure com os meus dados?**  
    Ficheiros do Azure e BLOBs do Azure storage oferecem ambos formas de armazenam grandes quantidades de dados na nuvem, mas são úteis para fins de ligeiramente diferentes. 
    
    Armazenamento de Blobs do Azure é útil para aplicações de grande escala, nativa na nuvem que precisam para armazenar dados não estruturados. Para maximizar o desempenho e dimensionamento, o Blob storage do Azure é uma abstração de armazenamento mais simples do que um sistema de ficheiros for VERDADEIRO. Pode aceder ao Blob storage do Azure apenas através de bibliotecas de cliente baseado em REST (ou diretamente através do protocolo baseado em REST).

    Ficheiros do Azure é especificamente um sistema de ficheiros. Ficheiros do Azure tem todos os abstracts de ficheiro que conhece e adoram de anos de trabalhar com sistemas de operativos no local. Como um armazenamento de Blobs do Azure, ficheiros do Azure oferece uma interface REST e bibliotecas de cliente baseado em REST. Ao contrário do armazenamento de Blobs do Azure, ficheiros do Azure oferece acesso SMB para partilhas de ficheiros do Azure. Ao utilizar o SMB, podem montar uma partilha de ficheiro Azure diretamente no Windows, Linux ou macOS, no local ou na nuvem VMs, sem escrever qualquer código ou a anexar o quaisquer controladores especiais ao sistema de ficheiros. Também pode colocar em cache as partilhas de ficheiros do Azure em servidores de ficheiros no local através da utilização de sincronização de ficheiros do Azure para um acesso rápido, próximo de onde os dados são utilizados. 
   
    Para obter uma descrição mais aprofundada sobre as diferenças entre os ficheiros do Azure e o armazenamento de Blobs do Azure, consulte [decidir quando deve utilizar o Blob storage do Azure, ficheiros do Azure ou do Azure discos](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais sobre armazenamento de Blobs do Azure, consulte o artigo [introdução ao Blob storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Por que motivo utilizaria uma partilha de ficheiros do Azure em vez de discos do Azure?**  
    Um disco em discos do Azure é simplesmente um disco. Um disco de autónomo por si só não é demasiado útil. Para obter o valor a partir de discos do Azure, tem de ligar um disco para uma máquina virtual que está em execução no Azure. Discos do Azure podem ser utilizados para tudo o que pretende utilizar um disco para um servidor no local. Pode utilizá-lo como um disco de SO do sistema, como o espaço de comutação de sistema operativo ou armazenamento dedicado para uma aplicação. É uma utilização interessante para discos do Azure criar um servidor de ficheiros na nuvem para utilizar os mesmos locais onde poderá utilizar uma partilha de ficheiros do Azure. Implementar um servidor de ficheiros em máquinas virtuais do Azure é uma forma de elevado desempenho para obter o armazenamento de ficheiros no Azure quando necessitar de opções de implementação que atualmente não são suportadas pelo Azure ficheiros (por exemplo, o NFS protocolo suporte ou premium armazenamento). 

    No entanto, a ser executado um servidor de ficheiros com discos do Azure como armazenamento de back-end, normalmente, é muito mais dispendioso do que utilizar uma partilha de ficheiros do Azure, para algumas razões. Em primeiro lugar, para além de pagar para armazenamento em disco, também tem paga para o custo de um ou mais VMs do Azure em execução. Segundo, também tem de gerir as VMs que são utilizadas para executar o servidor de ficheiros. Por exemplo, são responsáveis por atualizações do SO. Por fim, se for necessário, em última análise de dados em cache no local, é cópias de segurança para configurar e gerir tecnologias de replicação, tais como ficheiro de sistema replicação distribuídos (DFSR), para se certificar de que ocorrem.

    É uma abordagem para obter o melhor dos ficheiros do Azure e um servidor de ficheiros que está alojado no Azure Virtual Machines (além de utilizar discos do Azure como armazenamento de back-end) para instalar a sincronização de ficheiros do Azure num servidor de ficheiros que está alojado numa VM de nuvem. Se a partilha de ficheiros do Azure está na mesma região que o servidor de ficheiros, pode ativar a nuvem de camadas e defina o volume de percentagem de espaço livre para máximo (99%). Isto garante mínima duplicação de dados. Também pode utilizar todas as aplicações que pretende com os servidores de ficheiros, como o suportem de aplicações que necessitam de protocolo NFS.

    Para obter informações sobre uma opção para configurar um servidor de ficheiros de elevado desempenho e elevada disponibilidade no Azure, consulte [convidado de VM do IaaS implementar clusters no Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). Para obter uma descrição mais aprofundada das diferenças entre os ficheiros do Azure e os discos do Azure, consulte [decidir quando deve utilizar o Blob storage do Azure, ficheiros do Azure ou do Azure discos](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Para saber mais acerca dos discos do Azure, consulte [descrição geral do Azure gerida discos](../../virtual-machines/windows/managed-disks-overview.md).

* <a id="get-started"></a>**Como começar a utilizar a utilizar ficheiros do Azure?**  
   Introdução aos ficheiros do Azure é fácil. Primeiro, [criar uma partilha de ficheiros](storage-how-to-create-file-share.md)e, em seguida, montá-la no seu sistema de operativo preferencial: 

    * [Montar no Windows](storage-how-to-use-files-windows.md)
    * [Montar no Linux](storage-how-to-use-files-linux.md)
    * [No macOS de montagem](storage-how-to-use-files-mac.md)

   Para obter um guia mais aprofundado sobre a implementação de uma partilha de ficheiros do Azure substituir a produção de partilhas de ficheiros na sua organização, consulte [planear uma implementação de ficheiros do Azure](storage-files-planning.md).

* <a id="redundancy-options"></a>**Que opções de redundância de armazenamento são suportadas pelo Azure ficheiros?**  
    Atualmente, os ficheiros do Azure suporta armazenamento apenas localmente redundante (LRS) e armazenamento georredundante (GRS). Planeamos suportar o armazenamento com redundância de zona (ZRS) e armazenamento (RA-GRS) georredundante com acesso de leitura no futuro, mas não temos linhas cronológicas partilhar neste momento.

* <a id="tier-options"></a>**As camadas de armazenamento são suportadas em ficheiros do Azure?**  
    Atualmente, os ficheiros do Azure suporta apenas a camada de armazenamento standard. Temos linhas cronológicas partilhar para armazenamento premium e do armazenamento de acesso esporádico suportam neste momento. 
    
    > [!NOTE]
    > Não é possível criar partilhas de ficheiros do Azure de contas do storage apenas de BLOBs ou de contas de armazenamento premium.

* <a id="give-us-feedback"></a>**Quero realmente ver uma funcionalidade específica adicionada aos ficheiros do Azure. Pode adicioná-lo?**  
    A equipa de ficheiros do Azure está interessada em hearing todos os comentários que tem sobre o nosso serviço. Votar em pedidos de funcionalidades em [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Iremos procura reencaminhar para delighting com muitas funcionalidades novas.

## <a name="azure-file-sync"></a>Sincronização de ficheiros do Azure
* <a id="afs-region-availability"></a>**As regiões são suportadas para a sincronização de ficheiros do Azure (pré-visualização)?**  
    Atualmente, a sincronização de ficheiros do Azure está disponível nos EUA oeste, Europa Ocidental, leste da Austrália e Sudeste asiático. Suporte para obter mais regiões será adicionado à medida que trabalha na direção de disponibilidade geral. Para obter mais informações, consulte [disponibilidade de região](storage-sync-files-planning.md#region-availability).

* <a id="cross-domain-sync"></a>**Pode ter servidores associados a um domínio e não associados a domínios no mesmo grupo de sincronização?**  
    Sim. Um grupo de sincronização pode conter pontos finais do servidor que têm diferentes membros do Active Directory, mesmo que não estejam associados a um domínio. Embora tecnicamente funciona esta configuração, não recomendamos esta como configuração típica porque listas de controlo de acesso (ACL) que são definidas para os ficheiros e pastas num servidor não poderá ser impostas por outros servidores no grupo de sincronização. Para obter os melhores resultados, recomendamos que a sincronização entre os servidores que estão na mesma floresta do Active Directory, entre os servidores que estão em diferentes florestas do Active Directory, mas que tenham estabelecido relações de confiança ou entre os servidores que não estejam num domínio. Recomendamos que evite utilizar uma combinação destas configurações.

* <a id="afs-change-detection"></a>**Criar um ficheiro diretamente na minha partilha de ficheiros do Azure utilizando o SMB ou no portal. Quanto tempo demora para o ficheiro a sincronizar para os servidores no grupo de sincronização?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

* <a id="afs-conflict-resolution"></a>**Se o mesmo ficheiro é alterado em dois servidores aproximadamente à mesma hora, o que acontece?**  
    Sincronização de ficheiros do Azure utiliza uma estratégia de resolução de conflitos simples: vamos manter ambas as alterações a ficheiros que são alterados em dois servidores ao mesmo tempo. A alteração mais recentemente gravada mantém o nome de ficheiro original. O ficheiro mais antigo tem a máquina "origem" e o número de conflito acrescentado ao nome. Segue-se esta taxonomia: 
   
    \<FileNameWithoutExtension\>-\<MachineName\>\[-#\].\< Ext\>  

    Por exemplo, o conflito de CompanyReport.docx primeiro iria tornar-se CompanyReport CentralServer.docx se CentralServer onde ocorreu a operação de escrita mais antiga. O segundo conflito seria ter um nome CompanyReport-CentralServer-1.docx.

* <a id="afs-storage-redundancy"></a>**Armazenamento georredundante é suportado para a sincronização de ficheiros do Azure?**  
    Sim, os ficheiros do Azure suporta armazenamento localmente redundante (LRS) e armazenamento georredundante (GRS). Se ocorrer uma ativação pós-falha GRS entre regiões emparelhadas, recomendamos que tratar a região novo como uma cópia de segurança de dados apenas. Sincronização de ficheiros do Azure não começa automaticamente a sincronizar com a região primária novo. 

* <a id="sizeondisk-versus-size"></a>**Por que motivo não o *tamanho do disco* propriedade para uma correspondência de ficheiro a *tamanho* propriedade depois de utilizar a sincronização de ficheiros do Azure?**  
    Explorador de ficheiros Windows expõe duas propriedades para representar o tamanho de um ficheiro: **tamanho** e **tamanho do disco**. Estas propriedades subtly diferem no significado. **Tamanho** representa o tamanho completo do ficheiro. **Tamanho do disco** representa o tamanho do fluxo de ficheiros que é armazenado no disco. Os valores para estas propriedades podem diferir por diversas razões, tais como compressão, utilize da eliminação de duplicados de dados ou na nuvem em camadas com sincronização de ficheiros do Azure. Se um ficheiro está em camadas para uma partilha de ficheiros do Azure, o tamanho do disco for zero, porque o fluxo do ficheiro está armazenado na partilha de ficheiros do Azure e não no disco. Também é possível para um ficheiro a ser parcialmente em camadas (ou parcialmente resgatar os). Num ficheiro parcialmente em camadas, parte do ficheiro está no disco. Esta situação pode ocorrer quando os ficheiros parcialmente são lidas pelas aplicações, como leitores multimedia ou zip utilitários. 

* <a id="is-my-file-tiered"></a>**Como posso saber se um ficheiro foi camado?**  
    Existem várias formas para verificar se um ficheiro tiver sido camado para a partilha de ficheiros do Azure:
    
   *  **Verifique os atributos de ficheiro no ficheiro.**
     Para tal, faça duplo clique num ficheiro, aceda a **detalhes**e, em seguida, desloque para baixo até o **atributos** propriedade. Um ficheiro em camadas tem os seguintes atributos definir:     
        
        | Letra de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o ficheiro deve ser feito pelo software de cópia de segurança. Este atributo é sempre definido, independentemente se o ficheiro está em camadas ou totalmente armazenado no disco. |
        | P | Ficheiro disperso | Indica que o ficheiro é um ficheiro disperso. Um ficheiro disperso é um tipo especializado de ficheiros NTFS oferece para uma utilização eficiente quando o ficheiro no fluxo de disco principalmente está vazio. Sincronização de ficheiros do Azure utiliza ficheiros dispersos porque um ficheiro é totalmente camado ou parcialmente resgatar os. Num ficheiro totalmente em camadas, a sequência de ficheiros é armazenada na nuvem. Um ficheiro parcialmente recalled, que parte dos ficheiros já se encontra no disco. Se um ficheiro é totalmente resgatar os para o disco, sincronização de ficheiros do Azure converte-o partir de um ficheiro disperso para um ficheiro regular. |
        | L | Ponto de reanálise | Indica que o ficheiro tem um ponto de reanálise. Um ponto de reanálise é um ponteiro especial para utilização por um filtro de sistema de ficheiros. Sincronização de ficheiros do Azure utiliza pontos de reanálise, para definir o filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys) a localização de nuvem onde está armazenado o ficheiro. Isto suporta o acesso totalmente integrado. Os utilizadores não precisam de saber que a sincronização de ficheiros do Azure está a ser utilizada ou como obter acesso ao ficheiro na partilha de ficheiros do Azure. Quando um ficheiro é totalmente resgatar os, Azure ficheiro Sync remove o ponto de reanálise do ficheiro. |
        | NÃ | Offline | Indica que alguns ou todos os conteúdos do ficheiro não estão armazenados num disco. Quando um ficheiro é totalmente resgatar os, Azure ficheiro Sync remove este atributo. |

        ![A caixa de diálogo de propriedades para um ficheiro, o separador de detalhes, selecionado](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Pode ver os atributos para todos os ficheiros numa pasta adicionando o **atributos** campo para a visualização de tabela do Explorador de ficheiros. Para tal, faça duplo clique numa coluna existente (por exemplo, **tamanho**), selecione **mais**e, em seguida, selecione **atributos** na lista pendente.
        
   * **Utilize `fsutil` para procurar pontos de reanálise num ficheiro.**
       Tal como descrito na opção anterior, um ficheiro em camadas tem sempre um conjunto de pontos de reanálise. Um ponteiro de reanálise é um ponteiro especial para o filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys). Para verificar se um ficheiro tem um ponto de reanálise, numa janela de linha de comandos ou PowerShell elevada, execute o `fsutil` utilitário:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o ficheiro tem um ponto de reanálise, que pode esperava **reanálise valor de etiqueta: 0x8000001e**. Este valor hexadecimal é o valor de ponto de reanálise que é propriedade de sincronização de ficheiros do Azure. A saída também contém os dados de reanálise que representa o caminho para o ficheiro na partilha de ficheiros do Azure.

        > [!WARNING]  
        > O `fsutil reparsepoint` comando utilitário tem também a capacidade de eliminar um ponto de reanálise. Não execute este comando, a menos que a equipa de engenharia da sincronização de ficheiros do Azure pede-lhe. Execute este comando pode resultar em perda de dados. 

* <a id="afs-recall-file"></a>**Um ficheiro que pretendo utilizar tenha sido em camadas. Como posso recuperar o ficheiro de disco para utilizá-lo localmente?**  
    É a forma mais fácil para recuperar um ficheiro de disco para abrir o ficheiro. O filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys) transfere o ficheiro de forma totalmente integrada a partilha de ficheiros do Azure sem qualquer trabalho da sua parte. Para tipos de ficheiro que podem ser parcialmente leitura, tais como ficheiros de suporte ou. zip, abertura de um ficheiro não transferir todo o ficheiro.

    Também pode utilizar do PowerShell para forçar um ficheiro para possível resgatar os. Esta opção pode ser útil se pretender recuperar vários ficheiros de uma só vez, por exemplo, todos os ficheiros numa pasta. Abra uma sessão do PowerShell para o nó de servidor em que a sincronização de ficheiros do Azure está instalada e, em seguida, execute os seguintes comandos do PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* <a id="afs-force-tiering"></a>**Como forçar um ficheiro ou diretório para ser colocado em camadas?**  
    Se estiver ativada a funcionalidade de camadas na nuvem, o cloud camadas automaticamente os ficheiros de camadas com base no último acesso e modifique vezes para alcançar a percentagem de espaço livre no volume especificada no ponto final da nuvem. Por vezes, no entanto, pode querer forçar manualmente um ficheiro para a camada. Isto poderá ser útil se guardar um ficheiro grande que não pretende utilizar novamente durante muito tempo e pretende que o espaço livre no seu volume agora a utilizar para outros ficheiros e pastas. Pode forçar a criação de camadas utilizando os seguintes comandos do PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* <a id="afs-files-excluded"></a>**Os ficheiros ou pastas são excluídas automaticamente por uma sincronização de ficheiros do Azure?**  
    Por predefinição, a sincronização de ficheiros do Azure exclui os seguintes ficheiros:
    * Desktop.ini
    * thumbs.dB
    * ehthumbs.dB
    * ~$\*.\*
    * \*.laccdb
    * \*.tmp
    * 635D02A9D91C401B97884B82B3BCDAEA.\*

    As seguintes pastas também são excluídas por predefinição:

    * Informações de volume \System
    * \$RECICLAGEM. RECICLAGEM
    * \SyncShareState

* <a id="afs-os-support"></a>**Pode utilizar sincronização de ficheiros do Azure com o Windows Server 2008 R2, o Linux ou o meu dispositivo de armazenamento ligados à rede (NAS)?**  
    Atualmente, a sincronização de ficheiros do Azure suporta apenas o Windows Server 2016 e o Windows Server 2012 R2. Neste momento, não temos planos de que pode partilhar, mas estamos abertos para suportar plataformas adicionais com base no pedido do cliente. Indique em [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) quais as plataformas gostaria de ver melhorado para suportar.

## <a name="security-authentication-and-access-control"></a>Segurança, autenticação e controlo de acesso
* <a id="ad-support"></a>**É suportada pelo Azure ficheiros de controlo de acesso e a autenticação baseada no Active Directory?**  
    Ficheiros do Azure oferece duas formas de gerir o controlo de acesso:

    - Pode utilizar assinaturas de acesso partilhado (SAS) para gerar tokens que tenham permissões específicas e que são válidas durante um intervalo de tempo especificado. Por exemplo, pode gerar um token com acesso só de leitura para um ficheiro específico que tenha uma expiração de 10 minutos. Qualquer pessoa que tiver o token enquanto o token é válido tem acesso só de leitura para esse ficheiro para os 10 minutos. Atualmente, as chaves de assinatura de acesso partilhado são suportadas apenas através da API REST ou em bibliotecas de cliente. Tem de montar a partilha de ficheiros do Azure através de SMB, utilizando as chaves de conta de armazenamento.

    - Sincronização de ficheiros do Azure preserva os dados são replicados ou todos os ACLs discricionárias DACL, (se baseada no Active Directory local ou) para todos os pontos finais do sincroniza-se para. Porque o Windows Server já pode autenticar com o Active Directory, sincronização de ficheiros do Azure é uma opção de intervalo de paragem em vigor até suporte total para autenticação baseada no Active Directory e chega do suporte ACL.

    Atualmente, o ficheiros do Azure não suporta diretamente do Active Directory.

* <a id="encryption-at-rest"></a>**Como garantir que o meu partilha de ficheiros do Azure é encriptada em pausa?**  
    Encriptação do serviço de armazenamento do Azure está no processo de ser ativado por predefinição em todas as regiões. Para estas regiões, não precisa de efetuar qualquer ação para ativar a encriptação. Para outras regiões, consulte [encriptação do lado do servidor](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>**Como pode fornecer acesso a um ficheiro específico, utilizando um browser?**  
    Pode utilizar assinaturas de acesso partilhado para gerar tokens que tenham permissões específicas e que são válidas durante um intervalo de tempo especificado. Por exemplo, pode gerar um token que fornece acesso só de leitura para um ficheiro específico, durante um período de tempo definido. Qualquer pessoa que tiver o URL pode aceder ao ficheiro diretamente a partir de qualquer browser, enquanto o token é válido. Pode facilmente gerar uma chave de assinatura de acesso partilhado de uma IU, como o Explorador de armazenamento.

* <a id="file-level-permissions"></a>**É possível especificar só de leitura ou permissões só de escrita em pastas na partilha de?**  
    Se montar a partilha de ficheiros utilizando o SMB, não tem controlo de nível de pasta sobre as permissões. No entanto, se criar uma assinatura de acesso partilhado ao utilizar a REST API ou bibliotecas de cliente, pode especificar permissões só de leitura ou só de escrita em pastas dentro da partilha.

* <a id="ip-restrictions"></a>**Pode implementar restrições de IP para uma partilha de ficheiros do Azure?**  
    Sim. Acesso à partilha de ficheiros do Azure pode ser restringido ao nível da conta de armazenamento. Para obter mais informações, consulte [configurar Firewalls de armazenamento do Azure e as redes virtuais](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>**As políticas de conformidade de dados suporta ficheiros do Azure?**  
   Ficheiros do Azure é executada sobre a mesma arquitetura de armazenamento que é utilizada nos outros serviços de armazenamento no armazenamento do Azure. Ficheiros do Azure aplicam-se as mesmas políticas de conformidade de dados que são utilizadas em outros serviços de armazenamento do Azure. Para obter mais informações sobre a compatibilidade de dados do Storage do Azure, pode transferir e consulte o [documento de proteção de dados do Microsoft Azure](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)e vá para o [Microsoft Trust Center](https://www.microsoft.com/TrustCenter/default.aspx).

## <a name="on-premises-access"></a>Acesso no local
* <a id="expressroute-not-required"></a>**É necessário utilizar o Azure ExpressRoute para ligar aos ficheiros do Azure ou utilizar a sincronização de ficheiros do Azure no local?**  
    Não. ExpressRoute não é necessário para aceder a uma partilha de ficheiros do Azure. Se está a montar um ficheiro do Azure partilhar diretamente no local, todos os que necessita é ter a porta 445 (saída de TCP), abra o acesso à internet (esta é a porta que o SMB utiliza para comunicar). Se estiver a utilizar a sincronização de ficheiros do Azure, tudo o que requer é a porta 443 (saída de TCP) para acesso HTTPS (não SMB necessário). No entanto, é *pode* utilizar o ExpressRoute com uma destas opções de acesso.

* <a id="mount-locally"></a>**Como montar uma partilha de ficheiros do Azure no meu computador local?**  
    É possível montar a partilha de ficheiros utilizando o protocolo SMB, se abrir a porta 445 (saída de TCP) e o cliente suportar o protocolo SMB 3.0 (por exemplo, se estiver a utilizar o Windows 10 ou Windows Server 2016). Se a porta 445 estiver bloqueada pela política da sua organização ou pelo seu ISP, pode utilizar a sincronização de ficheiros do Azure para aceder à partilha de ficheiros do Azure.

## <a name="backup"></a>Cópia de segurança
* <a id="backup-share"></a>**Como posso fazer cópia de segurança os meus ficheiros do Azure partilhar?**  
    Pode utilizar periódica [partilhar instantâneos (pré-visualização)](storage-how-to-use-files-snapshots.md) para proteção contra eliminações acidentais. Também pode utilizar AzCopy, Robocopy ou uma ferramenta de cópia de segurança de terceiros que pode fazer cópias de segurança de uma partilha de ficheiros montada. 

## <a name="share-snapshots"></a>Partilhar instantâneos
### <a name="share-snapshots-general"></a>Partilhar instantâneos: gerais
* <a id="what-are-snaphots"></a>**Quais são os instantâneos de partilha de ficheiros?**  
    Pode utilizar instantâneos de partilha de ficheiros do Azure para criar uma versão só de leitura das suas partilhas de ficheiros. Também pode utilizar ficheiros do Azure para copiar uma versão anterior do seu back conteúdo para a mesma partilha, para uma localização alternativa no Azure ou no local para que as modificações mais. Para obter mais informações sobre instantâneos de partilha, consulte o [partilhar descrição geral de instantâneo](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>**Onde estão armazenados os meus instantâneos partilha?**  
    Instantâneos de partilha são armazenados na mesma conta de armazenamento como a partilha de ficheiros.

* <a id="snapshot-perf-impact"></a>**Existem as implicações de desempenho para utilizar instantâneos de partilha?**  
    Instantâneos de partilha não têm qualquer overhead de desempenho.

* <a id="snapshot-consistency"></a>**Instantâneos de partilha são consistentes com aplicações?**  
    Não, os instantâneos de partilha não são consistentes com aplicações. O utilizador deve remover da cache as escritas da aplicação para a partilha antes de tirar o instantâneo de partilha.

* <a id="snapshot-limits"></a>**Existem limites sobre o número de instantâneos de partilha que posso utilizar?**  
    Sim. Ficheiros do Azure podem manter um máximo de instantâneos de partilha de 200. Instantâneos de partilha não é considerado a quota de partilha, pelo que não existe nenhum limite por partilha no total de espaço que é utilizado por todos os instantâneos de partilha. Limites de conta de armazenamento ainda são aplicáveis. Depois de instantâneos de partilha de 200, tem de eliminar instantâneos anteriores para criar a partilha dos novos instantâneos.

### <a name="create-share-snapshots"></a>Criar partilha de instantâneos
* <a id="file-snaphsots"></a>**Pode criar instantâneos de partilha de ficheiros individuais?**  
    Instantâneos de partilha são criados ao nível da partilha de ficheiros. Pode restaurar ficheiros individuais a partir do instantâneo de partilha de ficheiros, mas não é possível criar instantâneos de partilha ao nível dos ficheiros. No entanto, se tiver criado um instantâneo de partilha de nível da partilha e que pretende para listar os instantâneos de partilha onde foi alterada um ficheiro específico, para fazer isto em **versões anteriores** numa partilha de Windows montada. 
    
    Se precisar de uma funcionalidade de instantâneos de ficheiros, indique em [Azure ficheiros UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encypted-snapshots"></a>**Pode criar instantâneos de partilha de uma partilha de ficheiros encriptados?**  
    Pode tirar um instantâneo de partilha do Azure de partilhas de ficheiros que tenham a encriptação de Inativos ativada. Pode restaurar ficheiros a partir de um instantâneo de partilha para uma partilha de ficheiros encriptados. Se a partilha for encriptada, o instantâneo de partilha também é encriptado.

* <a id="geo-redundant-snaphsots"></a>**A minha instantâneos de partilha são georredundante?**  
    Instantâneos de partilha têm a mesma redundância como a partilha de ficheiros do Azure para a qual foram executadas. Se tiver selecionado o armazenamento georredundante para a sua conta, a partilha de instantâneo também é armazenada redundantly na região emparelhada.

### <a name="manage-share-snapshots"></a>Gerir a partilha de instantâneos
* <a id="browse-snapshots-linux"></a>**Pode procurar meu instantâneos de partilha do Linux?**  
    Pode utilizar o Azure CLI 2.0 para criar, listar, procurar e restaure instantâneos de partilha no Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>**Pode copiar os instantâneos de partilha para uma conta de armazenamento diferente?**  
    Pode copiar ficheiros de instantâneos de partilha para outra localização, mas não foi possível copiar os instantâneos de partilha próprios.

### <a name="restore-data-from-share-snapshots"></a>Restaurar dados de instantâneos de partilha
* <a id="promote-share-snapshot"></a>**Pode promover um instantâneo de partilha para a partilha de base?**  
    Pode copiar dados a partir de um instantâneo de partilha para qualquer outro tipo de destino. Não é possível promover um instantâneo de partilha para a partilha de base.

* <a id="restore-snapshotted-file-to-other-share"></a>**Posso restaure os dados a partir do meu instantâneo de partilha para uma conta de armazenamento diferente?**  
    Sim. Podem ser copiados os ficheiros a partir de um instantâneo de partilha para a localização original ou para uma localização alternativa que inclui a mesma conta de armazenamento ou uma conta de armazenamento diferente na mesma região ou em regiões diferentes. Também pode copiar ficheiros para uma localização no local ou para quaisquer outro nuvem.    
  
### <a name="clean-up-share-snapshots"></a>Limpar a partilha de instantâneos
* <a id="delete-share-keep-snapshots"></a>**Pode eliminar a minha partilha mas não eliminar instantâneos meu partilha?**  
    Se tiver instantâneos de partilha de Active Directory na sua partilha, não é possível eliminar a partilha. Pode utilizar uma API para eliminar os instantâneos de partilha, juntamente com a partilha. Também pode eliminar os instantâneos de partilha e a partilha no portal do Azure.

* <a id="delete-share-with-snapshots"></a>**O que acontece a minha instantâneos de partilha de eliminar a minha conta de armazenamento?**  
    Se eliminar a conta de armazenamento, os instantâneos de partilha também são eliminados.

## <a name="billing-and-pricing"></a>Faturação e preços
* <a id="vm-file-share-network-traffic"></a>**O tráfego de rede entre uma VM do Azure e uma contagem de partilha de ficheiros do Azure como largura de banda externa que é cobrada na subscrição?**  
    Se a partilha de ficheiros e VM estiverem na mesma região do Azure, há sem encargos adicionais para o tráfego entre a partilha de ficheiros e a VM. Se a partilha de ficheiros e a VM estiverem em regiões diferentes, o tráfego entre eles são cobrado como largura de banda externa.

* <a id="share-snapshot-price"></a>**Quanto deve partilhar o custo de instantâneos?**  
     Durante a pré-visualização, há sem encargos para a capacidade de instantâneos de partilha. Aplicam os custos de saída e de transações de armazenamento Standard. Depois de disponibilidade geral, serão cobradas subscrições da capacidade e transações na partilha de instantâneos.
     
     Os instantâneos de partilha são incrementais natureza. O instantâneo de base de partilha é a partilha de si próprio. Todos os instantâneos de partilha subsequentes são incrementais e armazenam apenas a diferença do instantâneo partilha anterior. É-lhe faturado apenas para o conteúdo alterado. Se tiver uma partilha com GiB 100 de dados, mas apenas 5 GiB foi alterada desde o último instantâneo de partilha, o instantâneo de partilha consome apenas 5 GiB adicionais e é faturado por 105 GiB. Para obter mais informações sobre transações e os encargos associados à saída padrão, consulte o [página de preços](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Dimensionamento e desempenho
* <a id="files-scale-limits"></a>**Quais são os limites de escala de ficheiros do Azure?**  
    Para informações sobre metas de desempenho e escalabilidade para ficheiros do Azure, consulte [metas de desempenho e escalabilidade de ficheiros do Azure](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>**Preciso de uma partilha de ficheiros maior do que os ficheiros do Azure oferece atualmente. Pode aumentar o tamanho das minha partilha de ficheiros do Azure?**  
    Não. O tamanho máximo de uma partilha de ficheiros do Azure é 5 TiB. Atualmente, este é um limite de disco rígido não é possível ajustar. Estamos a trabalhar uma solução para aumentar o tamanho da partilha 100 TiB, mas não temos linhas cronológicas partilhar neste momento.

* <a id="open-handles-quota"></a>**Quantos clientes podem aceder em simultâneo ao mesmo ficheiro?**   
    Há uma quota de 2.000 alças abertas num único ficheiro. Quando tiver 2.000 identificadores abertos, é apresentada uma mensagem de erro que indica a que for atingida a quota.

* <a id="zip-slow-performance"></a>**O meu desempenho é lento quando posso deszipar ficheiros nos ficheiros do Azure. O que devo fazer?**  
    Para transferir um grande número de ficheiros para ficheiros do Azure, recomendamos que utilize AzCopy (para Windows; na pré-visualização para Linux e UNIX) ou do Azure PowerShell. Estas ferramentas foram otimizadas para transferência de rede.

* <a id="slow-perf-windows-81-2012r2"></a>**É por que motivo o meu desempenho lento depois posso montar os meus partilha de ficheiros do Azure no Windows Server 2012 R2 ou Windows 8.1?**  
    Se um problema conhecido montar uma partilha de ficheiros do Azure no Windows Server 2012 R2 e Windows 8.1. O problema foi corrigido na atualização cumulativa Abril de 2014 para Windows 8.1 e Windows Server 2012 R2. Para um desempenho ideal, certifique-se de que todas as instâncias do Windows Server 2012 R2 e Windows 8.1 têm este patch aplicada. (Deve sempre receberá patches do Windows através do Windows Update.) Para obter mais informações, consulte o artigo associado da Base de dados de Conhecimento Microsoft [desempenho lento ao aceder a ficheiros do Azure a partir do Windows 8.1 ou Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funcionalidades e interoperabilidade com outros serviços
* <a id="cluster-witness"></a>**Posso utilizar o meu partilha de ficheiros do Azure como um *testemunho de partilha de ficheiros* para os meus Cluster de ativação pós-falha do Windows Server?**  
    Atualmente, esta configuração não é suportada para uma partilha de ficheiros do Azure. Para obter mais informações sobre como definir esta opção para o Blob storage do Azure, consulte [implementar um testemunho de nuvem para um Cluster de ativação pós-falha](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>**Podem montar uma partilha de ficheiros do Azure numa instância de contentor do Azure?**  
    Sim, as partilhas de ficheiros do Azure são uma boa opção quando pretender manter informações para além da duração de uma instância de contentor. Para obter mais informações, consulte [montar uma partilha de ficheiros do Azure com instâncias de contentor do Azure](../../container-instances/container-instances-mounting-azure-files-volume.md).

* <a id="rest-rename"></a>**Existe uma operação de mudança de nome na REST API?**  
    Neste momento, não.

* <a id="nested-shares"></a>**Posso configurar partilhas aninhadas? Por outras palavras, uma partilha numa partilha?**  
    Não. A partilha de ficheiros *é* o controlador virtual que é possível montar, pelo que não são suportadas partilhas aninhadas.

* <a id="ibm-mq"></a>**Como utilizar o ficheiros do Azure com o IBM MQ?**  
    A IBM lançou um documento que ajuda os clientes do IBM MQ configurar ficheiros do Azure com o serviço do IBM. Para obter mais informações, consulte [como configurar um Gestor de filas de várias instâncias do IBM MQ com o serviço de ficheiros do Microsoft Azure](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Consultar também
* [Resolver problemas de ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Resolver problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Resolver problemas de sincronização de ficheiros do Azure (pré-visualização)](storage-sync-files-troubleshoot.md)
