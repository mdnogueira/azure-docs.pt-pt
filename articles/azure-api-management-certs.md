---
title: "Carregar um certificado da API de gestão do Azure | Microsoft Docs"
description: "Saiba como carregar o certificado da API de gestão de athe para o Portal clássico do Azure."
services: cloud-services
documentationcenter: .net
author: Thraka
manager: timlt
editor: 
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: na
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: adegeo
ms.openlocfilehash: 89c08f2d1e0e244503e3fc4355cad0b4391618e1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="upload-an-azure-management-api-management-certificate"></a>Carregar um certificado de gestão de API de gestão do Azure
Certificados de gestão permitem-lhe autenticar com o modelo de implementação clássico fornecido pelo Azure. Muitos programas e ferramentas (tais como o Visual Studio ou o SDK do Azure) utilizam estes certificados para automatizar a configuração e implementação de vários serviços do Azure. 

> [!WARNING]
> Tenha cuidado! Estes tipos de certificados permite que qualquer pessoa que efetua a autenticação com os mesmos para gerir a subscrição que se encontram associados.
>
>

Se quiser obter mais informações sobre os certificados do Azure (incluindo a criar um certificado autoassinado), consulte o artigo [descrição geral de certificados para serviços de nuvem do Azure](cloud-services/cloud-services-certs-create.md#what-are-management-certificates).

Também pode utilizar [do Azure Active Directory](https://azure.microsoft.com/en-us/services/active-directory/) para autenticar o código de cliente para fins de automatização.

**Nota:** tem de ser um coadministrador na subscrição executar quaisquer operações em certificados de gestão. [Saiba mais](https://go.microsoft.com/fwlink/?linkid=849300) sobre como adicionar ou remover Coadministradores do novo Portal do Azure 

## <a name="upload-a-management-certificate"></a>Carregar um certificado de gestão
Depois de ter um certificado de gestão criado, (ficheiro. cer com apenas a chave pública) pode carregá-lo no portal. Quando o certificado está disponível no portal, qualquer pessoa com um certificado correspondente (chave privada) pode ligar através da API de gestão e aceder aos recursos da subscrição associada.

1. Inicie sessão no [Portal do Azure](http://portal.azure.com).
2. Clique em **mais serviços** na lista de serviço do Azure na parte inferior, em seguida, selecione **subscrições** no _geral_ grupo de serviço.

    ![Menu de subscrição](./media/azure-api-management-certs/subscriptions_menu.png)

3. Certifique-se de que seleciona a subscrição correta que pretende associar o certificado.     
4. Depois de selecionar a subscrição correta, prima **certificados de gestão** no _definições_ grupo.

    ![Definições](./media/azure-api-management-certs/mgmtcerts_menu.png)

5. Prima a **carregar** botão.

    ![Carregar a página de certificados](./media/azure-api-management-certs/certificates_page.png)
6. Preencha as informações de caixa de diálogo e prima **carregar**.

    ![Definições](./media/azure-api-management-certs/certificate_details.png)

## <a name="next-steps"></a>Passos seguintes
Agora que tem um certificado de gestão associado a uma subscrição, pode (depois de instalar o certificado correspondente localmente) através de programação ligar para o [REST API do modelo de implementação clássica](https://msdn.microsoft.com/library/azure/mt420159.aspx) e automatizar os vários recursos do Azure que também estão associados essa subscrição.
