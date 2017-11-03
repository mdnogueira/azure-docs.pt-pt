---
title: FAQ sobre VMs do Windows no Azure | Microsoft Docs
description: "Fornece respostas a algumas das perguntas comuns sobre máquinas virtuais do Windows criadas com o modelo do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: d8f457569ef1e9dfe400266982596ab53ec4f10d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Pergunta frequentes sobre máquinas virtuais do Windows
Este artigo aborda algumas perguntas comuns sobre máquinas virtuais do Windows criadas no Azure utilizando o modelo de implementação Resource Manager. Para a versão do Linux deste tópico, consulte [perguntas mais frequentes pergunta sobre máquinas virtuais do Linux](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter informações sobre a política de suporte para o software de servidor Microsoft em execução no Azure, consulte [o suporte do software de servidor da Microsoft para máquinas virtuais do Azure](https://support.microsoft.com/kb/2721672)

Determinadas versões do Windows 7, Windows 8.1 e Windows 10 estão disponíveis para subscritores do MSDN Azure benefício e os subscritores MSDN de programação e teste pay as you go para tarefas de desenvolvimento e teste. Para obter mais detalhes, incluindo instruções e limitações, veja [Imagens do cliente Windows para subscritores MSDN](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ser até 4 TB (4,095 GB). O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Discos gerida do Azure são as disco recomendado armazenamento ofertas para utilização com máquinas virtuais do Azure para armazenamento persistente de dados. Pode utilizar vários Managed Disks com cada Máquina Virtual. Os Managed Disks oferecem dois tipos de opções de armazenamento duráveis: Managed Disks Premium e Standard. Para obter informações sobre preços, consulte [geridos discos preços](https://azure.microsoft.com/pricing/details/managed-disks).

As contas do storage do Azure também podem fornecer armazenamento para o disco de sistema operativo e qualquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como aceder a minha máquina virtual?
Estabelece uma ligação remota utilizando a ligação de ambiente de trabalho remoto (RDP) para uma VM do Windows. Para obter instruções, consulte [como ligar e iniciar sessão a uma máquina virtual do Azure com o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). São suportados um máximo de duas ligações simultâneas, a menos que o servidor está configurado como um anfitrião de sessões de serviços de ambiente de trabalho remoto.  

Se tiver problemas com o ambiente de trabalho remoto, consulte o artigo [ligações de resolução de problemas de ambiente de trabalho remoto para uma baseado no Windows Azure Máquina Virtual](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Se estiver familiarizado com o Hyper-V, poderá querer uma ferramenta semelhante ao VMConnect. O Azure não oferece uma ferramenta semelhante porque o acesso à consola para uma máquina virtual não é suportado.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Pode utilizar o disco temporário (unidade de d: por predefinição) para armazenar dados?
Não utilize o disco temporário para armazenar dados. Só é armazenamento temporário, pelo que seria o risco de perda de dados que não não possível recuperar. Pode ocorrer a perda de dados quando a máquina virtual é movida para outro anfitrião. O redimensionamento de uma máquina virtual, a atualização do anfitrião ou uma falha de hardware no anfitrião são alguns dos motivos pelos quais uma máquina virtual pode ser movida.

Se tiver uma aplicação que necessita de utilizar a letra de unidade d:, pode reatribuir letras de unidade para que o disco temporário utiliza algo diferente de d:. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Como posso alterar a letra de unidade do disco temporário?
Pode alterar a letra de unidade ao mover o ficheiro de paginação e reatribuição letras de unidade, mas tem de certificar-se de que efetua os passos por uma ordem específica. Para obter instruções, veja [Alterar a letra de unidade do disco temporário do Windows](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Pode adicionar uma VM existente para um conjunto de disponibilidade?
Não. Se pretender que a VM fazer parte de um conjunto de disponibilidade, terá de criar a VM no conjunto de. Atualmente não é uma forma para adicionar uma VM a um conjunto depois de ter sido criado de disponibilidade.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Pode carregar uma máquina virtual para o Azure?
Sim. Para obter instruções, consulte [migração no local as VMs do Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Posso redimensionar o disco do SO?
Sim. Para obter instruções, consulte [como expandir o disco de SO de uma Máquina Virtual num grupo de recursos do Azure](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Pode copiar ou clonar uma VM do Azure existente?
Sim. Utilizar imagens geridas, pode criar uma imagem de uma máquina virtual e, em seguida, utilizar a imagem para criar várias VMs novas. Para obter instruções, consulte [criar uma imagem personalizada de uma VM](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que razão estou não ver Canadá Central e regiões Canadá Leste através do Azure Resource Manager?

As novo duas regiões do Canadá Central e Canadá Leste não estão registadas automaticamente para a criação da máquina virtual para as subscrições do Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implementada através do portal do Azure para qualquer outra região com o Azure Resource Manager. Depois de uma máquina virtual for implementada para qualquer outra região do Azure, regiões do novo devem estar disponíveis para máquinas de virtuais subsequentes.

## <a name="does-azure-support-linux-vms"></a>Azure suporta VMs com Linux?
Sim. Para criar rapidamente uma VM com Linux para experimentar, consulte [criar uma VM com Linux no Azure através do Portal](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma NIC à minha VM depois de criado?
Sim, isto é agora possível. A VM primeiro tem de ser parado desalocada. Em seguida, pode adicionar ou remover um NIC (exceto se for o último NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome de computador?
Sim. O nome do computador pode ter um máximo de 15 carateres de comprimento. Consulte [restrições e regras de convenções de nomenclatura](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter mais informações em torno os recursos de atribuição de nomes.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem qualquer recurso requisitos de nome de grupo?
Sim. O nome do grupo de recursos pode ter um máximo de 90 carateres de comprimento. Consulte [restrições e regras de convenções de nomenclatura](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de utilizador ao criar uma VM?

Nomes de utilizador pode ter um máximo de 20 carateres de comprimento e não pode terminar com um período ("."). 


Não são permitidos nomes de utilizador seguintes:
<table>
    <tr>
        <td style="text-align:center">Administrador </td><td style="text-align:center"> Admin </td><td style="text-align:center"> utilizador </td><td style="text-align:center"> User1</td>
    </tr>
    <tr>
        <td style="text-align:center">Teste </td><td style="text-align:center"> Utilizador2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> A</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">cópia de segurança </td><td style="text-align:center"> Consola do </td><td style="text-align:center"> David </td><td style="text-align:center"> convidado</td>
    </tr>
    <tr>
        <td style="text-align:center">João </td><td style="text-align:center"> Proprietário </td><td style="text-align:center"> raiz </td><td style="text-align:center"> servidor</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL Server </td><td style="text-align:center"> Suporte </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> SYS</td>
    </tr>
    <tr>
        <td style="text-align:center">TEST2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> user4 </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Quais são os requisitos de palavra-passe quando criar uma VM?
Palavras-passe tem de ter 12-123 carateres de comprimento e cumprir 3 fora os seguintes requisitos de 4 complexidade:

* Ter carateres inferiores
* Ter carateres superiores
* Ter um dígito
* Tem um caráter especial (Regex corresponder [\W_])

As palavras-passe seguintes não são permitidas:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Word do Pa$ $ </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Palavra-passe! </td>
        <td>Password1 </td>
        <td>Password22 </td>
        <td>ILOVEYOU! </td>
    </tr>
</table>
