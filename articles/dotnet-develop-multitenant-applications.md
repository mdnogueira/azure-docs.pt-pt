---
title: "Padrão de aplicação Web do multi-inquilino | Microsoft Docs"
description: "Localize descrições gerais da arquitetura e padrões de conceção que descrevem como implementar uma aplicação web do multi-inquilino no Azure."
services: 
documentationcenter: .net
author: wadepickett
manager: wpickett
editor: 
ms.assetid: 4f0281d2-1555-42b0-a99d-1222fade0b0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 06/05/2015
ms.author: wpickett
ms.openlocfilehash: 57ba0e46139bda2d74c9f7db0ffab2f2122b0df2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="multitenant-applications-in-azure"></a>Aplicações multi-inquilino no Azure
Uma aplicação multi-inquilino é um recurso partilhado que permite que os utilizadores separados, ou "inquilinos", para ver a aplicação como se se tratasse foi os seus próprios. Um cenário típico que presta-se a uma aplicação multi-inquilino é uma em que todos os utilizadores da aplicação podem pretender personalizar a experiência de utilizador, mas têm caso contrário, os mesmos requisitos de negócio básico. Exemplos de aplicações multi-inquilino grandes são do Office 365, Outlook.com e visualstudio.com.

Da perspetiva de um fornecedor de aplicação, as vantagens de arquitetura "multitenancy" principalmente relacionadas com resulta numa eficiência operacional e de custo. Uma versão da aplicação pode satisfazer as necessidades de muitos inquilinos/clientes, permitindo tarefas de administração, tais como monitorização, otimização de desempenho, manutenção do software e as cópias de segurança de dados de consolidação do sistema.

O seguinte fornece uma lista dos objetivos e requisitos da perspetiva de um fornecedor mais significativas.

* **Aprovisionamento**: tem de ser capaz de aprovisionar novos inquilinos para a aplicação.  Para aplicações multi-inquilino com um grande número de inquilinos, é geralmente necessário automatizar este processo, permitindo o aprovisionamento de self-service.
* **Maintainability**: tem de ser capaz de atualizar a aplicação e efetuar outras tarefas de manutenção enquanto estiver a utilizar vários inquilinos.
* **Monitorização**: tem de ser capaz de monitorizar a aplicação em todas as horas para identificar quaisquer problemas e resolvê-los. Isto inclui a forma como cada inquilino está a utilizar a aplicação de monitorização.

Uma aplicação multi-inquilino corretamente implementada fornece as seguintes vantagens para os utilizadores.

* **Isolamento**: as atividades de inquilinos individuais não afetam a utilização da aplicação por outros inquilinos. Os inquilinos não é possível aceder a dados entre si. É apresentado ao inquilino como se se tratasse têm utilização exclusiva da aplicação.
* **Disponibilidade**: individuais inquilinos pretendem que a aplicação ser constantemente disponível, talvez com garantias definidas um SLA. Novamente, as atividades de outros inquilinos não devem afetar a disponibilidade da aplicação.
* **Escalabilidade**: ajusta a aplicação para satisfazer o pedido de inquilinos individuais. A presença e ações de outros inquilinos não devem afetar o desempenho da aplicação.
* **Os custos**: os custos são inferiores ao executar uma aplicação de dedicado, único inquilino porque vários inquilinos permite que a partilha de recursos.
* **Customizability**. A capacidade para personalizar a aplicação para um inquilino individual de várias formas, como adicionar ou remover funcionalidades, a alteração de cores e logótipos ou mesmo a adicionar os seus próprios código ou script.

Em suma, enquanto existirem conta muitos aspetos que deve ter em consideração para fornecer um serviço altamente dimensionável, há também um número de objetivos e requisitos que são comuns a muitas aplicações multi-inquilino. Algumas não podem ser relevantes nos cenários específicos e a importância dos requisitos e objetivos individuais variam em cada cenário. Como um fornecedor da aplicação multi-inquilino, também terá objetivos e requisitos, tais como reunião dos inquilinos objetivos e requisitos, rentabilidade, faturação, vários níveis de serviço, aprovisionamento, maintainability monitorização e automatização.

Para obter mais informações sobre as considerações de design adicional de uma aplicação multi-inquilino, consulte [alojar uma aplicação multi-inquilino no Azure][Hosting a Multi-Tenant Application on Azure]. Para obter informações sobre os padrões da arquitetura de dados comuns de aplicações de base de dados de software como um serviço (Saas) de multi-inquilino, consulte [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database (Padrões de estrutura para Aplicações SaaS Multi-inquilino com a Base de Dados SQL do Azure)](sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

O Azure oferece muitas funcionalidades que permitem-lhe resolver os problemas chaves encontrados prioritários quando um sistema de multi-inquilino.

**Isolamento**

* Inquilinos do Web site do segmento por cabeçalhos de anfitrião com ou sem comunicação SSL
* Web site do segmento inquilinos os parâmetros de consulta
* Serviços Web em funções de trabalho
  * Funções de trabalho. Normalmente, que processam dados no back-end de uma aplicação.
  * Funções da Web que, normalmente, agirem como o front-end para aplicações.

**Armazenamento**

Gestão de dados como os serviços SQL Database do Azure ou o armazenamento do Azure, tais como o serviço de tabela que fornece serviços de armazenamento de grandes quantidades de dados não estruturados e o serviço Blob que fornece serviços de armazenar grandes quantidades de texto não estruturado ou binário dados, tais como as vídeo, áudio e imagens.

* Proteger dados de multi-inquilino adequada da base de dados do SQL inícios de sessão do SQL Server por inquilino.
* Utilizar tabelas do Azure para aplicações recursos, especificando uma política de acesso ao nível do contentor, pode ajustar as permissões sem ter de emitir novo URL para os recursos protegidos com assinaturas de acesso partilhado a capacidade.
* As filas do Azure para filas do Azure de recursos de aplicação são frequentemente utilizadas para processamento de unidade em nome de inquilinos, mas também podem ser utilizadas para distribuir trabalho necessário para o aprovisionamento ou de gestão.
* Filas de barramento de serviço para recursos de aplicação que envia trabalhar um partilhado para um serviço, pode utilizar uma fila única onde remetente cada inquilino tem apenas permissões (uma vez que deriva de afirmações emitidas do ACS) para enviar para essa fila, enquanto tem apenas os recetores do serviço permissão para solicitar a partir da fila dos dados provenientes de vários inquilinos.

**Serviços de segurança e de ligação**

* Service Bus do Azure, uma infraestrutura de mensagens que se encontra entre aplicações, permitindo-lhes trocar mensagens de uma forma de dimensionamento melhorada aproximadamente ligado conjugada e resiliência.

**Serviços de rede**

O Azure oferece vários serviços de rede que suportam a autenticação e melhorar a capacidade de gestão das suas aplicações alojadas. Estes serviços incluem o seguinte:

* Permite de rede Virtual do Azure, Aprovisiona e gerir redes privadas virtuais (VPNs) no Azure, bem como ligação com segurança estes com infraestrutura de TI no local.
* Gestor de tráfego de rede virtual permite-lhe para tráfego de entrada de balanceamento de carga em vários serviços do Azure alojados quer está a ser executados no mesmo datacenter ou em diferentes centros de dados em todo o mundo.
* Azure Active Directory (Azure AD) é um serviço de moderno, baseado em REST que fornece capacidades de controlo de acesso e gestão de identidade para as suas aplicações em nuvem. Utilizar o Azure AD para os recursos de aplicação Azure AD fornece uma forma fácil de autenticar e autorizar utilizadores a obterem acesso aos seus serviços e aplicações web, permitindo que as funcionalidades de autenticação e autorização para ser factored fora do seu código.
* Service Bus do Azure fornece um serviço de mensagens segura e distribuídos para a capacidade do fluxo de dados e aplicações híbridas, tais como a comunicação entre o Azure alojado aplicações e aplicações no local e serviços, sem necessidade de segurança e de firewall complexa infraestruturas. Utilizando o reencaminhamento do Service Bus para recursos de aplicação para os serviços que são expostos como pontos finais poderá pertencer ao inquilino (por exemplo, alojado fora do sistema, tal como no local) ou podem ser serviços aprovisionados especificamente para o inquilino porque ( os dados confidenciais, específica do inquilino mudar entre eles).

**Aprovisionamento de recursos**

O Azure oferece de diversas formas novos inquilinos de aprovisionamento para a aplicação. Para aplicações multi-inquilino com um grande número de inquilinos, é geralmente necessário automatizar este processo, permitindo o aprovisionamento de self-service.

* As funções de trabalho permitem-lhe aprovisionar e aprovisionar desativação por inquilino recursos (por exemplo, quando um novo inquilino inicia-se ou cancela), recolher métricas de medição de utilizar e gerir o dimensionamento seguir uma determinada agenda ou em resposta a cruzamento de limiares de chave de desempenho indicadores. Esta mesma função também pode ser utilizada para emitir saída atualizações e melhoramentos para a solução.
* Os Blobs do Azure podem ser utilizados para aprovisionar computação ou previamente inicializados pacotes, imagens VHD e outros recursos do serviço de recursos de armazenamento para novos inquilinos ao fornecer políticas de acesso ao nível do contentor proteger a capacidade de cálculo.
* As opções para o aprovisionamento de recursos de base de dados SQL para um inquilino incluem:
  
  * DDL em scripts ou incorporados como recursos dentro de assemblagens
  * SQL Server 2008 R2 pacotes DAC implementadas através de programação.
  * Copiar de uma base de dados de referência principal
  * Utilizar base de dados, importar e exportar para aprovisionar novas bases de dados a partir de um ficheiro.

<!--links-->

[Hosting a Multi-Tenant Application on Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
