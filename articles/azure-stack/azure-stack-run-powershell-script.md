---
title: Implementar o Kit de desenvolvimento de pilha do Azure | Microsoft Docs
description: "Saiba como preparar o Kit de desenvolvimento de pilha do Azure e execute o script do PowerShell para implementá-la."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 0ad02941-ed14-4888-8695-b82ad6e43c66
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 7/17/2017
ms.author: erikje
ms.openlocfilehash: b67cabf0ecdb48f137bfcfbce95eee568a1c298d
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="deploy-the-azure-stack-development-kit"></a>Implementar o Kit de desenvolvimento de pilha do Azure

*Aplica-se a: Azure da pilha Kit de desenvolvimento*

Para implementar o [Kit de desenvolvimento de pilha do Azure](azure-stack-poc.md), tem de concluir os seguintes passos:

1. [Transferir o pacote de implementação](https://azure.microsoft.com/overview/azure-stack/try/?v=try) para obter o Cloudbuilder.vhdx.
2. [Preparar o cloudbuilder.vhdx](#prepare-the-development-kit-host) executando o script de asdk installer.ps1 para configurar o computador (o anfitrião do kit de desenvolvimento) no qual pretende instalar o kit de desenvolvimento. Após este passo, irá arrancar o anfitrião do kit de desenvolvimento para o Cloudbuilder.vhdx.
3. [Implementar o kit de desenvolvimento](#deploy-the-development-kit) no anfitrião do kit de desenvolvimento.

> [!NOTE]
> Para obter os melhores resultados, mesmo se pretender utilizar um ambiente desligado de pilha do Azure, é melhor implementar enquanto estiver ligado à internet. Dessa forma, a versão de avaliação do Windows Server 2016 pode ser ativada no momento da implementação.
> 
> 

## <a name="download-and-extract-the-development-kit"></a>Transferir e extrair o kit de desenvolvimento
1. Antes de iniciar a transferência, certifique-se de que o computador cumpre os seguintes pré-requisitos:

   * O computador tem de ter, pelo menos, 60 GB de espaço livre em disco.
   * [.NET framework 4.6 (ou uma versão posterior)](https://aka.ms/r6mkiy) tem de estar instalado.

2. [Aceda à página Introdução ao](https://azure.microsoft.com/overview/azure-stack/try/?v=try), forneça os detalhes e clique em **submeter**.
3. Em **transferir o software**, clique em **Kit de desenvolvimento de pilha do Azure**.
4. Execute o ficheiro de AzureStackDownloader.exe transferido.
5. No **o dispositivo de transferência do Azure pilha Development Kit** janela, siga os passos 1 a 5.
6. Depois de concluída a transferência, clique em **executar** para iniciar o MicrosoftAzureStackPOC.exe.
7. Reveja as informações do assistente Self-Extractor e o ecrã de contrato de licença e, em seguida, clique em **seguinte**.
8. Reveja as informações do assistente Self-Extractor e o ecrã de declaração de privacidade e, em seguida, clique em **seguinte**.
9. Selecione o destino para os ficheiros extraídos, clique em **seguinte**.
   * A predefinição é: <drive letter>:\<pasta atual > \Microsoft pilha do Azure
10. Reveja as informações do assistente Self-Extractor e o ecrã de localização de destino e, em seguida, clique em **extrair** para extrair os ficheiros de ThirdPartyLicenses.rtf e CloudBuilder.vhdx (~ 25 GB). Este processo irá demorar algum tempo a concluir.

> [!NOTE]
> Depois de extrair os ficheiros, pode eliminar os ficheiros. exe e bin para recuperar espaço no computador. Em alternativa, pode mover estes ficheiros para outra localização para que, se precisa de Reimplementar não precisam de transferir os ficheiros novamente.
> 
> 

## <a name="prepare-the-development-kit-host"></a>Preparar o anfitrião do kit de desenvolvimento
1. Certifique-se de que fisicamente podem ligar para o anfitrião do kit de desenvolvimento ou tem o acesso à consola físico (por exemplo, KVM). Tem de ter esse acesso depois de reiniciar o anfitrião do kit de desenvolvimento no passo 13 abaixo.
2. Certifique-se de que o anfitrião do kit de desenvolvimento cumpre o [requisitos mínimos](azure-stack-deploy.md). Pode utilizar o [Verificador de implementação para a pilha de Azure](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar os seus requisitos.
3. Inicie sessão como administrador Local para o anfitrião do kit de desenvolvimento.
4. Copiar ou mover o ficheiro de CloudBuilder.vhdx na raiz da unidade C:\ (C:\CloudBuilder.vhdx).
5. Execute o seguinte script para transferir o ficheiro de instalador do kit de desenvolvimento (asdk installer.ps1) para a pasta de c:\AzureStack_Installer no anfitrião do kit de desenvolvimento.
    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
    $LocalPath = 'c:\AzureStack_Installer'

    # Create folder
    New-Item $LocalPath -Type directory

    # Download file
    Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
    ```
6. Abra uma consola elevada do PowerShell > execute o script de C:\AzureStack_Installer\asdk-installer.ps1 > clique **preparar o ambiente**.
7. No **selecione Cloudbuilder vhdx** página do instalador, procure e selecione o ficheiro de cloudbuilder.vhdx que transferiu nos passos anteriores.
8. Opcional: Verificar o **adicionar controladores** caixa para especificar uma pasta que contém os controladores que pretende no anfitrião.
9. No **definições opcionais** , indique a conta de administrador local para o anfitrião do kit de desenvolvimento. Se não fornecer estas credenciais, terá acesso KVM para o anfitrião durante o processo de instalação abaixo.
10. Ainda no **definições opcionais** página, tem a opção para definir o seguinte:
    - **ComputerName**: esta opção define o nome para o anfitrião do kit de desenvolvimento. O nome tem de cumprir os requisitos de FQDN e tem de ser 15 carateres ou menos. A predefinição é um nome de computador aleatório gerado pelo Windows.
    - **Fuso horário**: define o fuso horário para o anfitrião do kit de desenvolvimento. A predefinição é (UTC-8:00) hora do Pacífico (E.U.A. e Canadá).
    - **Configuração de IP estático**: define a sua implementação utilizar um endereço IP estático. Caso contrário, quando o instalador reinicia com o cloudbuilder.vhx, as interfaces de rede são configuradas com DHCP.
11. Clique em **Seguinte**.
12. Se optou por uma configuração de IP estático no passo anterior, tem de agora:
    - Selecione um adaptador de rede. Certifique-se de que pode ligar ao adaptador antes de clicar em **seguinte**.
    - Certifique-se de que o **endereço IP**, **Gateway**, e **DNS** valores estão corretos e, em seguida, clique em **seguinte**.
13. Clique em **seguinte** para iniciar o processo de preparação.
14. Quando a preparação indica **concluído**, clique em **seguinte**.
15. Clique em **reiniciar agora** para arrancar o cloudbuilder.vhdx e continuar o processo de implementação.

## <a name="deploy-the-development-kit"></a>Implementar o kit de desenvolvimento
1. Inicie sessão como administrador Local para o anfitrião do kit de desenvolvimento. Utilize as credenciais especificadas nos passos anteriores.

    > [!IMPORTANT]
    > Para implementações do Azure Active Directory, a pilha do Azure requer acesso à Internet, diretamente ou através de um proxy transparente. A implementação suporta exatamente um NIC de redes. Se tiver vários NICs, certifique-se de que só está ativado (e todos os outros estão desativados) antes de executar o script de implementação na secção seguinte.
    
2. Abra uma consola elevada do PowerShell > execute o script de \AzureStack_Installer\asdk-installer.ps1 (que pode ser numa unidade diferente no Cloudbuilder.vhdx) > clique **instalar**.
3. No **tipo** caixa, selecione **nuvem do Azure** ou **ADFS**.
    - **Nuvem do Azure**: Azure Active Directory é o fornecedor de identidade. Utilize este parâmetro para especificar um diretório específico, em que a conta do AAD tem permissões de administrador global. Nome completo de um inquilino do AAD. Por exemplo,. c o m. 
    - **ADFS**: O serviço de diretório de carimbo de predefinição é o fornecedor de identidade, a conta predefinida para iniciar sessão com azurestackadmin@azurestack.local, e a palavra-passe é fornecido como parte da configuração.
4. Em **palavra-passe de administrador Local**, além de **palavra-passe** caixa, escreva a palavra-passe de administrador local (que tem de coincidir com a palavra-passe de administrador local configurado atual) e, em seguida, clique em **Seguinte**.
5. Selecione um adaptador de rede a utilizar para o kit de desenvolvimento e, em seguida, clique em **seguinte**.
6. Selecione o DHCP ou a configuração de rede estático para a máquina virtual de BGPNAT01.
    - **DHCP** (predefinição): A máquina virtual obtém a configuração de rede IP do servidor DHCP.
    - **Estático**: Utilize esta opção apenas se o DHCP não é possível atribuir um endereço IP válido para a pilha do Azure aceder à Internet. Tem de ser especificado um endereço IP estático com o comprimento de subnetmask (por exemplo, 10.0.0.5/24).
7. Opcionalmente, defina os seguintes valores:
    - **ID de VLAN**: define o ID de VLAN. Utilize esta opção apenas se o anfitrião e AzS BGPNAT01 tem de configurar o ID de VLAN para aceder à rede física (e a Internet). 
    - **Reencaminhador DNS**: um servidor DNS é criado como parte da implementação da pilha do Azure. Para permitir que os computadores dentro da solução para resolver nomes fora de carimbo, forneça o seu servidor DNS da infraestrutura existente. O servidor DNS no carimbo reencaminha os pedidos de resolução de nome desconhecido para este servidor.
    - **Hora server**: Isto necessário campo define o servidor de tempo e tem de ser um endereço IP. Para localizar um servidor de tempo de endereço IP, visite [pool.ntp.org](http:\\pool.ntp.org) ou ping time.windows.com. 
8. Clique em **Seguinte**. 
9. No **verificar as propriedades de cartão de interface de rede** página, verá uma barra de progresso. 
    - Se diz **não é possível transferir uma atualização**, siga as instruções na página.
    - Quando indicar **concluído**, clique em **seguinte**.
10. No **resumo** página, clique em **implementar**.
11. Se estiver a utilizar uma implementação do Azure Active Directory, ser-lhe-á perguntado insira as credenciais de conta de administrador global do Azure Active Directory.
12. O processo de implementação pode demorar algumas horas, durante o qual o sistema automaticamente reinicia uma vez.
   
   > [!IMPORTANT]
   > Se pretender monitorizar o progresso da implementação, inicie sessão como azurestack\AzureStackAdmin. Se iniciar sessão como um administrador local depois do computador está associado ao domínio, não verá o progresso da implementação. Não execute novamente a implementação, em vez disso, inicie sessão como azurestack\AzureStackAdmin validar que está em execução.
   > 
   > 
   
    Quando a implementação for bem sucedida, será apresentada a consola do PowerShell: **concluída: a ação 'Implementação'**.
   
Se a implementação falhar, pode utilizar o seguinte script do PowerShell, volte a executar na mesma janela elevada do PowerShell:

```powershell
cd c:\CloudDeployment\Setup
.\InstallAzureStackPOC.ps1 -Rerun
```

Este script irá reiniciar a implementação do último passo que tiveram êxito.

Em alternativa, pode [Reimplementar](azure-stack-redeploy.md) a partir do zero.


## <a name="reset-the-password-expiration-to-180-days"></a>Repor a expiração de palavra-passe para 180 dias

Para se certificar de que a palavra-passe para o anfitrião do kit de desenvolvimento não expira demasiado cedo, siga estes passos depois de implementar:

Para alterar a política de expiração de palavra-passe a partir do Powershell:
1. A partir da janela do Powershell, execute o comando; Conjunto ADDefaultDomainPasswordPolicy - MaxPasswordAge 180.00:00:00-azurestack.local de identidade

Para alterar manualmente a política de expiração de palavra-passe:
1. No anfitrião do kit de desenvolvimento, abra **gestão de políticas de grupo** e navegue para **gestão de políticas de grupo** – **floresta: azurestack.local** – **domínios** – **azurestack.local**.
2. Clique com o botão direito **política de domínio predefinida** e clique em **editar**.
3. No Editor de grupo política de gestão, navegue para **configuração do computador** – **políticas** – **definições do Windows** – **definições de segurança**– **Políticas de conta** – **política de palavra-passe**.
4. No painel da direita, faça duplo clique **antiguidade de palavra-passe máximo**.
5. No **antiguidade de palavra-passe máximo propriedades** caixa de diálogo, altere o **palavra-passe expira em** valor 180, em seguida, clique em **OK**.


## <a name="next-steps"></a>Passos seguintes

[Instalar o PowerShell](azure-stack-powershell-configure-quickstart.md)

[Registar a pilha do Azure com a sua subscrição do Azure](azure-stack-register.md)

[Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)

