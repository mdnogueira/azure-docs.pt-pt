---
title: Gerir os custos de forma eficaz para o SQL Server em virtual machines do Azure | Microsoft Docs
description: "Fornece as melhores práticas para escolher o direita máquina do SQL Server virtual modelo de preços."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/17/2017
ms.author: jroth
ms.openlocfilehash: 391f30e0c81aeaf313d58f1f4af877b5be9ed919
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Preço orientação para as VMs do SQL do Azure

Este artigo fornece orientação preço por máquinas virtuais do SQL Server no Azure. Existem várias opções que afetam o custo e é importante escolher a imagem correta equilibrar os custos com os requisitos empresariais.

## <a name="free-licensed-sql-server-editions"></a>Liberte-edições licenciadas de SQL Server

Se pretender que a desenvolver, testar ou criar uma prova de conceito, em seguida, utilize o livre licenciadas **edição do SQL Server Developer**. Nesta edição tem tudo na edição Enterprise do SQL Server, deste modo, pode utilizá-la para criar qualquer aplicação que pretende. -Apenas não deu permissão para ser executada na produção. Uma VM do SQL Server para programadores apenas custos o custo da VM, não para licenciamento do SQL Server.

Se pretender executar uma carga de trabalho simples em produção (< 4 núcleos, < 1 GB de memória, < 10 GB/base de dados), em seguida, utilize o livre licenciadas **SQL Server Express edition**. Uma VM do SQL Server Express apenas cobra o custo da VM, não licenciamento do SQL Server.

Para estas desenvolvimento/teste ou cargas de trabalho de produção simples, também pode poupar dinheiro ao escolher um tamanho mais pequeno da VM que corresponde a estas cargas de trabalho. O DS1v2 poderá ser uma boa opção para estas cargas de trabalho.

Para criar uma VM do SQL Server 2017 do Azure com uma destas imagens, consulte as seguintes ligações:

| Plataforma | Imagens livremente licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 para programadores do Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[VM Express Azure 2017 do SQL Server](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 para programadores do Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[VM Express Azure 2017 do SQL Server](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| Servidor Linux Empresarial SUSE | [SQL Server 2017 para programadores do Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[VM Express Azure 2017 do SQL Server](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [SQL Server 2017 para programadores do Azure VM](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[VM Express Azure 2017 do SQL Server](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Pagas edições do SQL Server

Se tiver uma carga de trabalho de produção não lightweight, utilize uma das seguintes edições do SQL Server:

| Edição do SQL Server | Carga de trabalho |
|-----|-----|
| Web | Pequeno web sites |
| Standard | Pequenas e médias cargas de trabalho |
| Enterprise | Grandes ou fundamentais cargas de trabalho|

Tem duas opções pagar para licenciamento do SQL Server para estas edições: *pague por utilização* ou *traga a sua própria licença (BYOL)*.

### <a name="pay-per-usage"></a>Pagar por utilização

**Pagar a licença do SQL Server por utilização** significa que o custo por minuto de execução da VM do Azure inclui o custo de licenciamento do SQL Server. Pode ver os preços para as várias edições do SQL Server (Web, Standard, Enterprise) no [VM do Azure, página de preços](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). O custo é igual para todas as versões do SQL Server (2012 SP3 para 2017). Tal como acontece com licenciamento do SQL Server em geral, o custo de licenciamento por minuto depende o número de núcleos VM.

Pagar com o SQL Server de licenciamento por utilização é recomendada para:

- Cargas de trabalho temporárias ou periódicas. Por exemplo, uma aplicação que precisa suportar um evento para alguns meses cada ano ou análise de negócio em segundas.
- Cargas de trabalho com duração desconhecida ou escala. Por exemplo, uma aplicação que não pode ser necessário em alguns meses ou que pode exigir mais ou menos capacidade de computação, consoante a pedido.

Para criar uma VM do SQL Server 2017 do Azure com uma destas imagens de pagamento por utilização, consulte as seguintes ligações:

| Plataforma | Imagens licenciadas |
|---|---|
| Windows Server 2016 | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[VM do Azure Standard do SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[VM do Azure Standard do SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| Servidor Linux Empresarial SUSE | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[VM do Azure Standard do SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [SQL Server 2017 Web do Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[VM do Azure Standard do SQL Server 2017](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[SQL Server 2017 Enterprise Azure VM](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando cria uma máquina virtual do SQL Server no portal, o **escolher um tamanho** janela mostra um custo estimado. É importante salientar que esta estimativa apenas os custos de computação para executar a VM, juntamente com quaisquer janelas de custos de licenciamento para VMs do Windows. Não inclua adicionais do SQL Server custos para Web, Standard e Enterprise edições de licenciamento. Também não inclui quaisquer custos de licenciamento adicionais para sistemas de operativos Linux de terceiros para VMs com Linux. Para obter a estimativa de preço mais exata, selecione o sistema operativo e a edição do SQL Server, na página de preços para [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).
>
> ![Escolha o painel de tamanho VM](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)

### <a name="bring-your-own-license-byol"></a>BYOL (traga a sua própria licença)

**Colocar a sua própria licença do SQL Server através da licença mobilidade**, também referido como **BYOL**, significa através de uma licença de Volume de servidor de SQL existente com Software Assurance numa VM do Azure. Uma VM do servidor SQL com custos apenas BYOL para o custo de execução da VM, não para licenciamento do SQL Server, uma vez que já tenham adquirido licenças e Software Assurance através de um programa de licenciamento em Volume.

> [!NOTE]
> As imagens BYOL atualmente só estão disponíveis para máquinas virtuais do Windows. No entanto, pode instalar manualmente do SQL Server numa VM apenas de Linux. Consulte as diretrizes no [FAQ de VM do Linux SQL](../../linux/sql/sql-server-linux-faq.md).

Licenciamento através de mobilidade de licenças é recomendado colocar o seus próprios SQL para:

- Cargas de trabalho contínuas. Por exemplo, uma aplicação que precisa suportar operações de negócio 24x7.
- Cargas de trabalho com duração conhecida e a escala. Por exemplo, uma aplicação que é necessário para o ano todo e que a pedido tiver sido prevista.

Para utilizar BYOL com uma VM do SQL Server, tem de ter uma licença para o SQL Server Standard ou Enterprise e [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), que é uma opção obrigatório através de alguns [licenciamento em Volume](https://www.microsoft.com/en-us/download/details.aspx?id=10585) programas e opcional comprar com outras pessoas.  O nível de preço fornecido através de programas de licenciamento em Volume varia consoante o tipo de contrato e a quantidade e ou compromisso para SQL Server. Mas como uma regra geral, colocar a sua própria licença para cargas de trabalho de produção contínua tem as seguintes vantagens:

| Benefício BYOL | Descrição |
|-----|-----|
| **Reduções de custos** | Colocar a sua própria licença do SQL Server é mais económico que pagar por utilização, se uma carga de trabalho continuamente executa o SQL Server Standard ou Enterprise para *mais de 10 meses*. |
| **Poupança de longa duração** | Em média, é *30% mais barata por ano* para comprar ou renovar uma licença do SQL Server para os primeiro 3 anos. Além disso, após a 3 anos, não precisa de renovar a licença já, paga apenas Software Assurance. Nessa altura, é *200% mais barata*. |
| **Réplica secundária de passiva livre** | Outra vantagem de colocar a sua própria licença é o [livre de licenciamento para uma réplica secundária passiva](https://azure.microsoft.com/pricing/licensing-faq/) por SQL Server para fins de elevada disponibilidade. Isto cuts no meio o custo de licenciamento de uma implementação do SQL Server altamente disponível (por exemplo, através de grupos de disponibilidade Always). Os direitos para executar secundário passivo são fornecidos através o benefício de ativação pós-falha servidores Software Assurance. |

Para criar uma VM do SQL Server 2016 do Azure com uma destas imagens bring-your-proprietário-licenças, consulte as VMs o prefixo "{BYOL}":

- [SQL Server 2016 Enterprise Azure VM](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [VM do Azure Standard do SQL Server 2016](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Indique dentro de 10 dias quantas licenças de SQL Server irá utilizar no Azure. As ligações para as imagens anteriores tem instruções sobre como fazê-lo.

## <a name="avoid-unnecessary-costs"></a>Evitar custos desnecessários

Para evitar custos desnecessários, escolha um tamanho ideal da máquina virtual e considere encerramentos intermitentes para cargas de trabalho não contínuos.

### <a id="machinesize"></a>Tamanho corretamente a VM

O custo de licenciamento do SQL Server está diretamente relacionada com o número de núcleos. Escolha um tamanho VM que corresponda às suas necessidades esperadas para a CPU, memória, armazenamento e largura de banda de e/s. Para obter uma lista completa de opções de tamanho da máquina, consulte [tamanhos de Windows VM](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e [tamanhos de VM com Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Existem novos tamanhos de máquina que funcionam bem com determinados tipos de cargas de trabalho do SQL Server. Estes tamanhos de máquinas manter os níveis de altas de memória, armazenamento e largura de banda de e/s, mas têm uma contagem de núcleos virtualizado inferior. Por exemplo, considere o exemplo seguinte:

| Tamanho da VM | vCPUs | Memória | Número máximo discos | Débito de e/s máx. | Custos de licenciamento do SQL Server | Custos totais (computação + licenciamento) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51,200 IOPS ou 768 MB/s | | |
| **Standard_DS14 4v2** | 4 | 112 GB | 32 | 51,200 IOPS ou 768 MB/s | 75% inferior | % de 57 inferior |

> [!IMPORTANT]
> Este é um exemplo de ponto no tempo. As especificações mais recentes, consulte os artigos de tamanhos de máquina e o página de preços do Azure [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

No exemplo anterior, pode ver que as especificações das **Standard_DS14v2** e **Standard_DS14 4v2** idênticas, à exceção vCPUs. O sufixo **-4v2** no fim do **Standard_DS14 4v2** tamanho da máquina indica o número de vCPUs Active Directory. Porque os custos de licenciamento do SQL Server estão associados ao número de núcleos, esta opção reduz significativamente o custo da VM em cenários onde não são necessários os vCPUs adicionais. Este é um exemplo e existem muitas tamanhos de máquinas com vCPUs restrita que são identificadas com este padrão de sufixo. Para obter mais informações, consulte a mensagem de blogue [anunciar novos tamanhos de VM do Azure para o trabalho de base de dados económica mais](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Encerre a VM quando possível

Se estiver a utilizar quaisquer cargas de trabalho que não executem continuamente, considere a encerrar a máquina virtual durante os períodos de inativos. Só paga o que utilizar.

Por exemplo, se apenas estiver a tentar terminar o SQL Server numa VM do Azure, seria não quiser pagar ao acidentalmente deixá-lo em execução para semanas. É uma solução utilizar o [funcionalidade de encerramento automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Autoshutdown de VM do SQL Server](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

Encerramento automático faz parte de um conjunto maior de semelhantes funcionalidades fornecidas pelo [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Para outros fluxos de trabalho, considere automaticamente encerrar e reiniciar as VMs do Azure com uma solução de script, tais como [da automatização do Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Encerrar e Desalocação da VM são a única forma de evitar custos. Basta parar ou utilizar as opções de energia para encerre a VM ainda incorreu custos de utilização.

## <a name="next-steps"></a>Passos seguintes

Para o Azure geral preços orientações, consulte o artigo [evitar custos inesperados com faturação do Azure e custos de gestão](../../../billing/billing-getting-started.md).

Para as máquinas virtuais mais recente preços, incluindo o SQL Server, consulte o [VM do Azure, página de preços](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

Saiba mais sobre as máquinas virtuais do SQL Server para ambos [VMs de SQL Server Windows](virtual-machines-windows-sql-server-iaas-overview.md) e [VMs Linux do SQL Server](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
