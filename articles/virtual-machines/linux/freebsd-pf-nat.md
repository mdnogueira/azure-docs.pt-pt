---
title: Utilizar o filtro de pacotes do FreeBSD para criar uma firewall no Azure | Microsoft Docs
description: "Saiba como implementar uma firewall NAT através do FreeBSD PF no Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: cd777291a1321eabf4efe0d7b9b101f932d9398b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Como utilizar o filtro de pacotes do FreeBSD para criar uma firewall segura no Azure
Este artigo apresenta como implementar uma firewall NAT através Packer filtro do FreeBSD através do modelo Azure Resource Manager para o cenário comum de servidor web.

## <a name="what-is-pf"></a>O que é PF?
PF (filtro de pacote, também escritos pf) é um filtro de pacotes de monitorização de estado licenciada BSD, uma parte do software para firewalling central. PF tem uma vez que evoluiu e deu lugar rapidamente e agora tem várias vantagens relativamente ao outras firewalls disponíveis. Tradução de endereços de rede (NAT) está a ser PF desde um dia, em seguida, o agendador de pacotes e gestão do Active Directory fila foram integradas no PF, ao integrar o ALTQ e ao fazer configurável através da configuração do PF. Funcionalidades como pfsync e CARP para ativação pós-falha e redundância, authpf para autenticação de sessão e do proxy de ftp para facilitar a firewalling do protocolo FTP difícil, também expandiu PF. Em suma, PF é uma firewall de elevado desempenho e a funcionalidade avançada. 

## <a name="get-started"></a>Introdução
Se estiver interessado em configurar uma firewall segura na nuvem para os servidores web, vamos começar. Também pode aplicar os scripts utilizados neste modelo Azure Resource Manager para configurar a topologia de rede.
O modelo Azure Resource Manager configurar uma máquina virtual FreeBSD que efetua /redirection NAT utilizando PF e duas máquinas de virtuais FreeBSD com o servidor de web Nginx instalado e configurado. Para além de efetuar NAT para o tráfego de saída do dois web servidores, a máquina virtual NAT/redirecionamento interceta pedidos de HTTP e redirecioná-los para os servidores web de duas em round robin. A VNet utiliza o privada não encaminháveis internos IP endereço espaço 10.0.0.2/24 e pode modificar os parâmetros do modelo. O modelo Azure Resource Manager também define uma tabela de rota da vnet todo, que é uma coleção de rotas individuais utilizada para substituir as rotas predefinidas do Azure com base no endereço IP de destino. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Implementar através da CLI do Azure
Tem a versão mais recente [Azure CLI 2.0](/cli/azure/install-az-cli2) instalado e registado para uma conta do Azure utilizando [início de sessão az](/cli/azure/#login). Crie um grupo de recursos com [az group create](/cli/azure/group#create). O exemplo seguinte cria um nome de grupo de recursos `myResourceGroup` no `West US` localização.

```azurecli
az group create --name myResourceGroup --location westus
```

Em seguida, implementar o modelo [pf-freebsd-configuração](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) com [criar a implementação do grupo az](/cli/azure/group/deployment#create). Transferir [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) sob o mesmo caminho e definir os seus próprios valores de recursos, tais como `adminPassword`, `networkPrefix`, e `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Depois de cerca de cinco minutos, irá obter as informações de `"provisioningState": "Succeeded"`. Em seguida, pode ssh para o front-end VM (NAT) ou acesso Nginx o servidor de web num browser utilizando o endereço IP público ou o FQDN da VM (NAT) de front-end. O exemplo seguinte lista o FQDN e endereço IP público que atribuída para a VM (NAT) de front-end no `myResourceGroup` grupo de recursos. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Passos seguintes
Pretende configurar a sua própria NAT no Azure? Abrir a origem, gratuitamente mas powerful? Em seguida, PF é uma boa opção. Utilizando o modelo [pf-freebsd-configuração](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), só precisa de cinco minutos configurar uma firewall NAT com utilizando FreeBSD de balanceamento de carga round robin da PF no Azure para o cenário comum de servidor web. 

Se pretender saber a oferta de FreeBSD no Azure, consulte [introdução ao FreeBSD no Azure](freebsd-intro-on-azure.md).

Se pretender saber mais sobre PF, consulte [FreeBSD handbook](https://www.freebsd.org/doc/handbook/firewalls-pf.html) ou [Guia do utilizador do-PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
