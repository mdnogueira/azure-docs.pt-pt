---
title: "Licenciamento Microsoft® uniforme transmissão em fluxo cliente transferências Kit"
description: "Saiba mais sobre como ao licenciamento do Microsoft® uniforme de transmissão em fluxo cliente transferências Kit."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: e3b488e7-8428-4c10-a072-eb3af46c82ad
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: xpouyat
ms.openlocfilehash: b5a36ac6771bef220afe29446cd56c1b65a498d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licenciamento Microsoft® uniforme transmissão em fluxo cliente transferências Kit
## <a name="overview"></a>Descrição geral
Microsoft uniforme de transmissão em fluxo cliente transferências Kit (**SSPK** para abreviar) é uma implementação de cliente transmissão em fluxo uniforme que está otimizada para o ajudar fabricantes de dispositivos incorporados, cabo e móveis operadores, fornecedores de serviços de conteúdo, fabricantes handset, os fabricantes independentes de software (ISV) e fornecedores de soluções para criar conteúdo num formato de transmissão em fluxo uniforme de produtos e serviços para a transmissão em fluxo adaptável. SSPK é uma implementação independente de plataforma e de dispositivos do cliente de transmissão em fluxo uniforme de mensagens em fila que pode ser convertidos serem por licensee para qualquer plataforma e de dispositivos. 

Incluída abaixo é uma arquitetura de alto nível e caixa IIS uniforme de transmissão em fluxo transferências Kit é a implementação de cliente de transmissão em fluxo uniforme fornecida pela Microsoft e inclui toda a lógica de núcleos para reprodução de conteúdos de transmissão em fluxo uniforme. Isto é, em seguida, convertidos serem por parceiros de uma plataforma ou dispositivo específico implementando interfaces adequadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

## <a name="description"></a>Descrição
SSPK está licenciado nos termos que disponibilizam valor empresarial excelente. Licença SSPK fornece a indústria com:

* Origem de Kit de transferências de transmissão em fluxo uniforme em C++ 
  * implementa a funcionalidade de cliente de transmissão em fluxo uniforme
  * Adiciona o formato de análise heurística de colocação em memória intermédia lógica, etc.
* APIs de aplicação de leitor 
  * interfaces de programação para interação com uma aplicação de leitor de multimédia
* Interface de (PAL) de camada de Abstração de plataforma 
  * interfaces de programação para interação com o sistema operativo (threads, sockets)
* Interface de (HAL) de camada de Abstração de hardware 
  * interfaces para interação com o hardware de um de programação / decoders V (descodificar, composição)
* Interface de gestão (DRM) de direitos digitais 
  * interfaces de programação para processamento DRM através de camada de Abstração do DRM (DAL)
  * Microsoft PlayReady transferências Kit é fornecido em separado, mas integra-se através desta interface. Para obter mais detalhes sobre o licenciamento do Microsoft PlayReady Device, clique em [aqui](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
* Exemplos de implementação 
  * implementação de PAL de exemplo para Linux
  * implementação de HAL de exemplo para GStreamer

## <a name="licensing-options"></a>Opções de Licenciamento
Microsoft uniforme de transmissão em fluxo cliente transferências Kit é disponibilizada para distribuição em dois contratos de licença distintos: um para desenvolver uniforme de transmissão em fluxo cliente provisório os produtos e outra para distribuir uniforme de transmissão em fluxo cliente Final produtos para os utilizadores finais.

* Para os fabricantes de chipset, integradores de sistemas ou independentes de software fornecedores (ISVs) que necessitam de uma origem de código transferências kit para desenvolver provisório produtos, um Microsoft uniforme de transmissão em fluxo cliente transferências Kit **licença do produto provisório** deve ser executado.
* Para os fabricantes de dispositivos ou os ISVs que necessitam de direitos de distribuição para uniforme de transmissão em fluxo cliente Final produtos para os utilizadores finais, o Microsoft uniforme de transmissão em fluxo cliente transferências Kit **Final licença do produto** deve ser executado.

### <a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Uniforme Microsoft cliente transferências licença do produto provisório do Kit de transmissão em fluxo
Com esta licença, a Microsoft oferece uma uniforme de transmissão em fluxo cliente transferências Kit e os direitos de propriedade intelectual necessários para desenvolver e distribuir uniforme produtos de provisório cliente de transmissão em fluxo para outras distribuição de dispositivo de uniforme de transmissão em fluxo cliente transferências Kit distribuir uniforme de transmissão em fluxo cliente Final produtos.

#### <a name="fee-structure"></a>Estrutura de taxa
Uma taxa de uma única licença E.U.A. $ 50 000 fornece acesso para o uniforme de transmissão em fluxo cliente transferências Kit. 

### <a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Uniforme Microsoft cliente transferências licença do produto Kit Final de transmissão em fluxo
Com esta licença, a Microsoft oferece todos os direitos de propriedade intelectual necessário para receber uniforme de transmissão em fluxo cliente provisório os produtos de outras distribuição uniforme de transmissão em fluxo cliente transferências Kit e distribuir a imagem corporativa da empresa uniforme de transmissão em fluxo cliente Final produtos para os utilizadores finais.

#### <a name="fee-structure"></a>Estrutura de taxa
O cliente uniforme de transmissão em fluxo produto Final é oferecido com um modelo de royalty como em:

* $0.10 por implementação do dispositivo enviada
* O royalty está limitado a 50 000 $ por ano
* Não existem royalty para primeiro 10 000 implementações de dispositivo por ano 

## <a name="licensing-procedure-and-sspk-access"></a>Procedimento de licenciamento e SSPK acesso
Envie um e-mail [ sspkinfo@microsoft.com ](mailto:sspkinfo@microsoft.com) para todas as consultas de licenciamento.

O [portal SSPK distribuição](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) está acessível para distribuição de provisório registada.

Distribuição provisório e Final SSPK pode submeter questões técnicas para [ smoothpk@microsoft.com ](mailto:smoothpk@microsoft.com).

## <a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Uniforme Microsoft cliente produto provisória contrato distribuição de transmissão em fluxo
* Soluções de negócio adroit, Inc.
* SA de difusão Digital avançadas
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Ltd. de tecnologias de Albis
* Alticast Corporation
* Serviços de Digital Amazon, Inc.
* Tecnologia de Arion, Inc.
* AVC suporte Software Co., Ltd.
* Cavium, Inc.
* EchoStar compra Corporation
* Enseo, Inc.
* Fluendo da A.S.
* HANDAN BroadInfoCom Co., Ltd.
* Infomir GMBH
* Inc. do Irdeto dos E.U.A.
* iWEDIA da A.S. 
* Liberty BV de serviços globais
* MediaTek Inc.
* MStar Co, Lda.
* Nintendo Co., Ltd.
* OpenTV, Inc.
* Saffron Digital limitado
* Sichuan Changhong Electric Co., Lda.
* SoftAtHome
* Sony Corporation
* Inc. de tecnologia de Tatung
* TCL Technoly Electronics (Huizhou) Co., Ltd.
* Parte superior Victory investimentos, Ltd.
* Gra Vestel Elektronik Sanayi Ticaret A.S.
* VisualOn, Inc.
* ZTE Corporation

## <a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Uniforme Microsoft distribuição de contrato do cliente produto Final de transmissão em fluxo
* SA de difusão Digital avançadas
* AirTies Kablosuz Iletism Sanayive Dis Ticaret A.S.
* Ltd. de tecnologias de Albis
* Serviços de Digital Amazon, Inc.
* Tecnologia de AmTRAN Co., Ltd.
* Arcadyan tecnologia Corporation
* Tecnologia de Arion, Inc.
* ATMACA ELEKTRONİK SAN. GRA TİC. A.Ş
* Território Sky difusão limitado
* CastPal tecnologia Inc., Shenzhen
* Compal Electronics, Inc.
* Dongguan Digital AV tecnologia Corp., Ltd.
* EchoStar compra Corporation
* Enseo, Inc.
* Filmflex filmes limitado
* Fluendo da A.S.
* Gibson inovações limitado
* Haier informações Applicantion S.R.L
* HANDAN BroadInfoCom Co., Ltd.
* Hisense internacional Co., Ltd. 
* Homecast Co., Lda.
* Hon Hai precisão da indústria Co., Ltd.
* Infomir GMBH
* Kaonmedia Co., Ltd.
* KDDI Corporation
* Nintendo Co., Ltd.
* Cor de laranja SA
* Saffron Digital limitado
* Sagemcom banda larga SAS
* Shenzhen Coship Electronics CO., Lda.
* Shenzhen Jiuzhou Electric Co., Lda.
* Shenzhen Skyworth Digital tecnologia Co., Lda.
* Sichuan Changhong Electric Co., Ltd.
* Corp. de nível empresarial Skardin
* Sky Deutschland Fernsehen GmbH & Co. KG
* SmarDTV da A.S.
* SoftAtHome
* Sony Corporation
* Limitado TCL Overseas Marketing (Offshore comercial de Macao)
* Tecnologias de entrega de Technicolor, SAS
* Ltd. Global de Tongfang
* Parte superior Victory investimentos, Ltd.
* Produtos Toshiba Lifestyle & Corporation de serviços
* Corporation de suporte de dados universal /Slovakia/ s.r.o.
* VIZIO, Inc.
* Wistron Corporation
* ZTE Corporation


## <a name="media-services-learning-paths"></a>Percursos de aprendizagem dos Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Enviar comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

