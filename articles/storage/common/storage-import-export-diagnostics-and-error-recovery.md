---
title: "Erro de diagnóstico e recuperação para tarefas de importação/exportação do Azure | Microsoft Docs"
description: "Saiba como ativar o registo para as tarefas do serviço de importação/exportação do Microsoft Azure verboso."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Erro de diagnóstico e recuperação para tarefas de importação/exportação do Azure
Para cada unidade de processamento, o serviço de importação/exportação do Azure cria um registo de erros na conta de armazenamento associados. Também pode ativar o registo verboso, definindo o `LogLevel` propriedade `Verbose` ao chamar o [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [propriedades da tarefa de atualização](/rest/api/storageimportexport/jobs#Jobs_Update) operações.

 Por predefinição, os registos são escritos para um contentor com o nome `waimportexport`. Pode especificar um nome diferente, definindo o `DiagnosticsPath` propriedade ao chamar o `Put Job` ou `Update Job Properties` operações. Os registos são armazenados como blobs de blocos com a seguinte convenção de nomenclatura: `waies/jobname_driveid_timestamp_logtype.xml`.

 Pode obter o URI de registos para uma tarefa chamando a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operação. O URI para o registo verboso é devolvido no `VerboseLogUri` propriedade para cada unidade, enquanto o URI para o registo de erro é devolvido no `ErrorLogUri` propriedade.

Pode utilizar os dados de registo para identificar os seguintes problemas.

## <a name="drive-errors"></a>Erros de unidade

Os seguintes itens são classificados como erros de unidade:

-   Erros de acesso ou ler o ficheiro de manifesto

-   Chaves do BitLocker incorretas

-   Erros de leitura/escrita da unidade

## <a name="blob-errors"></a>Erros de blob

Os seguintes itens são classificados como erros de blob:

-   Blob incorreto ou em conflito ou nomes

-   Ficheiros em falta

-   Blob não encontrado

-   Ficheiros truncados (os ficheiros no disco são com tamanho inferior ao especificado no manifesto)

-   Ficheiro danificado conteúdo (para tarefas de importação, detetado com um erro de correspondência de soma de verificação MD5)

-   Ficheiros de metadados e a propriedade de blob danificada (detetados com um erro de correspondência de soma de verificação MD5)

-   Esquema incorreto para as propriedades de blob e/ou os ficheiros de metadados

Pode haver casos em que algumas partes de uma tarefa de importação ou exportação não concluir com êxito, enquanto a tarefa geral ainda é concluída. Neste caso, pode carregar ou transferir as peças em falta dos dados através de rede ou pode criar uma nova tarefa de transferência de dados. Consulte o [referência de ferramenta de importação/exportação do Azure](storage-import-export-tool-how-to-v1.md) para aprender a reparar os dados através de rede.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o REST API do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
