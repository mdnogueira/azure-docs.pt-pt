---
title: "Integração de centro de dados de pilha do Azure - DNS"
description: Saiba como integrar o DNS de pilha do Azure com o seu centro de dados DNS
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/10/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 40d6d4858ef2e3df61d04dc68c00e09c04f000e2
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---dns"></a>Integração de centro de dados de pilha do Azure - DNS

*Aplica-se a: Azure pilha integrado sistemas*

Para poder aceder aos pontos finais de pilha do Azure (`portal`, `adminportal`, `management`, `adminmanagement`, etc.)  da pilha fora do Azure, tem de integrar os serviços de DNS de pilha do Azure com os servidores DNS que alojam as zonas DNS que pretende utilizar na pilha do Azure.

## <a name="azure-stack-dns-namespace"></a>Espaço de nomes de DNS de pilha do Azure
É necessário para fornecer algumas informações importantes relacionados com o DNS quando implementar pilha do Azure.


|Campo  |Descrição  |Exemplo|
|---------|---------|---------|
|Região|A localização geográfica da sua implementação de pilha do Azure.|`east`|
|Nome de domínio externo|O nome da zona que pretende utilizar para a implementação de pilha do Azure.|`cloud.fabrikam.com`|
|Nome de domínio interno|O nome da zona interno que é utilizado para serviços de infraestrutura na pilha do Azure.  É integrado de serviço de diretório e privada (não acessível a partir de fora da implementação de pilha do Azure).|`azurestack.local`|
|Reencaminhador DNS|Servidores DNS que são utilizados para reencaminhar as consultas de DNS, zonas DNS e registos que estão alojados fora da pilha do Azure, ou na intranet da empresa ou da internet pública.|`10.57.175.34`<br>`8.8.8.8`|
|Atribuição de nome de prefixo (opcional)|O prefixo de nomenclatura pretende que os nomes de máquina de instância de função de infraestrutura do pilha do Azure ter.  Se não for indicado, a predefinição é `azs`.|`azs`|

O nome de domínio completamente qualificado (FQDN) da sua implementação de pilha do Azure e os pontos finais é a combinação do parâmetro de região e o parâmetro de nome de domínio externo. Utilizando os valores dos exemplos na tabela anterior, o FQDN para esta implementação de pilha do Azure seria o seguinte nome:

`east.cloud.fabrikam.com`

Como tal, os exemplos de alguns dos pontos finais para esta implementação teria aspeto os seguintes URLs:

`https://portal.east.cloud.fabrikam.com`

`https://adminportal.east.cloud.fabrikam.com`

Para utilizar este espaço de nomes DNS de exemplo para uma implementação de pilha do Azure, são necessárias as seguintes condições:

- A zona `fabrikam.com` está registado com uma entidade de registo do domínio, um servidor DNS empresarial interno ou ambos, dependendo dos requisitos de resolução do nome.
- Domínio subordinado `cloud.fabrikam.com` existe a zona `fabrikam.com`.
- Os servidores DNS que alojam as zonas `fabrikam.com` e `cloud.fabrikam.com` pode ser contactado desde a implementação de pilha do Azure.

Para conseguir resolver nomes DNS para pontos finais de pilha do Azure e instâncias da pilha fora do Azure, tem de integrar os servidores DNS que alojam a zona DNS externa para a pilha do Azure com os servidores DNS que alojam a zona de principal que pretende utilizar.


## <a name="resolution-and-delegation"></a>Resolução e delegação

Existem dois tipos de servidores DNS:

- Um servidor DNS autoritativo aloja zonas DNS. Responde a consultas DNS para os registos apenas dessas zonas.
- Um servidor DNS recursivo não aloja zonas DNS. Responde a todas as consultas DNS ao chamar servidores DNS autoritativos para recolher os dados de que necessita.

Pilha do Azure inclui os autoritativos e servidores DNS recursiva. Os servidores de recursiva são utilizados para resolver nomes de tudo, exceto para a zona privada interna e a zona DNS pública externa dessa implementação de pilha do Azure. 

![Arquitetura de pilha DNS do Azure](media/azure-stack-integrate-dns/Integrate-DNS-01.png)

## <a name="resolving-external-dns-names-from-azure-stack"></a>Resolução de nomes DNS externos da pilha do Azure

Para resolver nomes DNS para pontos finais fora da pilha do Azure (por exemplo: www.bing.com), tem de fornecer os servidores DNS que pilha do Azure pode utilizar para reencaminhar pedidos DNS para o qual não é autoritativa pilha do Azure. Para a implementação, os servidores DNS do Azure pilha encaminha pedidos para são necessários na folha de cálculo de implementação (no campo do reencaminhador de DNS). Fornece, pelo menos, dois servidores neste campo para tolerância a falhas. Sem estes valores, falha na implementação de pilha do Azure.

### <a name="configure-conditional-dns-forwarding"></a>Configurar o reencaminhamento condicional de DNS

> [!IMPORTANT]
> Isto aplica-se apenas a uma implementação do AD FS.

Para ativar a resolução de nomes com a sua infraestrutura DNS existente, configure o reencaminhamento condicional.

Para adicionar um reencaminhador condicional, tem de utilizar o ponto final com privilégios.

Para este procedimento, utilize um computador na sua rede de centro de dados que pode comunicar com o ponto final com privilégios na pilha do Azure.

1. Abra uma sessão do Windows PowerShell elevada (executar como administrador) e ligar para o endereço IP do ponto final com privilégios. Utilize as credenciais para autenticação de CloudAdmin.

   ```
   $cred=Get-Credential 
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred
   ```

2. Depois de ligar para o ponto final com privilégios, execute o seguinte comando do PowerShell. Substitua os valores de exemplo fornecidos com o nome de domínio e os endereços IP dos servidores DNS que pretende utilizar.

   ```
   Register-CustomDnsServer -CustomDomainName "contoso.com" -CustomDnsIPAddresses "192.168.1.1","192.168.1.2"
   ```

## <a name="resolving-azure-stack-dns-names-from-outside-azure-stack"></a>Resolver nomes de DNS de pilha do Azure da pilha fora do Azure
Os servidores autoritativos são aqueles que contêm as informações de zona DNS externas e zonas criados pelo utilizador. Integre com estes servidores para ativar a delegação de zona ou reencaminhamento condicional para resolver nomes de DNS de pilha do Azure da pilha fora do Azure.

## <a name="get-dns-server-external-endpoint-information"></a>Obter informações de ponto final externo do servidor DNS

Para integrar a implementação de pilha do Azure com a sua infraestrutura DNS, terá as seguintes informações:

- Servidor DNS FQDNs
- Endereços IP do servidor DNS

O FQDN para os servidores DNS de pilha do Azure com o seguinte formato:

`<NAMINGPREFIX>-ns01.<REGION>.<EXTERNALDOMAINNAME>`

`<NAMINGPREFIX>-ns02.<REGION>.<EXTERNALDOMAINNAME>`

Servidores utilizando os valores de exemplo, o FQDN para DNS são:

`azs-ns01.east.cloud.fabrikam.com`

`azs-ns02.east.cloud.fabrikam.com`


Estas informações também são criadas no final de todas as implementações de pilha do Azure num ficheiro denominado `AzureStackStampDeploymentInfo.json`. Este ficheiro está localizado no `C:\CloudDeployment\logs` pasta da máquina virtual implementação. Se não tiver a certeza de que os valores que foram utilizados para a implementação de pilha do Azure, pode obter os valores a partir daqui.

Se a máquina virtual de implementação já não está disponível ou está inacessível, pode obter os valores de ligar para o ponto final com privilégios e executando o `Get-AzureStackInfo` cmdlet do PowerShell. Para obter mais informações sobre o ponto final com privilégios, consulte (insert associar ao artigo aqui).

## <a name="setting-up-conditional-forwarding-to-azure-stack"></a>Configurar o reencaminhamento condicional a pilha do Azure

A forma mais simples e mais segura de integrar a pilha do Azure com a sua infraestrutura DNS é efetuar o reencaminhamento condicional da zona a partir do servidor que aloja o horário de principal. Esta abordagem recomenda-se de que tem controlo direto sobre os servidores DNS que alojam a zona principal para o Azure pilha externo espaço de nomes DNS.

Se não estiver familiarizado com como fazê-lo reencaminhamento condicional com o DNS, consulte o seguinte artigo do TechNet: [atribuir um reencaminhador condicional para um nome de domínio](https://technet.microsoft.com/library/cc794735), ou a documentação específica da sua solução DNS.

Em cenários em que especificou a zona de DNS do Azure pilha externo para o aspeto de um domínio subordinado do seu nome de domínio empresarial, não é possível utilizar reencaminhamento condicional. Tem de configurar a delegação de DNS.

Exemplo:

- Nome de domínio DNS empresarial:`contoso.com`
- Nome de domínio DNS externo de pilha do Azure:`azurestack.contoso.com`

## <a name="delegating-the-external-dns-zone-to-azure-stack"></a>Delegar a zona DNS externa à pilha do Azure

Para nomes DNS permitir a resolução de fora de uma implementação de pilha do Azure, terá de configurar a delegação de DNS.

Cada entidade de registo tem as suas próprias ferramentas de gestão de DNS para alterar os registos do servidor de nome de um domínio. Na página de gestão de DNS a entidade de registo, edite os registos NS e substitua os registos NS para a zona com na pilha do Azure.

A maioria das registrars DNS necessitam que forneça um mínimo de dois servidores DNS para concluir a delegação.

## <a name="next-steps"></a>Passos seguintes

[Integração do Centro de dados do Azure pilha - identidade](azure-stack-integrate-identity.md)
