---
title: "Registo de segurança de dados de análise | Microsoft Docs"
description: "Saiba mais sobre como a análise de registos protege a sua privacidade e protege os seus dados."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: a33bb05d-b310-4f2c-8f76-f627e600c8e7
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2017
ms.author: magoedte
ms.openlocfilehash: 91af774560860b35913e57b49fb7a1dd59f5640f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-data-security"></a>Registo de segurança de dados de análise
A Microsoft está empenhada em proteger a sua privacidade e proteger os dados, enquanto fornece software e serviços que ajudam a gerir a infraestrutura de TI da sua organização. Reconhecemos que, quando entrust os dados para outras pessoas, essa confiança requer a segurança rigorosas. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

Proteger e a proteção de dados é uma prioridade superior na Microsoft. Contacte-nos com quaisquer perguntas, sugestões ou problemas sobre qualquer uma das seguintes informações, incluindo as nossas políticas de segurança ao [as opções de suporte do Azure](http://azure.microsoft.com/support/options/).

Este artigo explica como os dados são recolhidos, processados e protegidos pela análise de registos no Operations Management Suite (OMS). Pode utilizar os agentes para ligar ao serviço web, utilize o System Center Operations Manager para recolher dados operacionais ou obter dados de diagnóstico do Azure para utilização por análise de registos. Os dados recolhidos são enviados através da Internet utilizando a autenticação baseada em certificado de & SSL 3 para o serviço de análise de registos, que está alojado no Microsoft Azure. Dados são comprimidos pelo agente antes de ser enviada.

O serviço de análise de registos gere os dados baseados na nuvem em segurança através dos seguintes métodos:

* segregação de dados
* retenção de dados
* segurança física
* Gestão de incidentes
* Conformidade
* certificações de normas de segurança

## <a name="data-segregation"></a>segregação de dados
Dados de cliente são mantidos separados de forma lógica em cada componente em todo o serviço do OMS. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço. Cada cliente tem um blob do Azure dedicado que aloja os dados de longa duração

## <a name="data-retention"></a>Retenção de dados
Dados de pesquisa indexada do registo são armazenados e mantidos, de acordo com seu plano de preços. Para obter mais informações, consulte [preços de análise do registo](https://azure.microsoft.com/pricing/details/log-analytics/).

Microsoft elimina dados de cliente de 30 dias após a área de trabalho do OMS está fechada. Microsoft também elimina a conta de armazenamento do Azure onde residem os dados. Quando os dados de cliente for removidos, serão destruídas não unidades físicas.

A tabela seguinte lista algumas das soluções disponíveis no OMS e recolher estes exemplos os tipos de dados.

| **Solução** | **Tipos de dados** |
| --- | --- |
| Avaliação da Configuração |Dados de configuração, metadados e dados de estado |
| Planeamento da Capacidade |Dados de desempenho e metadados |
| Antimalware |Dados de configuração e metadados |
| Avaliação de Atualizações do Sistema |Dados de metadados e o Estado |
| Gestão de Registos |Definido pelo utilizador registos de eventos, os registos de eventos do Windows e/ou os registos de IIS |
| Monitorização de Alterações |Inventário de software e os metadados do serviço do Windows |
| SQL Server e de avaliação do Active Directory |Dados WMI, dados de registo, dados de desempenho e gestão dinâmica do SQL Server ver resultados |

A tabela seguinte mostra exemplos dos tipos de dados:

| **Tipo de dados** | **Campos** |
| --- | --- |
| Alerta |Alerta de nome, descrição do alerta, Timemodified, ID do problema, IsMonitorAlert, RuleId, estado de resolução do, prioridade, gravidade, categoria, proprietário, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount |
| Configuração |CustomerID AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate |
| Evento |EventId EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, número, categoria, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded <br>**Nota:** quando escreve eventos com campos personalizados no registo de eventos do Windows, OMS recolhe-los. |
| Metadados |Timemodified, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, endereço IP, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime |
| Desempenho |ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded |
| Estado |StateChangeEventId StateId, NewHealthState, OldHealthState, contexto, TimeGenerated, TimeAdded, StateId2, Timemodified, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified |

## <a name="physical-security"></a>segurança física
A análise de registos no serviço do OMS é manned pela equipa da Microsoft e todas as atividades são registadas e podem ser auditadas. O serviço é executado completamente no Azure e está em conformidade com os critérios de engenharia comuns do Azure. Pode ver detalhes sobre a segurança física dos recursos do Azure na página 18 do [descrição geral de segurança do Microsoft Azure](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf). Direitos de acesso físico a áreas de proteger são alterados dentro de um dia útil para qualquer pessoa que já não tem responsabilidade para o serviço do OMS, incluindo a transferência e terminação. Pode ler sobre a infraestrutura física global utilizamos em [Microsoft Datacenters](https://www.microsoft.com/en-us/server-cloud/cloud-os/global-datacenters.aspx).

## <a name="incident-management"></a>Gestão de incidentes
OMS tem um processo de gestão de incidentes que cumprem todos os serviços Microsoft. Para resumir, iremos:

* Utilizar um modelo de responsabilidade partilhado onde uma parte de responsabilidade de segurança pertence à Microsoft e uma parte pertence ao cliente
* Gerir incidentes de segurança do Azure
  * Iniciar uma investigação mediante a deteção de um incidente
  * Avalie o impacto e a gravidade de um incidente por um membro da equipa de resposta a incidentes na chamada. A avaliação com base na provas, poderá ou não pode resultar em mais escalonamento para a equipa de resposta de segurança.
  * Diagnosticar um incidente por especialistas de resposta de segurança para realizar a investigação forense ou técnica, identifique as estratégias de contenção, mitigação e solução. Se a equipa de segurança Deteta que dados de clientes podem tornar-se exposto até um indivíduo ilegal ou não autorizado, começa a execução paralela do processo de notificação do cliente incidente em paralelo.  
  * Stabilize e recuperar o incidente. A equipa de resposta a incidentes cria um plano de recuperação para mitigar o problema. Passos de contenção crisis como quarentena sistemas afetados podem ocorrer imediatamente e em paralelo com diagnósticos adicionais. Poderão ser planeadas mais mitigações de termo que ocorrer após ter passado o risco de imediato.  
  * Fechar o incidente e realize um mortem post. A equipa de resposta a incidentes cria um mortem post que descreve os detalhes do incidente, com a sua intenção para rever as políticas, procedimentos e processos para impedir que uma periodicidade do evento.
* Notificar os clientes de incidentes de segurança
  * Determinar o âmbito dos clientes afetados e para fornecer a qualquer pessoa que é afetado como detalhadas um aviso quanto possível
  * Crie um aviso para fornecer aos clientes com detalhadas informações suficientes para que estes possam efetuar uma investigação no respetivo fim e cumprir os compromissos que estas efetuou para os respetivos utilizadores finais enquanto não unduly atrasando o processo de notificação.
  * Confirmar e declarar o incidente, conforme necessário.
  * Notificar os clientes com uma notificação de incidente sem unreasonable atraso e em conformidade com qualquer legal ou contractual compromisso. Notificações de incidentes de segurança são entregues a uma ou mais dos administradores de um cliente por qualquer meio que seleciona da Microsoft, incluindo através de e-mail.
* Realizar a preparação de equipa e formação
  * Equipa da Microsoft é necessárias para concluir a segurança e a preparação de deteção, o que ajuda-las para identificar e comunicar problemas de segurança suspeito.  
  * O serviço Microsoft Azure a trabalhar os operadores têm obrigações de formação de adição envolvente o acesso a sistemas confidenciais que alojam dados de cliente.
  * Técnico de resposta de segurança do Microsoft recebe formação especializada para as respetivas funções

Se ocorrer a perda de quaisquer dados de cliente, iremos notificá-lo cada cliente dentro de um dia. No entanto, a perda de dados de cliente nunca foi excedido com o OMS. Além disso, vamos manter cópias dos dados que foi criados e é distribuída geograficamente.

Para obter mais informações sobre como o Microsoft responde a incidentes de segurança, consulte [na nuvem do Microsoft Azure Security Response](https://gallery.technet.microsoft.com/Azure-Security-Response-in-dd18c678/file/150826/1/Microsoft Azure Security Response in the cloud.pdf).

## <a name="compliance"></a>Conformidade
Programa de segurança e governação de informações do OMS software desenvolvimento e o serviço da equipa suporta os requisitos de negócio e em conformidade com as leis e regulamentos conforme descrito em [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/) e [compatibilidade de centro de fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx). Como OMS estabelece requisitos de segurança, identifica os controlos de segurança, gere, monitores e riscos também estão descritas não existe. Anual, vamos rever as políticas, normas, diretrizes e procedimentos.

Cada membro de equipa de desenvolvimento do OMS recebe formação de segurança da aplicação formal. Internamente, iremos utilizar um sistema de controlo de versão para o desenvolvimento de software. Cada projeto de software está protegido pelo sistema de controlo de versão.

A Microsoft tem uma equipa de segurança e conformidade supervisiona e avalia todos os serviços Microsoft. Officers de segurança de informações, certifique-se a equipa e que não estiverem associados a engenharia departamentos que desenvolver OMS. Os officers de segurança têm as seus próprios cadeia de gestão e realize avaliações independentes dos produtos e serviços para garantir a segurança e conformidade.

Placa da Microsoft do directors é notificada através de um relatório sobre todos os programas de segurança de informações na Microsoft anual.

A equipa de desenvolvimento e o serviço de software do OMS ativamente está a trabalhar com as equipas de Legal Microsoft e de conformidade e outros parceiros da indústria para adquirir certificações vários.

## <a name="certifications-and-attestations"></a>Certificações e attestations
Análise de registos do OMS cumpre os seguintes requisitos:

* [ISO/IEC 27001](http://www.iso.org/iso/home/standards/management-standards/iso27001.htm)
* [NORMA ISO/IEC 27018:2014](http://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=61498)
* [ISO 22301](https://azure.microsoft.com/en-us/blog/iso22301/)
* [Padrão de segurança do dados da indústria (em conformidade de PCI) de cartão de pagamento (PCI DSS)](https://www.microsoft.com/en-us/TrustCenter/Compliance/PCI) por Council de normas de segurança PCI.
* [O tipo de serviço organização controlos (SOC) 1 1 e certificados SOC 2 tipo 1](https://www.microsoft.com/en-us/TrustCenter/Compliance/SOC1-and-2) em conformidade
* [HIPAA e HITECH](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) para empresas que têm um contrato de associar de negócio para HIPAA
* Critérios de engenharia comuns do Windows
* Informática Fidedigna da Microsoft
* Como um serviço do Azure, os componentes que utilizados pelo OMS cumprem requisitos de conformidade do Azure. Pode ler mais em [compatibilidade de centro de fidedignidade da Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/default.aspx).

> [!NOTE]
> Em alguns certificações/attestations, análise de registos está listado em seu nome anteriores do *das informações operacionais*.
>
>


## <a name="cloud-computing-security-data-flow"></a>Fluxo de dados de segurança a informática em nuvem
O diagrama seguinte mostra uma arquitetura de segurança na nuvem como o fluxo de informações da sua empresa e como são protegido, é movido para o serviço de análise de registos, em última análise visto por si no portal do OMS. Mais informações sobre cada passo segue o diagrama.

![Imagem de recolha de dados do OMS e segurança](./media/log-analytics-security/log-analytics-security-diagram.png)

## <a name="1-sign-up-for-log-analytics-and-collect-data"></a>1. Inscreva-se e recolher dados de análise de registo
Para a sua organização enviar dados para análise de registos, configure os de agentes do Windows, os agentes em execução em máquinas virtuais do Azure ou agentes OMS para Linux. Se utilizar agentes do Operations Manager, em seguida, utilizar um Assistente de configuração na consola de operações configurá-las. Utilizadores (que podem ser, outros utilizadores individuais ou um grupo de pessoas) crie uma ou mais contas OMS (áreas de trabalho do OMS) e registar agentes utilizando um dos seguintes contas:

* [ID organizacional](../active-directory/sign-up-organization.md)
* [Conta Microsoft - Outlook, Office em direto, MSN](http://www.microsoft.com/account/default.aspx)

Uma área de trabalho do OMS é onde dados recolhidos, agregados, analisados e apresentados. Uma área de trabalho é principalmente utilizada como um meio para dados de partição e cada área de trabalho é exclusiva. Por exemplo, pode querer ter os dados de produção geridos com uma área de trabalho do OMS e os dados de teste geridos com outra área de trabalho. Áreas de trabalho também ajudam a um administrador controlar utilizador o acesso aos dados. Cada área de trabalho pode ter várias contas de utilizador associadas à mesma e, cada conta de utilizador pode aceder a várias áreas de trabalho do OMS. Criar com base na região de centro de dados de áreas de trabalho. Cada área de trabalho é replicada para outros centros de dados na região, principalmente para disponibilidade de serviço do OMS.

Para o Operations Manager, quando o Assistente de configuração concluir, cada grupo de gestão do Operations Manager estabelece uma ligação com o serviço de análise de registos. Em seguida, utilize o Assistente para adicionar computadores para escolher quais os computadores no grupo de gestão estão autorizados a enviar dados para o serviço. Para outros tipos de agente, cada estabelece ligação com segurança para o serviço do OMS.

Todas as comunicações entre sistemas ligados e o serviço de análise de registos são encriptadas.  O protocolo TLS (HTTPS) é utilizado para encriptação.  O processo do Microsoft SDL é seguido para garantir a que análise de registos está atualizada com os mais recentes avanços nas protocolos criptográficos.

Cada tipo de agente recolhe dados para análise de registos. O tipo de dados que são recolhidos é depende os tipos de soluções utilizadas. Pode ver um resumo de recolha de dados em [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md). Além disso, as informações mais detalhadas de recolha estão disponíveis para a maioria das soluções. Uma solução é um pacote de vistas predefinidas, consultas de pesquisa de registo, as regras de recolha de dados e a lógica de processamento. Apenas os administradores podem utilizar a análise de registos para importar uma solução. Depois da solução é importada, é movido para servidores de gestão do Operations Manager (se utilizado) e, em seguida, quaisquer agentes que selecionou. Posteriormente, os agentes de recolhem os dados.

## <a name="2-send-data-from-agents"></a>2. Enviar dados de agentes
Registar todos os tipos de agente com uma chave de registo e é estabelecida uma ligação segura entre o agente e o serviço de análise de registos utilizando a autenticação baseada em certificados e o SSL com a porta 443. OMS utiliza um arquivo de secreto para gerar e manter as chaves. As chaves privadas rodam todos os 90 dias e são armazenadas no Azure e são geridas pelas operações do Azure que siga strict práticas de regulamentação e conformidade.

Com o Operations Manager, registe uma área de trabalho com o serviço de análise de registos e é estabelecida uma ligação HTTPS segura entre o servidor de gestão do Operations Manager.

Para agentes do Windows em execução em máquinas virtuais do Azure, uma chave de armazenamento só de leitura é utilizada para ler eventos de diagnóstico nas tabelas do Azure.

Se qualquer agente não consegue comunicar com o serviço por qualquer motivo, os dados recolhidos são armazenados localmente na cache de temporária e o servidor de gestão tenta reenviar os dados a cada oito minutos durante duas horas. Dados em cache do agente estão protegidos pelo arquivo de credenciais do sistema operativo. Se o serviço não é possível processar os dados depois de duas horas, os agentes ficarão em fila os dados. Se a fila ficar cheia, o OMS começa a remover tipos de dados, começando com dados de desempenho. O limite de fila do agente é uma chave de registo para pode modificá-la, se necessário. Os dados recolhidos são comprimidos e enviados para o serviço, ignorando as bases de dados no local, pelo que não adiciona qualquer carga aos mesmos. Depois dos dados recolhidos são enviados, este é removido da cache.

Como descrito acima, dados de agentes são enviados através de SSL para centros de dados do Microsoft Azure. Opcionalmente, pode utilizar o ExpressRoute para fornecer segurança adicional para os dados. O ExpressRoute é uma forma de ligar diretamente ao Azure a partir da sua rede WAN existente, tal como um protocolo multi etiqueta mudança VPN (MPLS) fornecida por um fornecedor de serviço de rede. Para obter mais informações, consulte [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

## <a name="3-the-log-analytics-service-receives-and-processes-data"></a>3. O serviço de análise de registos recebe e processa dados
O serviço de análise de registos assegura que recebidos dados de uma origem fidedigna validando certificados e a integridade dos dados com a autenticação do Azure. Os dados não processados não processados, em seguida, são armazenados como um blob na [armazenamento do Microsoft Azure](../storage/common/storage-introduction.md) e não está encriptada. No entanto, cada blob storage do Azure tem um conjunto de conjunto exclusivo de chaves, que é acessível apenas a esse utilizador. O tipo de dados armazenados depende os tipos de soluções que foram importados e utilizadas para recolher dados. Em seguida, o serviço de análise de registos processa os dados não processados para o blob storage do Azure.

## <a name="4-use-log-analytics-to-access-the-data"></a>4. Utilize a análise de registos para aceder aos dados
Pode iniciar sessão análise de registos no portal do OMS através da conta institucional ou a conta Microsoft que configurou anteriormente. Todo o tráfego entre o portal do OMS e análise de registos na OMS é enviado através de um canal HTTPS seguro. Quando utilizar o portal do OMS, um ID de sessão é gerado no cliente do utilizador (web browser) e os dados são armazenados numa cache local até a sessão é terminada. Quando terminar, a cache é eliminada. Os cookies do lado do cliente, que não contêm informações de identificação pessoal, não são automaticamente removidos. Cookies de sessão estão marcados HTTPOnly e estão protegidos. Após um período de inatividade previamente determinado, foi terminada a sessão de portal do OMS.

Utilizar o portal do OMS, pode exportar dados para um ficheiro CSV e podem aceder a dados através de APIs de pesquisa. Exportação CSV está limitada a 50 000 linhas por exportar e dados de API está limitado a 5000 linhas por pesquisa.

## <a name="next-steps"></a>Passos seguintes
* [Introdução à análise de registos](log-analytics-get-started.md) para saber mais sobre a análise de registos e começar a trabalhar em minutos.
