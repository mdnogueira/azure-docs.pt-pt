---
title: Perguntas mais frequentes sobre bases de dados ClearDB MySql App Service do Azure | Microsoft Docs
description: "Respostas a perguntas comuns sobre a utilização de bases de dados ClearDB MySQL App Service do Azure."
documentationcenter: php
services: 
author: sunbuild
manager: yochayk
editor: 
tags: mysql
ms.assetid: c2ed5e78-6d7d-4d0c-b7ee-a52ae41ceab8
ms.service: multiple
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ms.openlocfilehash: b0fad1d151799f88c72cfe1359978d69cc1421c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>FAQ sobre as bases de dados do MySql ClearDB com Serviço de Aplicações do Azure
Estas FAQ responde a questões recorrentes sobre a utilizar e aquisição de bases de dados ClearDB MySQL para Web Apps do Azure.

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>As opções que tem para MySQL no Azure?
Tem várias opções:

* [Base de dados MySQL partilhado de ClearDB](/marketplace/partners/cleardb/databases/)
* [Clusters Premium de MySQL ClearDB](/marketplace/partners/cleardb-clusters/cluster/)
* [Cluster MySQL em execução numa VM do Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Instância de MySQL em execução numa VM do Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

ClearDB é um MySQL que aloja o serviço e gere a infraestrutura de MySQL por si. Quando executar o seu próprio MySQL cluster ou base de dados de uma Máquina Virtual no Azure, terá de configurar o servidor de MySQL e manter atualizado com correções de erros.

## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>É necessário um cartão de crédito para a aplicação Web + modelo de MySQL no Azure Marketplace?
Isto depende do tipo de subscrição que está a utilizar. Seguem-se alguns tipos de subscrição frequentemente utilizadas:

* [Pay as you Go](/offers/ms-azr-0003p/): requer um cartão de crédito, e ao adquirir uma base de dados MySQL paga é cobrado o seu cartão de crédito.
* [Versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): inclui os créditos para utilização com o Microsoft Azure de serviços, mas não permite a compra de recursos de terceiros. Para comprar serviços de terceiros ou uma base de dados MySQL paga que tem de utilizar uma subscrição de cartão de crédito ativada. Para aplicações Web, pode criar uma base de dados gratuita ClearDB MySQL.
* [Subscrição do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) e **MSDN programador teste Pay as you go**: semelhante à versão de avaliação gratuita, uma subscrição do MSDN requer que tenha um cartão de crédito para comprar uma solução paga do MySQL ClearDB.
* [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): clientes EA são faturados contra os respetivos EA cada trimestre para todas as compras de (terceiros) do Azure Marketplace numa fatura separada, consolidada. É-lhe faturado fora de compromisso monetário para qualquer compras de marketplace. Tenha em atenção que, neste momento, loja do Azure não está disponível para clientes inscritos no Azerbaijan, Croatia, Noruega e Porto Rico. 
* [DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99): pode criar apenas bases de dados de ClearDB livre para aplicações Web. Não há nenhum limite no número de bases de dados gratuita ClearDB MySQL que pode criar. Tenha em atenção que bases de dados gratuitas não deve ser utilizada para aplicações web de produção, como este serviço destina-se apenas de avaliação.

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Por que razão me foram cobrados encargos $3.50 para uma aplicação Web + MySQL no Azure Marketplace?
A opção de base de dados predefinida é Titan, que é $3.50. Iremos não mostrar o custo durante a criação da base de dados e, por engano poderá adquirir uma base de dados que não pretende. Estamos a tentar encontrar uma forma para melhorar a experiência, mas até, em seguida, tem de verificar todos os seus selecionados escalões de preço para a aplicação web e a base de dados antes de clicar em **criar** e iniciar a implementação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estou a ser executado MySQL no meu próprio máquina virtual do Azure. Pode ligar a minha aplicação web do Azure para a minha base de dados?
Sim. Pode ligar a aplicação web à sua base de dados, desde que a VM do Azure tem dado acesso remoto à sua aplicação web. Para obter mais informações, consulte [MySQL instalar numa máquina virtual](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countries-are-cleardb-premium-mysql-clusters-supported"></a>Em que países são clusters de ClearDB Premium MySQL suportados?
[Clusters de ClearDB Premium MySQL](/marketplace/partners/cleardb-clusters/cluster/) estão disponíveis em todas as regiões do Azure em todo o mundo com a exceção Índia, da Austrália, sul do Brasil e China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Pode criar um novo cluster antes de criar uma base de dados com a solução de cluster de premium ClearDB?
Não, a criação de clusters de ClearDB vazios não é suportada. O portal do Azure permite-lhe criar bases de dados num cluster, que pode criar um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Será posso avisado se tentar eliminar uma base de dados ClearDB MySQL que está a ser utilizado por uma das minhas aplicações?
Não, Azure irá não avisá-lo se eliminar uma compra do marketplace que depende da sua aplicação.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>As regiões que posso criar ClearDB bases de dados?
Azure Marketplace não está disponível para clientes inscritos no Azerbaijan, Croatia, Noruega ou Porto Rico. ClearDB não está disponível nestas regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>O escalão de preço deve seleccionar para uma aplicação web de produção e a base de dados
Utilize básica ou de um escalão de preço superior para aplicações Web. Para ClearDB, recomendamos um Saturno ou Júpiter plano. Reveja as funcionalidades & limitações de cada escalão de preço para ambos [Web Apps](https://azure.microsoft.com/pricing/details/app-service/) e [bases de dados ClearDB MySQL](/marketplace/partners/cleardb/databases/) para escolher a mais se adequa às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como atualizar a minha base de dados de ClearDB de um plano para outro?
No [portal do Azure](https://portal.azure.com), pode dimensionar um ClearDB alojamento base de dados partilhada. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para obter mais informações. Atualmente não suportamos a atualização para clusters de ClearDB Premium no portal do Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Não é possível vejo o meu base de dados de ClearDB no portal do Azure?
Se criamos ClearDB base de dados com o Azure Resource Manager ou [novo Portal do Azure](https://portal.azure.com), não serão visível no [antigo Portal do Azure](https://manage.windowsazure.com). Para trabalho-contornar esta situação está a ligar a base de dados manualmente para a aplicação web. Da mesma forma se criar a base de dados de ClearDB no [portal antigo](https://manage.windowsazure.com) não será capaz de ver a sua base de dados no [novo Portal do Azure](https://portal.azure.com). Não há nenhum trabalho-à volta para o cenário anterior será ignorada.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem posso contacto para suporte quando a minha base de dados está inativo?
Contacte [ClearDB suporte](https://www.cleardb.com/developers/help/support) para qualquer base de dados problemas relacionados. Esteja preparado para lhes fornecer as suas informações de subscrição do Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Pode criar utilizadores adicionais para a minha solução de cluster de base de dados ClearDB MySQL?
Não. Não é possível criar utilizadores adicionais, mas pode criar bases de dados adicionais no seu cluster de base de dados de ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Pode estar bases de dados de série de Basic/Pro atualizado no local semelhante a Planetary planos de hoje em dia no portal de ClearDB?
Sim, série básico bases de dados podem ser atualizada no local (60 básico e 500 básico). Série profissional pode ser atualizada no local (125 Pro através do profissional 1000), exceto para Pro 60. Não é suportada atualmente a atualizar a base de dados de Pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando migrar os meus recursos de uma subscrição para outro, a minha base de dados ClearDB MySQL obter migrado bem?
Quando efetuar a migração de recursos nas subscrições, algumas [limitações](app-service/app-service-move-resources.md) aplicar. Uma base de dados ClearDB MySQL é um serviço de terceiros e, por conseguinte, não obter migrada durante a migração da subscrição do Azure. Se não gere a migração da base de dados MySQL antes da migração de recursos do Azure, as bases de dados ClearDB MySQL podem ser desativados. Migrar manualmente as bases de dados pela primeira vez e, em seguida, efetuar a migração da subscrição do Azure para a sua aplicação web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Atingiu o limite de gastos na minha subscrição. Posso remover o limite e o meu serviço de aplicações está online, no entanto, a base de dados não está acessível. Como novamente ativar a base de dados de ClearDB?
Contacte [ClearDB suporte](https://www.cleardb.com/developers/help/support) voltar a ativar a base de dados. Fornecê-los com o nome de base de dados e informações de subscrição do Azure.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Posso transferir uma base de dados de ClearDB de uma subscrição de cartão de crédito para uma subscrição de EA?
Bases de dados de ClearDB existentes utilizam o cartão de crédito associado às subscrições existentes. Para utilizar uma subscrição de EA terá de migrar os dados para uma nova base de dados:

* Compre uma nova base de dados de ClearDB com a sua subscrição EA.
* Migre os dados para a sua nova base de dados.
* Atualize a sua aplicação para utilizar a nova base de dados.
* Elimine a base de dados de ClearDB antigo.

Quando criar uma nova aplicação web com MySQL (ClearDB) ou criar uma base de dados MySQL (ClearDB), a subscrição, escolha determina como será paga para o serviço. Com uma subscrição de EA, não podemos irá bloquear o aprovisionamento dos serviços de terceiros, tais como ClearDB no portal do Azure. Subscrições de EA são faturadas fora de compromisso monetário e são cobradas trimestral e no arrears. O cliente EA teria de configurar um método de pagamento, como um cartão de crédito pagar para todos os serviços de terceiros marketplace.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde posso ver os custos de ClearDB recursos na subscrição EA?
Para clientes de EA direto, os encargos de Azure Marketplace estão visíveis no Enterprise Portal. Tenha em atenção que todas as compras de marketplace consumo são faturadas fora de compromisso monetário e são cobradas trimestralmente e no arrears. Os clientes EA necessário pagar diretamente para os fornecedores de serviços de terceiros e podem fazê-ao ativar um método de pagamento, como um cartão de crédito à respetiva conta EA.

Os clientes EA indiretos podem encontrar as suas subscrições do Azure Marketplace no **gerir subscrições** página do Portal da empresa, mas preços está oculto. Os clientes devem contactar o respetivo LSP para obter informações sobre os encargos de marketplace.

Acesso ao Azure Marketplace para serviços de terceiros pode ser gerido por administradores de inscrição do EA Azure. Estes podem desativar ou reativar o acesso para compras de terceiros 3rd da loja em Gerir contas e as subscrições sob a secção de contas no Portal da empresa.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem posso contacte para perguntas sobre a minha fatura para serviços de ClearDB na minha subscrição EA?
Contacte [suporte ao cliente da empresa](http://aka.ms/AzureEntSupport) relativamente a faturação em sua inscrição EA. A equipa de suporte de Portal EA irá responder a sua pergunta ou ajudar a resolver o problema.

## <a name="more-information"></a>Mais informações
[FAQ do Azure Marketplace](/marketplace/faq/)

