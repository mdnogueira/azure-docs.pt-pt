---
title: Guia para programadores do Cofre de chaves do Azure
description: "Os programadores podem utilizar o Cofre de chaves do Azure para gerir as chaves criptográficas dentro do ambiente do Microsoft Azure."
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: bruceper
ms.openlocfilehash: 8d617726a4ee9335728ab82104efbd845e3b0d05
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="azure-key-vault-developers-guide"></a>Guia para programadores do Cofre de chaves do Azure

O Cofre de chaves permite-lhe aceder de forma segura as informações confidenciais dentro das suas aplicações:

- As chaves e segredos estão protegidos sem ter de escrever o código por si e conseguir facilmente utilizá-los a partir das suas aplicações.
- Conseguir têm os seus clientes próprias e gerir as suas próprias chaves para pode concentrar-se em fornecer as principais funcionalidades de software. Desta forma, as aplicações não serão proprietários, a responsabilidade ou um potencial encargo para chaves de inquilino dos clientes e segredos.
- A aplicação pode utilizar as chaves para assinatura e encriptação ainda mantém a gestão de chaves externas à sua aplicação, permitindo que a solução seja adequada como uma aplicação distribuída geograficamente.
- Na versão de Setembro de 2016 do Cofre de chaves, as aplicações podem agora utilizar o Cofre de chaves [certificados](https://docs.microsoft.com/rest/api/keyvault/certificate-operations). Para obter mais informações, consulte [sobre certificados, chaves e segredos](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Para obter informações mais gerais sobre o Cofre de chaves do Azure, consulte [que é o Cofre de chave](key-vault-whatis.md).

## <a name="public-previews"></a>Pré-visualizações públicos

Periodicamente, Lançamos pré-visualização pública de uma funcionalidade nova do Cofre de chaves. Experimente estas e informe-na sua opinião através de azurekeyvault@microsoft.com, endereço de correio eletrónico nosso comentários.

### <a name="storage-account-keys---july-10-2017"></a>Chaves de conta de armazenamento - 10 de Julho de 2017

>[!NOTE]
>Para esta atualização do Cofre de chaves do Azure apenas o **chaves de conta de armazenamento** funcionalidade está em pré-visualização.

Esta pré-visualização inclui a funcionalidade de chaves de conta de armazenamento novo, disponível através destas interfaces; [.NET / C #](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) e [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Para obter mais informações sobre a nova funcionalidade de chaves de conta de armazenamento, consulte [descrição geral do chaves de conta de armazenamento do Cofre de chaves do Azure](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Vídeos

Este vídeo mostra como criar o seu próprio Cofre de chaves e como utilizá-la a partir da aplicação de exemplo de 'Hello Cofre de chaves'.

- [Programadores do Cofre de chaves - guia de introdução](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Recursos mencionados acima vídeo:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de exemplo do Cofre de chaves do Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Criar e gerir cofres de chaves

O Cofre de chaves do Azure fornece uma forma de armazenar com segurança as credenciais e outras chaves e segredos, mas o seu código tem de autenticar para o Cofre de chaves para recuperar. Identidade de serviço geridas (MSI) permite a resolver este problema mais simples, conferindo aos serviços do Azure uma identidade gerida automaticamente no Azure Active Directory (Azure AD). Pode utilizar esta identidade para autenticar a qualquer serviço que suporta a autenticação do Azure AD, incluindo o Cofre de chaves, sem ter as credenciais no seu código. 

Para obter mais informações sobre MSI, consulte [geridos serviço de identidade (MSI) para recursos do Azure](https://docs.microsoft.com/azure/active-directory/msi-overview).

Para obter mais informações sobre como trabalhar com o AAD, consulte [integrar aplicações com o Azure Active Directory](/active-directory/develop/active-directory-integrating-applications).

Antes de trabalhar com as chaves, os segredos ou certificados no seu Cofre de chaves, irá criar e gerir o seu Cofre de chaves através da CLI, do PowerShell, modelos do Resource Manager ou REST, conforme descrito nos seguintes artigos:

- [Criar e gerir cofres de chave com o CLI](key-vault-manage-with-cli2.md)
- [Criar e gerir cofres de chave com o PowerShell](key-vault-get-started.md)
- [Criar um cofre de chaves e adicionar um segredo através de um modelo Azure Resource Manager](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Criar e gerir cofres de chave com REST](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Codificação com o Cofre de chaves

O sistema de gestão do Cofre de chaves para programadores é composta por várias interfaces. Esta secção contém ligações para todos os idiomas, bem como algumas exampls de código. 

### <a name="supported-programming-and-scripting-languages"></a>Programação e scripting idiomas suportados

#### <a name="rest"></a>REST

Todos os seus recursos do Cofre de chaves estão acessíveis através da interface REST; cofres de chaves, segredos, etc. 

[Referência de API de REST do Cofre de chaves](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[Refence .NET API para o Cofre de chaves](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Para obter mais informações sobre a versão 2. x do .NET SDK, consulte o [notas de versão](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK para o Cofre de chaves](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

No Node.js, a API de gestão do Cofre de chaves e o objeto Cofre de chaves de API são separados. O seguinte artigo de descrição geral dá-lhe acesso a ambos. 

[Módulos do Cofre de chaves do Azure para Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Bibliotecas de Cofre de chaves do Azure para Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>CLI do Azure 2

[CLI do Azure para o Cofre de chaves](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell para o Cofre de chaves](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Guias de introdução rápidos

- [Criar Cofre de chaves](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Introdução ao Cofre de chaves no Node.js](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Exemplos de código

Para obter exemplos concluídos com o Cofre de chaves com as suas aplicações, consulte:

- [Exemplos de código do Cofre de chaves do Azure](http://www.microsoft.com/download/details.aspx?id=45343) -exemplo de aplicação .NET *HelloKeyVault* e um exemplo de serviço web do Azure. 
- [Utilizar o Cofre de chaves do Azure de uma aplicação Web](key-vault-use-from-web-application.md) -tutorial para o ajudar a saber como utilizar o Cofre de chaves do Azure a partir de uma aplicação web no Azure. 

## <a name="how-tos"></a>Procedimentos

Os seguintes cenários e artigos fornecem orientações específicas de tarefas para trabalhar com o Cofre de chaves do Azure:

- [ID de inquilino do Cofre de chaves de alteração depois de subscrição mover](key-vault-subscription-move-fix.md) - quando mover inquilino A sua subscrição do Azure ao inquilino B, os cofres de chaves existentes não estão acessíveis de principais (utilizadores e aplicações) no inquilino correção B. através deste guia.
- [Aceder ao Cofre de chaves protegidos por firewall](key-vault-access-behind-firewall.md) - para um cofre de chaves, a aplicação de cliente do Cofre de chaves tem de ser capazes de aceder a vários pontos finais para diversas funcionalidades de acesso.
- [Como gerar e Transfer HSM-Protected chaves para o Cofre de chaves do Azure](key-vault-hsm-protected-keys.md) -Isto ajudará a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para utilizar com o Cofre de chaves do Azure.
- [Como passar valores seguros (tais como palavras-passe) durante a implementação](../azure-resource-manager/resource-manager-keyvault-parameter.md) - quando tem de transmitir um valor seguro (como uma palavra-passe) como um parâmetro durante a implementação, pode armazenar esse valor como um segredo num cofre de chaves do Azure e o valor no outro recurso de referência Modelos de gestor.
- [Como utilizar o Cofre de chaves extensível gestão de chaves com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -permite que o conector de servidor de SQL Server para o Cofre de chaves do Azure do SQL Server e SQL-na-a-VM para tirar partido do serviço do Cofre de chaves do Azure como um fornecedor de gestão de chaves extensível (EKM) para proteger o chaves de encriptação para ligação de aplicações A encriptação transparente de dados, a encriptação de cópias de segurança e a encriptação de nível de coluna.
- [Como implementar certificados para as VMs do Cofre de chaves](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - uma aplicação na nuvem em execução numa VM do Azure tem um certificado. Como pode proceder para que este certificado para esta VM hoje?
- [Como configurar o Cofre de chaves com auditoria e rotação de chave ponto a ponto](key-vault-key-rotation-log-monitoring.md) - esta explica como configurar a rotação da chave e auditoria com o Cofre de chaves do Azure.
- [Implementar o certificado de aplicação Web do Azure através do Cofre de chaves]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) fornece instruções passo a passo para implementar certificados armazenados no Cofre de chave como parte da [certificado de serviço de aplicação](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) oferta.
- [Conceder permissão para muitas aplicações para um cofre de chaves de acesso](key-vault-group-permissions-for-apps.md) 16 entradas suporta apenas a política de controlo de acesso do Cofre de chaves. No entanto, pode criar um grupo de segurança do Azure Active Directory. Adicione todos os principais de serviço associado a este grupo de segurança e, em seguida, conceder acesso a este grupo de segurança ao Cofre de chaves.
- Para obter mais orientações de tarefas específicas no integrar e utilizar cofres de chave com o Azure, consulte [exemplos de modelo Azure Resource Manager dos Ryan Jones Cofre de chaves](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Como utilizar a eliminação de forma recuperável do Cofre de chaves com a CLI](key-vault-soft-delete-cli.md) orienta-o a utilização e o ciclo de vida de um cofre de chaves e vários objetos do Cofre de chaves com eliminação de forma recuperável ativada.
- [Como utilizar a eliminação de forma recuperável do Cofre de chaves com o PowerShell](key-vault-soft-delete-powershell.md) orienta-o a utilização e o ciclo de vida de um cofre de chaves e vários objetos do Cofre de chaves com eliminação de forma recuperável ativada.

## <a name="integrated-with-key-vault"></a>Integrado com o Cofre de chaves

Estes artigos são sobre os serviços que utilizam ou integram com o Cofre de chaves e outros cenários.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) tira partido do padrão da indústria [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funcionalidade do Windows e o [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funcionalidade do Linux para fornecer a encriptação de volume para o SO e os discos de dados. A solução é integrada com o Cofre de chaves do Azure para o ajudar a controlar e gerir as chaves de encriptação de disco e segredos na sua subscrição do Cofre de chaves, garantindo que todos os dados em discos de máquinas virtuais sejam encriptados Inativos no armazenamento do Azure.
- [O Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) oferece a opção para a encriptação de dados que são armazenados na conta. Gestão de chaves, o Data Lake Store fornece dois modos para gerir as chaves de encriptação mestra (MEKs), que são necessárias para desencriptar os dados são armazenados no Data Lake Store. Pode optar por deixar o Data Lake Store gerir os MEKs por si ou optar por manter a propriedade de MEKs utilizando a sua conta do Cofre de chaves do Azure. Especifique o modo de gestão de chaves ao criar uma conta de Data Lake Store. 
- [O Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) permite-lhe manager sua própria chave de inquilino. Por exemplo, em vez da Microsoft gerir a sua chave de inquilino (predefinição), pode gerir sua própria chave de inquilino para estar em conformidade com regulamentos específicos que se aplicam à sua organização. Gerir a sua própria chave de inquilino também é referido como traga a sua própria chave ou BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Conceitos e descrições gerais do Cofre de chaves

- [Comportamento de eliminação de forma recuperável do Cofre de chaves](key-vault-ovw-soft-delete.md) descreve uma funcionalidade que permite a recuperação de objetos eliminados, se a eliminação foi acidental ou intencional.
- [Cliente do Cofre de chaves limitação](key-vault-ovw-throttling.md) descreve os os conceitos básicos de limitação e oferece uma abordagem para a sua aplicação.
- [Descrição geral do chaves de conta de armazenamento do Cofre de chaves](key-vault-ovw-storage-keys.md) descreve as chaves de contas do Storage do Azure de integração do Cofre de chaves.
- [Universos de segurança do Cofre de chaves](key-vault-ovw-security-worlds.md) descreve as relações entre regiões e áreas de segurança.

## <a name="social"></a>Redes sociais

- [Blogue do Cofre de chaves](http://aka.ms/kvblog)
- [Fórum do Cofre de chaves](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Bibliotecas de suporte

- [Biblioteca de núcleo do Cofre de chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece **IKey** e **IKeyResolver** interfaces para localizar as chaves de identificadores e efetuar operações com chaves.
- [As extensões do Microsoft Azure chave de cofre](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornece capacidades expandidas para o Cofre de chaves do Azure.


