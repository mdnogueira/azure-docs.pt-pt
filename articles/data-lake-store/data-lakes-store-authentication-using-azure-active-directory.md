---
title: "Autenticação no Data Lake Store utilizando o Azure Active Directory | Microsoft Docs"
description: Saiba como autenticar com o Data Lake Store utilizando o Azure Active Directory
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/11/2017
ms.author: nitinme
ms.openlocfilehash: 6cbdda98c079d25c9cce1342c79dea6e66aec3ad
ms.sourcegitcommit: d03907a25fb7f22bec6a33c9c91b877897e96197
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2017
---
# <a name="authentication-with-data-lake-store-using-azure-active-directory"></a>Autenticação com o Data Lake Store utilizando o Azure Active Directory

O Azure Data Lake Store utiliza o Azure Active Directory para autenticação. Antes de criar uma aplicação que funciona com o Azure Data Lake Store, terá de decidir como autenticar a sua aplicação no Azure Active Directory (Azure AD). 

## <a name="authentication-options"></a>Opções de autenticação

* **Autenticação de utilizador final** -credenciais do Azure de um utilizador final são utilizadas para autenticar com o Data Lake Store. A aplicação que cria para trabalhar com o Data Lake Store pede-lhe estas credenciais de utilizador. Consequentemente, este mecanismo de autenticação é *interativa* e a aplicação é executada no contexto do utilizador com sessão iniciada. Para obter mais informações e instruções, consulte [autenticação de utilizador final para o Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md).

* **Autenticação de serviço a serviço** -Utilize esta opção se pretender que uma aplicação para se autenticar com o Data Lake Store. Nestes casos, pode criar uma aplicação do Azure Active Directory (AD) e utiliza a chave da aplicação do Azure AD para autenticar com o Data Lake Store. Consequentemente, este mecanismo de autenticação é *não interativo*. Para obter mais informações e instruções, consulte [autenticação do serviço de serviço do Data Lake Store](data-lake-store-service-to-service-authenticate-using-active-directory.md).

A tabela seguinte ilustra como pelo utilizador final e mecanismos de autenticação do serviço de serviço são suportados para o Data Lake Store. Eis como ler a tabela.

* O símbolo ✔ * indica que a opção de autenticação é suportada e liga a um artigo que demonstra como utilizar a opção de autenticação. 
* O símbolo ✔ indica que a opção de autenticação é suportada. 
* As células em branco denotam que a opção de autenticação não é suportada.


|Utilize esta opção de autenticação com...                   |.NET         |Java     |PowerShell |CLI 2.0 | Python   |REST     |
|:---------------------------------------------|:------------|:--------|:----------|:-------------|:---------|:--------|
|Utilizador final (sem MFA * *)                        |   ✔ |    ✔    |    ✔      |       ✔      |    **[✔ *](data-lake-store-end-user-authenticate-python.md#end-user-authentication-without-multi-factor-authentication)**(preterido)     |    **[✔*](data-lake-store-end-user-authenticate-rest-api.md)**    |
|Utilizador final (com a MFA)                           |    **[✔*](data-lake-store-end-user-authenticate-net-sdk.md)**        |    **[✔*](data-lake-store-end-user-authenticate-java-sdk.md)**     |    ✔      |       **[✔*](data-lake-store-get-started-cli-2.0.md)**      |    **[✔*](data-lake-store-end-user-authenticate-python.md#end-user-authentication-with-multi-factor-authentication)**     |    ✔    |
|Para serviços (utilizando a chave de cliente)         |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-client-secret)** |    **[✔*](data-lake-store-service-to-service-authenticate-java.md)**    |    ✔      |       ✔      |    **[✔*](data-lake-store-service-to-service-authenticate-python.md#service-to-service-authentication-with-client-secret-for-account-management)**     |    **[✔*](data-lake-store-service-to-service-authenticate-rest-api.md)**    |
|Para serviços (utilizando o certificado de cliente) |    **[✔*](data-lake-store-service-to-service-authenticate-net-sdk.md#service-to-service-authentication-with-certificate)**        |    ✔    |    ✔      |       ✔      |    ✔     |    ✔    |

<i>* Clique o <b>✔\* </b> símbolo. É uma ligação.</i><br>
<i>* * MFA representa a autenticação multifator</i>

Consulte [cenários de autenticação do Azure Active Directory](../active-directory/develop/active-directory-authentication-scenarios.md) para obter mais informações sobre como utilizar o Azure Active Directory para autenticação.

## <a name="next-steps"></a>Passos seguintes

* [Autenticação de utilizador final](data-lake-store-end-user-authenticate-using-active-directory.md)
* [Autenticação serviço a serviço](data-lake-store-service-to-service-authenticate-using-active-directory.md)


