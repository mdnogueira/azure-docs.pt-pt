---
title: Criar uma VM com Linux através do Portal do Azure | Microsoft Docs
description: Crie uma VM com Linux através do Portal do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: v-livech

---
# Criar uma VM com Linux no Azure através do Portal
Este artigo mostra como utilizar o [Portal do Azure](https://portal.azure.com/) para criar rapidamente uma Máquina Virtual Linux. Os únicos requisitos são [uma conta do Azure](https://azure.microsoft.com/pricing/free-trial/) e [ficheiros de chaves SSH privadas e públicas](virtual-machines-linux-mac-create-ssh-keys.md).

1. Após iniciar sessão no Portal do Azure com a sua identidade da conta do Azure, clique em **+ Novo** no canto superior esquerdo:
   
    ![ecrã1](../media/virtual-machines-linux-quick-create-portal/screen1.png)
2. Clique em **Virtual Machines** no **Marketplace** e, em seguida, em **Ubuntu Server 14.04 LTS** na lista de imagens **Aplicações em Destaque**.  Na parte inferior, certifique-se de que o modelo de implementação é `Resource Manager` e, em seguida, clique em **Criar**.
   
    ![ecrã2](../media/virtual-machines-linux-quick-create-portal/screen2.png)
3. Na página **Noções básicas**, introduza:
   
   * um nome para a VM
   * um nome de utilizador para o Utilizador de Admin
   * o Tipo de Autenticação definido como **Chave pública SSH**
   * a Chave pública SSH como uma cadeia (a partir do diretório `~/.ssh/`)
   * um nome do grupo de recursos ou selecione um existente
     
     e Clique em **OK** para continuar e escolha o tamanho da VM; deve ser algo semelhante ao seguinte:
     
     ![ecrã3](../media/virtual-machines-linux-quick-create-portal/screen3.png)
4. Escolha o tamanho **DS1**, que instala o Ubuntu num SSD Premium, e clique em **Selecionar** para configurar as definições.
   
    ![ecrã4](../media/virtual-machines-linux-quick-create-portal/screen4.png)
5. Em **Definições**, deixe as predefinições para os valores de Armazenamento e de Rede e clique em **OK** para ver o resumo.  Tenha em atenção que o tipo de disco foi definido como SSD Premium ao escolher DS1, o **S** significa SSD.
   
    ![ecrã5](../media/virtual-machines-linux-quick-create-portal/screen5.png)
6. Confirme as definições para a nova VM com Ubuntu e clique em **OK**.
   
    ![ecrã6](../media/virtual-machines-linux-quick-create-portal/screen6.png)
7. Abra o Dashboard do Portal e, em **Interfaces de rede** escolha o seu NIC
   
    ![ecrã7](../media/virtual-machines-linux-quick-create-portal/screen7.png)
8. Abrir o menu de Endereços IP públicos nas definições da NIC
   
    ![ecrã8](../media/virtual-machines-linux-quick-create-portal/screen8.png)
9. SSH para o IP público utilizando a chave pública SSH

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## Passos Seguintes
Acabou de criar uma VM com Linux rapidamente para fins de teste ou de demonstração. Para criar uma VM Linux personalizada de acordo com a sua infraestrutura, pode seguir qualquer um destes artigos.

* [Criar uma VM com Linux no Azure utilizando Modelos](virtual-machines-linux-cli-deploy-templates.md)
* [Criar uma VM Linux Protegida por SSH no Azure através de Modelos](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Criar uma VM com Linux através da CLI do Azure](virtual-machines-linux-create-cli-complete.md)

<!--HONumber=Sep16_HO3-->


