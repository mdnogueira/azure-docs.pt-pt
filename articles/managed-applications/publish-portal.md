---
title: "Publicar um Azure geridos aplicação através do portal | Microsoft Docs"
description: "Mostra como utilizar o Azure portal para criar um Azure geridos aplicação destina-se a membros da sua organização."
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 11/02/2017
ms.author: tomfitz
ms.openlocfilehash: 3d3c6c95763bc8eb67f092bda61bb58c7af9daf2
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/03/2017
---
# <a name="publish-a-service-catalog-application-through-azure-portal"></a>Publicar uma aplicação do catálogo de serviço através do portal do Azure

Pode utilizar o portal do Azure para publicar [geridos aplicações](overview.md) que se destinam a ser membros da sua organização. Por exemplo, um departamento de TI pode publicar aplicações geridas que garantir a conformidade com as normas organizacionais. Estas aplicações geridas estão disponíveis através do catálogo de serviço, não no Azure marketplace.

## <a name="prerequisites"></a>Pré-requisitos

Ao publicar uma aplicação gerida, especifique uma identidade para gerir os recursos. É recomendável que especificar um grupo de utilizador do Azure Active Directory. Para criar um grupo de utilizadores do Azure Active Directory, consulte [criar um grupo e adicionar membros no Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md). 

O ficheiro. zip que contém a definição de aplicação gerida deve estar disponível através de um URI. Recomendamos que carregue o ficheiro. zip para um blob de armazenamento. 

## <a name="create-managed-application-with-portal"></a>Criar aplicações geridas com o portal

1. No canto superior esquerdo, selecione **+ novo**.

   ![Novo serviço](./media/publish-portal/new.png)

1. Procurar **catálogo de serviço**.

1. Nos resultados, desloque-se até encontrar **a definição de aplicações geridas do serviço catálogo**. Selecione-o.

   ![Procurar definições de aplicações geridas](./media/publish-portal/select-managed-apps-definition.png)

1. Selecione **criar** para iniciar o processo de criação de definição da aplicação gerida.

   ![Criar definição de aplicações geridas](./media/publish-portal/create-definition.png)

1. Fornece valores para o nome, nome a apresentar, descrição, localização, subscrição e grupo de recursos. Para o ficheiro de pacote URI, forneça o caminho para o ficheiro zip que criou.

   ![Indique os valores](./media/publish-portal/fill-application-values.png)

1. Quando chegar à secção de autenticação e o nível de bloqueio, selecione **adicionar autorização**.

   ![Adicionar autorização](./media/publish-portal/add-authorization.png)

1. Selecione um grupo do Azure Active Directory para gerir os recursos e selecione **OK**.

   ![Adicionar grupo de autorização](./media/publish-portal/add-auth-group.png)

1. Quando tiver sido fornecido a todos os valores, selecione **criar**.

   ![criar aplicações geridas](./media/publish-portal/create-app.png)

## <a name="next-steps"></a>Passos seguintes

* Para uma introdução para aplicações geridas, consulte [descrição geral de aplicações gerido](overview.md).
* Para obter exemplos de aplicações geridas, consulte [projetos de exemplo para o Azure geridos aplicações](sample-projects.md).
* Para obter informações sobre a publicação de aplicações geridas no Azure Marketplace, consulte [Azure geridos aplicações no mercado](publish-marketplace-app.md).
* Para saber como criar um ficheiro de definição de IU para uma aplicação gerida, consulte [começar com CreateUiDefinition](create-uidefinition-overview.md).