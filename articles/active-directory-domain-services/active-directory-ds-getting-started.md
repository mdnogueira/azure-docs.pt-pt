---
title: "Serviços de domínio do Azure Active Directory: Introdução | Microsoft Docs"
description: Ativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: maheshu
ms.openlocfilehash: 7d80049d4b6f7d57924522e3f273c42f4c887fee
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Ativar o Azure Active Directory Domain Services no portal do Azure
Este artigo mostra como ativar o Azure Active Directory Domain Services (Azure AD DS) no portal do Azure.

Para iniciar o **serviços de domínio do AD de Azure ativar** assistente, execute os seguintes passos:

1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. No painel esquerdo, clique em **novo**.
3. No **novo** , escreva **dos serviços de domínio** na barra de procura.

    ![Pesquisa para serviços de domínio](./media/getting-started/search-domain-services.png)

4. Clique para selecionar **serviços de domínio do Azure AD** da lista de sugestões de pesquisa. No **serviços de domínio do Azure AD** página, clique em de **criar** botão.

    ![Vista de serviços de domínio](./media/getting-started/domain-services-blade.png)

5. O **serviços de domínio do AD de Azure ativar** é iniciado o assistente.


## <a name="task-1-configure-basic-settings"></a>Tarefa 1: configurar as definições básicas
No **Noções básicas** página do assistente, pode especificar o nome de domínio DNS para o domínio gerido. Também pode escolher o grupo de recursos e a localização do Azure para o qual deve ser implementado domínio gerido.

![Configurar as noções básicas](./media/getting-started/domain-services-blade-basics.png)

1. Escolha o **nome de domínio DNS** para o seu domínio gerido.

   > [!NOTE]
   > **Diretrizes para selecionar um nome de domínio DNS**
   > * **Nome de domínio incorporada:** por predefinição, o assistente Especifica o nome de domínio predefinida/Built-do diretório (com um **. onmicrosoft.com** sufixo) por si. Se optar por ativar o acesso seguro de LDAP para o domínio gerido através da internet, espere problemas de criação de um registo DNS público ou obter um certificado LDAP seguro de uma AC pública para este nome de domínio. Microsoft detém o *. onmicrosoft.com* domínio e as ACs não emitirá certificados pela garantia para este domínio.
   * **Nomes de domínio personalizados:** também pode escrever um nome de domínio personalizado. Neste exemplo, o nome de domínio personalizado é *contoso100.com*.
   * **Sufixos de domínio não encaminhável:** recomendamos, geralmente, evitando um sufixo de nome de domínio não encaminháveis internos. Por exemplo, é melhor evitar a criação de um domínio com o nome de domínio DNS 'contoso'. O sufixo DNS de '.local' não é encaminhável e pode causar problemas com a resolução DNS.
   * **Restrições de prefixo do domínio:** o prefixo de nome de domínio especificado (por exemplo, *contoso100* no *contoso100.com* nome de domínio) tem de conter 15 ou menos carateres. Não é possível criar um domínio gerido com um prefixo mais de 15 carateres.
   * **Conflitos de nome de rede:** Certifique-se de que o nome de domínio DNS que escolheu para o domínio gerido ainda não existir na rede virtual. Especificamente, verifique se:
       * Já tem um domínio do Active Directory com o mesmo nome de domínio DNS na rede virtual.
       * A rede virtual onde pretende ativar o domínio gerido tem uma ligação VPN à sua rede no local. Neste cenário, certifique-se que não tiver um domínio com o mesmo nome de domínio DNS na sua rede no local.
       * Tem um serviço cloud existente com este nome na rede virtual.
    >

2. Selecione o Azure **subscrição** na qual gostaria de criar o domínio gerido.

3. Selecione o **grupo de recursos** que deve pertencer ao domínio gerido. Pode escolher uma o **criar nova** ou **utilizar existente** opções para selecionar o grupo de recursos.

4. Escolha o Azure **localização** no qual deverá ser criado um domínio gerido. No **rede** página do assistente, pode ver redes apenas virtuais que pertencem a localização que selecionou.

5. Quando tiver terminado, clique em **OK** mover para o **rede** página do assistente.


## <a name="next-step"></a>Passo seguinte
[Tarefa 2: configurar as definições de rede](active-directory-ds-getting-started-network.md)
