---
title: Conector do PowerShell | Microsoft Docs
description: Este artigo descreve como configurar o conector do Microsoft Windows PowerShell.
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 0e5ccf5a38072e31d85bbc63eb0c608b0c34cfc2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="windows-powershell-connector-technical-reference"></a>Referência técnica do conector do Windows PowerShell
Este artigo descreve o conector do Windows PowerShell. O artigo aplica-se os seguintes produtos:

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * Tem de utilizar correção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-powershell-connector"></a>Descrição geral do conector do PowerShell
O conector do PowerShell permite-lhe integrar o serviço de sincronização com sistemas externos que oferecem as APIs baseadas em Windows PowerShell. O conector fornece uma ponte entre as capacidades do agente de gestão baseado na chamada de conectividade extensível framework (ECMA2) 2 e o Windows PowerShell. Para obter mais informações sobre a arquitetura de ECMA, consulte o [extensível referência do agente de gestão 2.2 conectividade](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx).

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar o conector, certifique-se de que tem o seguinte no servidor de sincronização:

* Arquitetura do Microsoft .NET 4.5.2 ou posterior
* Windows PowerShell 2.0, 3.0 ou 4.0

A política de execução no servidor do serviço de sincronização tem de ser configurada para permitir que o conector executar scripts do Windows PowerShell. A menos que os scripts que executa o conector é assinado digitalmente, configurar a política de execução ao executar o comando:  
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>Criar um novo conector
Para criar um conector do Windows PowerShell no serviço de sincronização, tem de fornecer uma série de scripts do Windows PowerShell que executar os passos pedidos pelo serviço de sincronização. Consoante a origem de dados liga a e a funcionalidade que necessita, os scripts tem de implementar varia. Esta secção descreve cada um dos scripts que podem ser implementadas e quando são necessárias.

O conector do Windows PowerShell foi concebido para armazenar todos os scripts no interior da base de dados do serviço de sincronização. Embora seja possível executar scripts que são armazenados no sistema de ficheiros, é mais fácil insira o corpo de cada script diretamente na configuração do conector.

Para criar um conector do PowerShell, no **serviço de sincronização** selecione **agente de gestão** e **criar**. Selecione o **PowerShell (Microsoft)** conector.

![Criar conector](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>Conectividade
Fornece parâmetros de configuração para ligar a um sistema remoto. Estes valores são armazenados pelo serviço de sincronização e disponibilizados para os scripts do Windows PowerShell quando o conector é executado em segurança.

![Conectividade](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

Pode configurar os seguintes parâmetros de conectividade:

**Conetividade**

| Parâmetro | Valor predefinido | Objetivo |
| --- | --- | --- |
| Servidor |<Blank> |Nome do servidor que o conector, deve ligar a. |
| domínio |<Blank> |Domínio da credencial para armazenar para utilização quando o conector é executado. |
| Utilizador |<Blank> |Nome de utilizador da credencial para armazenar para utilização quando o conector é executado. |
| Palavra-passe |<Blank> |Palavra-passe da credencial para armazenar para utilização quando o conector é executado. |
| Representar a conta do conector |Falso |Quando verdadeiro, o serviço de sincronização executa os scripts do Windows PowerShell no contexto das credenciais fornecidas. Sempre que possível, recomenda-se que o **$Credentials** parâmetro é transmitido para cada script é utilizado em vez de representação. Para obter mais informações sobre permissões adicionais que são necessárias para utilizar esta opção, consulte [configuração adicional para representação](#additional-configuration-for-impersonation). |
| Carregar perfil do utilizador quando a representar |Falso |Dá instruções ao Windows para o perfil de utilizador de credenciais do conector de carga durante a representação. Se o utilizador representado tem um perfil de roaming, o conector não carregar perfil itinerante. Para obter mais informações sobre permissões adicionais que são necessárias para utilizar este parâmetro, consulte [configuração adicional para representação](#additional-configuration-for-impersonation). |
| Tipo de início de sessão quando a representar |Nenhuma |Tipo de início de sessão durante a representação. Para obter mais informações, consulte o [dwLogonType] [ dw] documentação. |
| Apenas Scripts assinados |Falso |Se for VERDADEIRO, o conector do Windows PowerShell valida que cada script possui uma assinatura digital válida. Se for FALSO, certifique-se de que a política de execução do servidor do serviço de sincronização do Windows PowerShell é RemoteSigned ou Unrestricted. |

**Módulo comuns**  
O conector permite-lhe armazenar um módulo do Windows PowerShell partilhado na configuração. Quando o conector executa um script, é extraído o módulo Windows PowerShell para o sistema de ficheiros para que possa ser importada por cada script.

Para scripts de importação, a exportação e a sincronização de palavra-passe, o módulo comum é extraído à pasta de MAData o conector. Para scripts de deteção do esquema, validação, hierarquia e partição, o módulo comum é extraído para a pasta % TEMP %. Em ambos os casos, o script de módulo comuns extraído com o nome, de acordo com a definição do nome do módulo de Script comuns.

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 da pasta MAData, utilize a seguinte instrução:`Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

Para carregar um módulo chamado FIMPowerShellConnectorModule.psm1 da pasta % TEMP %, utilize a seguinte instrução:`Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**Validação dos parâmetros**  
O Script de validação é um script do Windows PowerShell opcional que pode ser utilizado para se certificar de que os parâmetros de configuração do conector fornecidos pelo administrador são válidos. Validação de servidor, as credenciais de ligação e os parâmetros de conectividade são utilizações comuns do script de validação. O script de validação denomina-se após os seguintes separadores e caixas de diálogo são modificadas:

* Conectividade
* Parâmetros globais
* Configuração de partição

O script de validação recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |O separador de configuração ou a caixa de diálogo que acionou o pedido de validação. |
| ConfigParameters |[KeyedCollection] [ keyk] [cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |

O script de validação deverá devolver um objeto de ParameterValidationResult único para o pipeline.

**Deteção de esquema**  
O script de deteção de esquema é obrigatório. Este script devolve os tipos de objeto, atributos e as restrições de atributo que o serviço de sincronização utiliza quando configurar as regras do fluxo de atributos. O script de deteção de esquema é executado durante a criação do conector e preenche o esquema do conector. Também é utilizado pela ação atualizar esquema o Synchronization Service Manager.

O script de deteção do esquema recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection] [ keyk] [cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |

O script tem de devolver um único [esquema] [ schema] para o pipeline. O objeto de esquema é composto por [SchemaType] [ schemaT] objetos que representam os tipos de objeto (por exemplo: utilizadores e grupos). O objeto de SchemaType contém uma coleção de [SchemaAttribute] [ schemaA] objetos que representam os atributos (por exemplo: nome próprio, apelido e endereço postal) do tipo.

**Parâmetros adicionais**  
Além das definições de configuração padrão, pode definir as definições de configuração personalizada adicionais que são específicas para a instância do conector. Estes parâmetros podem ser especificados o conector, partição, ou o passo de execução níveis e acessível a partir do script do Windows PowerShell relevante. Definições de configuração personalizados podem ser armazenadas na base de dados do serviço de sincronização no formato de texto simples ou podem ser encriptados. O serviço de sincronização automaticamente encripta e desencripta a proteger as definições de configuração quando necessário.

Para especificar as definições de configuração personalizado, separe o nome de cada um dos parâmetros com uma vírgula (,).

Aceder a definições de configuração personalizada a partir de um script, tem de sufixo do nome com um caráter de sublinhado ( \_ ) e o âmbito do parâmetro (Global, partição ou RunStep). Por exemplo, para o parâmetro de nome de ficheiro Global de acesso, utilize este fragmento de código:`$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>Capacidades
O separador de capacidades do Designer de agente de gestão define o comportamento e a funcionalidade do conector. Não não possível alterar as seleções efetuadas neste separador, quando o conector foi criado. Esta tabela lista as definições de capacidade.

![Capacidades](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| Capacidade | Descrição |
| --- | --- |
| [Estilo de nome único][dnstyle] |Indica se o conector suporta o nomes únicos e por isso, o que estilo. |
| [Tipo de exportação][exportT] |Determina o tipo de objetos que são apresentadas para o script de exportação. <li>AttributeReplace – inclui o conjunto completo de valores para um atributo com múltiplos valor, quando o atributo é alterado.</li><li>AttributeUpdate – inclui apenas os deltas para um atributo com múltiplos valor, quando o atributo é alterado.</li><li>MultivaluedReferenceAttributeUpdate - inclui um conjunto completo de valores de atributos com múltiplos valores não seja de referência e deltas apenas para atributos de referência com múltiplos valores.</li><li>ObjectReplace – inclui todos os atributos de um objeto quando quaisquer alterações de atributos</li> |
| [Normalização de dados][DataNorm] |Indica o serviço de sincronização para normalizar os atributos de âncora antes de que forem fornecidos scripts. |
| [Confirmação de objeto][oconf] |Configura o comportamento de importação pendente no serviço de sincronização. <li>Normal – comportamento predefinido que espera exportadas todas as alterações a ser confirmada através de importação</li><li>NoDeleteConfirmation – quando é eliminado um objeto, não há nenhum importação pendente gerada.</li><li>NoAddAndDeleteConfirmation – quando um objeto é criado ou eliminado, não há nenhum importação pendente gerada.</li> |
| Utilizar DN como âncora |Se o estilo de nome único é definido para o LDAP, o atributo âncora para o espaço de conector também é o nome único. |
| Operações simultâneas de vários conectores |Quando selecionado, vários conectores do Windows PowerShell podem ser executados em simultâneo. |
| Partições |Quando selecionado, o conector suporta várias partições e deteção de partição. |
| hierarquia |Quando selecionado, o conector suporta uma estrutura hierárquica de estilo LDAP. |
| Ativar a importar |Quando selecionado, o conector importa dados através de scripts de importação. |
| Ativar a importação Delta |Quando selecionado, o conector pode solicitar deltas dos scripts de importação. |
| Ativar a exportação |Quando selecionado, o conector exporta dados através de scripts de exportação. |
| Ativar a exportação completa |Quando a opção estiver marcada, os scripts de exportação suportam exportar o espaço de conector completo. Para utilizar esta opção, a ativar exportar tem de ser verificado. |
| Não existem valores de referência de passagem de exportar primeiro |Quando a opção estiver marcada, os atributos de referência são exportados numa segunda passagem de exportação. |
| Ativar a mudança de nome de objeto |Quando a opção estiver marcada, os nomes únicos podem ser modificados. |
| Eliminar-adicionar, substituir |Quando selecionado, elimine-adicione operações são exportadas como um único de substituição. |
| Permitir operações de palavra-passe |Quando a opção estiver marcada, os scripts de sincronização de palavra-passe são suportadas. |
| Ativar a palavra-passe de exportação na primeira passagem |Quando a opção estiver marcada, as palavras-passe definida durante o aprovisionamento são exportadas quando o objeto ser criado. |

### <a name="global-parameters"></a>Parâmetros globais
O separador de parâmetros globais no estruturador de agente de gestão permite-lhe configurar scripts do Windows PowerShell que são executados pelo conector. Também pode configurar global valores para definições de configuração personalizada definidas no separador de conectividade.

**Deteção de partição**  
Uma partição é um espaço de nomes separado dentro de um esquema partilhado. Por exemplo, cada domínio no Active Directory é uma partição dentro de uma floresta. Uma partição é o agrupamento lógico de importação e exportação de operações. Importação e exportação têm partição, tal como acontece de um contexto e todas as operações neste contexto. As partições são deveria para representar uma hierarquia no LDAP. O nome distinto de uma partição é utilizado na importação para verificar se todos os objetos devolvidos são dentro do âmbito de uma partição. O nome distinto da partição também é utilizado durante o aprovisionamento do metaverso ao espaço de conector para determinar a partição de que um objeto deve ser associado durante a exportação.

O script de deteção de partição recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |

O script tem de devolver um a um único [partição] [ part] objeto ou uma lista [T] de objetos de partição para o pipeline.

**Deteção de hierarquia**  
O script de deteção de hierarquia só é utilizado quando a capacidade de estilo de nome único LDAP. O script é utilizado para lhe permitem procurar e selecionar um conjunto de contentores que é considerado dentro ou fora do âmbito de importação e exportação de operações. O script só deve fornecer uma lista de nós que são elementos subordinados diretos do nó raiz fornecido para o script.

O script de deteção de hierarquia recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| ParentNode |[HierarchyNode][hn] |O nó de raiz da hierarquia sob a qual o script deverá devolver a elementos subordinados diretos. |

O script tem de devolver um a um objeto de HierarchyNode único subordinado ou uma lista [T] objetos de HierarchyNode subordinados para o pipeline.

#### <a name="import"></a>Importar
Os conectores que suportam operações de importação tem de implementar scripts de três.

**Começar a importar**  
O script de importação de begin é executado no início de um passo de importação executar. Durante este passo, pode estabelecer uma ligação ao sistema de origem e executar passos preparatórios antes de importar dados do sistema ligado.

O script de importação de begin recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informa o script sobre o tipo de partição de importação executar (delta ou completo), hierarquia, marca d'água e tamanho da página esperado. |
| Tipos de |[Esquema][schema] |Esquema para o espaço de conector que é importado. |

O script tem de devolver um único [OpenImportConnectionResults] [ oicres] objeto para o pipeline, por exemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`

**Importar dados**  
O script de dados de importação é chamado pelo conector até que o script não indica que não existe nenhum mais dados para importar. O conector do Windows PowerShell tem um tamanho de página de 9,999 objetos. Se o script devolver mais do que 9,999 objetos para importação, tem de suportar a paginação. O expõe conector denomina-se uma propriedade de dados personalizados que pode utilizar para um arquivo de uma marca d'água para que cada vez que o script de dados de importação, o script de retoma importar objetos onde parou.

O script de dados de importação recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| GetImportEntriesRunStep |[ImportRunStep][irs] |Contém marca de água (CustomData) que pode ser utilizada durante a paginada importações e importa delta. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informa o script sobre o tipo de partição de importação executar (delta ou completo), hierarquia, marca d'água e tamanho da página esperado. |
| Tipos de |[Esquema][schema] |Esquema para o espaço de conector que é importado. |

O script de dados de importação tem de escrever uma lista [[CSEntryChange][csec]] para o pipeline. Esta coleção é composta por CSEntryChange atributos que representam a cada objeto que está a ser importado. Durante uma importação completa, esta coleção deve ter um conjunto completo de objetos de CSEntryChange que têm todos os atributos para cada objeto. Durante um Delta importar o objeto de CSEntryChange está deve conter as diferenças de nível de atributo para cada objeto para importar ou uma representação completa dos objetos que tenham sido alterados (modo de substituir).

**Importação de fim**  
A conclusão da execução de importação, é execute o script de importar do fim. Este script deve executar quaisquer tarefas de limpeza necessárias (por exemplo, fechar ligações aos sistemas) e responder a falhas.

O script de importação de fim recebe os seguintes parâmetros do conector do:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |Informa o script sobre o tipo de partição de importação executar (delta ou completo), hierarquia, marca d'água e tamanho da página esperado. |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |Informa o script sobre o motivo pelo qual que a importação foi terminada. |

O script tem de devolver um único [CloseImportConnectionResults] [ cicres] objeto para o pipeline, por exemplo:`Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`

#### <a name="export"></a>Exportar
Idêntica à arquitetura de importação do conector, conectores que suportam a exportação tem de implementar scripts de três.

**Exportação de começar**  
O script de exportação de begin é executado no início de um passo de execução de exportação. Durante este passo, pode estabelecer uma ligação ao sistema de origem e realizar quaisquer passos preparatórios antes de exportar dados para o sistema ligado.

O script de exportação de begin recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informa o script sobre o tipo de partição de execução de exportação (delta ou completo), a hierarquia e o tamanho da página esperado. |
| Tipos de |[Esquema][schema] |Esquema para o espaço de conector que seja exportado. |

O script não deve devolver quaisquer dados para o pipeline.

**Exportar dados**  
O serviço de sincronização chama o script de exportar dados tantas vezes quanto é necessário para processar todas as exportações pendentes. Se o espaço de conector tiver mais exportações pendentes que o tamanho de página o conector, o script de dados de exportação pode ser chamado a várias vezes e, possivelmente, várias vezes para o mesmo objeto.

O script de dados de exportação recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| CSEntries |IList[CSEntryChange][csec] |Lista de todo o espaço de conector objetos com pendentes exporta a serem processados durante este passo. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informa o script sobre o tipo de partição de execução de exportação (delta ou completo), a hierarquia e o tamanho da página esperado. |
| Tipos de |[Esquema][schema] |Esquema para o espaço de conector que seja exportado. |

O script de dados de exportação tem de devolver um [PutExportEntriesResults] [ peeres] para o pipeline. Este objeto não é necessário incluir informações de resultado para cada conector exportado ocorra um erro ou uma alteração ao atributo âncora. Por exemplo, para devolver um objeto de PutExportEntriesResults para o pipeline:`Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**Exportação de fim**  
Na conclusão da exportação executar, o script de exportação do fim a executar. Este script deve executar quaisquer tarefas de limpeza necessárias (por exemplo, fechar ligações aos sistemas) e responder a falhas.

O script de exportação de fim recebe os seguintes parâmetros do conector do:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |Informa o script sobre o tipo de partição de execução de exportação (delta ou completo), a hierarquia e o tamanho da página esperado. |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |Informa o script sobre o motivo pelo qual que a exportação foi terminada. |

O script não deve devolver quaisquer dados para o pipeline.

#### <a name="password-synchronization"></a>Sincronização de palavra-passe
Conectores do Windows PowerShell podem ser utilizados como um destino para as alterações/reposições de palavra-passe.

O script de palavra-passe recebe os seguintes parâmetros de conector:

| Nome | Tipo de dados | Descrição |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][cadeia, [ConfigParameter][cp]] |Tabela de parâmetros de configuração para o conector. |
| Credencial |[PSCredential][pscred] |Contém as credenciais introduzidas pelo administrador no separador de conectividade. |
| Partição |[Partição][part] |Partição de diretório que está a ser o CSEntry. |
| CSEntry |[CSEntry][cse] |Entrada de espaço de conector para o objeto que seja recebida uma alteração de palavra-passe ou a reposição. |
| OperationType |Cadeia |Indica se a operação é uma reposição (**SetPassword**) ou uma alteração (**ChangePassword**). |
| PasswordOptions |[PasswordOptions][pwdopt] |Comportamento de reposição de sinalizadores que especificam a palavra-passe pretendida. Este parâmetro só está disponível se OperationType **SetPassword**. |
| OldPassword |Cadeia |Preenchidos com antigo palavra-passe o objeto para as alterações de palavra-passe. Este parâmetro só está disponível se OperationType **ChangePassword**. |
| NewPassword |Cadeia |Preenchidos com nova palavra-passe o objeto que o script deve ser definido. |

O script de palavra-passe não é esperado para devolver resultados para o pipeline do Windows PowerShell. Se ocorrer um erro no script de palavra-passe, o script deve acionar um dos seguintes exceções para informar o serviço de sincronização sobre o problema:

* [PasswordPolicyViolationException] [ pwdex1] – emitida se a palavra-passe não cumprem a política de palavra-passe no sistema ligado.
* [PasswordIllFormedException] [ pwdex2] – emitida se a palavra-passe não é aceitável para o sistema ligado.
* [PasswordExtension] [ pwdex3] – emitida para todos os outros erros no script de palavra-passe.

## <a name="sample-connectors"></a>Conectores de exemplo
Para obter uma descrição completa dos conectores exemplo disponíveis, consulte [recolha de conector do Windows PowerShell conector exemplo][samp].

## <a name="other-notes"></a>Outras notas
### <a name="additional-configuration-for-impersonation"></a>Configuração adicional para representação
Conceda ao utilizador que é representado as seguintes permissões no servidor do serviço de sincronização:

Acesso de leitura para as seguintes chaves do registo:

* HKEY_USERS\\\Software\Microsoft\PowerShell [SynchronizationServiceServiceAccountSID]
* HKEY_USERS\\\Environment [SynchronizationServiceServiceAccountSID]

Para determinar o identificador de segurança (SID) da conta de serviço do serviço de sincronização, execute os seguintes comandos do PowerShell:

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

Acesso de leitura para as seguintes pastas de sistema de ficheiros:

* %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft forefront Identity Manager\2010\Synchronization Service\MaData\\{o ConnectorName}

Substitua o nome do conector do Windows PowerShell para o marcador de posição {o ConnectorName}.

## <a name="troubleshooting"></a>Resolução de problemas
* Para obter informações sobre como ativar o registo resolver o conector, consulte o [como ativar o rastreio do ETW para conectores](http://go.microsoft.com/fwlink/?LinkId=335731).

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291
