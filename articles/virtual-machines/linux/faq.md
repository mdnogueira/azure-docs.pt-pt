---
title: Perguntas mais frequentes sobre as VMs do Linux no Azure | Microsoft Docs
description: Fornece respostas a algumas das perguntas comuns sobre computadores virtuais Linux criados com o modelo do Resource Manager.
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: f7fb3f24e9ca6b1827028d118cf833aad830e6a1
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Pergunta frequentes sobre as máquinas virtuais Linux
Este artigo aborda algumas perguntas comuns sobre computadores virtuais Linux criados no Azure utilizando o modelo de implementação Resource Manager. Para a versão do Windows deste tópico, consulte [perguntas mais frequentes pergunta sobre máquinas virtuais do Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>O que posso executar numa VM do Azure?
Todos os subscritores podem executar software de servidor numa máquina virtual do Azure. Para obter mais informações, consulte [Linux em Azure-Endorsed distribuições](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Quanto armazenamento posso utilizar com uma máquina virtual?
Cada disco de dados pode ser até 4 TB (4,095 GB). O número de discos de dados que pode utilizar depende do tamanho da máquina virtual. Para obter mais detalhes, veja [Tamanhos das Virtual Machines](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Discos gerida do Azure são as disco recomendado armazenamento ofertas para utilização com máquinas virtuais do Azure para armazenamento persistente de dados. Pode utilizar vários Managed Disks com cada Máquina Virtual. Os Managed Disks oferecem dois tipos de opções de armazenamento duráveis: Managed Disks Premium e Standard. Para obter informações sobre preços, consulte [geridos discos preços](https://azure.microsoft.com/pricing/details/managed-disks).

As contas do storage do Azure também podem fornecer armazenamento para o disco de sistema operativo e qualquer discos de dados. Cada disco é um ficheiro .vhd armazenado como um blob de páginas. Para detalhes de preços, veja [Detalhes de Preço do Armazenamento](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Como aceder a minha máquina virtual?
Estabelece uma ligação remota para iniciar sessão na máquina virtual, utilizando Secure Shell (SSH). Consulte as instruções sobre como ligar [a partir do Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [no Linux e Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Por predefinição, o SSH permite um máximo de 10 ligações simultâneas. Pode aumentar este número, editando o ficheiro de configuração.

Se tiver problemas, consulte o [ligações de resolução de problemas de Secure Shell (SSH)](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Pode utilizar o disco temporário (dev/sdb1) para armazenar dados?
Não utilize o disco temporário (dev/sdb1) para armazenar dados. Destina-se apenas existir armazenamento temporário. O risco de perda de dados que não podem ser recuperados.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Pode copiar ou clonar uma VM do Azure existente?
Sim. Para obter instruções, consulte [como criar uma cópia de uma máquina virtual Linux no modelo de implementação Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Por que razão estou não ver Canadá Central e regiões Canadá Leste através do Azure Resource Manager?
As novo duas regiões do Canadá Central e Canadá Leste não estão registadas automaticamente para a criação da máquina virtual para as subscrições do Azure existentes. Este registo é feito automaticamente quando uma máquina virtual é implementada através do portal do Azure para qualquer outra região com o Azure Resource Manager. Depois de uma máquina virtual for implementada para qualquer outra região do Azure, regiões do novo devem estar disponíveis para máquinas de virtuais subsequentes.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Posso adicionar uma NIC à minha VM depois de criado?
Sim, isto é agora possível. A VM primeiro tem de ser parado desalocada. Em seguida, pode adicionar ou remover um NIC (exceto se for o último NIC na VM). 

## <a name="are-there-any-computer-name-requirements"></a>Existem quaisquer requisitos de nome de computador?
Sim. O nome do computador pode ter um máximo de 64 carateres de comprimento. Consulte [restrições e regras de convenções de nomenclatura](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações em torno os recursos de atribuição de nomes.

## <a name="are-there-any-resource-group-name-requirements"></a>Existem qualquer recurso requisitos de nome de grupo?
Sim. O nome do grupo de recursos pode ter um máximo de 90 carateres de comprimento. Consulte [restrições e regras de convenções de nomenclatura](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para obter mais informações sobre grupos de recursos.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Quais são os requisitos de nome de utilizador ao criar uma VM?
Nomes de utilizador tem de ser 1-64 carateres de comprimento.

Não são permitidos nomes de utilizador seguintes:

<table>
    <tr>
        <td style="text-align:center">Administrador </td><td style="text-align:center"> Admin </td><td style="text-align:center"> utilizador </td><td style="text-align:center"> User1</td>
    </tr>
    <tr>
        <td style="text-align:center">Teste </td><td style="text-align:center"> Utilizador2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> user3</td>
    </tr>
    <tr>
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
As palavras-passe tem de ser 6 72 carateres de comprimento e cumprir 3 fora os seguintes requisitos de 4 complexidade:

* Ter carateres inferiores
* Ter carateres superiores
* Ter um dígito
* Tem um caráter especial (Regex corresponder [\W_])

As palavras-passe seguintes não são permitidas:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Word do Pa$ $</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Palavra-passe!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
