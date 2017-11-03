---
title: "Compreender os endereços IP partilhados no Azure DevTest Labs | Microsoft Docs"
description: "Saiba como o Azure DevTest Labs utiliza endereços IP partilhados para minimizar os endereços IP públicos necessários para aceder ao seu laboratório VMs."
services: devtest-lab
documentationcenter: na
author: camsoper
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2017
ms.author: casoper
ms.openlocfilehash: 9f6e1980bf5ea5b41da98a135d89f1c5159921a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Compreender os endereços IP partilhados no Azure DevTest Labs

Azure DevTest Labs permite partilhar o mesmo endereço IP público para minimizar o número de endereços IP públicos necessárias para aceder ao seu laboratório individuais VMs de VMs de laboratório.  Este artigo descreve como partilhado de IPs de trabalho e as opções de configuração relacionados.

## <a name="shared-ip-setting"></a>Partilhado a definição de IP

Quando criar um laboratório, reside numa sub-rede de uma rede virtual.  Por predefinição, esta sub-rede é criada com **ativar partilhado IP público** definido como *Sim*.  Esta configuração cria um endereço IP público para toda a sub-rede.  Para obter mais informações sobre como configurar redes virtuais e sub-redes, consulte [configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Nova sub-rede de laboratório](media/devtest-lab-shared-ip/lab-subnet.png)

Para laboratórios existentes, pode ativar esta opção, selecionando **políticas de configuração e > redes virtuais**. Em seguida, selecione uma rede virtual da lista e escolha **ATIVAR PARTILHADO IP público** para uma sub-rede selecionada. Também pode desativar esta opção em qualquer laboratório se não pretender partilhar um endereço IP público em laboratório VMs.

Quaisquer VMs criadas nesta predefinição de laboratório para utilizar um IP partilhado.  Ao criar a VM, esta definição pode ser observada no **definições avançadas** painel em **configuração do endereço IP**.

![Nova VM](media/devtest-lab-shared-ip/new-vm.png)

- **Partilhado:** todas as VMs criadas como **partilhados** são colocados de um grupo de recursos (RG). Um único endereço IP é atribuído para que o RG e todas as VMs no RG irão utilizar esse endereço IP.
- **Público:** cada VM que cria tem o seu próprio endereço IP e é criado no seu próprio grupo de recursos.
- **Privados:** cada VM que criar utiliza um endereço IP privado. Não será possível estabelecer ligação com esta VM diretamente a partir da internet com o ambiente de trabalho remoto.

Sempre que uma VM com o IP partilhado ativada é adicionada à sub-rede, DevTest Labs automaticamente adiciona a VM a um balanceador de carga e atribui um número de porta TCP no endereço IP público, reencaminhamento à porta RDP na VM.  

## <a name="using-the-shared-ip"></a>Utilizar o IP partilhado

- **Os utilizadores do Linux:** SSH para a VM utilizando o endereço IP ou nome de domínio completamente qualificado, seguido por um vírgula, seguido de porta. Por exemplo, na imagem abaixo, o endereço do RDP para ligar à VM é `doclab-lab13998814308000.centralus.cloudapp.azure.com:51686`.

  ![Exemplo VM](media/devtest-lab-shared-ip/vm-info.png)

- **Utilizadores do Windows:** selecionar o **Connect** botão no portal do Azure para transferir um ficheiro RDP previamente configurado e aceda à VM.

## <a name="next-steps"></a>Passos seguintes

* [Definir políticas de laboratório no Azure DevTest Labs](devtest-lab-set-lab-policy.md)
* [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md)





