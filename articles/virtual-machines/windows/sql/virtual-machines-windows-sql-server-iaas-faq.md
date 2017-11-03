---
title: "SQL Server em FAQ de máquinas virtuais do Azure Windows | Microsoft Docs"
description: "Este artigo fornece respostas às perguntas mais frequentes sobre a execução do SQL Server em VMs do Azure."
services: virtual-machines-windows
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/04/2017
ms.author: v-shysun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88c54293b4a9b470febeda3c0e9a1755ad3b0638
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-for-sql-server-on-windows-azure-virtual-machines"></a>Perguntas mais frequentes para o SQL Server em máquinas virtuais do Microsoft Azure

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-faq.md)
> * [Linux](../../linux/sql/sql-server-linux-faq.md)

Este tópico fornece respostas a algumas perguntas mais comuns sobre a execução [do SQL Server em máquinas virtuais do Microsoft Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [!NOTE]
> Este tópico centra-se nos problemas específicos ao SQL Server em VMs do Windows. Se estiver a executar o SQL Server em VMs do Linux, consulte o [Linux FAQ](../../linux/sql/sql-server-linux-faq.md).

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Perguntas Mais Frequentes

1. **Como posso criar uma máquina virtual do Azure com o SQL Server?**

   A solução mais fácil consiste em criar uma Máquina Virtual que inclui o SQL Server. Para um tutorial de inscrição no Azure e criar uma VM do SQL a partir do portal, consulte [aprovisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Pode selecionar uma imagem de máquina virtual que utiliza o licenciamento de SQL Server de pagamento por minuto ou pode utilizar uma imagem que permite-lhe trazer a sua própria licença do SQL Server. Também tem a opção de instalar manualmente do SQL Server numa VM com o uma edição licenciada livremente (programador ou Express) ou através da reutilização de uma licença no local. Se utilizar a sua própria licença, tem de ter [licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Qual é a diferença entre as VMs do SQL Server e o serviço de base de dados SQL?**

   Concecionais, executar o SQL Server numa máquina virtual do Azure é não que diferente do SQL Server a executar num centro de dados remoto. Em contrapartida, [base de dados SQL](../../../sql-database/sql-database-technical-overview.md) oferece a base de dados-como-um-serviço. Com base de dados do SQL Server, não dispõe de acesso aos computadores que alojam as bases de dados. Para ver uma comparação completa, consulte [escolher uma opção do SQL Server na nuvem: SQL Database do Azure (PaaS) ou o SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como migrar a minha base de dados de SQL Server no local para a nuvem?**

   Em primeiro lugar, crie uma máquina virtual do Azure com uma instância do SQL Server. Em seguida, migre as bases de dados no local para essa instância. Para as estratégias de migração de dados, consulte [migrar uma base de dados do SQL Server para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md).

1. **Pode instalar uma segunda instância do SQL Server na VM do mesma? Pode alterar as funcionalidades instaladas da instância predefinida?**

   Sim. O suporte de dados de instalação do SQL Server está localizado numa pasta no **C** unidade. Executar **Setup.exe** partir dessa localização para adicionar novas instâncias do SQL Server ou funcionalidades de alterar outras instaladas do SQL Server na máquina. Tenha em atenção que algumas funcionalidades, tais como a cópia de segurança automatizada, a aplicação de patches automatizada e integração do Cofre de chaves do Azure, só operam contra a instância predefinida.

1. **Pode desinstalar a instância predefinida do SQL Server?**

   Sim. Mas existem algumas considerações. Como indicado na resposta anterior, as funcionalidades que dependem de [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) operar apenas na instância predefinida. Se desinstalar a instância predefinida, a extensão continua a procure- e poderá gerar erros de registo de eventos. Estes erros são das duas seguintes origens: **gestão de credenciais do Microsoft SQL Server** e **IaaS do Microsoft SQL Server Agent**. Um dos erros poderá ser semelhante ao seguinte:

      Ocorreu um erro relacionado com a rede ou específico da instância ao estabelecer uma ligação ao SQL Server. O servidor não foi encontrado ou não estava acessível.

   Se optar por desinstalar a instância predefinida, também desinstalar o [extensão de agente do SQL Server IaaS](virtual-machines-windows-sql-server-agent-extension.md) bem.

1. **Como atualizar para uma nova versão/edição do SQL Server numa VM do Azure?**

   Atualmente, não há nenhuma atualização no local para o SQL Server em execução numa VM do Azure. Criar uma nova máquina virtual do Azure com a versão/edição de SQL Server pretendido e, em seguida, migre as bases de dados para o novo servidor através de padrão [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md).

1. **Como instalar a minha cópia licenciada do SQL Server numa VM do Azure?**

   Existem duas formas para efetuar este procedimento. Pode aprovisionar um do [imagens da máquina virtual que suporta licenças](virtual-machines-windows-sql-server-iaas-overview.md#BYOL). Outra opção consiste em copiar o suporte de dados de instalação do SQL Server para uma VM do Windows Server e, em seguida, instale o SQL Server na VM. Para licenciamento motivos, tem de ter [licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Para obter mais informações, consulte [Pricing guidance for SQL Server Azure VMs (Documentação de orientação sobre preços de VMs do Azure do SQL Server)](virtual-machines-windows-sql-server-pricing-guidance.md).

1. **Pode alterar uma VM para utilizar o meu próprio licença do SQL Server se foi criada a partir de uma das imagens gallery pay as you go?**

   Não. Não pode mudar de licenciamento de pagamento por minuto para a sua própria licença de utilização. Criar uma nova máquina virtual do Azure utilizando um do [imagens BYOL](virtual-machines-windows-sql-server-iaas-overview.md#BYOL), e, em seguida, migre as bases de dados para o novo servidor através de padrão [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md).

1. **Instâncias de Cluster (FCI) do SQL Server ativação pós-falha são suportados em VMs do Azure?**

   Sim. Pode [criar um Cluster de ativação pós-falha do Windows no Windows Server 2016](virtual-machines-windows-portal-sql-create-failover-cluster.md) e utilizar espaços de armazenamento direto (S2D) para o armazenamento de cluster. Em alternativa, pode utilizar as soluções de clustering ou armazenamento de terceiros, tal como descrito no [elevada disponibilidade e recuperação após desastre para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions).

1. **É necessário pagar a licença do SQL Server numa VM do Azure se está a ser utilizado apenas para o modo de espera/ativação pós-falha?**

   Se tiver Software Assurance e utilizar mobilidade de licenças, conforme descrito em [FAQ de licenciamento de Máquina Virtual](http://azure.microsoft.com/pricing/licensing-faq/) , em seguida, não dispõe de pagar ao licenciamento do SQL Server que participam como réplica secundária passiva numa implementação HA. Caso contrário, terá de pagar licenciá-lo.

1. **Como são atualizações e service packs aplicados numa VM do SQL Server?**

   Máquinas virtuais conceder controlo sobre o computador anfitrião, incluindo quando e como aplicar atualizações. Para o sistema operativo, pode aplicar manualmente as atualizações do windows, ou pode ativar a um serviço de agendamento denominado [aplicação de patches automatizada](virtual-machines-windows-sql-automated-patching.md). A Aplicação de Patches Automatizada instala todas as atualizações marcadas como importantes, incluindo atualizações do SQL Server nessa categoria. As outras atualizações opcionais do SQL Server têm de ser instaladas manualmente.

1. **É possível definir as configurações não mostradas na galeria da máquina virtual (por exemplo, Windows 2008 R2 + SQL Server 2012)?**

   Não. Para imagens de Galeria de máquina virtual que incluem o SQL Server, tem de selecionar uma das imagens fornecidas.

1. **Como instalar o SQL Server Data tools na minha VM do Azure?**

    Transferir e instalar as ferramentas de dados SQL a partir do [Microsoft SQL Server Data Tools - Business Intelligence para o Visual Studio 2013](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos

**VMs do Windows**:

* [Descrição geral do SQL Server do Windows VM](virtual-machines-windows-sql-server-iaas-overview.md).
* [Aprovisionar um SQL Server do Windows VM](virtual-machines-windows-portal-sql-server-provision.md)
* [Migrar uma base de dados para o SQL Server numa VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Elevada disponibilidade e recuperação após desastre do SQL Server em Virtual Machines do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Padrões de Aplicação e Estratégias de Desenvolvimento para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs com Linux**:

* [Descrição geral do SQL Server numa VM com Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Aprovisionar uma VM com Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [FAQ (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)