---
title: "Referência rápida para comandos de tarefa de importação de ferramenta de importação/exportação do Azure - v1 | Microsoft Docs"
description: "Referência de comandos de ferramenta de importação/exportação do Azure para comandos de tarefa de importação utilizadas frequentemente. Isto refere-se v1 da ferramenta de importação/exportação."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 370cf6fae7ae106e8341f65086c8b8187d335746
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="quick-reference-for-frequently-used-commands-for-import-jobs"></a>Referência rápida para comandos utilizados com frequência para tarefas de importação
Esta secção fornece que uma referência rápida para algumas utilizadas frequentemente comandos. Para utilização detalhada, consulte [preparar os discos rígidos de uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md).  

## <a name="prepare-a-hard-drive-when-data-has-already-been-copied-to-the-hard-drive"></a>Preparar um disco rígido quando os dados já foi copiados para o disco rígido
 O seguinte comando prepara um disco rígido quando os dados já foi copiados para o mesmo, mas ainda não foi encriptados com BitLocker:  
  
```  
  WAImportExport.exe PrepImport /j:9WM35C2V.jrn /id:session#1 /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /t:d /encrypt /srcdir:d:\movies\drama /dstdir:movies/drama/ /skipwrite
```    

## <a name="copy-a-single-directory-to-a-hard-drive"></a>Copiar um único diretório para um disco rígido  
 O comando seguinte copia um diretório de origem única para um disco rígido que ainda não foi encriptado com BitLocker:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
## <a name="copy-two-directories-to-a-hard-drive"></a>Copie os dois diretórios para um disco rígido  
 Para copiar dois diretórios de origem para uma unidade, utilize os seguintes comandos:  
  
 O primeiro comando Especifica o diretório de registo, a chave de conta de armazenamento, a letra de unidade de destino, `format/encrypt` requisitos e os parâmetros comuns:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:movies /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:d:\Movies /dstdir:entertainment/movies/  
```  
  
 O segundo comando Especifica o ficheiro de diário de alterações, um novo ID de sessão e as localizações de origem e de destino:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:music /srcdir:d:\Music /dstdir:entertainment/music/  
```  
  
## <a name="copy-a-large-file-to-a-hard-drive-in-a-second-copy-session"></a>Copiar um ficheiro grande para um disco rígido uma segunda sessão de cópia  
 O comando seguinte copia um único ficheiro grande para um disco rígido que foi preparado numa sessão de cópia anterior:  
  
```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:dvd /srcfile:d:\dvd\favoritemovie.vhd /dstblob:dvd/favoritemovie.vhd  
```  
  
## <a name="next-steps"></a>Passos seguintes

* [Fluxo de trabalho de amostra para preparar unidades de disco rígido para uma tarefa de importação](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
