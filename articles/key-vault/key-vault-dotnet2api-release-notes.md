---
title: "Notas de versão do .NET 2 API do Cofre de chaves | Microsoft Docs"
description: "Os programadores de .NET irão utilizar esta API código para o Cofre de chaves do Azure"
services: key-vault
author: BrucePerlerMS
manager: mbaldwin
editor: bruceper
ms.assetid: 1cccf21b-5be9-4a49-8145-483b695124ba
ms.service: key-vault
ms.devlang: CSharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/02/2017
ms.author: bruceper
ms.openlocfilehash: c5b5fd7f16faf17d16ecc82269fb1264adf4dd06
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Cofre de chaves do Azure .NET 2.0 - Guia de migração e notas de versão
As seguintes notas e orientações são para os programadores que trabalham com o Azure chave de cofre .NET / C # biblioteca. A transição da 1.0 versão para a versão 2.0, um número de atualizações foram efetuado que irá necessitar de trabalho de migração no seu código por ordem para beneficiar as melhorias funcionais e de como a funcionalidade adições **certificados do Cofre de chaves** suportar.

## <a name="key-vault-certificates"></a>Certificados de Cofre de chaves

Fornece suporte de certificados do Cofre de chaves para a gestão das suas x509 certificados e os comportamentos seguintes:  

* Permite que um proprietário de certificado criar um certificado através de um processo de criação do Cofre de chaves ou através da importação de um certificado existente. Isto inclui ambos autoassinados e autoridade de certificação gerados certificados.
* Permite que um proprietário de certificado do Cofre de chaves implementar o armazenamento seguro e a gestão de X509 certificados sem interação com o material de chave privada.  
* Permite que um proprietário de certificado criar uma política que direciona o Cofre de chaves para gerir o ciclo de vida de um certificado.  
* Permite que os proprietários de certificado fornecer informações de contacto para notificação sobre eventos de ciclo de vida de expiração e renovação de certificado.  
* Suporta a renovação automática com emissores selecionados - parceiro do Cofre de chaves X509 fornecedores de certificado / autoridades de certificados.
  
  * Tenha em atenção - fornecedores/autoridades não parcerias também são permitidas, mas, não suporta a funcionalidade de renovação automática.

## <a name="net-support"></a>Suporte de .NET

* **O .NET 4.0** não é suportada pela versão 2.0 do .NET Cofre de chaves do Azure / c# biblioteca
* **.NET core** é suportada pela versão 2.0 do .NET Cofre de chaves do Azure / c# biblioteca

## <a name="namespaces"></a>Espaços de nomes

* O espaço de nomes para **modelos** é alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
* O **Microsoft.Azure.KeyVault.Internal** espaço de nomes é ignorado.
* O espaço de nomes de dependências do SDK do Azure são mudou de **Hyak.Common** e **Hyak.Common.Internals** para **Microsoft.Rest** e **Microsoft.Rest.Serialization**

## <a name="type-changes"></a>Alterações de tipo

* *Segredo* alterado para *SecretBundle*
* *Dicionário* alterado para *IDictionary*
* *Lista<T>, string []* alterado para *IList<T>*
* *NextList* alterado para *NextPageLink*

## <a name="return-types"></a>Tipos de retorno

* **KeyList** e **SecretList** irá devolver *IPage<T>*  em vez de *ListKeysResponseMessage*
* Gerado **BackupKeyAsync** irá devolver *BackupKeyResult* que contém *valor* (BLOBs de cópia de segurança). Antes do método foi encapsulado e devolver apenas o valor.

## <a name="exceptions"></a>Exceções

* *KeyVaultClientException* é alterado para *KeyVaultErrorException*
* O erro de serviço é alterado de *exceção. Erro* para *exceção. Body.Error.Message*.
* Removido informações adicionais da mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores

* Em vez de a aceitar uma *HttpClient* como um argumento de construtor, o construtor apenas aceita *HttpClientHandler* ou *DelegatingHandler [-]*.

## <a name="downloaded-packages"></a>Pacotes transferidos

Quando um cliente está a processar uma dependência no Cofre de chaves foram transferido do seguinte

### <a name="previous-package-list"></a>Lista de pacotes anterior

* versão do pacote id="Hyak.Common" = "1.0.2" targetFramework = "net45"
* versão do pacote id="Microsoft.Azure.Common" = "2.0.4" targetFramework = "net45"
* versão do pacote id="Microsoft.Azure.Common.Dependencies" = "1.0.0" targetFramework = "net45"
* versão do pacote id="Microsoft.Azure.KeyVault" = "1.0.0" targetFramework = "net45"
* versão do pacote id="Microsoft.Bcl" = "1.1.9" targetFramework = "net45"
* versão do pacote id="Microsoft.Bcl.Async" = "1.0.168" targetFramework = "net45"
* versão do pacote id="Microsoft.Bcl.Build" = "1.0.14" targetFramework = "net45"
* versão do pacote id="Microsoft.Net.Http" = "2.2.22" targetFramework = "net45"

### <a name="current-package-list"></a>Lista de pacote atual

* versão do pacote id="Microsoft.Azure.KeyVault" = "2.0.0-preview" targetFramework = "net45"
* versão do pacote id="Microsoft.Rest.ClientRuntime" = "2.2.0" targetFramework = "net45"
* versão do pacote id="Microsoft.Rest.ClientRuntime.Azure" = "3.2.0" targetFramework = "net45"

## <a name="class-changes"></a>Alterações de classe

* **UnixEpoch** classe foi removida
* **Base64UrlConverter** classe é mudada para **Base64UrlJsonConverter**

## <a name="other-changes"></a>Outras alterações

* Foi adicionado suporte para a configuração de política de repetição de operação de KV em falhas transitórias para esta versão da API.

## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet

* Para as operações que devolveram um *cofre*, o tipo de retorno foi uma classe que continha uma propriedade do cofre. O tipo de retorno é agora *cofre*.
* *PermissionsToKeys* e *PermissionsToSecrets* estão agora *Permissions.Keys* e *Permissions.Secrets*
* Algumas das alterações de tipos de retorno aplicam-se para o controlo-plane bem.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet

* O pacote foi interrompido até **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.

