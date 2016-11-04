---
title: Instalação do agente do Azure AD Connect Health | Microsoft Docs
description: Esta é a página do Azure AD Connect Health que descreve a instalação do agente para AD FS e sincronização.
services: active-directory
documentationcenter: ''
author: karavar
manager: femila
editor: karavar

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/05/2016
ms.author: vakarand

---
# Instalação do Agente do Azure AD Connect Health
Este documento descreve a instalação e configuração dos Agentes do Azure AD Connect Health. Pode transferir os agentes de [aqui](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent).

## Requisitos
A tabela seguinte é uma lista de requisitos para utilizar o Azure AD Connect Health.

| Requisito | Descrição |
| --- | --- |
| Azure AD Premium |O Azure AD Connect Health é uma funcionalidade do Azure AD Premium e requer o Azure AD Premium. </br></br>Para obter mais informações, consulte [ Introdução ao Azure AD Premium](active-directory-get-started-premium.md) </br>Para iniciar uma versão de avaliação gratuita de 30 dias, consulte [Iniciar uma versão de avaliação.](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Tem de ser um administrador global do seu Azure AD para começar a utilizar o Azure AD Connect Health |Por predefinição, apenas os administradores globais podem instalar e configurar os agentes de estado de funcionamento para iniciar, aceder ao portal e executar operações no Azure AD Connect Health. Para informações adicionais, consulte [Administrar o seu diretório do Azure AD](active-directory-administer.md). <br><br> Utilizando um Controlo de Acesso Baseado em Funções, pode permitir o acesso ao Azure AD Connect Health a outros utilizadores na sua organização. Para mais informações consulte [Controlo de Acesso Baseado em Funções para o Azure AD Connect Health.](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control) </br></br>**Importante:** a conta que utiliza ao instalar os agentes deve ser uma conta escolar ou profissional e não pode ser uma conta Microsoft. Para mais informações, consulte [Inscrever-se no Azure como uma organização](sign-up-organization.md) |
| O Agente do Azure AD Connect Health é instalado em cada servidor de destino |O Azure AD Connect Health requer a instalação de um agente nos servidores de destino, que fornecerá os dados que são apresentados no portal. </br></br>Por exemplo, para obter dados acerca da sua infraestrutura do AD FS no local, o agente tem de estar instalado nos servidores do AD FS, nos servidores Proxy do AD FS e nos servidores Proxy de Web Apps. Do mesmo modo, para obter os dados na sua infraestrutura local do AD DS, o agente tem de ser instalado nos controladores de domínio. </br></br>**Importante:** a conta que utiliza ao instalar os agentes deve ser uma conta escolar ou profissional e não pode ser uma conta Microsoft.   Para mais informações consulte [Inscrever-se no Azure como uma organização](sign-up-organization.md) |
| Conectividade de saída para os pontos finais de serviço do Azure |Durante a instalação e tempo de execução, o agente necessita de conectividade aos pontos de fim de serviço do Azure AD Connect Health listados abaixo. Se bloqueia a conectividade de saída, certifique-se de que os seguintes são adicionados à lista de permitidos: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
| Portas da firewall do servidor onde o agente é executado. |O agente requer que as seguintes portas de firewall estejam abertas para que possa comunicar com os pontos finais de serviço do Azure AD Health.</br></br><li>TCP/UDP port 443</li><li>TCP/UDP port 5671</li> |
| Permitir os seguintes Web sites, caso esteja ativada a Segurança Avançada do IE |Os Web sites seguintes têm de estar permitidos, caso a Segurança Avançada do IE esteja ativada no servidor onde vai ser instalado o agente.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>O servidor de federação para a sua organização considerado fidedigno pelo o Azure Active Directory. Por exemplo: https://sts.contoso.com</li> |

## Instalar o agente Azure AD Connect Health para o AD FS
Para iniciar a instalação do agente, faça duplo clique no ficheiro .exe que transferiu. No primeiro ecrã, clique em Instalar.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Uma vez concluída a instalação, clique em Configurar Agora.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Este procedimento inicia uma linha de comandos seguida de comandos PowerShell, que irão executar o Registo AzureADConnectHealthADFSAgent. Ser-lhe-á pedido que inicie sessão no Azure. Faça-o, inicie sessão.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

Depois de iniciar sessão, o PowerShell prosseguirá. Uma vez concluído, pode fechar o PowerShell e a configuração estará concluída.

Nessa altura, os serviços deverão ser iniciados automaticamente e o agente estará agora a monitorizar e a recolher dados.  Tenha em atenção de que verá avisos na janela do PowerShell se não tiver cumprido todos os pré-requisitos que foram descritos nas secções anteriores. Antes de instalar o agente, certifique-se [aqui](active-directory-aadconnect-health-agent-install.md#requirements) de que concluiu todos os requisitos. A captura de ecrã abaixo mostra um exemplo destes erros.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Para verificar se o agente foi instalado, abra Serviços e procure o seguinte. Se concluiu a configuração, estes serviços devem estar a ser executados. Caso contrário, não arrancarão até a configuração estar concluída.

* Serviço de Diagnóstico do Azure AD Connect Health AD FS
* Serviço de Informações do Azure AD Connect Health AD FS
* Serviço de Monitorização do Azure AD Connect Health AD FS

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

### Instalação do agente em servidores do Windows Server 2008 R2
Para servidores do Windows Server 2008 R2, faça o seguinte:

1. Certifique-se de que o servidor está a ser executado com o Service Pack 1 ou superior.
2. Desative o IE ESC para instalação de agentes:
3. Antes de instalar o agente AD Health, instale o Windows PowerShell 4.0 em cada um dos servidores.  Para instalar o Windows PowerShell 4.0:
   * Instale o [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779) utilizando a seguinte ligação para transferir o instalador offline.
   * Instale o ISE do PowerShell (a partir de Funcionalidades do Windows)
   * Instale o [Windows Management Framework 4.0.](https://www.microsoft.com/download/details.aspx?id=40855)
   * Instale no servidor o Internet Explorer versão 10 ou posterior. É uma exigência do Serviço de Integridade para o autenticar com as suas credenciais de administrador do Azure.
4. Para obter informações adicionais sobre como instalar o Windows PowerShell 4.0 no Windows Server 2008 R2, consulte o artigo wiki [aqui](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx).

### Ativar a auditoria para o AD FS
Para que a funcionalidade de Análise de Utilização possa recolher e analisar dados, o agente do Azure AD Connect Health precisa das informações nos Registos de Auditoria do AD FS. Estes registos não estão ativados por predefinição. Isto aplica-se apenas aos servidores de federação do AD FS. Não é necessário ativar a auditoria em servidores Proxy do AD FS ou em servidores Proxy de Web Apps. Utilize os procedimentos seguintes para ativar a auditoria do AD FS e para localizar os registos de auditoria do AD FS.

#### Para ativar a auditoria para o AD FS 2.0
1. Clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e, em seguida, clique em **Política de Segurança Local**.
2. Navegue até à pasta **Security Settings\Local Policies\User Rights Management** e, em seguida, faça duplo clique em Gerar auditorias de segurança.
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS 2.0 está listada. Se não estiver presente, clique em **Adicionar Utilizador ou Grupo**, adicione-a à lista e, em seguida, clique em **OK**.
4. Abra uma linha de comandos com privilégios elevados e execute o seguinte comando para ativar a auditoria.<code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>
5. Feche a Política de Segurança Local e, em seguida, abra o snap-in Gestão.  Para abrir o snap-in Gestão, clique em **Iniciar**, aponte para **Programas**, aponte para **Ferramentas Administrativas** e, em seguida, clique em AD FS 2.0 Gestão.
6. No painel Ações, clique em Editar Propriedades do Serviço de Federação.
7. Na caixa de diálogo **Propriedades do Serviço de Federação**, clique no separador **Eventos**.
8. Selecione as caixas de verificação **Auditorias de êxito** e **Auditorias de falha**.
9. Clique em **OK**.

#### Para ativar a auditoria para o AD FS no Windows Server 2012 R2
1. Abra a **Política de Segurança Local** abrindo o **Gestor de Servidores** no ecrã Iniciar ou o Gestor de Servidores na barra de tarefas no ambiente de trabalho e clique em **Ferramentas/Política de Segurança Local**.
2. Navegue até à pasta **Security Settings\Local Policies\User Rights Assignment** e, em seguida, faça duplo clique em **Gerar auditorias de segurança**.
3. No separador **Definição de Segurança Local**, certifique-se de que a conta de serviço do AD FS está listada. Se não estiver presente, clique em **Adicionar Utilizador ou Grupo**, adicione-a à lista e, em seguida, clique em **OK**.
4. Abra uma linha de comandos com privilégios elevados e execute o seguinte comando para ativar a auditoria: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.</code>
5. Feche **Política de Segurança Local** e, em seguida, abra o snap-in **Gestão do AD FS** (no Gestor de Servidores, clique em Ferramentas e selecione Gestão do AD FS).
6. No painel Ações, clique em **Editar Propriedades do Serviço de Federação**.
7. Na caixa de diálogo Propriedades do Serviço de Federação, clique no separador **Eventos**.
8. Selecione as caixas de verificação **Auditorias de êxito e Auditorias de falha** e clique em **OK**.

#### Para localizar os registos de auditoria do AD FS
1. Abra o **Visualizador de Eventos**.
2. Aceda aos Registos do Windows e selecione **Segurança**.
3. À direita, clique em **Filtrar os Registos Atuais**.
4. Em Origem do evento, selecione **Auditoria do AD FS**.

![Registos de auditoria do AD FS](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [!WARNING]
> Se tiver uma política de grupo que desativa a auditoria do AD FS, então o Agente do Azure AD Connect Health não conseguirá recolher informações. Certifique-se de que não tem uma política de grupo que possa estar a desativar a auditoria.
> 
> 

[//]: # (Start of Agent Proxy Configuration Section)

## Instalar o agente Azure AD Connect Health para sincronização
O agente do Azure AD Connect Health para sincronização é instalado automaticamente na compilação mais recente do Azure AD Connect.  Para utilizar o Azure AD Connect para sincronização, terá de transferir a versão mais recente do Azure AD Connect e instalá-la.  Pode transferir a versão mais recente [aqui](http://www.microsoft.com/download/details.aspx?id=47594).

Para verificar se o agente foi instalado, abra Serviços e procure o seguinte. Se concluiu a configuração, estes serviços devem estar a ser executados. Caso contrário, não arrancarão até a configuração estar concluída.

* Serviço de Informações do Azure AD Connect Health Sincronização
* Serviço de Monitorização do Azure AD Connect Health Sincronização

![Verificar o Azure AD Connect Health para sincronização](./media/active-directory-aadconnect-health-sync/services.png)

> [!NOTE]
> Lembre-se de que a utilização do Azure AD Connect Health requer o Azure AD Premium.  Se não tiver o Azure AD Premium, não conseguirá concluir a configuração no Portal do Azure.  Para mais informações, consulte os requisitos [aqui](active-directory-aadconnect-health-agent-install.md#requirements).
> 
> 

## Registo manual do Azure AD Connect Health para sincronização
Se o registo do agente Azure AD Connect Health para sincronização falhar após a instalação com êxito o Azure AD Connect, pode utilizar o seguinte comando do PowerShell para registar manualmente o agente.

> [!IMPORTANT]
> Só é necessário utilizar este comando PowerShell se falhar o registo do agente depois de instalar o Azure AD Connect.
> 
> 

O comando PowerShell abaixo SÓ é necessário se falhar o registo do agente de estado de funcionamento mesmo após uma instalação e configuração com êxito do Azure AD Connect. Nestes casos, os serviços do Azure AD Connect Health NÃO arrancam até que o agente seja registado com êxito.

Pode registar manualmente o agente do Azure AD Connect Health para sincronização com o seguinte comando do PowerShell:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

O comando recebe os seguintes parâmetros:

* AttributeFiltering: $true (predefinição) - se o Azure AD Connect não estiver a sincronizar o conjunto predefinido de atributos e tiver sido personalizado para utilizar um conjunto filtrado de atributos. $false, caso contrário.
* StagingMode: $false (predefinição) - se o servidor do Azure AD Connect NÃO estiver no modo de teste, $true se o servidor estiver configurado para estar no modo de teste.

Quando lhe for pedida a autenticação, deve utilizar a mesma conta de administrador global (por exemplo, admin@domain.onmicrosoft.com), que foi utilizada para configurar o Azure AD Connect.

## Instalar o Agente Azure AD Connect Health para o AD DS
Para iniciar a instalação do agente, faça duplo clique no ficheiro .exe que transferiu. No primeiro ecrã, clique em Instalar.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install1.png)

Uma vez concluída a instalação, clique em Configurar Agora.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install2.png)

Este procedimento inicia uma linha de comandos seguida de comandos PowerShell, que irão executar o Registo AzureADConnectHealthADDSAgent. Ser-lhe-á pedido que inicie sessão no Azure. Faça-o, inicie sessão.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install3.png)

Depois de iniciar sessão, o PowerShell prosseguirá. Uma vez concluído, pode fechar o PowerShell e a configuração estará concluída.

Nessa altura, os serviços deverão ser iniciados automaticamente e o agente estará agora a monitorizar e a recolher dados. A captura de ecrã abaixo mostra um exemplo do resultado. Tenha em atenção de que verá avisos na janela do PowerShell se não tiver cumprido todos os pré-requisitos que foram descritos nas secções anteriores. Antes de instalar o agente, certifique-se [aqui](active-directory-aadconnect-health-agent-install.md#requirements) de que concluiu todos os requisitos. 

![Verificar o Azure AD Connect Health para o AD DS](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install4.png)

Para verificar se o agente foi instalado, abra Serviços e procure o seguinte:

* Serviço de Informações do Azure AD Connect Health AD DS
* Serviço de Monitorização do Azure AD Connect Health AD DS

Estes dois serviços não arrancarão até a configuração estar concluída.

![Verificar o Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnect-health-adds-agent-install5.png)

## Instalar o Agente Azure AD Connect Health para o AD DS no Server Core
Depois de instalar o ficheiro .exe, pode concluir o processo de registo com o seguinte comando do PowerShell:

`Register-AzureADConnectHealthADDSAgent -Credentials $cred

## Configurar os Agentes do Azure AD Connect Health para utilizarem o Proxy de HTTP
Pode configurar os Agentes do Azure AD Connect Health para trabalharem com um Proxy de HTTP.

> [!NOTE]
> * A utilização de “Netsh WinHttp define ProxyServerAddress” não irá funcionar uma vez que o agente utiliza System.Net para fazer pedidos web em vez dos Serviços HTTP do Microsoft Windows.
> * O endereço de Http Proxy configurado será utilizado para pass-through de mensagens encriptadas do Https.
> * Proxies autenticados (utilizando HTTPBasic) não são suportados.
> 
> 

### Alterar a configuração de Proxy do Agente de Estado de Funcionamento
Tem as seguintes opções para configurar o Agente do Azure AD Connect Health para utilizar um Proxy de HTTP.

> [!NOTE]
> Tem de reiniciar todos os serviços do Agente do Azure AD Connect Health para que as definições de proxy sejam atualizadas. Execute o seguinte comando:<br>
> Restart-Service AdHealth*
> 
> 

#### Importar Definições de proxy existente
##### Importar do Internet Explorer
Pode importar as definições de proxy de HTTP do seu Internet Explorer e utilizá-las para os Agentes Azure AD Connect Health executando o seguinte comando do PowerShell em cada servidor que executa o Agente de Estado de Funcionamento.

    Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importar do WinHTTP
Pode importar as definições de proxy do WinHTTP executando o seguinte comando do PowerShell em cada servidor que executa o Agente de Estado de Funcionamento.

    Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Especificar manualmente endereços Proxy
Pode especificar manualmente um servidor proxy executando o seguinte comando do PowerShell em cada servidor que executa o Agente de Estado de Funcionamento.

    Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Exemplo: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

* “address” pode ser um nome de servidor DNS resolvível ou um endereço IPv4
* pode ser omitida a “porta”. Se for omitida, então 443 é escolhida como porta predefinida.

#### Limpar a configuração de proxy existente
Pode limpar a configuração de proxy existente executando o seguinte comando.

    Set-AzureAdConnectHealthProxySettings -NoProxy


### Ler as definições de proxy atuais
Pode utilizar o seguinte comando para ler as definições de proxy atualmente configuradas.

    Get-AzureAdConnectHealthProxySettings


## Testar a conectividade ao Serviço Azure AD Connect Health
É possível que possam surgir problemas que levem o agente do Azure AD Connect Health a perder a conectividade ao serviço do Azure AD Connect Health.  Incluem problemas de rede, problemas de permissão ou vários outros motivos.

Se o agente não conseguir enviar dados para o serviço do Azure AD Connect Health durante mais de 2 horas, verá um Alerta que indica “Dados do Serviço de Estado de Funcionamento não estão atualizados”.  Caso ocorra esta situação, pode então testar se os agentes do Azure AD Connect Health conseguem carregar dados para o serviço do Azure AD Connect Health executando o seguinte comando do PowerShell a partir da máquina cujo agente está a ter este problema.

    Test-AzureADConnectHealthConnectivity -Role Adfs

O parâmetro de função toma atualmente os seguintes valores:

* Adfs
* Sync
* AD DS

Pode utilizar o sinalizador - ShowResults no comando para ver registos detalhados.  Utilize o seguinte exemplo:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

> [!NOTE]
> Para utilizar a ferramenta de conectividade, primeiro tem de concluir o registo do agente.  Se não conseguir concluir o registo do agente, certifique-se de que cumpriu todos os [requisitos](active-directory-aadconnect-health-agent-install.md#requirements) para o Azure AD Connect Health.  Este teste de conectividade é efetuado por predefinição durante o registo do agente.
> 
> 

## Ligações relacionadas
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Operações do Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD Connect Health com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD Connect Health para sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD Connect Health com o AD DS](active-directory-aadconnect-health-adds.md)
* [FAQ do Azure AD Connect Health](active-directory-aadconnect-health-faq.md)
* [Histórico das Versões do Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)

<!--HONumber=Sep16_HO4-->


