---
title: "Ligação de VM do SQL Server para a Azure Search | Microsoft Docs"
description: "Ativar ligações encriptadas e configurar a firewall para permitir ligações ao SQL Server numa máquina virtual do Azure (VM) a partir de um indexador na Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: pablocas
editor: 
ms.assetid: 46e42e0e-c8de-4fec-b11a-ed132db7e7bc
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/23/2017
ms.author: heidist
ms.openlocfilehash: bb61330ba5511955e0da16dcd5b8b19529d0e44b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma ligação de um indexador de Azure Search para SQL Server numa VM do Azure
Conforme indicado no [ligar Azure base de dados SQL para a Azure Search utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq), criação de indexadores contra **do SQL Server em VMs do Azure** (ou **VMs do SQL do Azure** para abreviar) é suportada através da Azure Search, mas existem alguns pré-requisitos relacionadas com segurança ao asseguramos primeiro. 

**Duração de tarefa:** cerca de 30 minutos, assumindo que já instalado um certificado na VM.

## <a name="enable-encrypted-connections"></a>Ativar ligações encriptadas
A pesquisa do Azure necessita de um canal encriptado para todos os pedidos de indexador através de uma ligação à internet pública. Esta secção lista os passos para isto funcionar.

1. Assinale as propriedades do certificado para verificar que o nome do requerente é o nome de domínio completamente qualificado (FQDN) da VM do Azure. Pode utilizar uma ferramenta como CertUtils ou o snap-in de certificados para ver as propriedades. Pode obter o FQDN da secção de Essentials o painel de serviço VM, além de **etiqueta de nome de endereço de IP público/DNS** campo, no [portal do Azure](https://portal.azure.com/).
   
   * Para VMs criadas utilizando o mais recente **Resource Manager** modelo, o FQDN é formatado como `<your-VM-name>.<region>.cloudapp.azure.com`. 
   * Para as VMs mais antigas, criadas como um **clássico** VM, o FQDN é formatado como `<your-cloud-service-name.cloudapp.net>`. 
2. Configure o SQL Server para utilizar o certificado utilizando o Editor de registo (regedit). 
   
    Apesar do Gestor de configuração do SQL Server é frequentemente utilizado para esta tarefa, não é possível utilizá-lo para este cenário. -Não localizar o certificado importado porque o FQDN da VM no Azure não corresponde ao FQDN conforme determinado pela VM (que identifica o domínio como o computador local ou o domínio de rede ao qual está associado). Quando os nomes não corresponderem, utilize o regedit para especificar o certificado.
   
   * No regedit, navegue para esta chave de registo: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
     O `[MSSQL13.MSSQLSERVER]` parte varia com base no nome de versão e instância. 
   * Defina o valor da **certificado** chave para o **thumbprint** do certificado SSL que importou para a VM.
     
     Existem várias formas de obter o thumbprint, algumas melhor do que outras pessoas. Se copiar-à partir de **certificados** snap-in MMC,, provavelmente, selecionará um caráter à esquerda invisível [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta num erro quando tenta uma ligação . Existem várias soluções para corrigir este problema. O mais fácil é RETROCESSO ativação pós-falha e, em seguida, volte a escrever o primeiro caráter do thumbprint para remover o caráter à esquerda no campo valor de chave regedit. Em alternativa, pode utilizar uma ferramenta diferentes para copiar o thumbprint.
3. Conceder permissões para a conta de serviço. 
   
    Certifique-se que a conta de serviço do SQL Server é concedida permissões adequadas na chave privada do certificado SSL. Se overlook neste passo, não irá iniciar o SQL Server. Pode utilizar o **certificados** snap-in ou **CertUtils** para esta tarefa.
4. Reinicie o serviço do SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conectividade do SQL Server na VM
Depois de configurar a ligação encriptada necessária para a Azure Search, existem passos de configuração adicionais intrínseco ao SQL Server em VMs do Azure. Se não tiver o feito, o passo seguinte é concluir a configuração utilizando qualquer um dos seguintes artigos:

* Para um **Resource Manager** VM, consulte [ligar a um SQL Server Máquina Virtual no Azure com o Resource Manager](../virtual-machines/windows/sql/virtual-machines-windows-sql-connect.md). 
* Para um **clássico** VM, consulte [ligar a um SQL Server Máquina Virtual no Azure clássico](../virtual-machines/windows/classic/sql-connect.md).

Em particular, consulte a secção cada artigo para "ligar através da internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurar o grupo de segurança de rede (NSG)
Não é bastante invulgar configurar o NSG e ponto final do Azure correspondente ou lista de controlo de acesso (ACL) para que a VM do Azure acessível a outras entidades. Possibilidades são que tiver terminado antes de permitir a sua própria lógica de aplicação para ligar à VM do Azure SQL. É igual para uma ligação de pesquisa do Azure para a VM do SQL do Azure. 

As ligações abaixo fornecem instruções sobre a configuração do NSG para implementações de VM. Utilize estas instruções para a ACL de um ponto final de Azure SEarch com base no respetivo endereço IP.

> [!NOTE]
> Para em segundo plano, consulte [que é um grupo de segurança de rede?](../virtual-network/virtual-networks-nsg.md)
> 
> 

* Para um **Resource Manager** VM, consulte [como criar NSGs para implementações de ARM](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 
* Para um **clássico** VM, consulte [como criar NSGs para implementações clássicas](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Endereçamento IP pode apresentam alguns desafios que são facilmente ultrapassar se tem conhecimento do problema e possíveis soluções. As secções restantes fornecem recomendações para problemas relacionados com endereços IP na ACL de processamento.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restringir o acesso ao endereço IP do serviço de pesquisa
Recomendamos vivamente que restringir o acesso ao endereço IP do seu serviço de pesquisa na ACL em vez de efetuar as suas VMs do SQL do Azure ao nível aberta para todos os pedidos de ligação. Pode descobrir facilmente o endereço IP efetuando o FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

#### <a name="managing-ip-address-fluctuations"></a>Gerir flutuações de endereço IP
Se o serviço de pesquisa tem apenas uma unidade de pesquisa (ou seja, uma réplica e uma partição), o endereço IP será alterado durante a reinicialização de rotina do serviço, invalidar uma ACL existente com o endereço IP do seu serviço de pesquisa.

Uma forma de evitar o erro de conectividade subsequentes consiste em utilizar mais do que uma réplica e uma partição na Azure Search. Se o fizer, aumenta o custo, mas que também resolve o problema de endereço IP. Na Azure Search, endereços IP não alterar quando tem mais do que uma unidade de pesquisa.

Uma abordagem segundo é para permitir a ligação falhar e, em seguida, reconfigurar as ACLs no NSG. Em média, pode contar com endereços IP para alterar a cada algumas semanas. Para os clientes que efetue a indexação controlada de forma pouco frequente, esta abordagem poderá ser viável.

Uma abordagem de viável (mas não segura particularmente) terceira é especificar o intervalo de endereços IP da região do Azure onde o serviço de pesquisa está aprovisionado. A lista de intervalos de IP a partir da qual os endereços IP públicos são atribuídos para recursos do Azure está publicada no [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Inclui os endereços IP portais do Azure Search
Se estiver a utilizar o portal do Azure para criar um indexador, lógica portal do Azure Search também precisa de acesso à sua VM do Azure SQL durante a hora de criação. Endereços IP portais de pesquisa do Azure podem ser encontrados efetuando `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Passos seguintes
Com a configuração de forma, pode agora especificar um SQL Server numa VM do Azure como origem de dados para um indexador de Azure Search. Consulte [ligar Azure base de dados SQL para a Azure Search utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md) para obter mais informações.

