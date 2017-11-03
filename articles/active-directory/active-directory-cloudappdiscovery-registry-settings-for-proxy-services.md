---
title: "Definições de registo de deteção de aplicação para serviços do Proxy de nuvem | Microsoft Docs"
description: "O objetivo deste tópico consiste em fornecer-lhe os passos que necessários para efetuar para definir a porta necessária nos computadores que executa o agente de Cloud App Discovery."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 8d78e925-e331-40ba-904a-e4ef14260cac
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: b8584809b76d6be12a6f489f0bb819081d1803d2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-app-discovery-registry-settings-for-proxy-services"></a>Definições de registo do cloud App Discovery para serviços do Proxy
O objetivo deste tópico é indicam como efetuar para definir a porta necessária nos computadores que executa o agente de Cloud App Discovery. Por predefinição, o agente de Cloud App Discovery está configurado para utilizar apenas as portas 80 ou 443. Se estiver a planear sobre como instalar o Cloud App Discovery num ambiente com um servidor proxy que está a utilizar uma porta personalizada (nem 80 nem 443), terá de configurar os agentes para utilizar esta porta. A configuração baseiam-se uma chave de registo.

> [!TIP] 
> Veja os melhoramentos para a Cloud App Discovery agora sem agente no Azure Active Directory (Azure AD), que são melhorados por [integração com o Microsoft Cloud App Security](https://portal.cloudappsecurity.com).

## <a name="modify-the-port-used-by-the-computer-running-the-cloud-app-discovery-agent"></a>Modificar a porta utilizada pelo computador que executa o agente de Cloud App Discovery

1. Inicie o editor de registo.
  ![Executar](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy01.png)
2. Navegue para ou crie a seguinte chave de registo: **HKLM_LOCAL_MACHINE\Software\Microsoft\Cloud Discovery\Endpoint de aplicação**
3. Crie um novo **cadeia múltipla** valor chamado **portas**. 
  ![Novo](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy02.png)
4. Para abrir o **Editar cadeia múltipla** caixa de diálogo, faça duplo clique o **portas** valor.
5. No **dados do valor**, introduza os seguintes valores e adicione todas as portas personalizadas que são utilizadas pela sua organização: <br><br>
   **80** <br>
   **8080** <br>
   **8118** <br>
   **8888** <br>
   **81** <br>
   **12080** <br>
   **6999** <br>
   **30606** <br>
   **31595** <br>
   **4080** <br>
   **443** <br>
   **1110** <br><br>
   ![Editar cadeia múltipla](./media/active-directory-cloudappdiscovery-registry-settings-for-proxy-services/proxy03.png)
6. Clique em **OK** para fechar o **Editar cadeia múltipla** caixa de diálogo.

## <a name="next-steps"></a>Passos seguintes

* [Como pode detetar aplicações na nuvem não sancionadas utilizadas dentro da minha organização](active-directory-cloudappdiscovery-whatis.md) 

