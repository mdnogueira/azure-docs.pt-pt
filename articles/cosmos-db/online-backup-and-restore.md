---
title: "Cópia de segurança online e de restauro com base de dados do Azure Cosmos | Microsoft Docs"
description: "Saiba como efetuar cópia de segurança automática e restaurar uma base de dados do Azure Cosmos DB."
keywords: "cópia de segurança e restauro, cópia de segurança online"
services: cosmos-db
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 09/18/2017
ms.author: raprasa
ms.openlocfilehash: 84b26c9ff354adef3f1bc1e61f235c520b63df13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Cópia de segurança online automática e de restauro com base de dados do Azure Cosmos
BD do Azure do Cosmos demora automaticamente cópias de segurança de todos os seus dados em intervalos regulares. As cópias de segurança automáticas são executadas sem afetar o desempenho ou a disponibilidade das suas operações de base de dados. Todas as suas cópias de segurança são armazenadas em separado no outro serviço de armazenamento e as cópias de segurança global são replicadas para resiliência contra desastres inesperados regionais. As cópias de segurança automáticas destinam-se para cenários quando elimina acidentalmente o contentor de BD do Cosmos e mais tarde necessitam de recuperação de dados ou uma solução de recuperação após desastre.  

Este artigo começa com um resumo rápido de redundância de dados e disponibilidade na base de dados do Cosmos e, em seguida, descreve as cópias de segurança. 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Elevada disponibilidade com base de dados do Cosmos - um resumo
BD do cosmos foi concebido para ser [globalmente distribuída](distribute-data-globally.md) – permite-lhe dimensionar débito em várias regiões do Azure, juntamente com a política suscitada pelo departamento de ativação pós-falha e APIs multi homing transparentes. Como uma oferta de sistema da base de dados [99,99% de disponibilidade SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db), todas as escritas na base de dados do Cosmos são forma durável consolidadas para discos locais por um quórum de réplicas dentro do Centro de dados local antes de confirmar para o cliente. Tenha em atenção que a elevada disponibilidade do Cosmos DB baseia-se no armazenamento local e não depender de quaisquer tecnologias de armazenamento externo. Além disso, se a sua conta de base de dados está associada a mais do que uma região do Azure, as escritas são replicadas em outras regiões, bem como. Dimensionar os débito e acesso de dados, baixas latências, pode ter como muitas ler regiões associadas à conta da base de dados como pretender. Em cada região de leitura, os dados (replicados) de forma durável é persistente através de um conjunto de réplicas.  

Conforme ilustrado no diagrama seguinte, um único contentor de BD do Cosmos é [horizontalmente particionada](partition-data.md). "Partição" está em falta que por um círculo no diagrama seguinte, e cada partição é disponibilizada elevada disponibilidade através de um conjunto de réplicas. Esta é a distribuição local numa única região do Azure (em falta por que o eixo X). Além disso, cada partição (com o respetivo conjunto de réplica correspondente) é, então, distribuída global em várias regiões associadas à sua conta de base de dados (por exemplo, nas regiões ilustração três – EUA leste, EUA oeste e Índia Central). O "conjunto de partição" é uma globalmente distribuída que inclui entidades de várias cópias dos seus dados em cada região (em falta por que o eixo Y). Pode atribuir prioridade a regiões associadas à sua conta de base de dados e base de dados do Cosmos transparente efetuará a ativação pós-falha para a região seguinte em caso de desastre. Pode também manualmente simulam a ativação pós-falha para testar a disponibilidade de ponto a ponto da sua aplicação.  

A imagem seguinte ilustra o elevado grau de redundância de BD do Cosmos.

![Nível elevado de redundância com Cosmos DB](./media/online-backup-and-restore/redundancy.png)

![Nível elevado de redundância com Cosmos DB](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>Cópias de segurança completas automáticas, online
UPS, posso eliminar meu contentor ou a base de dados! Com base de dados do Cosmos, não apenas os dados, mas as cópias de segurança dos seus dados são também efetuada altamente redundantes e resiliente para perante desastres regionais. Estas cópias de segurança automatizadas atualmente são executadas aproximadamente a cada quatro horas e mais recentes 2 cópias de segurança são armazenadas em qualquer momento. Se os dados acidentalmente for removidos ou danificados, [contacte o suporte do Azure](https://azure.microsoft.com/support/options/) dentro de oito horas. 

As cópias de segurança são efetuadas sem afetar o desempenho ou a disponibilidade das suas operações de base de dados. Cosmos DB demora a cópia de segurança em segundo plano, sem utilizar os aprovisionamento RUs ou afetar o desempenho e sem afetar a disponibilidade da base de dados. 

Ao contrário dos seus dados são armazenados no interior do Cosmos DB, as cópias de segurança automáticas são armazenadas no serviço de armazenamento de Blobs do Azure. Para garantir o carregamento de latência baixa/eficiente, o instantâneo da sua cópia de segurança é carregado para uma instância do Blob storage do Azure na mesma região que a região de escrita atual da sua conta de base de dados de base de dados do Cosmos. Para resiliência contra desastres regionais, cada instantâneo dos dados da sua cópia de segurança no armazenamento de Blobs do Azure novamente é replicado através de armazenamento georredundante (GRS) noutra região. O diagrama seguinte mostra que o contentor de BD do Cosmos inteiro (com todos os três partições primárias nos EUA oeste, neste exemplo) de cópias de segurança numa conta do Blob Storage do Azure remota nos EUA oeste e, em seguida, GRS são replicados para EUA Leste. 

A imagem seguinte ilustra periódicas cópias de segurança completas de todas as entidades de BD do Cosmos GRS do armazenamento do Azure.

![Cópias de segurança completas periódicas de todas as entidades de BD do Cosmos GRS do armazenamento do Azure](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>Período de retenção de cópias de segurança
Conforme descrito acima, base de dados do Azure Cosmos tira instantâneos dos seus dados em quatro horas ao nível da partição. Em qualquer momento, apenas os dois últimos instantâneos são retidos. No entanto, se a coleção/base de dados é eliminado, vamos manter os instantâneos existentes para todas as partições eliminadas dentro de determinada coleção/base de dados para 30 dias.

Se pretender manter os seus próprios instantâneos, pode utilizar a exportação para a opção de JSON na base de dados do Azure Cosmos [ferramenta de migração de dados](import-data.md#export-to-json-file) para agendar cópias de segurança adicionais.

## <a name="restoring-a-database-from-an-online-backup"></a>Restaurar uma base de dados a partir da cópia de segurança online
Se elimina acidentalmente a base de dados ou uma coleção, pode [um pedido de suporte de ficheiros](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) ou [contactar o suporte do Azure](https://azure.microsoft.com/support/options/) para restaurar os dados da última cópia de segurança automática. Se tiver de restaurar a base de dados devido ao problema de Corrupção de dados (inclui casos em que os documentos numa coleção são eliminados), consulte [danos nos dados de processamento](#handling-data-corruption) conforme é necessário tomar medidas adicionais para impedir que os dados danificados substituam as cópias de segurança existentes. Para um instantâneo específico da sua cópia de segurança ser restauradas, base de dados do Cosmos requer que os dados estavam disponíveis durante o ciclo de cópia de segurança nesse instantâneo.

## <a name="handling-data-corruption"></a>Processamento de danos nos dados
BD do Azure do Cosmos mantém as últimas duas cópias de segurança de cada partição na conta de base de dados. Este modelo funciona bem quando um contentor (coleção de documentos, gráfico, tabela) ou uma base de dados sejam eliminado acidentalmente porque uma das versões último pode ser restaurada. No entanto, no caso de quando os utilizadores poderão apresentar um problema de danos de dados, base de dados do Azure Cosmos poderá ser RECONHECENDO a existência de danos de dados e é possível que os danos podem substituir as cópias de segurança existentes. Assim que for detetada corrupção, o utilizador deve eliminar o contentor danificado (coleção/gráfico/tabela) para que as cópias de segurança estão protegidas de ser substituído com dados danificados.

## <a name="next-steps"></a>Passos seguintes

Para replicar a base de dados em múltiplos centros de dados, consulte [distribuir dados globalmente com Cosmos DB](distribute-data-globally.md). 

Contactar o suporte do Azure, o ficheiro [um ticket do portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

