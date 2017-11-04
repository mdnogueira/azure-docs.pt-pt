---
title: "Resolver problemas comuns de ligação para a Base de Dados SQL do Azure"
description: "Passos para identificar e resolver erros comuns de ligação de SQL Database do Azure."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: daleche
ms.openlocfilehash: cc9b1e1474e67628857dd80a63850634469ca5e8
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Resolver problemas de ligação à SQL Database do Azure
Quando a falha a ligação à SQL Database do Azure, receber [mensagens de erro](sql-database-develop-error-messages.md). Este artigo é um tópico centralizado que o ajuda a resolver problemas de conectividade da SQL Database do Azure. Introduz [as causas comuns](#cause) dos problemas de ligação, recomenda [uma ferramenta de resolução de problemas](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) que ajuda a identidade o problema e fornece passos de resolução de problemas para resolver [transitório erros](#troubleshoot-transient-errors) e [persistentes ou não transitório erros](#troubleshoot-persistent-errors). 

Se ocorrerem problemas de ligação, tente os passos de resolução de problemas que são descritos neste artigo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa
Problemas de ligação podem ser causados por qualquer um dos seguintes:

* Falha ao aplicar as melhores práticas e diretrizes de conceção durante o processo de design da aplicação.  Consulte [descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md) para começar a utilizar.
* Reconfiguração de base de dados SQL do Azure
* Definições de firewall
* Tempo limite de ligação
* Informações de início de sessão incorreto
* Atingido o limite máximo na alguns recursos da SQL Database do Azure

Geralmente, os problemas de ligação à SQL Database do Azure podem ser classificados da seguinte forma:

* [Erros transitórios (curta duração ou intermitentes)](#troubleshoot-transient-errors)
* [Erros persistentes ou não transitório (erros regularmente repetir)](#troubleshoot-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Tente troubleshooter para problemas de conectividade da SQL Database do Azure
Se ocorrer um erro de ligação específico, tente [esta ferramenta](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database), que irá ajudá-lo rapidamente identidade e resolver o problema.

## <a name="troubleshoot-transient-errors"></a>Resolver problemas de erros transitórios

Quando uma aplicação se liga a uma base de dados SQL do Azure, receber a seguinte mensagem de erro:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Esta mensagem de erro é normalmente transitória (curta duração).
> 
> 

Este erro ocorre quando a base de dados do Azure está a ser movido (ou reconfigurado) e a sua aplicação perde a ligação para a base de dados do SQL Server. Eventos de reconfiguração de base de dados do SQL Server ocorrerem devido a um evento planeado (por exemplo, uma atualização de software) ou um evento não planeado (por exemplo, uma falha de processo ou o balanceamento de carga). A maioria dos eventos de reconfiguração são geralmente curta duração e devem ser realizados em menos de 60 segundos, no máximo. No entanto, estes eventos podem ocasionalmente demorar mais tempo a concluir, por exemplo, quando uma transação grande faz com que uma recuperação de longa execução.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passos de resolução de problemas de conectividade transitório

1. Verifique o [Dashboard de serviço do Microsoft Azure](https://azure.microsoft.com/status) para quaisquer falhas conhecidas que ocorreram durante o período de tempo durante os quais os erros comunicados pela aplicação.
2. As aplicações que se ligam a um serviço em nuvem, tais como SQL Database do Azure deve esperar eventos de reconfiguração periódica e implementar repetir lógica para lidar com estes erros em vez de analisar como erros de aplicações para utilizadores. Reveja o [erros transitórios](sql-database-connectivity-issues.md) secção e as melhores práticas e diretrizes de conceção em [descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md) para obter mais informações e geral Repita estratégias. Em seguida, ver exemplos de código em [bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md) para informações específicas.
3. Como uma base de dados se aproxima dos respetivos limites de recursos, pode parecer um problema de conectividade transitório. Consulte [resolução de problemas de desempenho](sql-database-troubleshoot-performance.md).
4. Se a problemas de conectividade continuar ou, se a duração para o qual a aplicação encontrar o erro excede 60 segundos ou se visualizar várias ocorrências do erro num determinado dia, ficheiro um pedido de suporte do Azure ao selecionar **obter suporta**no [suporte do Azure](https://azure.microsoft.com/support/options) site.

## <a name="troubleshoot-persistent-errors"></a>Resolver erros persistentes
Se a aplicação de forma permanente não for possível ligar à SQL Database do Azure, normalmente indica um problema com um dos seguintes:

* Configuração da firewall. Firewall de base de dados ou do lado do cliente do SQL do Azure está a bloquear as ligações ao SQL Database do Azure.
* A reconfiguração do lado do cliente de rede: por exemplo, um novo endereço IP ou um servidor proxy.
* Erro de utilizador: por exemplo, estar mal escrito parâmetros de ligação, como o nome do servidor na cadeia de ligação.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passos de resolução de problemas de conectividade persistente
1. Configurar [regras de firewall](sql-database-configure-firewall-settings.md) para permitir que o cliente do endereço IP. Para fins de teste temporários, configure uma regra de firewall utilizando 0.0.0.0 como o intervalo de endereços IP inicial e utilizar 255.255.255.255 como o intervalo de endereços IP final. Esta ação irá abrir o servidor para todos os endereços IP. Se este procedimento resolve o problema de conectividade, remover esta regra e criar uma regra de firewall para um endereço IP adequadamente limitado ou intervalo de endereços. 
2. Em todas as firewalls entre o cliente e a Internet, certifique-se de que a porta 1433 está aberta para ligações de saída. Reveja [configurar a Firewall do Windows para permitir o acesso do SQL Server](https://msdn.microsoft.com/library/cc646023.aspx) e [híbrida identidade necessários portas e protocolos](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) para apontadores adicionais relacionados com portas adicionais que é necessário abrir para o Azure Autenticação do Active Directory.
3. Certifique-se de que a cadeia de ligação e outras definições de ligação. Consulte a secção de cadeia de ligação no [tópico de problemas de conectividade](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Verifique o estado de funcionamento do serviço no dashboard. Se considerar que não há uma falha regional, consulte [recuperar a partir de uma falha](sql-database-disaster-recovery.md) para obter passos para recuperar para uma região de novo.

## <a name="next-steps"></a>Passos seguintes
* [Resolver problemas de desempenho de SQL Database do Azure](sql-database-troubleshoot-performance.md)
* [Procurar documentação no Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Ver as atualizações mais recentes para o serviço SQL Database do Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Recursos adicionais
* [Descrição geral do desenvolvimento de base de dados SQL](sql-database-develop-overview.md)
* [Orientações de processamento de falhas transitórias gerais](../best-practices-retry-general.md)
* [Bibliotecas de ligação para base de dados SQL e SQL Server](sql-database-libraries.md)

