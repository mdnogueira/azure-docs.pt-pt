---
title: "Base de dados de segurança - base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como base de dados do Azure Cosmos fornece segurança de dados e proteção de base de dados para os seus dados."
keywords: "nosql da base de dados segurança, segurança de informações, segurança de dados, a encriptação de base de dados, proteção de base de dados, as políticas de segurança, segurança de teste"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: a02a6a82-3baf-405c-9355-7a00aaa1a816
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 2f0f6578b14b2fdd3807303eb94df077df92ba77
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="azure-cosmos-db-database-security"></a>Segurança de base de dados do Cosmos BD do Azure

Este artigo descreve os procedimentos de segurança da base de dados e as principais funcionalidades oferecidas pelo Azure Cosmos DB para o ajudar a evitar, detetar e responder a falhas de base de dados.
 
## <a name="whats-new-in-azure-cosmos-db-security"></a>Novidades na segurança de base de dados do Azure Cosmos?

Encriptação de Inativos está agora disponível para documentos e cópias de segurança armazenadas na base de dados do Azure Cosmos em todas as regiões do Azure. Encriptação de Inativos é aplicada automaticamente para clientes novos e existentes, estas regiões. Não é necessário configurar nada; e obter a mesma latência excelente, débito, disponibilidade e funcionalidades como antes com a vantagem de saber os seus dados seguros e segura com a encriptação de inativos.

## <a name="how-do-i-secure-my-database"></a>Como proteger a minha base de dados? 

Segurança de dados é uma responsabilidade partilhada entre si, o cliente e o seu fornecedor de bases de dados. Consoante o fornecedor de bases de dados que escolher, a quantidade de responsabilidade executados pode variar. Se escolher uma solução no local, terá de fornecer tudo da proteção de ponto final a segurança física do seu hardware - ou seja, não existem tarefas fácil. Se optar por um fornecedor de base de dados de nuvem PaaS, tais como a base de dados do Azure Cosmos, a área de preocupação diminui consideravelmente. A imagem seguinte, borrowed a partir da Microsoft [partilhado responsabilidades de informática em nuvem](https://aka.ms/sharedresponsibility) documento técnico, mostra como a sua responsabilidade diminui com um fornecedor de PaaS como base de dados do Azure Cosmos.

![Responsabilidades de fornecedor de cliente e a base de dados](./media/database-security/nosql-database-security-responsibilities.png)

O diagrama anterior mostra cloud de alto nível componentes de segurança, mas que itens precisa de preocupar com especificamente para a sua solução de base de dados? E como pode comparar soluções entre si? 

Recomendamos a lista de verificação seguinte requisitos no qual serão comparadas sistemas de base de dados:

- Segurança de rede e as definições da firewall
- Autenticação de utilizador e controlos de utilizador detalhada
- Capacidade para replicar dados globalmente para falhas regionais
- Capacidade para executar as ativações pós-falha a partir do Centro de dados de um para outro
- Replicação de dados local no Centro de dados
- Cópias de segurança automáticas dos dados
- Restauro de dados eliminadas de cópias de segurança
- Proteger e a isolar os dados confidenciais
- Monitorização de ataques
- Responder a ataques
- Capacidade para dados de georreplicação fence para cumprir as restrições de governação de dados
- Proteção física dos servidores nos centros de dados protegidos

E embora possa parecer óbvios, recentes [falhas da base de dados em grande escala](http://thehackernews.com/2017/01/mongodb-database-security.html) lembrar-na importância simples mas críticos os seguintes requisitos:
- Corrigido servidores que são mantidos atualizados
- HTTPS através da encriptação SSL/predefinido
- Contas administrativas com palavras-passe fortes

## <a name="how-does-azure-cosmos-db-secure-my-database"></a>Como a base de dados do Azure Cosmos secure minha base de dados?

Vamos observar volta a lista anterior - quantos esses requisitos de segurança de base de dados do Azure Cosmos fornece? Cada um único.

Vamos aprofundar para cada um em detalhe.

|Requisito de segurança|Abordagem de segurança da BD Cosmos do Azure|
|---|---|---|
|Segurança da rede|A utilização de uma firewall IP é a primeira camada de proteção para proteger a base de dados. BD do Cosmos do Azure suporta a política orientadas por controlos de acesso baseado em IP para o suporte de firewall de entrada. Os controlos de acesso baseado em IP são semelhantes às regras de firewall utilizadas pelos sistemas de base de dados tradicionais, mas estes são expandidos para que uma conta de base de dados de base de dados do Azure Cosmos só é acessível a partir de um conjunto de máquinas ou serviços em nuvem aprovado. <br><br>BD do Cosmos do Azure permite-lhe permitir um endereço IP específico (168.61.48.0), um intervalo IP (168.61.48.0/8) e combinações de IPs e intervalos. <br><br>Todos os pedidos provenientes de máquinas fora desta lista de permitidos são bloqueados por base de dados do Azure Cosmos. Pedidos de máquinas aprovadas e os serviços de nuvem, em seguida, tem de concluir o processo de autenticação para o controlo de acesso aos recursos.<br><br>Saiba mais em [suporte de firewall de base de dados do Azure Cosmos](firewall-support.md).|
|Autorização|BD do Cosmos do Azure utiliza o código de autenticação de mensagens com base em hash (HMAC) para autorização. <br><br>Cada pedido é colocado em hash com a chave secreta de conta e o hash codificado de base-64 subsequente é enviado com cada chamada à base de dados do Azure Cosmos. Para validar o pedido, o serviço de base de dados do Azure Cosmos utiliza a chave secreta correta e as propriedades para gerar um hash e compara o valor do pedido. Se os dois valores corresponderem, a operação está autorizada com êxito e o processamento do pedido, caso contrário, não existe uma falha de autorização e o pedido for rejeitado.<br><br>Pode utilizar tanto um [chave mestra](secure-access-to-data.md#master-keys), ou um [token de recurso](secure-access-to-data.md#resource-tokens) permitir o acesso detalhado a um recurso, tais como um documento.<br><br>Saiba mais em [proteger o acesso aos recursos da base de dados do Azure Cosmos](secure-access-to-data.md).|
|Utilizadores e permissões|Utilizar o [chave mestra](#master-key) para a conta, pode criar recursos de utilizador e de recursos de permissão por base de dados. A [token de recurso](#resource-token) está associado uma permissão numa base de dados e determina se o utilizador tem acesso (leitura / escrita, só de leitura ou sem acesso) para um recurso de aplicação na base de dados. Recursos de aplicação incluem coleções, documentos, os anexos, procedimentos armazenados, acionadores e UDFs. O token de recurso, em seguida, é utilizado durante a autenticação para fornecer ou negar o acesso ao recurso.<br><br>Saiba mais em [proteger o acesso aos recursos da base de dados do Azure Cosmos](secure-access-to-data.md).|
|Integração do Active Directory (RBAC)| Também pode fornecer acesso à conta de base de dados utilizando o controlo de acesso (IAM) no portal do Azure, conforme mostrado na captura de ecrã que se segue nesta tabela. IAM fornece controlo de acesso baseado em funções e integrado no Active Directory. Pode utilizar funções incorporadas ou funções personalizadas para indivíduos e grupos, conforme mostrado na imagem seguinte.|
|Replicação global|BD do Azure do Cosmos oferece chave na mão distribuição global, que permite-lhe replicar os dados para qualquer um dos centros de dados de todo o mundo do Azure com o clique do botão para um. Replicação global permite-lhe dimensioná-las globalmente e fornecer acesso de latência baixa aos seus dados em todo o mundo.<br><br>No contexto de segurança, replicação global forma, assegura a proteção de dados contra falhas regionais.<br><br>Saiba mais em [distribuir dados globalmente](distribute-data-globally.md).|
|Ativações pós-falha regional|Se ter replicado dados no Centro de dados mais do que uma, base de dados do Azure Cosmos automaticamente faz o sobre as operações de deve um centro de dados regionais fique offline. Pode criar uma lista prioritária de regiões de ativação pós-falha utilizando as regiões na qual os dados são replicados. <br><br>Saiba mais em [as ativações pós-falha Regional do BD Azure Cosmos](regional-failover.md).|
|Replicação local|Mesmo dentro de um único centro de dados, base de dados do Azure Cosmos automaticamente replica os dados para elevada disponibilidade que lhe confere a escolha de [níveis de consistência](consistency-levels.md). Esta ação garante uma 99,99% [SLA de disponibilidade](https://azure.microsoft.com/support/legal/sla/cosmos-db) para todas as contas de única região e todas as contas de multirregião com consistência de flexíveis e 99.999% de disponibilidade em todas as contas de multirregião base de dados de leitura.|
|Cópias de segurança online automatizadas|Bases de dados de base de dados do Cosmos do Azure são uma cópia de segurança regularmente e armazenados num arquivo georedundant. <br><br>Saiba mais em [automática cópia de segurança online e de restauro com base de dados do Azure Cosmos](online-backup-and-restore.md).|
|Restaurar eliminado os dados|As cópias de segurança online automáticas podem ser utilizadas para recuperar dados, poderá ter acidentalmente eliminado até ~ 30 dias após o evento. <br><br>Saiba mais em [automática cópia de segurança online e de restauro com base de dados do Azure Cosmos](online-backup-and-restore.md)|
|Proteger e a isolar os dados confidenciais|Todos os dados nas regiões listados no [Novidades?](#whats-new) agora são encriptados em pausa.<br><br>PII e outros dados confidenciais podem ser isolados para coleções específicas e de leitura e escrita ou acesso só de leitura pode ser limitado a utilizadores específicos.|
|Monitor de ataques|Ao utilizar [registos de atividade e o registo de auditoria](logging.md), pode monitorizar a sua conta para a atividade normal e anormal. Pode ver as operações que foram efetuadas nos seus recursos, o que iniciou a operação quando ocorreu a operação, o estado da operação e muito mais como mostrado na captura de ecrã após esta tabela.|
|Responder a ataques|Depois de contactar o suporte do Azure para reportar um ataque potencial, um processo de resposta a incidentes passo 5 é arrancou. O objetivo do processo de passo 5 é restaurar mais rapidamente possível operações de segurança normal do serviço e depois é detetado um problema e é iniciada uma investigação.<br><br>Saiba mais em [na nuvem do Microsoft Azure Security Response](https://aka.ms/securityresponsepaper).|
|Barreiras geográficas|BD do Azure do Cosmos garante a governação de dados e conformidade para regiões sovereign (por exemplo, na Alemanha, China, nos US).|
|Instalações protegidas|Dados na base de dados do Azure Cosmos são armazenados em SSDs nos centros de dados protegidos do Azure.<br><br>Saiba mais em [os datacenters globais da Microsoft](https://www.microsoft.com/en-us/cloud-platform/global-datacenters)|
|Encriptação HTTPS/SSL/TLS|Todas as interações de base de dados do Azure Cosmos do serviço de cliente são SSL/TLS 1.2 imposta. Além disso, todos os intra datacenter e em vários centros de dados a replicação está SSL/TLS 1.2 imposta.|
|Encriptação inativa|Todos os dados armazenados na base de dados do Azure Cosmos são encriptados em pausa. Saiba mais em [encriptação de base de dados do Azure Cosmos Inativos](.\database-encryption-at-rest.md)|
|Corrigido servidores|Como uma base de dados gerido, base de dados do Azure Cosmos elimina a necessidade de gerir e aplicar o patch servidores, que efetuou para si, automaticamente.|
|Contas administrativas com palavras-passe fortes|É difícil de considerar precisamos, mesmo mencionar este requisito, mas, ao contrário de algumas das nossas concorrentes, não é possível ter uma conta de administrador com nenhuma palavra-passe do BD Azure Cosmos.<br><br> Segurança através de SSL e HMAC autenticação baseada em segredo está integrada por predefinição.|
|Certificações de proteção de dados e segurança|Tem de BD do Azure do Cosmos [ISO 27001](https://www.microsoft.com/en-us/TrustCenter/Compliance/ISO-IEC-27001), [alfabetos modelo cláusulas (EUMC)](https://www.microsoft.com/en-us/TrustCenter/Compliance/EU-Model-Clauses), e [HIPAA](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA) certificações. Certificações adicionais estão em curso.|

A seguinte captura de ecrã mostra a integração do Active Directory (RBAC) utilizando o controlo de acesso (IAM) no portal do Azure: ![o controlo de acesso (IAM) no portal do Azure - demonstrar a segurança da base de dados](./media/database-security/nosql-database-security-identity-access-management-iam-rbac.png)

Captura de ecrã seguinte mostra como pode utilizar os registos de auditoria do registo e a atividade monitorizar a sua conta: ![atividade nos registos do Azure Cosmos DB](./media/database-security/nosql-database-security-application-logging.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais detalhes sobre o mestre de chaves e os tokens de recursos, consulte [proteger o acesso aos dados da base de dados do Azure Cosmos](secure-access-to-data.md).

Para obter mais detalhes sobre o registo de auditoria, consulte [registo de diagnóstico de base de dados do Azure Cosmos](logging.md).

Para obter mais detalhes sobre certificações da Microsoft, consulte [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).
