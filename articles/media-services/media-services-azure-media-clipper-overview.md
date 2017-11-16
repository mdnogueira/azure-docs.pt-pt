---
title: "Criar aplicações de clips com Recortador de suporte de dados do Azure | Microsoft Docs"
description: "Descrição geral do Recortador de suporte de dados do Azure, uma ferramenta para criar aplicações de clips de suporte de dados de recursos"
services: media-services
keywords: "Clip; subclip codificação; suporte de dados"
author: dbgeorge
manager: jasonsue
ms.author: dwgeo
ms.date: 11/10/2017
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 6a5bb66db889dc1c52252773e3f129ba2c3d55f1
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="create-clips-with-azure-media-clipper"></a>Criar aplicações de clips com Recortador de suporte de dados do Azure
Recortador de suporte de dados do Azure é uma biblioteca de JavaScript livre que permite aos programadores de web fornecer os seus utilizadores com uma interface para criar aplicações de clips de suporte de dados. Esta ferramenta pode ser integrada em qualquer página web e fornece APIs para carregar os recursos e submeter tarefas de recorte.

Recortador de suporte de dados do Azure permite-lhe:
- Corte a pré-ardósia e pós-cópia slate em direto arquivos 
- Componha vídeos destaques do AMS eventos em direto, arquivos em direto ou fMP4 VOD ficheiros 
- Concatenar vídeos de várias origens 
- Produzir clips resumos dos seus recursos de suporte de dados do AMS 
- Vídeos clip com exatidão da moldura 
- Gerar o manifesto de filtros dinâmicos através de existentes em direto e de recursos do VOD com exatidão do grupo de imagens (GOP) 
- Criar tarefas de codificação contra os recursos na sua conta de media services

Para pedir novas funcionalidades, fornecer ideias ou comentários, submeter para [UserVoice de Media Services do Azure](http://aka.ms/amsvoice/). Se tiver problemas específicos, questões ou localizar uma linha na equipa de quaisquer erros, remova o suporte de dados de serviços e amcinfo@microsoft.com.

## <a name="release-notes"></a>Notas de versão
A lista seguinte descreve vários problemas conhecidos e changelog para a versão mais recente do Recortador:
- [Lista de problemas conhecidos](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [Changelog](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>Suporte de browsers
Recortador de suporte de dados do Azure é compilada com tecnologias de HTML5 modernas e suporta os browsers seguintes:

- Microsoft Edge 13 +
- Internet Explorer 11 +
- Chrome 54 +
- Safari 10 +
- Firefox 50 +

> [!NOTE]
> Apenas HTML5 reprodução de fluxos de Media Services do Azure é atualmente suportada.

## <a name="language-support"></a>Suporte de idiomas
O widget Recortador está disponível nos seguintes 18 idiomas:
- Chinês (Simplificado)
- Chinês (Tradicional)
- Checo
- Neerlandês, Flemish
- Português
- Francês
- Alemão
- Húngaro
- Italiano
- Japonês
- Coreano
- Polaco
- Português (Brasil)
- Português (Portugal)
- Russo
- Espanhol
- Sueco
- Turco

## <a name="next-steps"></a>Passos seguintes
Para começar a utilizar o Recortador de suporte de dados do Azure, leia o [introdução](media-services-azure-media-clipper-getting-started.md) artigo para obter detalhes sobre como implementar o widget.