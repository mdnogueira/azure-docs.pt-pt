---
title: "Azure proteger os dados pessoais Inativos com encriptação | Microsoft Docs"
description: "Este artigo é parte de uma série, ajudando-o a utilizar o Azure para proteger os dados pessoais"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 2bb8370d23d9450fb8154f21c27817666fd7852c
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="azure-encryption-technologies-protect-personal-data-at-rest-with-encryption"></a>Tecnologias de encriptação do Azure: proteger os dados pessoais Inativos com encriptação

Este artigo ajuda-o a compreender e utilizar tecnologias de encriptação do Azure para proteger dados inativos.

É essencial como melhor prática para proteger os dados confidenciais ou pessoais e para cumprir os requisitos de privacidade de dados de conformidade e a encriptação de dados inativos.
Encriptação de Inativos foi concebida para impedir que o atacante acedam a não encriptada dados, assegurando que os dados são encriptados quando no disco.

## <a name="scenario"></a>Cenário 

Uma empresa de grande cruise headquartered nos Estados Unidos, está a expandir as suas operações para oferecer itineraries Mediterranean e Baltic seas, bem como o Isles território. Para suportar os esforços, obteve várias linhas cruise mais pequenas com base em (Itália), na Alemanha, Dinamarca e o U.K.

A empresa utiliza o Microsoft Azure para armazenar dados empresariais na nuvem. Isto pode incluir informações de cliente e/ou dos empregados, tais como:

- endereços
- Números de telefone
- Números de identificação de dedução dos impostos
- informações de cartão de crédito

A empresa tem de proteger a privacidade dos dados de cliente e o empregado ao tornar dados acessíveis para os departamentos que precisem dele. (por exemplo, os departamentos de folha de pagamentos e reservas)

A linha cruise mantém também uma grande base de dados de membros de programa reward e loyalty que inclui informações pessoais, para controlar as relações com clientes atuais e anteriores.

### <a name="problem-statement"></a>Declaração do problema

A empresa tem de proteger a privacidade dos dados pessoais dos clientes e dos empregados ao tornar dados acessíveis para os departamentos que precisem dele (por exemplo, os departamentos de folha de pagamentos e reservas). Estes dados pessoais são armazenados fora do Centro de dados empresariais controlado e não estiver sob controlo física da empresa.

### <a name="company-goal"></a>Objetivo da empresa

Como parte de uma estratégia de segurança por várias camadas de defesa em profundidade, é um objetivo da empresa para se certificar de que todas as origens de dados que contêm dados pessoais são encriptadas, incluindo os que reside no armazenamento na nuvem. Se pessoas não autorizadas tiverem acesso aos dados pessoais, tem de ter um formulário que irá compor ilegível. Aplicar encriptação deve ser fácil ou transparente – para utilizadores e administradores.

## <a name="solutions"></a>Soluções

Serviços do Azure fornecem várias ferramentas e tecnologias para ajudar a proteger os dados pessoais Inativos ao encriptá-lo.

### <a name="azure-key-vault"></a>Azure Key Vault

[O Cofre de chaves do Azure](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-whatis) fornece armazenamento seguro para chaves utilizadas para encriptar dados Inativos nos serviços do Azure e é a solução de armazenamento e gestão de chave recomendada. Gestão de chaves de encriptação é essencial para proteger os dados armazenados.

#### <a name="how-do-i-use-azure-key-vault-to-protect-keys-that-encrypt-personal-data"></a>Como utilizar o Cofre de chaves do Azure para proteger chaves de encriptar dados pessoais?

Para utilizar o Cofre de chaves do Azure, terá de uma subscrição para uma conta do Azure. Também precisa de Azure PowerShell instalada. Passos incluem utilizando cmdlets do PowerShell para fazer o seguinte:

1. Estabelecer a ligação às suas subscrições

2. Criar um cofre de chaves

3. Adicionar uma chave ou segredo ao cofre de chaves

4. Registe as aplicações que irão utilizar o Cofre de chaves com o Azure Active Directory

5. Autorizar as aplicações a utilizar a chave ou segredo

Para criar um cofre de chaves, utilize o cmdlet do PowerShell New-AzureRmKeyVault. Irá atribuir um nome do cofre, o nome do grupo de recursos e a localização geográfica. Irá utilizar o nome do Cofre de chaves através de outros Cmdlets de gestão. As aplicações que utilizam o Cofre através da API REST, irão utilizar o URI do cofre.

O Cofre de chaves do Azure pode fornecer uma chave protegida por software para si, ou pode importar uma chave existente num. Ficheiro PFX. Pode também armazenar segredos (palavras-passe) no cofre.

Também pode gerar uma chave no seu HSM local e transferi-la para HSMs no serviço Cofre de chaves, sem a chave saia do limite HSM.

Para obter instruções detalhadas sobre como utilizar o Cofre de chaves do Azure, siga os passos no [introdução ao Cofre de chaves do Azure.](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-get-started)

Para obter uma lista de Cmdlets do PowerShell utilizados com o Cofre de chaves do Azure, consulte [AzureRM.KeyVault](https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/?view=azurermps-4.2.0).

### <a name="azure-disk-encryption-for-windows"></a>Encriptação de disco do Azure para Windows

[Azure encriptação de disco para Windows e as VMs de IaaS Linux](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption) protege os dados pessoais Inativos em máquinas virtuais do Azure e integra-se com o Cofre de chaves do Azure. Utiliza a Azure Disk Encryption [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) no Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) no Linux para encriptar o SO e discos de dados. Encriptação de disco do Azure é suportada no Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016 e, em clientes Windows 8 e Windows 10.

#### <a name="how-do-i-use-azure-disk-encryption-to-protect-personal-data"></a>Como utilizar o Azure Disk Encryption para proteger os dados pessoais?

Para utilizar o Azure Disk Encryption, precisa de uma subscrição para uma conta do Azure. Para ativar a encriptação de disco do Azure para Windows e VMs com Linux, efetue o seguinte:

1. Utilize o modelo de encriptação de disco do Azure Resource Manager, PowerShell ou a interface de linha de comandos (CLI) para ativar a encriptação de disco e especificar a configuração de encriptação. 

2. Conceder acesso para a plataforma do Azure para ler o material de encriptação do seu Cofre de chaves.

3. Fornece uma identidade de aplicação do Azure Active Directory (AAD) para o material de chave de encriptação de escrita para o seu Cofre de chaves.

Azure irá atualizar a VM e a configuração do Cofre de chaves e configurar a sua VM encriptado.

Quando configurar o seu Cofre de chaves para suportar a encriptação de disco do Azure, pode adicionar uma chave de encriptação de chaves (KEK) para segurança adicional e para suportar a cópia de segurança de máquinas virtuais encriptadas.

![](media/protect-personal-data-at-rest/create-key.png)

Instruções detalhadas para cenários de implementação específico e as experiências de utilizador estão incluídas no [encriptação de disco do Azure para Windows e as VMs de IaaS Linux.](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption)

### <a name="azure-storage-service-encryption"></a>Encriptação do Serviço de Armazenamento do Azure

[Azure armazenamento serviço encriptação (SSE) para dados Inativos](https://docs.microsoft.com/en-us/azure/storage/storage-service-encryption) ajuda a proteger e salvaguardar os seus dados para satisfazer os seus compromissos de segurança e conformidade organizacionais. Armazenamento do Azure encripta os dados através da encriptação AES de 256 bits antes de a persistência para o armazenamento e automaticamente desencripta a mesma antes da obtenção. Este serviço está disponível para Blobs do Azure e os ficheiros.

#### <a name="how-do-i-use-storage-service-encryption-to-protect-personal-data"></a>Como utilizar a encriptação do serviço de armazenamento para proteger os dados pessoais

Para ativar a encriptação do serviço de armazenamento, efetue o seguinte:

1. Inicie sessão no portal do Azure.

2. Selecione uma conta de armazenamento.

3. Nas definições, na secção de serviço Blob, selecione encriptação.

4. Na secção de serviço de ficheiro, selecione a encriptação.

Depois de clicar na definição de encriptação, pode ativar ou desativar a encriptação do serviço de armazenamento.

![](media/protect-personal-data-at-rest/storage-service-encryption.png)

Novos dados serão encriptados. Dados de ficheiros existentes nesta conta de armazenamento irão permanecer desencriptados.

Depois de ativar a encriptação, copie dados para a conta de armazenamento através de um dos seguintes métodos:

1. Copiar os blobs ou ficheiros com o [utilitário de linha de comandos do AzCopy](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy).

2. [Montar uma partilha de ficheiros utilizando o SMB](https://docs.microsoft.com/en-us/azure/storage/storage-file-how-to-use-files-windows) pelo que pode utilizar um utilitário, tal como Robocopy para copiar ficheiros.

3. Copiar blob ou ficheiro de dados para e do armazenamento de BLOBs ou entre contas de armazenamento com [bibliotecas de cliente de armazenamento, tais como .NET](https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-how-to-use-blobs).

4.  Utilize um [Explorador de armazenamento](https://docs.microsoft.com/en-us/azure/storage/storage-explorers) para carregar os blobs à sua conta de armazenamento com a encriptação ativada.

### <a name="transparent-data-encryption"></a>Encriptação de Dados Transparente

Encriptação de dados transparente (TDE) é uma funcionalidade do SQL Azure através do qual pode encriptar dados níveis de base de dados e no servidor. TDE está agora ativada por predefinição em todas as bases de dados recentemente criados. TDE efetua a encriptação de e/s em tempo real e a desencriptação dos ficheiros de dados e de registo.

#### <a name="how-do-i-use-tde-to-protect-personal-data"></a>Como utilizar o TDE para proteger os dados pessoais?

Pode configurar TDE através do portal do Azure, utilizando a API REST ou utilizando o PowerShell. Para ativar a TDE na base de dados existente com o Portal do Azure, efetue o seguinte:

1. Visitar o portal do Azure em <https://portal.azure.com> e inicie sessão com a sua conta de administrador do Azure ou de contribuinte.

2. Na faixa esquerda, clique para procurar e, em seguida, clique em bases de dados SQL.

3. Com bases de dados SQL selecionadas no painel esquerdo, clique em sua base de dados do utilizador.

4. No painel da base de dados, clique em todas as definições.

5. No painel de definições, clique em parte de encriptação transparente de dados para abrir o painel de encriptação transparente de dados.

6. No painel de encriptação de dados, mova o botão de encriptação de dados e, em seguida, clique em Guardar (na parte superior da página) para aplicar a definição. O estado de encriptação serão aproximada o progresso da encriptação transparente de dados.

![Ativar a encriptação de dados](media/protect-personal-data-at-rest/turn-data-encryption-on.png)

É possível encontrar instruções sobre como ativar o TDE e informações sobre a desencriptação de bases de dados protegida TDE e muito mais no artigo [encriptação transparente de dados com a SQL Database do Azure.](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database)

## <a name="summary"></a>Resumo

A empresa pode realizar o seu objetivo de encriptação de dados pessoais armazenados na nuvem do Azure. Pode fazem isto, utilizando a Azure Disk Encryption para proteger volumes completos. Isto pode incluir os ficheiros do sistema operativo e os ficheiros de dados que contêm informações pessoais e outros dados confidenciais. Encriptação do serviço de armazenamento do Azure pode ser utilizada para proteger os dados pessoais que são armazenados em blobs e os ficheiros. Para dados que são armazenados nas bases de dados SQL do Azure, a encriptação transparente de dados fornece proteção contra exposição não autorizada de informações pessoais.

Para proteger as chaves que são utilizadas para encriptar os dados no Azure, a empresa pode utilizar o Cofre de chaves do Azure. Isto simplifica o processo de gestão de chaves e permite à empresa a manter o controlo de teclas que acede e encripta os dados pessoais.

## <a name="next-steps"></a>Passos seguintes

- [Guia de resolução de problemas de encriptação de disco do Azure](https://docs.microsoft.com/en-us/azure/security/azure-security-disk-encryption-tsg)

- [Encriptar uma Máquina Virtual do Azure](https://docs.microsoft.com/en-us/azure/security-center/security-center-disk-encryption?toc=%2fazure%2fsecurity%2ftoc.json)

- [Encriptação de dados no Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-encryption)

- [Encriptação de base de dados do Cosmos DB do Azure Inativos](https://docs.microsoft.com/en-us/azure/cosmos-db/database-encryption-at-rest)
