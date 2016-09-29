<properties
    pageTitle="Implementar a Prova de Conceito do Azure Stack | Microsoft Azure"
    description="Saiba como preparar a Prova de Conceito do Azure e executar o script do PowerShell para implementá-la."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/29/2016"
    ms.author="erikje"/>


# Implementar a Prova de Conceito do Azure Stack
Para implementar a Prova de Conceito do Azure Stack, tem primeiro de [preparar o computador de implementação](#prepare-the-deployment-machine) e, em seguida, [executar o script de implementação do PowerShell](#run-the-powershell-deployment-script).

## Preparar o computador de implementação

1. Verifique se pode ligar fisicamente ao computador de implementação ou ter acesso físico à consola (como KVM). Precisará desse acesso depois de reiniciar o computador de implementação no passo 9.
 
2. Verifique se o computador de implementação cumpre os [requisitos mínimos](azure-stack-deploy.md). Pode utilizar o [Deployment Checker for Azure Stack Technical Preview 1 (Verificador de Implementação do Azure Stack Technical Preview 1)](https://gallery.technet.microsoft.com/Deployment-Checker-for-76d824e1) para confirmar os requisitos.

3.  [Transfira](http://aka.ms/ReqOSforAzureStack) e instale o Windows Server 2016 Datacenter Edition Technical Preview 4 EN-US (Edição Completa).

4.  [Transfira](https://azure.microsoft.com/overview/azure-stack/try/?v=try) o pacote de implementação da Prova de Conceito do Azure Stack para uma pasta na unidade C, (por exemplo, C:\\AzureStack).

5.  Execute o ficheiro **Microsoft Azure Stack POC.exe**.

    Este procedimento cria a pasta \\Prova de Conceito do Microsoft Azure Stack\\ que contém os seguintes itens:

    -   DeployAzureStack.ps1: script do PowerShell de instalação da Prova de Conceito do Azure Stack

    -   MicrosoftAzureStackPOC.vhdx: pacote de dados do Azure Stack

    -   SQLServer2014.vhdx: VHD do SQL Server

    -   WindowsServer2012R2DatacenterEval.vhd

    -   WindowsServer2016Datacenter.vhdx: VHD do Windows Server 2016 Datacenter (inclui KB 3124262)

    > [AZURE.IMPORTANT] Tem de ter, pelo menos, 128 GB de espaço livre no volume de arranque físico.

6. Copie o WindowsServer2016Datacenter.vhdx para a unidade C:\ e mude o nome para MicrosoftAzureStackPOCBoot.vhdx.

7. No Explorador de Ficheiros, clique com o botão direito do rato em MicrosoftAzureStackPOCBoot.vhdx e clique em **Montar**.

8. Abra uma janela da Linha de Comandos como administrador e execute o comando bcdboot abaixo. Este comando cria um ambiente de arranque duplo. A partir deste ponto, o arranque deve ser feito na opção de arranque superior.

        bcdboot <mounted drive letter>:\windows

9. Reinicie o computador. Irá automaticamente executar a Configuração do Windows à medida que o sistema VHD é preparado. A partir deste momento, terá de ligar fisicamente ao computador de implementação ou ter acesso físico à consola para concluir os passos seguintes.

10. Se o seu BIOS incluir essa opção, deverá configurá-lo para utilizar a hora local em vez da hora UTC.

11. Quando lhe for solicitado, indique o seu país, idioma, teclado e outras preferências. Se lhe for solicitada a chave do produto, poderá encontrá-la em [Requisitos do Sistema e Instalação](https://technet.microsoft.com/library/mt126134.aspx).

12. Quando lhe for pedido, defina a palavra-passe da conta de Administrador e, em seguida, inicie sessão com esse nome de conta e palavra-passe.

13. Depois de reiniciar o computador e iniciar sessão, se não tiver o DHCP, defina a configuração estática na NIC.

14. Para ativar as Ligações ao Ambiente de Trabalho Remoto, aceda às **Propriedades do Sistema** e selecione a opção **Permitir ligações remotas a este computador**.

15. Inicie sessão com uma conta local com permissões de administrador.

16. Verifique se **precisamente** quatro unidades para os dados da Prova de Conceito do Azure Stack:
  - São visíveis na gestão de discos
  - Não estão a ser utilizadas
  - São mostradas como Online, Não alocadas

17. Confira que o anfitrião não está associado a um domínio.

18. Com o Internet Explorer, verifique a conetividade da rede ao Azure.com.

> [AZURE.IMPORTANT] A implementação da Prova de Conceito TP1 suporta precisamente quatro unidades para as funcionalidades de armazenamento e apenas uma NIC para as redes.
>
> - **Para o armazenamento**, utilize o gestor de dispositivos ou a WMI para desativar todas as outras unidades (não basta colocar os discos offline através do gestor de discos).
>
> - **Para a rede**, se tiver várias NICs, confira que apenas uma está ativada (e todas as outras desativadas) antes de executar o script de implementação abaixo.
>
> Se tiver utilizado os passos de arranque do VHD definidos acima, terá de executar estas atualizações após o arranque no VHD e antes de iniciar o script de implementação.

## Executar o script de implementação do PowerShell

1. Mova os seguintes ficheiros de implementação de D:\Prova de Conceito do Azure Stack\ para C:\Prova de Conceito do Azure Stack\.
    - DeployAzureStack.ps1
    - MicrosoftAzureStackPOC.vhdx
    - SQLServer14.vhdx
    - WindowsServer2012R2DatacenterEval.vhd
    - WindowsServer2016Datacenter.vhdx

2.  Abra o PowerShell como um Administrador.

3.  No PowerShell, aceda à localização da pasta do Azure Stack (\\Prova de Conceito do Microsoft Azure Stack\\ se utilizou a localização predefinida).

4.  Execute o comando de implementação:

        .\DeployAzureStack.ps1 –Verbose

    Na China, utilize antes o seguinte comando:

        .\DeployAzureStack.ps1 –Verbose -UseAADChina $true

    A implementação começa e o nome de domínio da Prova de Conceito do Azure Stack é codificado como azurestack.local.

5.  No pedido **Introduzir a palavra-passe do administrador incorporado**, introduza uma palavra-passe e, em seguida, confirme-a. Esta será a palavra-passe para todas as máquinas virtuais. Não se esqueça de registar esta palavra-passe de Administrador de Serviços.

6.  Em **Inicie sessão na sua conta do Azure no menu de contexto Página de autenticação do Azure**, prima qualquer tecla para abrir a caixa de diálogo de início de sessão do Microsoft Azure.

7.  Introduza as credenciais da sua conta do Azure Active Directory. Este utilizador deve ser o Administrador Global no inquilino do diretório.

8.  Novamente no PowerShell, no pedido de confirmação de seleção da conta, introduza *s*. Esta ação cria dois utilizadores e três aplicações para o Azure Stack nesse inquilino do diretório: um utilizador administrador do Azure Stack, um utilizador inquilino dos testes TiP e uma aplicação para cada fornecedor de recursos do Portal, da API e de Monitorização. Para além disto, o instalador adiciona autorizações do Azure PowerShell, da CLI do XPlat e do Visual Studio para o referido Inquino do Diretório.

9.  No pedido **A Prova de Conceito do Microsoft Azure Stack está pronta a ser implementada. Continuar?**, introduza *s*.

10.  O processo de implementação demorará algumas horas, durante as quais irão ocorrer vários reinícios automatizados do sistema. Se iniciar sessão durante a implementação, será automaticamente aberta uma janela do PowerShell a indicar o progresso da implementação. A janela do PowerShell fecha-se assim que for concluída a implementação.

11. No computador da Prova de Conceito do Azure Stack, inicie sessão como AzureStack\administrador, abra o **Gestor do Servidores** e desative a **Configuração de Segurança Avançada do IE** para administradores e utilizadores.

Se a implementação falhar com um erro de hora ou data, configure o BIOS para utilizar a Hora Local em vez da UTC. Em seguida, implemente novamente.

Se o script falhar, reinicie o script. Se a falha persistir, apague e reinicie o script.

Pode localizar os registos dos scripts no anfitrião da Prova de Conceito `C:\ProgramData\microsoft\azurestack`.

### Parâmetros opcionais de DeployAzureStack.ps1

**AADCredential** (PSCredential) – Define o nome de utilizador e a palavra-passe do Azure Active Directory. Se este parâmetro não for fornecido, o script solicitar-lhe-á o nome de utilizador e a palavra-passe.

**AADTenant** (cadeia) – Define o diretório de inquilinos. Utilize este parâmetro para especificar um diretório específico se a conta do AAD tiver permissões para gerir vários diretórios. Se este parâmetro não for fornecido, o script solicitar-lhe-á o diretório.

**AdminPassword** (SecureString) – Define a palavra-passe de administrador predefinida. Se este parâmetro não for fornecido, o script solicitar-lhe-á a palavra-passe.

**Force** (Switch) – Define o cmdlet a ser executado sem confirmações.

**NATVMStaticGateway** (String) – Define o gateway predefinido utilizado no endereço IP estático da NATVM. Só utilize este parâmetro se o DHCP não conseguir atribuir um endereço IP válido para aceder à Internet. Se utilizar este parâmetro, também deverá utilizar o parâmetro NATVMStaticIP.
Por exemplo, `.\DeployAzureStack.ps1 –Verbose -NATVMStaticIP 10.10.10.10/24 – NATVMStaticGateway 10.10.10.1`

**NATVMStaticIP** (string) – Define um endereço IP estático adicional para a NATVM. Só utilize este parâmetro se o DHCP não conseguir atribuir um endereço IP válido para aceder à Internet.
Por exemplo, `.\DeployAzureStack.ps1 –Verbose -NATVMStaticIP 10.10.10.10/24`

**NoAutoReboot** (Switch) – Define o script a ser executado sem reinícios automáticos.

**ProxyServer** (String) – Define as informações de proxy. Só utilize este parâmetro se o seu ambiente tiver de utilizar um proxy para aceder à Internet. Não são suportados servidores de proxy que requeiram credenciais.
Por exemplo, `.\DeployAzureStack.ps1 -Verbose -ProxyServer 172.11.1.1:8080`

**PublicVLan** (String) – Define o ID da VLAN. Só utilize este parâmetro se o anfitrião e a NATVM tiverem de configurar o ID da VLAN para aceder à rede física (e à Internet).
Por exemplo, `.\DeployAzureStack.ps1 –Verbose –PublicVLan 305`

**TIPServiceAdminCredential** (PSCredential) – Define as credenciais de uma conta de administrador de serviços existente do Azure Active Directory. Esta conta é utilizada pelo TiP (Teste em Produção). Se este parâmetro não for fornecido, será automaticamente criada uma conta.

**TIPTenantAdminCredential** (PSCredential) – Define as credenciais de uma conta de administrador de inquilinos existente do Azure Active Directory. Esta conta é utilizada pelo TiP (Teste em Produção). Se este parâmetro não for fornecido, será automaticamente criada uma conta.

**UseAADChina**(Boolean) – Defina este parâmetro Booleano como $verdadeiro se pretender implementar a Prova de Conceito do Microsoft Azure Stack com o Azure China (Mooncake).

## Desative a funcionalidade de testes TiP automatizada (opcional)

O Microsoft Azure Stack Technical Preview 1 inclui um conjunto de testes de validação utilizados durante o processo de implementação e nos agendamentos diários recorrentes. Simulam ações executadas por um inquilino do Azure Stack, sendo criadas contas de utilizador Test-in-Poc (TiP) no Azure Active Directory com vista a executar os testes. Após uma implementação bem sucedida, pode desativar estes testes TiP. 

**Para desativar os testes automatizados TiP**

  - Na ClientVM, execute o seguinte cmdlet:

  `Disable-ScheduledTask -TaskName AzureStackSystemvalidationTask`

**Para ver os resultados do teste**

  - Na ClientVM, execute o seguinte cmdlet:

  `Get-AzureStackTiPTestsResult`



## Desativar a telemetria da Prova de Conceito do Microsoft Azure Stack (opcional)


Antes de implementar a Prova de Conceito do Microsoft Azure Stack, pode desativar a telemetria do Microsoft Azure Stack no computador a partir da qual é efetuada a implementação. Para desativar esta funcionalidade num único computador, veja: [http://windows.microsoft.com/pt-pt/windows-10/feedback-diagnostics-privacy-faq](http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq) e altere a definição **Dados de utilização e de diagnóstico** para **Básico**.



Depois de implementar a Prova de Conceito do Microsoft Azure Stack, pode desativar a telemetria em todas as máquinas virtuais associadas ao domínio do Azure Stack. Para criar uma política de grupo e gerir as definições de telemetria nessas máquinas virtuais, veja: [https://technet.microsoft.com/library/mt577208(v=vs.85).aspx\#BKMK\_UTC](https://technet.microsoft.com/library/mt577208%28v=vs.85%29.aspx#BKMK_UTC) e selecione **0** ou **1** para a política de grupo **Permitir Telemetria**. Existem duas máquinas virtuais (bgpvm e natvm) que não estão associadas ao domínio do Azure Stack. Para alterar separadamente as definições de Comentários e Diagnósticos nestas máquinas virtuais, veja: [http://windows.microsoft.com/pt-pt/windows-10/feedback-diagnostics-privacy-faq](http://windows.microsoft.com/en-us/windows-10/feedback-diagnostics-privacy-faq).

## Passos seguintes

[Ligar ao Azure Stack](azure-stack-connect-azure-stack.md)



<!--HONumber=Sep16_HO3-->


