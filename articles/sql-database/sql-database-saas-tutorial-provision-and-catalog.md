---
title: "Aprovisionar inquilinos novos numa aplicação multi-inquilino com a Base de Dados SQL do Azure | Microsoft Docs"
description: "Aprovisionar e catalogar inquilinos novos na aplicação Wingtip Tickets (WTP) SaaS de exemplo da Base de Dados SQL do Azure"
keywords: tutorial de base de dados sql
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: aae5d85a18f93b7821a6ef8fc7161dd9a6ebe533
ms.contentlocale: pt-pt
ms.lasthandoff: 05/12/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Aprovisionar novos inquilinos e registá-los no catálogo

Neste tutorial, vai aprovisionar novos inquilinos na aplicação SaaS Wingtip Tickets Platform (WTP). Vai criar inquilinos, bases de dados de inquilinos e registar os inquilinos no catálogo. O *catálogo* é uma base de dados que mantém o mapeamento entre os muitos inquilinos das aplicações SaaS e os seus dados. Utilize estes scripts para explorar os padrões de aprovisionamento e de catalogação utilizados e para saber como é implementado o registo de novos inquilinos no catálogo. O catálogo desempenha uma função importante no direcionamento dos pedidos de aplicações para as bases de dados corretas.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Aprovisionar um inquilino individual novo
> * Aprovisionar um lote de inquilinos adicionais
> * Passar para os detalhes de aprovisionamento dos novos inquilinos e como registá-los no catálogo


Para concluir este tutorial, devem ser cumpridos os seguintes pré-requisitos:

* A aplicação WTP está implementada. Para implementar em menos de cinco minutos, veja [Implementar e explorar a aplicação SaaS WTP](sql-database-saas-tutorial.md)
* O Azure PowerShell está instalado. Para obter mais detalhes, veja [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-catalog-pattern"></a>Introdução ao padrão de Catalogação SaaS

Na aplicação SaaS multi-inquilino suportada por base de dados, é importante saber onde estão armazenadas as informações de cada inquilino. No padrão de catalogação SaaS, é utilizada uma base de dados de catálogos para armazenar o mapeamento entre os inquilinos e o local onde os dados são armazenados. A aplicação WTP utiliza uma arquitetura de base de dados de inquilino individual, mas o padrão básico do armazenamento do mapeamento de inquilinos/base de dados num catálogo será aplicado, independentemente de ser utilizada uma base de dados de multi-inquilino ou de inquilino individual.

A cada inquilino é atribuída uma chave que distingue os seus dados no catálogo. Na aplicação WTP, a chave é formada a partir de um hash do nome do inquilino. Este padrão permite que a parte do nome do inquilino do URL da aplicação seja utilizada para construir a chave e obter a ligação com um inquilino específico. Podem ser utilizados outros esquemas de ID sem afetar o padrão geral.

O catálogo na aplicação WTP é implementado com a tecnologia de Gestão de Partições Horizontais na [Biblioteca de Clientes da Base de Dados Elástica (EDCL)](sql-database-elastic-database-client-library.md). A EDCL é responsável pela criação e gestão de um _catálogo_ suportado por base de dados onde é mantido um _mapa de partições horizontais_. O catálogo contém o mapeamento entre as chaves (inquilinos) e suas bases de dados (partições horizontais).

> [!IMPORTANT]
> Os dados de mapeamento estão acessíveis na base de dados do catálogo, mas *não deverá editá-los*! Edite os dados de mapeamento apenas com as APIs da Biblioteca de Clientes da Base de Dados Elástica. Se manipular diretamente os dados de mapeamento arrisca-se a danificar o catálogo, além de não ser suportado.

A aplicação SaaS Wingtip aprovisiona os inquilinos novos ao copiar uma base de dados *dourada*.

## <a name="get-the-wingtip-application-scripts"></a>Obter os scripts da aplicação Wingtip

Os scripts da Wingtip Tickets e o código fonte da aplicação estão disponíveis no repositório do github [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Os ficheiros dos scripts estão localizados na pasta [Módulos de Aprendizagem](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Transfira a pasta **Módulos de Aprendizagem** para o computador local, mantendo a estrutura das pastas.

## <a name="provision-a-new-tenant"></a>Aprovisionar um inquilino novo

Se já tiver criado um inquilino no primeiro tutorial da WTP, pode avançar para a secção seguinte: [Aprovisionar um lote de inquilinos](#provision-a-batch-of-tenants).

Execute o script *Demo-ProvisionAndCatalog* para criar rapidamente um inquilino e registá-lo no catálogo:

1. Abra **Demo-ProvisionAndCatalog.ps1** no ISE do PowerShell e defina os seguintes valores:
   * **$TenantName** = o nome do novo local (por exemplo, *Bushwillow Blues*). 
   * **$VenueType** = um dos tipos predefinidos de local: blues, música clássica, dança, jazz, judo, corrida de motos, multifunções, ópera, música rock, futebol.
   * **$DemoScenario** = 1. Deixe este valor definido como _1_ para **Aprovisionar um inquilino individual**.

1. Prima **F5** e execute o script.

Após a conclusão do script, o novo inquilino é aprovisionado e a aplicação *Eventos* é apresentada no browser:

![Novo inquilino](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Aprovisionar um lote de inquilinos

Este exercício aprovisiona um lote de inquilinos adicionais. É recomendado fazê-lo antes de concluir os outros tutoriais da WTP.

1. Abra ...\\Módulos de Aprendizagem\\Utilitários\\*Demo-ProvisionAndCatalog.ps1* no *ISE do PowerShell*  e defina o seguinte valor:
   * **$DemoScenario** = **3**. Defina como **3** para **Aprovisionar um lote de inquilinos**.
1. Prima **F5** e execute o script.

O script implementa um lote de inquilinos adicionais. Utiliza um [modelo do Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) que controla o lote e, em seguida, delega o aprovisionamento de cada base de dados a um modelo ligado. Utilizar modelos desta forma permite que o Azure Resource Manager funcione como o mediador no processo de aprovisionamento do script. Os modelos aprovisionam as bases de dados em paralelo sempre que podem e, se necessário, processam as tentativas ao otimizar o processo geral. O script é idempotent. Por isso, se for interrompido, execute-o novamente.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Verificar o lote de inquilinos implementados com êxito

* Abra o servidor *tenants1* no [portal do Azure](https://portal.azure.com) e clique em **Bases de Dados SQL**:

   ![lista de bases de dados](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Instruções detalhadas sobre o aprovisionamento e a catalogação

Para uma melhor compreensão da forma como a aplicação Wingtip implementa o aprovisionamento do novo inquilino, execute o script *Demo-ProvisionAndCatalog* novamente e aprovisione outro inquilino. Desta vez, adicione um ponto de interrupção e siga o fluxo de trabalho:

1. Abra ...\\Módulos de Aprendizagem \Utilitários\_Demo-ProvisionAndCatalog.ps1_ e defina os seguintes valores para os novos inquilinos que não existem no catálogo atual:
   * **$TenantName** = defina como um novo nome (por exemplo, *Hackberry Hitters*).
   * **$VenueType** = utilize um dos tipos predefinidos de local (por exemplo, *judo*).
   * **$DemoScenario** = 1. Defina como **1** para **Aprovisionar um inquilino individual**.

1. Adicione um ponto de interrupção ao colocar o cursor em qualquer parte na linha seguinte: *New-Tenant `* e, em seguida, prima **F9**.

   ![ponto de interrupção](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Prima **F5** para executar o script. Quando o ponto de interrupção for atingido, prima **F11** para avançar. Rastreie a execução do script com **F10** e **F11** para ignorar ou avançar para as funções chamadas. [Sugestões para trabalhar e depurar scripts do PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Examinar a implementação em detalhe do aprovisionamento e da catalogação ao seguir o script

O script aprovisiona e cataloga os novos inquilinos ao realizar os seguintes passos:

1. Módulo **Importar o SubscriptionManagement.psm1**, que contém as funções para iniciar sessão no Azure e selecionar a subscrição do Azure com a qual está a trabalhar.
1. Módulo **Importar o CatalogAndDatabaseManagement.psm1**, que fornece um catálogo e uma abstração de nível de inquilino sobre as funções de [Gestão de Partições Horizontais](sql-database-elastic-scale-shard-map-management.md). Este é um módulo importante, que contém grande parte do padrão de catalogação e que vale a pena explorar.
1. **Obter os detalhes de configuração**. Avance para _Get-Configuration_ (com **F11**) e veja como a configuração da aplicação é especificada. Os nomes dos recursos, nem como os outros valores específicos da aplicação, são definidos aqui. No entanto, não altere nenhum destes valores até estar familiarizado com os scripts.
1. **Obter o objeto de catálogo**. Avance para *Get-Catalog* para ver como o catálogo é inicializado com as funções de Gestão de Partições Horizontais, importadas de **AzureShardManagement.psm1**. O catálogo é composto pelos seguintes objetos:
   * $catalogServerFullyQualifiedName é construído com o radical padrão e o nome de utilizador: _catalog-\<utilizador\>.database.windows.net_.
   * $catalogDatabaseName é obtido a partir da configuração: *tenantcatalog*.
   * O objeto $shardMapManager é inicializado a partir da base de dados do catálogo.
   * O objeto $shardMap é inicializado a partir do mapa de partições horizontais *tenantcatalog* na base de dados do catálogo.
   Um objeto de catálogo é composto, devolvido e utilizado no script de nível mais elevado.
1. **Calcular a nova chave de inquilino**. É utilizada uma função hash para criar a chave de inquilino a partir do nome do inquilino.
1. **Verificar se a chave de inquilino existe**. O catálogo é verificado para garantir que a chave está disponível.
1. **A base de dados do inquilino é aprovisionada com New-TenantDatabase**. Utilize **F11** para avançar e ver como a base de dados é aprovisionada com um modelo do Resource Manager.
    
O nome de base de dados é construído a partir do nome de inquilino para ficar claro quais as partições horizontais que pertencem a que inquilino. (Podem facilmente ser utilizadas outras estratégias para atribuir nomes a base de dados.)

É utilizado um modelo do Resource Manager para **criar uma base de dados de inquilinos ao copiar uma  *base de dados* dourada** (baseTenantDB) no servidor de catálogo.  Um método alternativo pode ser criar uma base de dados vazia e, em seguida, inicializá-la através da importação de um ficheiro bacpac.

O modelo do Resource Manager está na pasta ...\\Módulos de Aprendizagem\\Comuns\\: *tenantdatabasecopytemplate.json*

Depois de criar a base de dados de inquilinos, continua a ser inicializada com o nome do local (inquilino) e o tipo de local. Também pode utilizar outra inicialização aqui.

A base de dados de inquilinos é registada no catálogo com *Add-TenantDatabaseToCatalog* através da chave de inquilino. Utilize **F11** para avançar para os detalhes:

* A base de dados do catálogo é adicionada ao mapa de partições horizontais (a lista de bases de dados conhecidas).
* É criado o mapeamento que liga o valor da chave à partição horizontal.
* São adicionados os metadados adicionais (nome do local) sobre o inquilino.

Depois de concluir o aprovisionamento, a execução devolve o script *Demo-ProvisionAndCatalog* original e a página **eventos** do novo inquilino é apresentada no browser:

   ![eventos](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Outros padrões de aprovisionamento

Outros padrões de aprovisionamento não incluídos neste tutorial incluem:

**Pré-aprovisionamento de bases de dados.** Este padrão explora o facto de as bases de dados num conjunto elástico não acarretarem custos adicionais (a faturação é referente ao conjunto elástico, não às bases de dados) e de as bases de dados inativas não consumirem recursos. Ao fazer o pré-aprovisionamento das bases de dados num conjunto e, em seguida, alocá-las, quando necessário, reduzirá significativamente o tempo de integração dos inquilinos. O número de bases de dados previamente aprovisionadas pode ser ajustado conforme necessário para manter uma memória intermédia adequada para a taxa de aprovisionamento previsível.

**Aprovisionamento automático.** Neste padrão, é utilizado um serviço de aprovisionamento dedicado para aprovisionar automaticamente servidores, conjuntos e bases de dados conforme necessário, incluindo o pré-aprovisionamento de bases de dados em conjuntos elásticos, se assim o desejar. E, se as bases de dados forem desativadas e eliminadas, os espaços nos conjuntos elásticos poderão ser preenchidos pelo serviço de aprovisionamento, conforme desejado. Esse serviço pode ser simples ou complexo (por exemplo, processar o aprovisionamento em várias localizações geográficas) e poderá configurar a georreplicação automaticamente se aquela estratégia estiver a ser utilizada para DR. Com o padrão de aprovisionamento automático, uma aplicação cliente ou script poderia submeter um pedido de aprovisionamento a uma fila para ser processado pelo serviço de aprovisionamento e, em seguida, poderia consultar o serviço para determinar se tinha sido concluído. Se for utilizado a pré-aprovisionamento, os pedidos poderiam ser processados rapidamente com o serviço de gestão do aprovisionamento de uma base de dados de substituição em execução em segundo plano.


## <a name="stopping-wingtip-saas-application-related-billing"></a>Parar a faturação relacionada com a aplicação SaaS Wingtip

Se não pretender continuar com outro tutorial, é recomendado que elimine todos os recursos para suspender a faturação. Elimine o grupo de recursos para o qual foi implementada a aplicação WTP. Todos os recursos serão eliminados.

* Navegue para o grupo de recursos da aplicação no portal e elimine-o para parar toda a faturação relacionada com a implementação da WTP.

## <a name="tips"></a>Sugestões

* A EDCL também fornece funcionalidades importantes que permitem às aplicações cliente ligar-se ao catálogo e manipulá-lo. Também pode utilizar a EDCL para obter uma ligação do ADO.NET para um determinado valor de chave, permitindo que a aplicação estabeleça ligação à base de dados correta. O cliente coloca em cache estas informações de ligação para minimizar o tráfego para a base de dados do catálogo e acelerar a aplicação.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]

> * Aprovisionar um inquilino individual novo
> * Aprovisionar um lote de inquilinos adicionais
> * Passar para os detalhes de aprovisionamento dos novos inquilinos e como registá-los no catálogo

[Tutorial de monitorização do desempenho](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>Recursos Adicionais

* [Tutoriais adicionais criados após a implementação inicial da aplicação Wingtip Tickets Platform (WTP)](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Biblioteca de clientes da base de dados elástica](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [How to Debug Scripts in Windows PowerShell ISE (Como Depurar os Scripts do ISE do Windows PowerShell)](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

