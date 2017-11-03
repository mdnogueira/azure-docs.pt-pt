---
title: "Encriptação de base de dados Inativos: base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como base de dados do Azure Cosmos fornece encriptação predefinido de todos os dados."
services: cosmos-db
author: voellm
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 99725c52-d7ca-4bfa-888b-19b1569754d3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: voellm
ms.openlocfilehash: d8967d4504a8ccabb444c7f3d5635e2d00f287c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-database-encryption-at-rest"></a>Encriptação de base de dados do Cosmos DB do Azure Inativos

Encriptação de Inativos é uma expressão que geralmente refere-se para a encriptação de dados nos dispositivos de armazenamento não volátil, tais como unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs). Cosmos DB armazena as suas bases de dados primárias em SSDs. Os anexos de suporte de dados e as cópias de segurança são armazenadas no Blob storage do Azure, que geralmente é feito por HDDs. Com o lançamento da encriptação de Inativos para Cosmos DB, todas as suas bases de dados, anexos de suporte de dados e as cópias de segurança são encriptadas. Os dados agora são encriptados em trânsito (através da rede) e inativos (armazenamento não volátil), dando-lhe encriptação ponto a ponto.

Como um serviço de PaaS, Cosmos DB é muito fácil de utilizar. Porque todos os dados de utilizador armazenados na base de dados do Cosmos são encriptados em descanso e em transporte, não tem de efetuar qualquer ação. Outra forma coloque isto é que a encriptação de Inativos é "em" por predefinição. Não existem nenhum controlos para ativá-la ou desativar. Podemos fornecer esta funcionalidade, enquanto que continuam a corresponder aos nossos [SLA de disponibilidade e desempenho](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="implement-encryption-at-rest"></a>Implementar a encriptação de Inativos

Encriptação de Inativos é implementada através da utilização de uma série de tecnologias de segurança, incluindo sistemas de armazenamento de chaves segura, redes encriptados e APIs de criptografia. Sistemas que desencriptar e processam os dados têm de comunicar com sistemas que gerir chaves. O diagrama mostra como armazenamento dos dados encriptados e a gestão de chaves são separadas. 

![Diagrama de design](./media/database-encryption-at-rest/design-diagram.png)

O fluxo básico de um pedido de utilizador é o seguinte:
- A conta de base de dados do utilizador é efetuada pronta e chaves de armazenamento da mesma são obtidas através de um pedido para o fornecedor de recursos do serviço de gestão.
- Um utilizador cria uma ligação à base de dados do Cosmos através de transporte HTTPS/secure. (Os SDKs abstrair os detalhes.)
- O utilizador envia um documento JSON armazenados através da ligação segura criada anteriormente.
- O documento JSON está indexado, a menos que o utilizador foi desativada a indexação.
- Ambos os documentos e o índice de dados JSON são escritos para proteger o armazenamento.
- Periodicamente, os dados são ler a partir do armazenamento seguro e cópias de segurança para o arquivo de Blob do Azure encriptado.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>P: quanto mais armazenamento do Azure custo se estiver ativada a encriptação do serviço de armazenamento?
R: não há sem custos adicionais.

### <a name="q-who-manages-the-encryption-keys"></a>P: quem gere as chaves de encriptação?
R: as chaves são geridas pela Microsoft.

### <a name="q-how-often-are-encryption-keys-rotated"></a>P: frequência rodam chaves de encriptação?
R: Microsoft tem um conjunto de internas diretrizes para rotação chave de encriptação, o que se segue a BD do Cosmos. Não são publicadas as diretrizes específicas. Microsoft publicar o [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), que é utilizado como um subconjunto de orientação interno e tem as melhores práticas útil para programadores.

### <a name="q-can-i-use-my-own-encryption-keys"></a>P: Posso utilizar o meus próprio chaves de encriptação?
R: cosmos DB é um serviço de PaaS e iremos rígido trabalhado para manter o serviço fáceis de utilizar. Tiver Detetámos que esta questão, muitas vezes, é pedido como uma pergunta de proxy para satisfazer um requisito de conformidade, como PCI-DSS. Como parte da criação desta funcionalidade, iremos trabalhou com auditores de conformidade para garantir que os clientes que utilizam Cosmos DB satisfazem os requisitos do seu sem a necessidade de gerir chaves próprios.
Como resultado, estamos atualmente não oferecem aos utilizadores a opção de burden próprios com gestão de chaves.

### <a name="q-what-regions-have-encryption-turned-on"></a>P: quais regiões tem a encriptação ativada?
R: regiões do Azure Cosmos DB todos os tem a encriptação ativada para todos os dados de utilizador.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>P: encriptação afeta a latência de desempenho e débito SLAs?
R: não há nenhum impacto ou alterações ao nível de desempenho SLAs agora que a encriptação de Inativos está ativada para todas as contas existentes e novas. Pode ler mais sobre o [SLA para a base de dados do Cosmos](https://azure.microsoft.com/support/legal/sla/cosmos-db) página para ver as garantias mais recentes.

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>P: o emulador local suporta a encriptação de Inativos?
R: o emulador é uma ferramenta de programador/teste autónomo e não utiliza os serviços de gestão de chaves que o serviço de base de dados do Cosmos gerido utiliza. É a nossa recomendação para ativar o BitLocker em unidades onde estão a armazenar dados de teste de emulador confidenciais. O [emulador suporta alterar o diretório de dados predefinido](local-emulator.md) , bem como a utilização de uma localização conhecida.

## <a name="next-steps"></a>Passos seguintes

Para obter uma descrição geral de segurança de BD do Cosmos e melhoramentos mais recentes, consulte [segurança da base de dados de base de dados do Azure Cosmos](database-security.md).
Para mais informações sobre certificações da Microsoft, consulte o [Centro de fidedignidade do Azure](https://azure.microsoft.com/en-us/support/trust-center/).
