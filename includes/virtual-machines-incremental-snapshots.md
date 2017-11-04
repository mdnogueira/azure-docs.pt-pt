# <a name="back-up-azure-unmanaged-vm-disks-with-incremental-snapshots"></a>Criar cópias de segurança do Azure discos VM não geridos com instantâneos incrementais
## <a name="overview"></a>Descrição geral
Armazenamento do Azure fornece a capacidade para criar instantâneos de blobs. Instantâneos capturam o estado do blob neste ponto no tempo. Neste artigo, vamos descrever um cenário em que pode manter cópias de segurança dos discos da máquina virtual através de instantâneos. Pode utilizar este metodologia quando optar por não utilizar o serviço de recuperação e cópia de segurança do Azure e pretender criar uma estratégia de cópia de segurança personalizada para os discos da máquina virtual.

Discos de máquina virtual do Azure são armazenados como blobs de páginas no armazenamento do Azure. Uma vez que vamos são que descrevem uma estratégia de cópia de segurança para os discos da máquina virtual neste artigo, vamos referir-se a instantâneos no contexto de blobs de páginas. Para obter mais informações sobre instantâneos, consulte [criar um instantâneo de um Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

## <a name="what-is-a-snapshot"></a>O que é um instantâneo?
Um instantâneo do blob é uma versão só de leitura de um blob que é capturada num ponto no tempo. Assim que tiver sido criado um instantâneo,-lo pode ser ler, copiar, ou eliminada, mas não modificado. Instantâneos proporcionam uma forma de fazer cópias de segurança de um blob como é apresentado um momento. Até REST versão 2015-04-05, era necessário a capacidade de copiar instantâneos completos. Com o resto versão 2015-07-08 e superior, também pode copiar instantâneos incrementais.

## <a name="full-snapshot-copy"></a>Cópia de instantâneo completo
Instantâneos podem ser copiados para outra conta de armazenamento como um blob para manter as cópias de segurança do base blob. Também pode copiar um instantâneo ao longo do respetivo blob base, o que é como restaurar o blob para uma versão anterior. Quando um instantâneo é copiado a partir de uma conta de armazenamento para outro, ocupar o mesmo espaço de como o blob de página base. Por conseguinte, copiar instantâneos todo a partir de uma conta de armazenamento para outro é lento e consome espaço na conta de armazenamento de destino.

> [!NOTE]
> Se copiar o blob base para outro destino, os instantâneos do blob não são copiados, juntamente com o mesmo. Da mesma forma, se substituir um blob base com uma cópia, os instantâneos associados com o blob base não são afetados e permanecem intactos sob o nome do base blob.
> 
> 

### <a name="back-up-disks-using-snapshots"></a>Criar cópias de segurança utilizando instantâneos de discos
Como uma estratégia de cópia de segurança para os discos da máquina virtual, que pode tomar periódicos instantâneos do blob de página ou disco e de cópia-los para outro armazenamento conta através de ferramentas, como [copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) operação ou [AzCopy](../articles/storage/common/storage-use-azcopy.md). Pode copiar um instantâneo para um blob de página de destino com um nome diferente. O blob de página de destino resultante é um blob de página gravável e não um instantâneo. Mais tarde neste artigo, vamos descreve os passos a efetuar cópias de segurança dos discos da máquina virtual através de instantâneos.

### <a name="restore-disks-using-snapshots"></a>Restaurar utilizando instantâneos de discos
Quando for altura para restaurar o disco para uma versão estável, que anteriormente foram capturada dos instantâneos de cópia de segurança, pode copiar um instantâneo sobre o blob de página base. Depois do instantâneo é promovido para a página base blob, mantém-se o instantâneo, mas a respetiva origem é substituído por uma cópia que pode ser de leitura e escrita. Neste artigo, iremos descrevem os passos para restaurar uma versão anterior do seu disco a partir do respetivo instantâneo.

### <a name="implementing-full-snapshot-copy"></a>Implementar a cópia de instantâneo completo
Pode implementar uma cópia completa de instantâneo efetuando o seguinte

* Em primeiro lugar, tire um instantâneo da utilização de base blob a [instantâneo Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) operação.
* Em seguida, copie o instantâneo para um armazenamento de destino conta utilizando [copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob).
* Repita este processo para manter cópias de segurança da sua blob base.

## <a name="incremental-snapshot-copy"></a>Cópia de instantâneo incremental
A nova funcionalidade do [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) API fornece uma forma muito melhor para efetuar cópias de segurança de instantâneos dos seus blobs de páginas ou discos. A API devolve a lista de alterações entre o blob base e os instantâneos, o que reduz a quantidade de espaço de armazenamento utilizado na conta de cópia de segurança. A API suporta blobs de páginas no armazenamento Premium, bem como o armazenamento Standard. Utilizar esta API, pode criar soluções de cópia de segurança mais rápidas e eficientes para VMs do Azure. Esta API estarão disponíveis na versão REST 07-2015-08 e planos superiores.

Cópia de instantâneo incremental permite-lhe copiar a partir de uma conta de armazenamento para outro a diferença entre,

* Base blob e o respetivo instantâneo ou um
* Todos os instantâneos do base blob dois

Especificar que as condições seguintes forem cumpridas,

* O blob foi criado no Jan-1-2016 ou posterior.
* O blob não foi substituído pela [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) ou [copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) entre duas instantâneos.

**Tenha em atenção**: esta funcionalidade está disponível para Blobs de página do Azure Standard e Premium.

Quando tiver uma estratégia de cópia de segurança personalizada através de instantâneos, copiar os instantâneos de uma conta de armazenamento para outro pode ser lento e pode consumir muito espaço de armazenamento. Em vez de copiar o instantâneo completo para uma conta de armazenamento de cópia de segurança, pode escrever a diferença entre instantâneos consecutivas para um blob de página de cópia de segurança. Desta forma, o tempo de cópia e o espaço para armazenar as cópias de segurança é reduzido.

### <a name="implementing-incremental-snapshot-copy"></a>Implementar a cópia de instantâneo Incremental
Pode implementar a cópia de instantâneo incremental efetuando o seguinte procedimento,

* Tirar um instantâneo da utilização de base blob [instantâneo Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob).
* Copiar o instantâneo para o através da conta de armazenamento de cópia de segurança do destino [copiar Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob). Este é o blob de página de cópia de segurança. Tirar um instantâneo do blob de página de cópia de segurança e armazene-o na conta de cópia de segurança.
* Crie outro instantâneo do blob base utilizando o Blob de instantâneo.
* Obter a diferença entre os instantâneos primeiro e segundo da utilização de base blob [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges). Utilizar o novo parâmetro **prevsnapshot**, para especificar o valor DateTime do instantâneo deseja obter a diferença horária. Quando este parâmetro estiver presente, a resposta REST inclui apenas as páginas que foram alteradas entre os instantâneos de destino e instantâneo anterior, incluindo páginas encriptadas.
* Utilize [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) para aplicar estas alterações para o blob de página de cópia de segurança.
* Por fim, tire um instantâneo do blob de página de cópia de segurança e armazene-o na conta de armazenamento de cópia de segurança.

Na secção seguinte, irá dita em mais detalhe como pode manter as cópias de segurança de discos utilizando a cópia de instantâneo Incremental

## <a name="scenario"></a>Cenário
Nesta secção, iremos descrevem um cenário que envolva uma estratégia de cópia de segurança personalizada para os discos da máquina virtual através de instantâneos.

Considere uma VM do Azure-série DS com o premium storage P30 disco ligado. O disco P30 chamado *mypremiumdisk* são armazenados numa conta de armazenamento premium chamada *mypremiumaccount*. Uma conta de armazenamento standard denominada *mybackupstdaccount* é utilizado para armazenar a cópia de segurança de *mypremiumdisk*. Gostaríamos de manter um instantâneo de *mypremiumdisk* cada 12 horas.

Para saber mais sobre a criação de conta de armazenamento e discos, consulte [contas do storage do Azure sobre](../articles/storage/storage-create-storage-account.md).

Para saber mais sobre a cópia de segurança de VMs do Azure, consulte [cópias de segurança de VM do Azure planear](../articles/backup/backup-azure-vms-introduction.md).

## <a name="steps-to-maintain-backups-of-a-disk-using-incremental-snapshots"></a>Passos para manter as cópias de segurança de um disco através de instantâneos de incrementais
Os passos seguintes descrevem como criar instantâneos de *mypremiumdisk* e manter as cópias de segurança no *mybackupstdaccount*. A cópia de segurança é um blob de página padrão chamado *mybackupstdpageblob*. O blob de página de cópia de segurança sempre reflete o estado do mesmo como o último instantâneo do *mypremiumdisk*.

1. Criar o blob de página de cópia de segurança para o disco de armazenamento premium, ao criar um instantâneo dos *mypremiumdisk* chamado *mypremiumdisk_ss1*.
2. Copie este instantâneo para mybackupstdaccount como um blob de página chamado *mybackupstdpageblob*.
3. Tirar um instantâneo *mybackupstdpageblob* chamado *mybackupstdpageblob_ss1*com [instantâneo Blob](https://docs.microsoft.com/rest/api/storageservices/Snapshot-Blob) e armazená-la no *mybackupstdaccount*.
4. Durante a janela de cópia de segurança, crie outro instantâneo de *mypremiumdisk*, diga *mypremiumdisk_ss2*e armazená-la no *mypremiumaccount*.
5. Obter as alterações incrementais entre os dois instantâneos, *mypremiumdisk_ss2* e *mypremiumdisk_ss1*com [GetPageRanges](https://docs.microsoft.com/rest/api/storageservices/Get-Page-Ranges) no *mypremiumdisk_ ss2* com o **prevsnapshot** parâmetro definido para o carimbo de *mypremiumdisk_ss1*. Estas alterações incrementais de escrita para o blob de página de cópia de segurança *mybackupstdpageblob* no *mybackupstdaccount*. Se existirem intervalos eliminados as alterações incrementais, tem de ser limpo do blob de página de cópia de segurança. Utilize [PutPage](https://docs.microsoft.com/rest/api/storageservices/Put-Page) ao escrever alterações incrementais para o blob de página de cópia de segurança.
6. Tirar um instantâneo do blob de página de cópia de segurança *mybackupstdpageblob*, chamado *mybackupstdpageblob_ss2*. Elimine o instantâneo anterior *mypremiumdisk_ss1* da conta de armazenamento premium.
7. Repita os passos 4 a 6 cada janela de cópia de segurança. Desta forma, pode manter as cópias de segurança de *mypremiumdisk* numa conta do standard storage.

![Cópia de segurança disco utilizando instantâneos de incrementais](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-1.png)

## <a name="steps-to-restore-a-disk-from-snapshots"></a>Passos para restaurar um disco de instantâneos
Os seguintes passos descrevem como restaurar o disco premium, *mypremiumdisk* para um instantâneo anterior da conta de armazenamento de cópia de segurança *mybackupstdaccount*.

1. Identifica o ponto no tempo que pretende restaurar o disco premium. Digamos que é instantâneo *mybackupstdpageblob_ss2*, que é armazenado na conta de armazenamento de cópia de segurança *mybackupstdaccount*.
2. No mybackupstdaccount, promover o instantâneo *mybackupstdpageblob_ss2* como o novo blob de página base cópia de segurança *mybackupstdpageblobrestored*.
3. Tirar um instantâneo este BLOBs de páginas de cópia de segurança restaurada, denominado *mybackupstdpageblobrestored_ss1*.
4. Copiar o blob de página restaurada *mybackupstdpageblobrestored* de *mybackupstdaccount* para *mypremiumaccount* como o novo disco premium *mypremiumdiskrestored*.
5. Tirar um instantâneo *mypremiumdiskrestored*, chamado *mypremiumdiskrestored_ss1* para efetuar cópias de segurança incrementais futuras.
6. Ponto da série DS VM ao disco restaurado *mypremiumdiskrestored* e desanexar o antigo *mypremiumdisk* da VM.
7. Iniciar o processo de cópia de segurança descrito na secção anterior para o disco restaurada *mypremiumdiskrestored*, utilizando o *mybackupstdpageblobrestored* como o blob de página de cópia de segurança.

![Restaurar o disco de instantâneos](../articles/virtual-machines/windows/media/incremental-snapshots/storage-incremental-snapshots-2.png)

## <a name="next-steps"></a>Passos Seguintes
Utilize as seguintes ligações para mais informações sobre a criação de instantâneos de um blob e planeamento da sua infraestrutura de cópia de segurança de VM.

* [Criar um instantâneo de um Blob](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
* [Planear a infraestrutura de cópia de segurança de VM](../articles/backup/backup-azure-vms-introduction.md)

