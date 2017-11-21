---
title: 'Databricks do Azure: Perguntas comuns e ajuda | Microsoft Docs'
description: "Obtenha respostas às perguntas comuns e informações de resolução de problemas sobre Databricks do Azure."
services: azure-databricks
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: nitinme
ms.openlocfilehash: 6bb542537ec713be272f7e58e0b247763214ef4a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="azure-databricks-preview-common-questions-and-help"></a>Pré-visualização do Azure do Databricks: Perguntas comuns e ajuda

Este artigo apresenta uma lista de consultas que poderá ter relacionadas com a Azure Databricks parte superior. Também apresenta uma lista alguns problemas comuns que poderá depare ao utilizar o Azure Databricks. Para obter mais informações sobre Databricks do Azure, consulte [Novidades Databricks do Azure?](what-is-azure-databricks.md) 

## <a name="common-questions"></a>Perguntas comuns

Esta secção lista as perguntas comuns relacionadas com o Azure Databricks.

### <a name="can-i-use-my-own-keys-for-local-encryption"></a>Pode utilizar as minhas próprias chaves de encriptação local? 
Na versão atual, utilizando as suas próprias chaves do Azure Keyvault não é suportada. 

### <a name="can-i-use-azure-vnets-with-azure-databricks"></a>Pode utilizar as VNETs do Azure com o Azure Databricks?
É criada uma nova VNET como parte do aprovisionamento Databricks do Azure. Como parte desta versão, não é possível utilizar os seus próprios VNET do Azure.

### <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>Como aceder Azure Data Lake Store a partir de um bloco de notas? 

1. No Azure Active Directory, aprovisionar um Principal de serviço e a chave de registo.
2. Atribua as permissões necessárias para o Principal de serviço no Azure Data Lake Store.
3. Para aceder a um ficheiro no Azure Data Lake Store, utilize as credenciais de Principal de serviço no bloco de notas.

Para obter mais informações, consulte [utilize Data Lake Store com o Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html#azure-data-lake-store).

## <a name="troubleshooting"></a>Resolução de problemas

Esta secção descreve como resolver problemas comuns com Databricks do Azure.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: Esta subscrição não está registada para utilizar o espaço de nomes 'Microsoft.Databricks'

**Mensagem de erro**

Esta subscrição não está registada para utilizar o espaço de nomes 'Microsoft.Databricks'. Consulte https://aka.ms/rps-not-found sobre como registar subscrições. (Código: MissingSubscriptionRegistration)

**Solução**

1. Aceda a [portal do Azure](https://portal.azure.com).
2. Clique em **subscrições**, a subscrição está a utilizar e, em seguida, clique em **fornecedores de recursos**. 
3. Na lista de fornecedores de recursos, contra **Microsoft.Databricks**, clique em **registar**. Função de contribuinte ou o proprietário tem de ter a subscrição para registar o fornecedor de recursos.


### <a name="issue-your-account-email-does-not-have-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: A conta {e-mail} não tem função de proprietário ou contribuinte no recurso de área de trabalho Databricks no portal do Azure.

**Mensagem de erro**

A conta {e-mail} não tem a função de proprietário ou contribuinte no recurso de área de trabalho Databricks no portal do Azure. Este erro também pode ocorrer se for um utilizador convidado no inquilino. Peça ao seu administrador para conceder acesso ou adicioná-o como um utilizador diretamente na área de trabalho de Databricks. 

**Solução**

Seguem-se algumas soluções para este problema:

* Para inicializar o inquilino, tem de ter sessão iniciada como um utilizador normal do inquilino, não um utilizador convidado. Também tem de ter a função de contribuinte no recurso Databricks área de trabalho. Pode garantir um acesso de utilizador do **(IAM) do controlo de acesso** separador dentro da sua área de trabalho do Azure Databricks no portal do Azure.

* Este erro também pode ocorrer se o seu nome de domínio de e-mail está atribuído a vários diretórios Active Directory. Para contornar este problema, crie um novo utilizador no Active Directory que contém a subscrição com a sua área de trabalho Databricks.

    a. No portal do Azure, aceda ao Azure Active Directory, clique em **utilizadores e grupos**, clique em **adicionar um utilizador**.

    b. Adicionar um utilizador com um `@<tenant_name>.onmicrosoft.com` de e-mail em vez de @< your_domain > e-mail. Pode encontrar < nome_de_inquilino >. onmicrosoft.com associada do seu Active Directory no **domínios personalizados** no Azure Active Directory no portal do Azure.
    
    c. Conceder este novo utilizador **contribuinte** função no recurso Databricks área de trabalho.
    
    d. Inicie sessão no portal do Azure com o novo utilizador e localize a área de trabalho Databricks.
    
    e. Inicie a área de trabalho Databricks como este utilizador.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: A conta {e-mail} não foi registada no Databricks 

**Solução**

Se não tiver criado a área de trabalho e são adicionados como um utilizador da área de trabalho, contacte a pessoa que criou a área de trabalho para adicionar estiver a utilizar a consola de administração do Azure Databricks. Para obter instruções, consulte [adicionar e gerir utilizadores](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html). Se tiver criado a área de trabalho e ainda obtiver este erro, tente clicar em "Inicializar a área de trabalho" novamente do portal do Azure.

### <a name="issue-cloud-provider-launch-failure-publicipcountlimitreached-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: Nuvem Iniciar falha no fornecedor (PublicIPCountLimitReached): foi encontrado um erro do fornecedor de nuvem ao configurar o cluster

**Mensagem de erro**

Falha no início do fornecedor de nuvem: Um erro do fornecedor de nuvem foi encontrado ao configurar o cluster. Consulte o guia de Databricks para obter mais informações. Código de erro do Azure: PublicIPCountLimitReached. Mensagem de erro do Azure: não é possível criar mais de 60 endereços IP públicos para esta subscrição nesta região.

**Solução**

Azure Databricks clusters utilizam um endereço IP público por nó. Se a sua subscrição já tiver utilizado a todos os IPs públicos, deve [pedido para aumentar a quota](https://docs.microsoft.com/en-us/azure/azure-supportability/resource-manager-core-quotas-request). Escolha **Quota** como o **tipo de problema**, **redes: ARM** como o **tipo de Quota**e pedir um aumento de quota de endereço IP público na  **Detalhes**. Por exemplo, se o limite é atualmente 60 e pretender criar um cluster de 100 nó, pedir um aumento de limite a 160.

### <a name="issue-cloud-provider-launch-failure-missingsubscriptionregistration-a-cloud-provider-error-was-encountered-while-setting-up-the-cluster"></a>Problema: Nuvem Iniciar falha no fornecedor (MissingSubscriptionRegistration): foi encontrado um erro do fornecedor de nuvem ao configurar o cluster

**Mensagem de erro**

Falha no início do fornecedor de nuvem: Um erro do fornecedor de nuvem foi encontrado ao configurar o cluster. Consulte o guia de Databricks para obter mais informações.
Código de erro do Azure: mensagem de erro do MissingSubscriptionRegistration Azure: A subscrição não está registada para utilizar o espaço de nomes 'Microsoft. Compute'. Consulte https://aka.ms/rps-not-found sobre como registar subscrições

**Solução**

1. Aceda a [portal do Azure](https://portal.azure.com).
2. Clique em **subscrições**, a subscrição está a utilizar e, em seguida, clique em **fornecedores de recursos**. 
3. Na lista de fornecedores de recursos, contra **Microsoft. Compute**, clique em **registar**. Função de contribuinte ou o proprietário tem de ter a subscrição para registar o fornecedor de recursos.

Consulte [fornecedores de recursos e tipos](../azure-resource-manager/resource-manager-supported-services.md) para obter instruções mais detalhadas.

## <a name="next-steps"></a>Passos seguintes
Para obter instruções passo a passo Criar uma fábrica de dados da versão 2, consulte os seguintes tutoriais:

- [Início rápido: Introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [O que é o Azure Databricks?](what-is-azure-databricks.md)

