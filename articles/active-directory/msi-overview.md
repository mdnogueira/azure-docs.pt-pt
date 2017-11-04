---
title: "Gerido identidade de serviço (MSI) para o Azure Active Directory"
description: "Uma descrição geral da identidade de serviço geridas para recursos do Azure."
services: active-directory
documentationcenter: 
author: skwan
manager: mbaldwin
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 10/31/2017
ms.author: skwan
ms.openlocfilehash: 473a9e3aa3b6ad79a5f50a37c8aadc1bba79f5ae
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Gerido identidade de serviço (MSI) para recursos do Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Um desafio comum quando a criação de aplicações em nuvem como gerir as credenciais que têm de ser no seu código para autenticar a serviços em nuvem. Manter estas credenciais segura é uma tarefa importante. Idealmente, que nunca são apresentados em estações de trabalho de programador ou obterem marcadas para o controlo de origem. O Cofre de chaves do Azure fornece uma forma de armazenar com segurança as credenciais e outras chaves e segredos, mas o seu código tem de autenticar para o Cofre de chaves para recuperar. Identidade de serviço geridas (MSI) permite a resolver este problema mais simples, conferindo aos serviços do Azure uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, incluindo o Cofre de chaves, sem ter as credenciais no seu código.

## <a name="how-does-it-work"></a>Como funciona?

Ao ativar a identidade de serviço geridas num serviço do Azure, o Azure cria automaticamente uma identidade para a instância de serviço no inquilino do Azure AD utilizado pela sua subscrição do Azure.  Nos bastidores, o Azure Aprovisiona as credenciais para a identidade para a instância de serviço.  O código, em seguida, pode efetuar um pedido de local para obter os tokens de acesso para os serviços que suportam a autenticação do Azure AD.  Azure encarrega-se de a anular as credenciais utilizadas pela instância do serviço.  Se a instância de serviço for eliminada, Azure limpa automaticamente as credenciais e da identidade no Azure AD.

Eis um exemplo de como a identidade de serviço geridas funciona com máquinas virtuais do Azure.

![Exemplo de MSI da máquina virtual](./media/msi-vm-example.png)

1. O Azure Resource Manager recebe uma mensagem para ativar o MSI numa VM.
2. O Azure Resource Manager cria um Principal de serviço no Azure AD para representar a identidade da VM. O Principal de serviço é criado no inquilino do Azure AD, que é considerado fidedigno por esta subscrição.
3. O Azure Resource Manager configura os detalhes de Principal de serviço na extensão de VM do MSI da VM.  Este passo inclui a configuração de ID de cliente e o certificado utilizado pela extensão para obter os tokens de acesso do Azure AD.
4. Agora que a identidade de Principal de serviço da VM é conhecida, pode conceder acesso aos recursos do Azure.  Por exemplo, se o seu código tem de chamar o Azure Resource Manager, em seguida, é iria atribuir a VM Principal de serviço a função adequada, utilizando o controlo de acesso baseado em funções (RBAC) no Azure AD.  Se o seu código tem de chamar o Cofre de chaves, teria de conceder o acesso de código para o segredo específico ou a chave no Cofre de chaves.
5. O código em execução na VM pede um token de um ponto final local que está alojado pela extensão de VM de MSI: oauth2/http://localhost:50342/token.  O parâmetro do recurso Especifica o serviço ao qual é enviado o token. Por exemplo, se pretender que o seu código para autenticar para o Azure Resource Manager, teria de utilizar recursos = https://management.azure.com/.
6. A extensão da VM MSI utiliza o respetivo ID de cliente configurada e o certificado para pedir um token de acesso do Azure AD.  Azure AD devolve um token de acesso de Token Web JSON (JWT).
7. O código envia o token de acesso numa chamada para um serviço que suporta a autenticação do Azure AD.

Cada serviço do Azure que suporta uma identidade de serviço geridas tem o seu próprio método para o seu código obter um token de acesso. Consulte os tutoriais para cada serviço determinar o método específico para obter um token.

## <a name="try-managed-service-identity"></a>Tente a identidade de serviço geridas

Experimente um tutorial de identidade de serviço geridas para saber mais cenários ponto a ponto para aceder a diferentes recursos do Azure:
<br><br>
| Do recurso de MSI-ativado | Saiba como |
| ------- | -------- |
| VM do Azure (Windows) | [Identidade do serviço gerido de acesso do Azure Data Lake Store com uma VM do Windows](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Identidade do serviço do Gestor de acesso a recursos do Azure com uma VM do Windows gerido](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Aceder ao Azure SQL Server com uma VM do Windows geridos identidade de serviço](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Armazenamento de Azure acesso através de chave de acesso com uma identidade de serviço geridas do Windows VM](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Aceder ao Azure Storage através de SAS com uma VM do Windows geridos identidade de serviço](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Aceder a um recurso de AD não do Azure com uma identidade de serviço geridas do Windows VM e o Cofre de chaves do Azure](msi-tutorial-windows-vm-access-nonaad.md) |
| VM do Azure (Linux)   | [Aceder ao Azure Data Lake Store com uma VM com Linux geridos de identidade de serviço](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Identidade do serviço do Gestor de acesso a recursos do Azure com uma VM com Linux gerido](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Armazenamento de Azure acesso através de chave de acesso com uma identidade de serviço geridas do Linux VM](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Aceder ao Azure Storage através de SAS com uma VM com Linux geridos de identidade de serviço](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Aceder a um recurso de AD não do Azure com uma identidade de serviço geridas do Linux VM e o Cofre de chaves do Azure](msi-tutorial-linux-vm-access-nonaad.md) |
| Serviço de Aplicações do Azure  | [Utilizar identidade de serviço geridas com o App Service do Azure ou as funções do Azure](/azure/app-service/app-service-managed-service-identity) |
| Função do Azure     | [Utilizar identidade de serviço geridas com o App Service do Azure ou as funções do Azure](/azure/app-service/app-service-managed-service-identity) |

## <a name="which-azure-services-support-managed-service-identity"></a>Os serviços do Azure suportam uma identidade de serviço geridas?

Serviços do Azure que suportam a identidade de serviço geridas podem utilizar MSI para se autenticarem em serviços que suportam a autenticação do Azure AD.  Estamos no processo de integração autenticação MSI e o Azure AD através do Azure.  Verifique back frequentemente para atualizações.

### <a name="azure-services-that-support-managed-service-identity"></a>Serviços do Azure que suportam a identidade de serviço geridas

Os seguintes serviços do Azure suportam uma identidade de serviço geridas.

| Serviço | Estado | Data | Configurar | Obter um token |
| ------- | ------ | ---- | --------- | ----------- |
| Máquinas Virtuais do Azure | Pré-visualização | Setembro de 2017 | [Portal do Azure](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[CLI do Azure](msi-qs-configure-cli-windows-vm.md)<br>[Modelos Azure Resource Manager](msi-qs-configure-template-windows-vm.md) | [.NET](msi-how-to-get-access-token-using-msi.md#net)<br>[PowerShell](msi-how-to-get-access-token-using-msi.md#azure-powershell-token)<br>[Bash/Curl](msi-how-to-get-access-token-using-msi.md#bashcurl)<br>[REST](msi-how-to-get-access-token-using-msi.md#rest) |
| Serviço de Aplicações do Azure | Pré-visualização | Setembro de 2017 | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Funções do Azure | Pré-visualização | Setembro de 2017 | [Portal do Azure](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Modelo do Azure Resource Manager](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Suporte do Azure AD que a autenticação de serviços do Azure

Os seguintes serviços de suportam a autenticação do Azure AD e foi testados com serviços de cliente que utilizam a identidade de serviço geridas.

| Serviço | ID do Recurso | Estado | Data | Atribuir acesso |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://Management.Azure.com/ | Disponível | Setembro de 2017 | [Portal do Azure](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[CLI do Azure](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://Vault.Azure.NET/ | Disponível | Setembro de 2017 | |
| Azure Data Lake | https://datalake.Azure.NET/ | Disponível | Setembro de 2017 | |
| SQL do Azure | https://Database.Windows.NET/ | Disponível | Outubro de 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Quantidade de identidade de serviço geridas custos?

Identidade de serviço geridas inclui gratuito do Azure Active Directory, que é a predefinição para as subscrições do Azure.  Não há sem custos adicionais para a identidade de serviço geridas.

## <a name="support-and-feedback"></a>Suporte e comentários

Gostaríamos de ouvir da!

* Coloque questões procedimentos no Stack Overflow com a etiqueta [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Efetuar pedidos de funcionalidade ou fazer comentários sobre o [fórum de comentários do Azure AD para os programadores](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






