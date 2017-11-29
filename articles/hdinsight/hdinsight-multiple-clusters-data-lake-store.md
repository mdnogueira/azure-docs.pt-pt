---
title: "Utilizar vários clusters do HDInsight com uma conta do Azure Data Lake Store - Azure | Microsoft Docs"
description: "Saiba como utilizar mais do que um cluster do HDInsight com uma única conta de Data Lake Store"
keywords: "armazenamento do hdinsight, hdfs, dados estruturados, dados não estruturados, arquivo data lake"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 1fe81286c395a529a14ba87edc26390a2bab3f90
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/29/2017
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-store-account"></a>Utilizar vários clusters do HDInsight com uma conta do Azure Data Lake Store

A partir do HDInsight versão 3.5, pode criar clusters do HDInsight com contas do Azure Data Lake Store como o sistema de ficheiros predefinido.
Data Lake Store suporta armazenamento ilimitado que torna a ideal não só para o alojamento de grandes quantidades de dados; mas também para alojar vários clusters do HDInsight que a partilha de uma única conta do Data Lake Store. Para instructionson como para criar um cluster do HDInsight com o Data Lake armazenar como o armazenamento, consulte [criar clusters do HDInsight com o Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Este artigo fornece recomendações para o Data Lake armazenam administrador para configurar um único e partilhado armazenar conta do Data Lake que podem ser utilizadas em vários **Active Directory** clusters do HDInsight. Estas recomendações aplicam-se para alojar vários clusters do Hadoop seguras, bem como não segura numa conta do Data Lake store partilhada.


## <a name="data-lake-store-file-and-folder-level-acls"></a>ACLs de nível de pasta e ficheiro de arquivo data Lake

O resto deste artigo pressupõe que tem um conhecimento boa de nível de ficheiros e pastas ACLs no Azure Data Lake Store, que é descrita em detalhe em [controlo de acesso no Azure Data Lake Store](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-store-setup-for-multiple-hdinsight-clusters"></a>Configuração do Data Lake Store para vários clusters do HDInsight
Informe-na ter uma hierarquia de pastas de dois níveis para explicar as recomendações para a utilização de clusters do HDInsight mutilple com uma conta de Data Lake Store. Considere tiver uma conta de Data Lake Store com a estrutura de pasta **/clusters/Finanças**. Com esta estrutura, todos os clusters necessários para a organização do departamento financeiro podem utilizar o /clusters/finance como a localização de armazenamento. No futura, se outra organização, diga de Marketing, pretende criar clusters com a mesma conta de Data Lake Store, pode criar clusters/marketing do HDInsight. Por agora, vamos apenas utilizar **/clusters/Finanças**.

Para ativar esta estrutura de pastas a serem utilizadas eficazmente os clusters do HDInsight, o administrador do Data Lake Store tem de atribuir as permissões adequadas, conforme descrito na tabela. As permissões mostradas na tabela correspondem às ACLs de acesso e não ACLs de predefinição. 


|Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeado | Com o nome de grupo | Permissões de grupo nomeado |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x - x  |Admin |Admin  |Principal de serviço |-x  |FINGRP   |r x         |
|/clusters | rwxr-x - x |Admin |Admin |Principal de serviço |-x  |FINGRP |r x         |
|clusters/financeiro | rwxr-x - t |Admin |FINGRP  |Principal de serviço |rwx  |-  |-     |

Na tabela,

- **administração** é o criador e o administrador da conta do Data Lake Store.
- **Principal de serviço** é o principal de serviço do Azure Active Directory (AAD) associado à conta.
- **FINGRP** é um grupo de utilizadores criado no AAD que contenha os utilizadores da organização do departamento financeiro.

Para obter instruções sobre como criar uma aplicação AAD (que também cria um Principal de serviço), consulte [criar uma aplicação AAD](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Para obter instruções sobre como criar um grupo de utilizadores no AAD, consulte [gerir grupos no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md).

Alguns pontos chaves a considerar.

- As duas nível na estrutura de pastas (**/clusters/Finanças/**) tem de ser criado e aprovisionada com as permissões adequadas ao administrador do Data Lake Store **antes** utilizando a conta de armazenamento para clusters. Esta estrutura não é criada automaticamente durante a criação de clusters.
- Exemplo acima recomenda a definir o proprietário do grupo **/clusters/Finanças** como **FINGRP** e que permite a **r x** acesso ao FINGRP para a hierarquia de pastas de toda a iniciar raiz. Isto garante que os membros de FINGRP podem navegar a estrutura da pasta a partir de raiz.
- No caso de quando principais do serviço AAD diferente pode criar clusters em **/clusters/Finanças**, o bit temporária (quando definido no **financeiro** pasta) garante que pastas criado por um Principal de serviço Não é possível eliminar por outros.
- Assim que a estrutura de pasta e as permissões estão ativadas, o processo de criação de cluster do HDInsight cria loaction um armazenamento de cluster específicos em **/clusters/Finanças/**. Por exemplo, poderia ser o armazenamento para um cluster com o nome fincluster01 **/clusters/finance/fincluster01**. A propriedade e permissões para as pastas criadas pelo cluster do HDInsight é mostrado na tabela aqui.

    |Pasta  |Permissões  |Utilizador proprietário  |Grupo proprietário  | Utilizador nomeado | Permissões de utilizador nomeado | Com o nome de grupo | Permissões de grupo nomeado |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/fincluster01 | rwxr-x--  |Principal de serviço |FINGRP  |- |-  |-   |-  | 
   


## <a name="recommendations-for-job-input-and-output-data"></a>Recomendações para dados de entrada e saídos da tarefa

Recomendamos que os dados de entrada para uma tarefa e as saídas de uma tarefa ser armazenados numa pasta fora **/clusters**. Isto garante que, mesmo se a pasta de específicas do cluster é eliminada para reclamar algum espaço de armazenamento, as entradas de tarefa e saídas ainda estão disponíveis para utilização futura. Nesse caso, certifique-se de que a hierarquia da pasta para armazenar as entradas de tarefa e saídas permite nível adequado de acesso para o Principal de serviço.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limitar em clusters de partilhar uma única conta de armazenamento

O limite no número de clusters que pode partilhar uma única conta de Data Lake Store depende a carga de trabalho a ser executada esses clusters. Ter demasiadas clusters ou cargas de trabalho pesadas muito em clusters que partilham uma conta de armazenamento pode fazer com que o armazenamento conta entrada/saída ao obter limitadas.

## <a name="support-for-default-acls"></a>Suporte para ACLs de predefinido

Ao criar um Principal de serviço com acesso de utilizador com o nome (conforme mostrado na tabela acima), recomendamos **não** adicionar o utilizador com o nome com uma ACL de predefinição. Aprovisionamento de utilizador com o nome de acesso através de resultados de ACLs de predefinição na atribuição de 770 permissões para o utilizador proprietário, grupo de proprietário e outros. Enquanto o valor predefinido 770 não requeira ausente permissões de utilizador de proprietário (7) ou o proprietário-grupo (7), demora ausente todas as permissões para outras pessoas (0). Isto resulta num problema conhecido com um determinado caso de utilização que é abordado em detalhe no [conhecidos, problemas e soluções](#known-issues-and-workarounds) secção.

## <a name="known-issues-and-workarounds"></a>Problemas conhecidos e soluções

Esta secção lista os problemas conhecidos para utilizar o HDInsight com o Data Lake Store e as respetivas soluções.

### <a name="publicly-visible-localized-yarn-resources"></a>Recursos YARN localizados publicamente visíveis

Quando é criada uma nova conta do Azure Data Lake store, o diretório de raiz é automaticamente aprovisionado com bits de permissão de acesso ACL definidas como 770. O proprietário da pasta raiz utilizador está definido para o utilizador que criou a conta (o administrador do Data Lake Store) e o grupo de proprietário está definido para o grupo principal do utilizador que criou a conta. Sem acesso é fornecido para "outros".

Estas definições são conhecidas por afetam um específico HDInsight caso de utilização capturado no [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). As submissões de tarefa poderá falhar com uma mensagem de erro semelhante a isto:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Conforme indicado na JIRA YARN anteriormente, ligado ao localizing recursos públicos, o localizer valida que todos os recursos pedidos públicos, de facto, verificando as respetivas permissões no sistema de ficheiros remoto. Qualquer LocalResource que se enquadram nessa condição é rejeitado para que a localização. A verificação de permissões, inclui acesso de leitura ao ficheiro para "outros". Este cenário não funciona out of box quando alojam clusters do HDInsight no Azure Data Lake, uma vez que o Azure Data Lake nega todos os acessos ao "outros" ao nível da pasta de raiz.

#### <a name="workaround"></a>Solução
Conjunto de leitura-permissões de execução para **outros** através da hierarquia, por exemplo, na  **/** , **/clusters** e   **/clusters/Finanças** conforme mostrado na tabela acima.

## <a name="see-also"></a>Consultar também

* [Criar um cluster do HDInsight com o Data Lake Store, como armazenamento](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)


