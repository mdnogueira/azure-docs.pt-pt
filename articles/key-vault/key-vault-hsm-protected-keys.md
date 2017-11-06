---
title: Como gerar e transferir as chaves protegidas de HSM para o Cofre de chaves do Azure | Microsoft Docs
description: "Utilize este artigo para o ajudar a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para utilizar com o Cofre de chaves do Azure. Também conhecido como BYOK ou traga a sua própria chave."
services: key-vault
documentationcenter: 
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 51abafa1-812b-460f-a129-d714fdc391da
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: barclayn
ms.openlocfilehash: 6c49b086fd35a855fa8e32fa576c5b52d16f1d04
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2017
---
# <a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>As chaves como gerar e transferir protegida por HSM para o Cofre de chaves do Azure
## <a name="introduction"></a>Introdução
Para maior segurança, quando utiliza o Cofre de chaves do Azure, pode importar ou gerar chaves nos módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Este cenário é frequentemente referido como *traga a sua própria chave*, ou BYOK. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. O Cofre de chaves do Azure utiliza da família nShield da Thales HSMs para proteger as suas chaves.

Utilize as informações neste tópico para o ajudar a planear, gerar e, em seguida, transferir as suas próprias chaves protegidas por HSM para utilizar com o Cofre de chaves do Azure.

Esta funcionalidade não está disponível para o Azure China.

> [!NOTE]
> Para mais informações sobre o Cofre de chaves do Azure, consulte [que é o Cofre de chaves do Azure?](key-vault-whatis.md)  
>
> Para um tutorial de introdução, que incluem a criação de um cofre de chaves para chaves protegidas de HSM, consulte [introdução ao Cofre de chaves do Azure](key-vault-get-started.md).
>
>

Mais informações sobre a gerar e transferir uma chave protegida por HSM através da Internet:

* Gerar a chave a partir de uma estação de trabalho offline, o que reduz a superfície de ataque.
* A chave for encriptada com uma chave de troca (KEK), que permanece encriptada até ser transferida para os HSMs do Azure chave de cofre. Apenas a versão encriptada da sua chave deixa a estação de trabalho original.
* O conjunto de ferramentas define as propriedades da chave de inquilino que está vinculado a sua chave para o universo de segurança do Cofre de chaves do Azure. Por isso, depois dos HSMs do Azure chave de cofre receberem e desencriptarem a chave, apenas estes HSMs utilizá-lo. Não é possível exportar a chave. Este vínculo é imposto pelos HSMs da Thales.
* Chave de troca de chaves (KEK) que é utilizado para encriptar a chave é gerada os HSMs de Cofre de chaves do Azure e não é exportável. Os HSMs impõem que não pode existir uma versão não encriptada da KEK fora dos mesmos. Além disso, o conjunto de ferramentas inclui um atestado da Thales que a KEK não é exportável e que foi gerada num HSM genuíno que foi fabricado pela Thales.
* O conjunto de ferramentas inclui um atestado da Thales que o universo de segurança do Cofre de chaves do Azure também foi gerado num HSM genuíno fabricado pela Thales. Este atestado prova Microsoft está a utilizar hardware genuíno.
* A Microsoft utiliza KEKs separadas e Universos de segurança em cada região geográfica separados. Esta separação garante que a sua chave pode ser utilizada apenas nos centros de dados na região na qual a encriptou. Por exemplo, não é possível utilizar uma chave de um cliente Europeu nos centros de dados América do Norte ou Ásia.

## <a name="more-information-about-thales-hsms-and-microsoft-services"></a>Mais informações sobre serviços HSMs da Thales e da Microsoft
Da Thales e-Security é dos principais fornecedores globais e soluções de segurança informático para de serviços financeiros, alta tecnologia, fabrico, administração pública e setores de tecnologia de encriptação de dados. Com um ano de 40 historial de proteger empresarial e informações de government, as soluções da Thales são utilizadas por quatro as empresas do setor energético e aeroespacial maiores cinco. As soluções também sejam utilizadas pelo 22 países da NATO e proteger mais de 80 por cento de transações de pagamento em todo o mundo.

A Microsoft tem colaborado com a Thales para melhorar a tecnologia dos HSMs. Estas melhorias permitem-lhe obter as vantagens típicas dos serviços alojados sem abdicar do controlo as suas chaves. Especificamente, estas melhorias permitem que a Microsoft gerir os HSMs para que não é necessário. Como um serviço em nuvem, o Cofre de chaves do Azure ajusta-se com rapidez para satisfazer os picos de utilização da sua organização. Ao mesmo tempo, a sua chave está protegida no interior de HSMs da Microsoft: mantém o controlo ao longo do ciclo de vida da chave porque gerar a chave e transferi-la para HSMs da Microsoft.

## <a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementar traga a sua própria chave (BYOK) para o Cofre de chaves do Azure
Utilize as seguintes informações e procedimentos se irá gerar a sua própria chave protegida por HSM e, em seguida, transfira-o ao Cofre de chaves do Azure – traga seu cenário de chave (BYOK).

## <a name="prerequisites-for-byok"></a>Pré-requisitos para o BYOK
Consulte a tabela seguinte para obter uma lista dos pré-requisitos de traga a sua própria chave (BYOK) para o Cofre de chaves do Azure.

| Requisito | Mais informações |
| --- | --- |
| Uma subscrição do Azure |Para criar um cofre de chaves do Azure, terá de uma subscrição do Azure: [inscrever-se a versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) |
| A camada de serviços do Azure Premium do Cofre de chave para suportar chaves protegidas de HSM |Para obter mais informações sobre as camadas de serviços e capacidades para o Cofre de chaves do Azure, consulte o [preços do Azure chave de cofre](https://azure.microsoft.com/pricing/details/key-vault/) Web site. |
| HSM da Thales, smart cards e software de suporte |Tem de ter acesso a um módulo de Hardware de segurança da Thales e conhecimentos operacionais básicos dos Hmss da Thales. Consulte [módulo de Hardware de segurança da Thales](https://www.thales-esecurity.com/msrms/buy) para obter a lista de modelos compatíveis ou para comprar um HSM, se não tiver um. |
| O seguinte hardware e software:<ol><li>Um offline x64 estação de trabalho com um sistema operativo do Windows 7 e da Thales software nShield, pelo menos, versão 11.50.<br/><br/>Se esta estação de trabalho executa o Windows 7, tem de [instalar Microsoft .NET Framework 4.5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Uma estação de trabalho que está ligada à Internet e tem um sistema de operativo mínimo do Windows do Windows 7 e [Azure PowerShell](/powershell/azure/overview) **versão mínima 1.1.0** instalado.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil que tenha pelo menos 16 MB de espaço livre.</li></ol> |Por motivos de segurança, recomendamos que a primeira estação de trabalho não está ligada a uma rede. No entanto, esta recomendação não é imposta através de programação.<br/><br/>Tenha em atenção que nas instruções que se seguem, esta estação de trabalho é referida como a estação de trabalho desligada.</p></blockquote><br/>Além disso, se a chave de inquilino para uma rede de produção, recomendamos que utilize uma segunda estação de trabalho separada para transferir o conjunto de ferramentas e carregar a chave de inquilino. Mas, para fins de teste, pode utilizar a mesma estação de trabalho como primeiro.<br/><br/>Tenha em atenção que nas instruções que se seguem, esta segunda estação de trabalho é referida como a estação de trabalho ligada à Internet.</p></blockquote><br/> |

## <a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gerar e transferir a chave para o HSM de Cofre de chaves do Azure
Irá utilizar os seguintes cinco passos para gerar e transferir a chave para um HSM de Cofre de chaves do Azure:

* [Passo 1: Preparar a estação de trabalho ligada à Internet](#step-1-prepare-your-internet-connected-workstation)
* [Passo 2: Preparar a estação de trabalho desligada](#step-2-prepare-your-disconnected-workstation)
* [Passo 3: Gerar a chave](#step-3-generate-your-key)
* [Passo 4: Preparar a chave de transferência](#step-4-prepare-your-key-for-transfer)
* [Passo 5: Transferir a chave para o Cofre de chaves do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passo 1: Preparar a estação de trabalho ligada à Internet
Neste primeiro passo, efetue os seguintes procedimentos na estação de trabalho que está ligado à Internet.

### <a name="step-11-install-azure-powershell"></a>Passo 1.1: Instalar o Azure PowerShell
Da estação de trabalho ligada à Internet, transfira e instale o módulo Azure PowerShell, que inclui os cmdlets para gerir o Cofre de chaves do Azure. Isto requer a versão mínima do 0.8.13.

Para obter instruções de instalação, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

### <a name="step-12-get-your-azure-subscription-id"></a>Passo 1.2: Obter o ID de subscrição do Azure
Iniciar uma sessão do Azure PowerShell e inicie sessão na sua conta do Azure utilizando o seguinte comando:

        Add-AzureAccount
Na janela pop-up do browser, introduza o seu nome de utilizador da conta do Azure e a palavra-passe. Em seguida, utilize o [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) comando:

        Get-AzureSubscription
O resultado, localize o ID da subscrição que irá utilizar para o Cofre de chaves do Azure. Este ID de subscrição que irá precisar posteriormente.

Não feche a janela do PowerShell do Azure.

### <a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Passo 1.3: Transferir o conjunto de ferramentas BYOK para o Cofre de chaves do Azure
Vá para a Microsoft Download Center e [transferir o conjunto de ferramentas BYOK de Cofre de chaves do Azure](http://www.microsoft.com/download/details.aspx?id=45345) para a região geográfica ou a instância do Azure. Utilize as seguintes informações para identificar o nome do pacote de transferir e o hash do pacote de SHA-256 correspondente:

- - -
**Estados Unidos:**

States.zip Unidas de KeyVault BYOK ferramentas

2E8C00320400430106366A4E8C67B79015524E4EC24A2D3A6DC513CA1823B0D4

- - -
**Europa:**

KeyVault BYOK-ferramentas Europe.zip

9AAA63E2E7F20CF9BB62485868754203721D2F88D300910634A32DFA1FB19E4A

- - -
**Ásia:**

KeyVault BYOK-ferramentas AsiaPacific.zip

4BC14059BF0FEC562CA927AF621DF665328F8A13616F44C977388EC7121EF6B5

- - -
**América Latina:**

KeyVault BYOK-ferramentas LatinAmerica.zip

E7DFAFF579AFE1B9732C30D6FD80C4D03756642F25A538922DD1B01A4FACB619

- - -
**Japão:**

KeyVault BYOK-ferramentas Japan.zip

3933C13CC6DC06651295ADC482B027AF923A76F1F6BF98B4D4B8E94632DEC7DF

- - -
**Coreia:**

KeyVault BYOK-ferramentas Korea.zip

71AB6BCFE06950097C8C18D532A9184BEF52A74BB944B8610DDDA05344ED136F

- - -
**Austrália:**

KeyVault BYOK-ferramentas Australia.zip

CD0FB7365053DEF8C35116D7C92D203C64A3D3EE2452A025223EEB166901C40A

- - -
[**Azure Government:**](https://azure.microsoft.com/features/gov/)

KeyVault BYOK-ferramentas USGovCloud.zip

F8DB2FC914A7360650922391D9AA79FF030FD3048B5795EC83ADC59DB018621A

- - -
**EUA DOD:**

KeyVault BYOK-ferramentas USGovernmentDoD.zip

A79DD8C6DFFF1B00B91D1812280207A205442B3DDF861B79B8B991BB55C35263

- - -
**Canadá:**

KeyVault BYOK-ferramentas Canada.zip

61BE1A1F80AC79912A42DEBBCC42CF87C88C2CE249E271934630885799717C7B

- - -
**Alemanha:**

KeyVault BYOK-ferramentas Germany.zip

5385E615880AAFC02AFD9841F7BADD025D7EE819894AA29ED3C71C3F844C45D6

- - -
**Índia:**

KeyVault BYOK-ferramentas India.zip

49EDCEB3091CF1DF7B156D5B495A4ADE1CFBA77641134F61B0E0940121C436C8

- - -
**Reino Unido:**

KeyVault BYOK-ferramentas UnitedKingdom.zip

432746BD0D3176B708672CCFF19D6144FCAA9E5EB29BB056489D3782B3B80849

- - -

Para validar a integridade da sua transferido conjunto de ferramentas BYOK, da sessão do Azure PowerShell, utilize o [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) cmdlet.

    Get-FileHash KeyVault-BYOK-Tools-*.zip

O conjunto de ferramentas inclui o seguinte:

* Um pacote de chave de troca de chaves (KEK) que tem um nome que começa com **BYOK-KEK - pkg-.**
* Um pacote de universo de segurança que tem um nome que começa com **BYOK-SecurityWorld - pkg-.**
* Um script de python com o nome **verifykeypackage.py.**
* Um ficheiro executável da linha de comandos denominado **KeyTransferRemote.exe** e DLLs associados.
* Um Visual C++ Redistributable Package, com o nome **vcredist_x64.exe.**

Copie o pacote para uma pen USB ou outro armazenamento portátil.

## <a name="step-2-prepare-your-disconnected-workstation"></a>Passo 2: Preparar a estação de trabalho desligada
Para este passo segundo, efetue os seguintes procedimentos na estação de trabalho que não está ligado a uma rede (Internet ou rede interna).

### <a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>Passo 2.1: Preparar a estação de trabalho desligada com o HSM da Thales
Instalar o software de suporte nCipher (Thales) num computador Windows e, em seguida, anexe um HSM da Thales a esse computador.

Certifique-se de que as ferramentas de Thales estão no seu caminho (**%nfast_home%\bin**). Por exemplo, escreva o seguinte:

        set PATH=%PATH%;"%nfast_home%\bin"

Para obter mais informações, consulte o guia de utilizador incluído no HSM da Thales.

### <a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Passo 2.2: Instalar o conjunto de ferramentas BYOK na estação de trabalho desligada
Copie o conjunto de ferramentas byok da unidade USB ou outro armazenamento portátil e, em seguida, efetue o seguinte:

1. Extraia os ficheiros do pacote transferido para qualquer pasta.
2. A partir da pasta, execute vcredist_x64.exe.
3. Siga as instruções para a instalar os componentes do Visual C++ runtime para o Visual Studio 2013.

## <a name="step-3-generate-your-key"></a>Passo 3: Gerar a chave
Para este passo terceiro, efetue os seguintes procedimentos na estação de trabalho desligada. Para concluir este passo seu HSM deve estar no modo de inicialização. 


### <a name="step-31-change-the-hsm-mode-to-i"></a>Passo 3.1: Altere o modo HSM para' '
Se estiver a utilizar nShield da Thales limite, para alterar o modo de: 1. Utilize o botão de modo para realçar o modo necessário. 2. Dentro de alguns segundos, prima e mantenha premido o botão Limpar para alguns segundos. Se alterar o modo, LED o novo modo interrompe flashing e permanece lit. O LED de estado poderão flash irregularly por alguns segundos e, em seguida, está intermitente regularmente quando o dispositivo está pronto. Caso contrário, o dispositivo permanece no modo de atual, com o modo adequado LED lit.

### <a name="step-32-create-a-security-world"></a>Passo 3.2: Criar um universo de segurança
Inicie uma linha de comandos e execute o programa de novo universo da Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa cria um **universo de segurança** ficheiros em % NFAST_KMDATA%\local\world, que corresponde à pasta C:\ProgramData\nCipher\Key Management Data\local. Pode utilizar valores diferentes para o quórum, mas no nosso exemplo, é-lhe pedido que introduza três cartões em branco e pins para cada um deles. Em seguida, os dois cartões dar acesso total ao universo de segurança. Estes cartões tornam-se a **conjunto de cartões de administrador** para o novo universo de segurança.

Em seguida, faça o seguinte:

* Cópia de segurança do ficheiro do universo. Proteger e proteger o ficheiro do universo, os cartões de administrador e os respetivos pins e certifique-se de que nenhuma pessoa única tem acesso a mais do que um cartão.

### <a name="step-33-change-the-hsm-mode-to-o"></a>Passo 3.3: Altere o modo HSM para ' O'
Se estiver a utilizar nShield da Thales limite, para alterar o modo de: 1. Utilize o botão de modo para realçar o modo necessário. 2. Dentro de alguns segundos, prima e mantenha premido o botão Limpar para alguns segundos. Se alterar o modo, LED o novo modo interrompe flashing e permanece lit. O LED de estado poderão flash irregularly por alguns segundos e, em seguida, está intermitente regularmente quando o dispositivo está pronto. Caso contrário, o dispositivo permanece no modo de atual, com o modo adequado LED lit.


### <a name="step-34-validate-the-downloaded-package"></a>Passo 3.4: Validar o pacote transferido
Este passo é opcional mas recomendado para que possa validar o seguinte:

* A chave de troca de chaves que está incluído no conjunto de ferramentas foi gerada a partir de um HSM da Thales genuíno.
* O hash do universo de segurança que está incluído no conjunto de ferramentas foi gerado num HSM da Thales genuíno.
* A chave de troca não é exportável.

> [!NOTE]
> Para validar o pacote transferido, o HSM tem de estar ligado e tem de ter um universo de segurança no mesmo (tal como aquele que acabou de criar).
>
>

Para validar o pacote transferido:

1. Execute o script verifykeypackage.py ao escrever um dos seguintes, consoante a região geográfica ou a instância do Azure:

   * América do Norte:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
   * Para a Europa:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
   * Para a Ásia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
   * Para a América Latina:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
   * Para o Japão:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
   * Para a Coreia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-KOREA-1 -w BYOK-SecurityWorld-pkg-KOREA-1
   * Para a Austrália:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
   * Para [Azure Government](https://azure.microsoft.com/features/gov/), que utiliza a instância do Governo dos E.U.A. do Azure:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
   * Para EUA DOD:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-USDOD-1 -w BYOK-SecurityWorld-pkg-USDOD-1
   * Para Canadá:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
   * Para a Alemanha:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
   * Para Índia:

         "%nfast_home%\python\bin\python" verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
     > [!TIP]
     > O software da Thales inclui python em %NFAST_HOME%\python\bin
     >
     >
2. Confirme se vê o seguinte, que indica uma validação com êxito: **resultado: êxito**

Este script valida a cadeia de signatário até a chave de raiz da Thales. O hash desta chave de raiz é incorporado no script e respetivo valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Pode também confirmar este valor separadamente, visitando o [Web site da Thales](http://www.thalesesec.com/).

Agora, está pronto para criar uma nova chave.

### <a name="step-35-create-a-new-key"></a>Passo 3.5: Criar uma nova chave
Gerar uma chave, utilizando a Thales **generatekey** programa.

Execute o seguinte comando para gerar a chave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando executar este comando, utilize estas instruções:

* O parâmetro *proteger* tem de ser definida para o valor **módulo**, conforme mostrado. Esta ação cria uma chave protegida por um módulo. O conjunto de ferramentas BYOK não suporta chaves protegidas por OCS.
* Substitua o valor de *contosokey* para o **ident** e **plainname** com qualquer valor de cadeia. Para minimizar as tarefas administrativas adicionais e reduzir o risco de erros, recomendamos que utilize o mesmo valor para ambos. O **ident** valor tem de conter apenas números, travessões e letras minúsculas.
* O pubexp fica em branco (predefinição) neste exemplo, mas pode especificar valores específicos. Para obter mais informações, consulte a documentação da Thales.

Este comando cria um ficheiro de chave com token na pasta %NFAST_KMDATA%\local com um nome começado por **key_simple_**, seguido de **ident** que foi especificado no comando. Por exemplo: **key_simple_contosokey**. Este ficheiro contém uma chave encriptada.

Fazer uma cópia de segurança deste ficheiro de chave com token numa localização segura.

> [!IMPORTANT]
> Quando mais tarde transferir a chave ao Cofre de chaves do Azure, Microsoft não é possível exportar esta chave para, pelo que é extremamente importante que efetue cópias de segurança da chave e segurança do universo em segurança. Contacte a Thales para obter orientações e melhores práticas de cópia de segurança da sua chave.
>
>

Agora está pronto para transferir a chave para o Cofre de chaves do Azure.

## <a name="step-4-prepare-your-key-for-transfer"></a>Passo 4: Preparar a chave de transferência
Para este passo quarto, efetue os seguintes procedimentos na estação de trabalho desligada.

### <a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Passo 4.1: Criar uma cópia da sua chave com permissões reduzidas

Abra uma nova linha de comandos e altere o diretório atual para a localização onde unzipped que o ficheiro zip do BYOK. Para reduzir as permissões na sua chave, numa linha de comandos, execute um dos seguintes, consoante a região geográfica ou a instância do Azure:

* América do Norte:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
* Para a Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
* Para a Ásia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
* Para a América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
* Para o Japão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
* Para a Coreia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1
* Para a Austrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
* Para [Azure Government](https://azure.microsoft.com/features/gov/), que utiliza a instância do Governo dos E.U.A. do Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
* Para EUA DOD:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1
* Para Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
* Para a Alemanha:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
* Para Índia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1

Quando executar este comando, substitua *contosokey* com o mesmo valor que especificou no **passo 3.5: criar uma nova chave** do [gerar a chave de](#step-3-generate-your-key) passo.

É-lhe pedido para o plug-in seus cartões de administrador do universo de segurança.

Quando o comando for concluído, verá **resultado: êxito** e a cópia da sua chave com permissões reduzidas estão presentes no ficheiro denominado key_xferacid _<contosokey>.

Pode inspeciona o ACLS utilizando os seguintes comandos utilizando os utilitários da Thales:

* aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
* kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
  Quando executar estes comandos, substitua contosokey com o mesmo valor que especificou na **passo 3.5: criar uma nova chave** do [gerar a chave de](#step-3-generate-your-key) passo.

### <a name="step-42-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Passo 4.2: Encriptar a chave, utilizando a chave de troca da Microsoft
Execute um dos seguintes comandos, consoante a região geográfica ou a instância do Azure:

* América do Norte:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Ásia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para o Japão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Coreia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-KOREA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-KOREA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Austrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para [Azure Government](https://azure.microsoft.com/features/gov/), que utiliza a instância do Governo dos E.U.A. do Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para EUA DOD:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USDOD-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USDOD-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para a Alemanha:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
* Para Índia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey

Quando executar este comando, utilize estas instruções:

* Substitua *contosokey* com o identificador que utilizou para gerar a chave no **passo 3.5: criar uma nova chave** do [gerar a chave de](#step-3-generate-your-key) passo.
* Substitua *SubscriptionID* com o ID da subscrição do Azure que contém o seu Cofre de chaves. Obter este valor anteriormente, no **passo 1.2: obter o seu ID de subscrição do Azure** do [preparar a estação de trabalho ligada à Internet](#step-1-prepare-your-internet-connected-workstation) passo.
* Substitua *ContosoFirstHSMKey* com uma etiqueta que é utilizada para o nome de ficheiro de saída.

Quando estiver concluído com êxito, mostra **resultado: êxito** e existe um novo ficheiro na pasta atual, que tem o seguinte nome: KeyTransferPackage -*ContosoFirstHSMkey*. byok

### <a name="step-43-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Passo 4.3: Copiar o pacote de transferência da chave para a estação de trabalho ligada à Internet
Utilize uma pen USB ou outro armazenamento portátil para copiar o ficheiro de saída do passo anterior (KeyTransferPackage-ContosoFirstHSMkey.byok) para a estação de trabalho ligada à Internet.

## <a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passo 5: Transferir a chave para o Cofre de chaves do Azure
Para este passo final, na estação de trabalho ligada à Internet, utilize o [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurermkeyvaultkey) cmdlet para carregar o pacote de transferência da chave que copiou da estação de trabalho desligada para o HSM de Cofre de chaves do Azure:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\KeyTransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se o carregamento for bem-sucedido, verá apresentar as propriedades da chave que acabou de adicionar.

## <a name="next-steps"></a>Passos seguintes
Agora, pode utilizar esta chave protegida por HSM no seu Cofre de chaves. Para obter mais informações, consulte o **se pretender utilizar um módulo de segurança de hardware (HSM)** secção o [introdução ao Cofre de chaves do Azure](key-vault-get-started.md) tutorial.
