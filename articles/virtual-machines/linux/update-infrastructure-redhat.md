---
title: "Infraestrutura de atualização do Red Hat | Microsoft Docs"
description: "Saiba mais sobre a infraestrutura de atualização do Red Hat para instâncias do Red Hat Enterprise Linux a pedido no Microsoft Azure"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/10/2017
ms.author: borisb
ms.openlocfilehash: ea9a5cbd9b9b7b67ceb131cb8ba1d2476dbd5f72
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização do Red Hat para a pedido Red Hat Enterprise Linux VMs no Azure
 [Infraestrutura de atualização do Red Hat](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) permite aos fornecedores de nuvem, como o Azure, para espelhar conteúdo do repositório alojadas Red Hat, criar conteúdo repositórios personalizados com específicos do Azure e disponibilizá-lo para as VMs do utilizador final.

Imagens de pay as you go (PAYG) do Red Hat Enterprise Linux (RHEL) vêm pré-configurados para aceder ao Azure RHUI. É necessária qualquer configuração adicional. Para obter as atualizações mais recentes, execute `sudo yum update` depois da instância do RHEL está pronta. Este serviço é incluído como parte das taxas de software RHEL PAYG.

## <a name="important-information-about-azure-rhui"></a>Informações importantes sobre RHUI do Azure
* Azure RHUI atualmente suporta apenas a versão secundária mais recente em cada família RHEL (RHEL6 ou RHEL7). Para atualizar uma instância de RHEL VM à RHUI para a versão secundária mais recente, execute `sudo yum update`.

    Por exemplo, se aprovisionar uma VM a partir de uma imagem de RHEL 7.2 PAYG e executar `sudo yum update`, acaba por ficar com uma VM de 7.4 RHEL (a versão mais recente menores na família RHEL7).

    Para evitar este comportamento, terá de criar a sua própria imagem conforme descrito no [criar e carregar uma máquina de virtual baseada no Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) artigo. Em seguida, é necessário estabelecer a ligação para uma infraestrutura de atualizações diferente ([diretamente para Red Hat conteúdo de entrega servidores](https://access.redhat.com/solutions/253273) ou um [servidor Red Hat satélite](https://access.redhat.com/products/red-hat-satellite)).

* Acesso a RHUI alojado no Azure está incluído no preço de imagem RHEL PAYG. Se a anular o registo de uma VM RHEL PAYG RHUI alojado no Azure que não converter a máquina virtual para um tipo de bring-your-proprietário-licença (BYOL) da VM. Se registar a mesma VM com outra origem das atualizações, pode implicar _indireta_ duplo encargos. Está a debitado pela primeira vez, a taxa de software RHEL do Azure. Está a cobrada na segunda vez para subscrições do Red Hat que foram anteriormente adquiridas. Se pretender utilizar uma infraestrutura de atualização que não seja alojado no Azure RHUI consistentemente, considere criar e implementar as suas próprias imagens (BYOL-type). Este processo é descrito no [criar e carregar uma máquina de virtual baseada no Red Hat para o Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Duas classes de imagens RHEL PAYG no Azure (RHEL para SAP HANA) e RHEL para aplicações empresariais de SAP estão ligadas ao canais RHUI dedicados que deverá permanecer disponível na versão secundária de RHEL específica conforme necessário para certificação SAP. 

* Acesso a RHUI alojado no Azure está limitado a VMs dentro de [intervalos IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Se a funcionalidade de proxy de todo o tráfego de VM através de uma infraestrutura de rede no local, poderá ter de configurar rotas definidas pelo utilizador para as VMs de PAYG RHEL aceder a RHUI do Azure.

### <a name="the-ips-for-the-rhui-content-delivery-servers"></a>Os IPs para os servidores de entrega de conteúdos RHUI

RHUI está disponível em todas as regiões onde as imagens do RHEL a pedido estão disponíveis. Atualmente inclui todas as regiões públicas listadas no [dashboard de estado do Azure](https://azure.microsoft.com/status/) página, Azure US Government e regiões de Datacenters do Microsoft Azure. 

Se estiver a utilizar uma configuração de rede para restringir mais o acesso de RHEL PAYG VMs, certifique-se os seguintes IPs são permitidos para `yum update` funcione, consoante o ambiente está em: 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="rhui-azure-infrastructure-update"></a>Atualização de infraestrutura do RHUI Azure

Setembro de 2016, foi implementada uma RHUI atualizado do Azure. Abril de 2017, iremos encerrar o antigo RHUI do Azure. Se tiver utilizado as imagens RHEL PAYG (ou os respetivos instantâneos) a partir de Setembro de 2016 ou posterior, está a ligar automaticamente para o novo RHUI do Azure. Se, no entanto, que possui instantâneos anteriores nas suas VMs, terá de atualizar manualmente a configuração para aceder a RHUI de Azure conforme descrito na secção seguinte.

Os novos servidores do Azure RHUI são implementados com [Traffic Manager do Azure](https://azure.microsoft.com/services/traffic-manager/). No Gestor de tráfego, um único ponto final (rhui 1.microsoft.com) pode ser utilizado por qualquer VM, independentemente da região. 

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Resolver problemas de ligação ao Azure RHUI
Se tiver problemas a ligar ao Azure RHUI da sua VM do Azure RHEL PAYG, siga estes passos:

1. Verifique a configuração de VM para o ponto final do Azure RHUI:

    a. Verifique se o `/etc/yum.repos.d/rh-cloud.repo` ficheiro contém uma referência a `rhui-[1-3].microsoft.com` no `baseurl` do `[rhui-microsoft-azure-rhel*]` secção do ficheiro. Se existir, está a utilizar o novo RHUI do Azure.

    b. Se aponta para uma localização com o seguinte padrão, `mirrorlist.*cds[1-4].cloudapp.net`, é necessária uma atualização da configuração. Está a utilizar o instantâneo VM antigo e, terá de atualizá-lo para apontar para o novo RHUI do Azure.

2. Acesso a RHUI alojado no Azure está limitado a VMs dentro de [intervalos de IP do datacenter do Azure] (https://www.microsoft.com/download/details.aspx?id=41653).
 
3. Se estiver a utilizar a nova configuração, tem de verificar que a VM liga-se de que o intervalo de IP do Azure e ainda não é possível ligar ao Azure RHUI, ficheiro um incidente de suporte com a Microsoft ou Red Hat.

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Procedimento de atualização manual para utilizar os servidores de RHUI do Azure
Este procedimento é fornecido para referência apenas. Imagens de RHEL PAYG já tem a configuração correta para ligar ao Azure RHUI. Para atualizar manualmente a configuração para utilizar os servidores Azure RHUI, execute os seguintes passos:

1. Transferir a assinatura de chave pública via curl.

   ```bash
   curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
   ```

2. Verificar a validade da chave transferido.

   ```bash
   gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
   ```

3. Verifique a saída e, em seguida, certifique-se do `keyid` e `user ID packet`.

   ```bash
   Version: GnuPG v1.4.7 (GNU/Linux)
   :public key packet:
           version 4, algo 1, created 1446074508, expires 0
           pkey[0]: [2048 bits]
           pkey[1]: [17 bits]
           keyid: EB3E94ADBE1229CF
   :user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
   :signature packet: algo 1, keyid EB3E94ADBE1229CF
           version 4, created 1446074508, md5len 0, sigclass 0x13
           digest algo 2, begin of digest 1a 9b
           hashed subpkt 2 len 4 (sig created 2015-10-28)
           hashed subpkt 27 len 1 (key flags: 03)
           hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
           hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
           hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
           hashed subpkt 30 len 1 (features: 01)
           hashed subpkt 23 len 1 (key server preferences: 80)
           subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
           data: [2047 bits]
   ```

4. Instale a chave pública.

   ```bash
   sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
   sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
   ```

5. Transferir, certifique-se e instalar um cliente Gestor de pacote RPM (RPM).
    
    >[!NOTE]
    >Alterar a versões do pacote. Se ligar manualmente RHUI do Azure, pode encontrar a versão mais recente do pacote de cliente para cada família RHEL ao aprovisionamento a imagem mais recente da galeria do.
  
   a. Transferir. 
   
    - Para RHEL 6:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.1-32.noarch.rpm 
        ```
    
    - Para RHEL 7:
        ```bash
        curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.1-19.noarch.rpm  
        ```

   b. Certifique-se.

   ```bash
   rpm -Kv azureclient.rpm
   ```

   c. Verifique a saída para se certificar de que a assinatura do pacote é OK.

   ```bash
   azureclient.rpm:
       Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
       Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
       V3 RSA/SHA256 Signature, key ID be1229cf: OK
       MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
   ```

   d. Instale o RPM.

    ```bash
    sudo rpm -U azureclient.rpm
    ```

6. Depois de concluir, certifique-se de que pode aceder ao Azure RHUI da VM.

## <a name="next-steps"></a>Passos seguintes
Para criar uma VM com Linux do Red Hat Enterprise a partir de uma imagem do Azure Marketplace PAYG e utilizar RHUI alojado no Azure, vá para o [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). 