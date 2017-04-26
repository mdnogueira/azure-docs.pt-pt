---
title: Criar registo privado do Docker - Portal do Azure | Microsoft Docs
description: "Introdução à criação e gestão de registos privados de contentores Docker com o portal do Azure"
services: container-registry
documentationcenter: 
author: stevelas
manager: balans
editor: dlepow
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2017
ms.author: stevelas
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: e74c5428f0e31d9d3cf06b85aa8cefde868e9d67
ms.lasthandoff: 03/27/2017

---

# <a name="create-a-private-docker-container-registry-using-the-azure-portal"></a>Criar um registo privado de contentores Docker com o portal do Azure
Utilize o portal do Azure para criar um registo de contentores e gerir as respetivas definições. Também pode criar e gerir registos de contentores com os [comandos da CLI do Azure 2.0](container-registry-get-started-azure-cli.md) ou programaticamente com a [API REST](https://go.microsoft.com/fwlink/p/?linkid=834376) do Registo de Contentores.

Para explicações e conceitos, veja [a descrição geral](container-registry-intro.md).



## <a name="create-a-container-registry"></a>Criar um registo de contentores
1. No [portal do Azure](https://portal.azure.com), clique em **+Novo**.
2. Pesquise **registo de contentores do Azure** no marketplace.
3. Selecione **Registo de Contentores do Azure**, com a **Microsoft** como editora.
    ![Serviço de Registo de Contentores no Azure Marketplace](./media/container-registry-get-started-portal/container-registry-marketplace.png)
4. Clique em **Criar**. É apresentado o painel **Registo de Contentores do Azure**.

    ![Definições do registo de contentores](./media/container-registry-get-started-portal/container-registry-settings.png)
5. No painel **Registo de Contentores do Azure**, introduza as informações seguintes. Clique em **Criar** quando tiver concluído.

    a. **Nome do registo**: um nome de domínio de nível superior globalmente exclusivo para o seu registo específico. Neste exemplo, o nome do registo é *myRegistry01*, mas substitua por um nome exclusivo seu. O nome só pode conter letras e números.

    b. **Grupo de recursos**: selecione um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) ou escreva o nome de um grupo novo.

    c. **Localização**: selecione a localização de um datacenter do Azure onde o serviço esteja [disponível](https://azure.microsoft.com/regions/services/), como **E.U.A. Centro-Sul**.

    d. **Utilizador administrador**: se quiser, ative um utilizador administrador para aceder ao registo. Pode alterar esta definição depois de criar o registo.

    > [!IMPORTANT]
    > Para além de proporcionarem acesso através de uma conta de utilizador administrador, os registos de contentores suportam autenticação com base em principais de serviço do Azure Active Directory. Para obter mais informações e considerações, veja [Authenticate with a container registry (Autenticar num registo de contentores)](container-registry-authentication.md).


    e. **Conta de armazenamento**: utilize a predefinição para criar uma [conta de armazenamento](../storage/storage-introduction.md) ou selecione uma já existente na mesma localização. Atualmente, não há suporte para o Armazenamento Premium.


## <a name="manage-registry-settings"></a>Gerir as definições do registo
Depois de criar o registo, encontre as definições do mesmo, começando no painel **Registos de Contentores** do portal. Por exemplo, poderá precisar das definições para iniciar sessão no seu registo ou poderá querer ativar ou desativar o utilizador administrador.

1. No painel **Registos de Contentores**, clique no nome do registo.

    ![Painel Registo de Contentores](./media/container-registry-get-started-portal/container-registry-blade.png)
2. Para gerir as definições de acesso, clique em **Chave de acesso**.

    ![Acesso ao registo de contentores](./media/container-registry-get-started-portal/container-registry-access.png)
3. Tenha em conta as definições seguintes:

   * **Servidor de início de sessão** - o nome completamente qualificado que utiliza para iniciar sessão no registo. Neste exemplo, é `myregistry01.azurecr.io`.
   * **Utilizador administrador** - alterne para ativar ou desativar a conta de utilizador administrador do registo.
   * **Nome de utilizador** e **Palavra-passe** - as credenciais da conta de utilizador administrador (se ativada) que pode utilizar para iniciar sessão no registo. Opcionalmente, pode voltar a gerar as palavras-passe. São criadas duas palavras-passe, para que possa manter ligações no registo, ao utilizar uma palavra-passe enquanto volta a gerar a outra palavra-passe. Em vez disso, para autenticar com um principal de serviço, consulte [Authenticate with a private Docker container registry (Autenticar com um registo de contentor Docker privado)](container-registry-authentication.md).

## <a name="next-steps"></a>Passos seguintes
* [Push your first image using the Docker CLI (Enviar a sua primeira imagem com a CLI do Docker)](container-registry-get-started-docker-cli.md)

