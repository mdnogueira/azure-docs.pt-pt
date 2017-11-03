---
title: "As instruções de instalação para as ferramentas do Azure Stream Analytics para o Visual Studio | Microsoft Docs"
description: "Instruções de instalação para as ferramentas do Azure Stream Analytics para o Visual Studio"
keywords: visual studio
documentationcenter: 
services: stream-analytics
author: su-jie
manager: 
editor: 
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/19/2017
ms.author: sujie
ms.openlocfilehash: 80ce672ae91231e432f7ac9da49df29bb03efeca
ms.sourcegitcommit: 963e0a2171c32903617d883bb1130c7c9189d730
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/20/2017
---
# <a name="installation-instructions-for-stream-analytics-tools-for-visual-studio"></a>Instruções de instalação para as ferramentas de Stream Analytics para o Visual Studio
Ferramentas do Azure Stream Analytics suportam agora a Visual Studio 2017. o, 2015 ou 2013. Neste documento, introduzirmos como instalar e desinstalar as ferramentas.

Saiba como utilizar [ferramentas do Stream Analytics para o Visual Studio](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="install"></a>Instalar
### <a name="visual-studio-2017"></a>Visual Studio 2017
* Transferir [Visual Studio 2017, (15.3 ou superior)](https://www.visualstudio.com/). Enterprise (Ultimate/Premium), Professional e Community são suportadas as edições. Não é suportada a edição Express. 
* As ferramentas de análise de fluxo fazem parte do **programação do Azure** e **armazenamento de dados e processamento** cargas de trabalho no Visual Studio 2017. Ative uma das cargas de trabalho duas como parte da instalação do Visual Studio.

Ativar o **armazenamento de dados e processamento** carga de trabalho conforme mostrado:

![Carga de trabalho de dados, processamento e armazenamento](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-01.png)

Ativar o **programação do Azure** carga de trabalho conforme mostrado:

![Carga de trabalho de desenvolvimento do Azure](./media/stream-analytics-tools-for-vs/stream-analytics-tools-for-vs-2017-install-02.png)


### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
* Instale Visual Studio 2015 ou Visual Studio 2013 atualização 4. Enterprise (Ultimate/Premium), Professional e Community são suportadas as edições. Não é suportada a edição Express. 
* Instalar o Microsoft Azure SDK para .NET versão 2.7.1 ou superior, utilizando o [instalador de plataforma Web](http://www.microsoft.com/web/downloads/platform.aspx).
* Instalar [ferramentas do Azure Stream Analytics para o Visual Studio](http://aka.ms/asatoolsvs).



## <a name="update"></a>Atualizar

### <a name="visual-studio-2017"></a>Visual Studio 2017
O lembrete de versão nova aparece na notificação do Visual Studio. 

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
As ferramentas de Stream Analytics instaladas para o Visual Studio verifica a existência de novas versões automaticamente. Siga as instruções na janela de pop-up para instalar a versão mais recente. 


## <a name="uninstall"></a>Desinstalar

### <a name="visual-studio-2017"></a>Visual Studio 2017
Faça duplo clique o instalador do Visual Studio e selecione **modificar**. Limpar o **do Azure Data Lake e as ferramentas de análise de fluxo** caixa de verificação a partir de **processamento e armazenamento de dados** carga de trabalho ou o **programação do Azure** carga de trabalho.

### <a name="visual-studio-2013-2015"></a>Visual Studio 2013, 2015
Aceda ao painel de controlo e desinstalar **Microsoft Azure Data Lake e Stream Analytics tools para Visual Studio**.





