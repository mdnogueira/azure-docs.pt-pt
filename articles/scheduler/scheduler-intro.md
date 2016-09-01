<properties
 pageTitle="O que é o Agendador do Azure? | Microsoft Azure"
 description="O Agendador do Azure permite-lhe descrever detalhamente as ações a executar na nuvem. Então agenda e executa essas ações automaticamente."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.date="08/18/2016"
 ms.author="krisragh"/>

# O que é o Agendador do Azure?

O Agendador do Azure permite-lhe descrever detalhamente as ações a executar na nuvem. Então agenda e executa essas ações automaticamente.  O Agendador pode fazê-lo através do [Portal do Azure](scheduler-get-started-portal.md), POR código, [API REST](https://msdn.microsoft.com/library/mt629143.aspx) ou do Azure PowerShell.

O Agendador cria, mantém e convoca trabalho agendado.  O Agendador não aloja quaisquer cargas de trabalho ou executa nenhum código. Apenas _invoca_ código alojado noutro local, como no Azure, no local, ou noutro fornecedor. Invoca através de HTTP, HTTPS, uma fila de armazenamento, uma fila do barramento de serviço ou de um tópico de barramento de serviço.

O Agendador agenda [tarefas](scheduler-concepts-terms.md), mantém um histórico dos resultados de execução de tarefas que é possível analisar e de forma determinística, agenda a execução das cargas de trabalho. O WebJobs do Azure (parte da funcionalidade de Aplicações Web do Serviço de Aplicações do Azure) e outras funcionalidades de agendamento do Azure, utilizam o Agendador em segundo plano. A [API REST do Agendador](https://msdn.microsoft.com/library/mt629143.aspx) ajuda-o a gerir a comunicação para estas ações. Como tal, o Agendador suporta [agendas complexas e periodicidade avançada](scheduler-advanced-complexity.md) com mais facilidade.

Existem vários cenários que se devem à utilização do Agendador. Por exemplo:

+ _Ações de aplicação recorrente:_ recolha periódica de dados a partir do Twitter para um feed.
+ _Manutenção diária:_ eliminação diária de registos, realizar cópias de segurança e outras tarefas de manutenção. Por exemplo, um administrador pode escolher fazer uma cópia de segurança da base de dados às 1:00. todos os dias durante os próximos nove meses.

O Agendador permite-lhe criar, atualizar, eliminar, ver e gerir tarefas e [recolher tarefas](scheduler-concepts-terms.md) de forma programática utilizando scripts e através do portal.

## Consultar também

 [Conceitos, terminologia e hierarquia de entidades do Agendador do Azure](scheduler-concepts-terms.md)

 [Começar a utilizar o Agendador no portal do Azure](scheduler-get-started-portal.md)

 [Planos e faturação no Agendador do Azure](scheduler-plans-billing.md)

 [Como criar agendas complexas e periodicidade avançada com o Agendador do Azure](scheduler-advanced-complexity.md)

 [Referência da API REST do Agendador do Azure](https://msdn.microsoft.com/library/mt629143)

 [Referência de cmdlets do PowerShell do Agendador do Azure](scheduler-powershell-reference.md)

 [Elevada disponibilidade e fiabilidade do Agendador do Azure](scheduler-high-availability-reliability.md)

 [Limites, predefinições e códigos de erro do Agendador do Azure](scheduler-limits-defaults-errors.md)

 [Autenticação de saída do Agendador do Azure](scheduler-outbound-authentication.md)



<!--HONumber=ago16_HO4-->


