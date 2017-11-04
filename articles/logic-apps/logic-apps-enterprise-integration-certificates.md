---
title: Utilizar certificados com Enterprise Integration Pack | Microsoft Docs
description: "Saiba como utilizar certificados com o pacote de integração empresarial | Aplicações lógicas do Azure"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: cgronlun
ms.assetid: 4cbffd85-fe8d-4dde-aa5b-24108a7caa7d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 0570aab14283b38f9efcc50636f0c0c1c8e3ed13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="learn-about-certificates-and-enterprise-integration-pack"></a>Mais informações sobre certificados e o Enterprise Integration Pack
## <a name="overview"></a>Descrição geral
Integração empresarial com utiliza certificados para proteger as comunicações de B2B. Pode utilizar dois tipos de certificados nas suas aplicações de integração do enterprise:

* Certificados públicos, tem de ser adquiridos de uma autoridade de certificação (AC).
* Certificados privados, pode emitir por si. Estes certificados são, por vezes, referidos como certificados autoassinados.

## <a name="what-are-certificates"></a>Quais são os certificados?
Os certificados são digitais documentos que verifique a identidade dos participantes eletrónicas comunicações e que também protege as comunicações eletrónicas.

## <a name="why-use-certificates"></a>Porquê utilizar certificados?
Por vezes, comunicações B2B têm de ser mantidas confidenciais. Integração empresarial com utiliza certificados para proteger estas comunicações de duas formas:

* Ao encriptar o conteúdo de mensagens em fila
* Ao assinar digitalmente mensagens  

## <a name="upload-a-public-certificate"></a>Carregar um certificado público

Para utilizar um *certificado público* nas suas logic apps com capacidades de B2B, terá primeiro de carregar o certificado à sua conta de integração.  

Depois de carregar um certificado, fica disponível para o ajudar a proteger as mensagens B2B quando definir as respetivas propriedades no [contratos](logic-apps-enterprise-integration-agreements.md) que criar.  

Eis os passos detalhados para carregar os certificados públicos na sua conta de integração, depois de iniciar sessão no portal do Azure:

1. Selecione **mais serviços** e introduza **integração** na caixa de pesquisa de filtro. Selecione **contas de automatização** na lista de resultados     
![Selecione procurar](media/logic-apps-enterprise-integration-certificates/overview-1.png)  
2. Selecione a conta de integração para o qual pretende adicionar o certificado.  
![Selecione a conta de integração para o qual pretende adicionar o certificado](media/logic-apps-enterprise-integration-certificates/overview-3.png)  
3. Selecione o **certificados** mosaico.  
![Selecione o mosaico de certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)
4. No **certificados** painel que se abre, selecione o **adicionar** botão.   
![Selecione o botão de adição](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
5. Introduza um **nome** para o certificado e, em seguida, selecione o certificado escreva como **pública** na lista pendente.  
6. Selecione o ícone de pasta no lado direito do **certificado** caixa de texto. Quando abre o Seletor de ficheiros, localize e selecione o ficheiro de certificado que pretende carregar para a sua conta de integração.
7. Selecione o certificado e, em seguida, selecione **OK** no Seletor de ficheiros. Isto valida e carrega o certificado para a sua conta de integração.
8. Por fim, fazer uma cópia no **adicionar certificado** painel, selecione o **OK** botão.  
![Selecione o botão OK](media/logic-apps-enterprise-integration-certificates/certificate-3.png)  
9. Selecione o **certificados** mosaico. Deverá ver o certificado recentemente adicionado.  
![Ver o novo certificado](media/logic-apps-enterprise-integration-certificates/certificate-4.png)  

## <a name="upload-a-private-certificate"></a>Carregar um certificado privado

Para utilizar um *certificado privado* nas suas logic apps com B2B capacidades, pode carregar um certificado privado à sua conta de integração, efetuando os passos seguintes

1. [Carregar a chave privada para o Cofre de chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de chaves") e fornecer um **nome da chave** 
   
   > [!TIP]
   > Deve autorizar as Logic Apps para executar operações no Cofre de chaves. Pode conceder acesso para o principal de serviço de aplicações lógicas utilizando o seguinte comando do PowerShell:`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`  
   > 
   > 

Depois de ter sido passo anterior, adicione um certificado privado à conta de integração.

Seguem-se os passos detalhados para carregar os certificados privados na sua conta de integração, depois de iniciar sessão no portal do Azure:  
 
1. Selecione a conta de integração para o qual pretende adicionar o certificado e selecione o **certificados** mosaico.  
![Selecione o mosaico de certificados](media/logic-apps-enterprise-integration-certificates/certificate-1.png)  
2. No **certificados** painel que se abre, selecione o **adicionar** botão.   
![Selecione o botão de adição](media/logic-apps-enterprise-integration-certificates/certificate-2.png)
3. Introduza um **nome** de certificado e selecione o certificado escreva como **privada** na lista pendente.   
4. Selecione o ícone de pasta no lado direito do **certificado** caixa de texto. Quando abre o Seletor de ficheiros, localize o certificado público correspondente que pretende carregar para a sua conta de integração.   
   
   > [!Note]
   > Ao adicionar um certificado privado é importante adicionar o certificado público correspondente a mostrar no [contratos AS2](logic-apps-enterprise-integration-as2.md) receber e enviar as definições de assinatura e encriptação de mensagens.
   > 
   >   

5. Selecione o **grupo de recursos**, **Cofre de chaves**, **nome da chave** e selecione o **OK** botão.  
![Adicionar certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-1.png)  
6. Selecione o **certificados** mosaico. Deverá ver o certificado recentemente adicionado.
![Ver o novo certificado](media/logic-apps-enterprise-integration-certificates/privatecertificate-2.png)  



* [Criar um contrato de B2B](logic-apps-enterprise-integration-agreements.md)  
* [Saiba mais sobre o Cofre de chaves](../key-vault/key-vault-get-started.md "Saiba mais sobre o Cofre de chaves")  

