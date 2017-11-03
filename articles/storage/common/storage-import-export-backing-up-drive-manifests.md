---
title: "Cópia de segurança do Azure para importar/exportar unidade manifestos | Microsoft Docs"
description: "Saiba como o seu manifestos de unidade para o serviço de importação/exportação do Microsoft Azure, uma cópia de segurança automaticamente."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Cópia de segurança unidade manifestos para tarefas de importação/exportação do Azure

Manifestos de unidade podem ser automaticamente copiadas em segurança para blobs definindo a `BackupDriveManifest` propriedade `true` no [colocar tarefa](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) ou [propriedades da tarefa de atualização](/rest/api/storageimportexport/jobs#Jobs_Update) operações de REST API. Por predefinição, os manifestos de unidade não são guardados em cópia de segurança. As cópias de segurança de manifesto de unidade são armazenadas como blobs de blocos num contentor dentro da conta de armazenamento associado ao trabalho. Por predefinição, o nome do contentor é `waimportexport`, mas pode especificar um nome diferente no `DiagnosticsPath` propriedade ao chamar o `Put Job` ou `Update Job Properties` operações. O blob de manifesto de cópia de segurança são denominados no seguinte formato: `waies/jobname_driveid_timestamp_manifest.xml`.

 Pode obter o URI de manifestos de unidade de cópia de segurança para uma tarefa chamando a [Get Job](/rest/api/storageimportexport/jobs#Jobs_Get) operação. O blob URI é devolvido no `ManifestUri` propriedade para cada unidade.

## <a name="next-steps"></a>Passos seguintes

* [Utilizar o REST API do serviço de importação/exportação](storage-import-export-using-the-rest-api.md)
