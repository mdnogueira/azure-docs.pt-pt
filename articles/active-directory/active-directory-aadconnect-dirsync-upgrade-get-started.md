<properties
   pageTitle="Azure AD Connect: atualizar do DirSync | Microsoft Azure"
   description="Saiba como atualizar do DirSync para o Azure AD Connect. Este artigo descreve os passos para atualizar do DirSync para o Azure AD Connect."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.date="05/31/2016"
   ms.author="andkjell;shoatman;billmath"/>

# Azure AD Connect: atualizar do DirSync
O Azure AD Connect é o sucessor do DirSync. Encontrará neste tópico as forma de atualizar a partir do DirSync. Estes passos não funcionam para atualizar a partir de outra versão do Azure AD Connect ou a partir do Azure AD Sync.

Antes de começar a instalar o Azure AD Connect, certifique-se de que [transferiu o Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) e concluiu os pré-requisitos indicados em [Azure AD Connect: Hardware e pré-requisitos](active-directory-aadconnect-prerequisites.md).

Se não estiver a atualizar do DirSync, consulte a [documentação relacionada](#related-documentation) para obter outros cenários.

## Atualização do DirSync
Dependendo da sua implementação atual do DirSync, existem várias opções para a atualização. Se o tempo estimado para a atualização for inferior a 3 horas, recomenda-se efetuar uma atualização no local. Se o tempo estimado para a atualização for superior a 3 horas, recomenda-se efetuar uma implementação paralela noutro servidor. Estima-se que, se tiver mais de 50 000 objetos, a atualização demorará mais de 3 horas.

Cenário |  
---- | ----
[Atualização no local](#in-place-upgrade)  | Opção preferencial, caso se espere que a atualização demore menos de 3 horas.
[Implementação paralela](#parallel-deployment) | Opção preferencial, caso se espere que a atualização demore mais de 3 horas.

>[AZURE.NOTE] Quando planear atualizar do DirSync para o Azure AD Connect, não desinstale o DirSync antes da atualização. O Azure AD Connect lê e migra a configuração do DirSync e desinstala-o depois inspecionar o servidor.

**Atualização no local**  
O assistente apresenta o tempo esperado para concluir a atualização. Esta estimativa baseia-se no pressuposto de que demora 3 horas a concluir uma atualização para uma base de dados com 50 000 objetos (utilizadores, contactos e grupos). O Azure AD Connect analisa as definições atuais do DirSync e recomenda uma atualização no local se o número de objetos na base de dados for inferior a 50 000. Se decidir continuar, as definições atuais serão aplicadas automaticamente durante a atualização e o servidor retomará automaticamente a sincronização ativa.

Se pretender efetuar uma migração da configuração e efetuar uma implementação paralela, pode ignorar a recomendação de atualização no local. Poderá, por exemplo, aproveitar a oportunidade para atualizar o hardware e o sistema operativo. Consulte a secção [implementação paralela](#parallel-deployment) para obter mais informações.

**Implementação paralela**  
Recomendação a utilização de uma implementação paralela se tiver mais de 50 000 objetos. Evitará assim que os utilizadores sofram atrasos operacionais. A instalação do Azure AD Connect tenta estimar o período de indisponibilidade devido à atualização, mas se tiver atualizado anteriormente o DirSync, a sua experiência pessoal será, provavelmente, o melhor guia.

### Configurações suportadas do DirSync a atualizar
As seguintes alterações de configuração são suportadas com o DirSync e serão atualizadas:

- Filtragem de domínios e de UOs
- ID Alternativo (UPN)
- Sincronização de palavra-passe e definições híbridas do Exchange
- A floresta/domínio e as definições do Azure AD
- Filtragem baseada nos atributos de utilizador

Não é possível atualizar a alteração seguinte. Se efetuou esta alteração, a atualização será bloqueada:

- Alterações não suportadas do DirSync, por exemplo, atributos removidos e a utilização de uma DLL personalizada

![Atualização bloqueada](./media/active-directory-aadconnect-dirsync-upgrade-get-started/analysisblocked.png)

Nestes casos, a recomendação é instalar um novo servidor do Azure AD Connect em [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode) e verificar a antiga configuração do DirSync e a nova configuração do Azure AD Connect. Volte a aplicar as alterações utilizando uma configuração personalizada, conforme descrito em [Configuração personalizada da Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md).

Não é possível obter as palavras-passe utilizadas pelo DirSync para as contas de serviço, não serão pois migradas. Estas palavras-passe serão repostas durante a atualização.

### Passos de alto nível para atualizar do DirSync para o Azure AD Connect.

1. Bem-vindo ao Azure AD Connect
2. Análise da configuração atual do DirSync
3. Recolher a palavra-passe de administrador global do Azure AD
4. Recolher as credenciais para uma conta de administrador da empresa (utilizada apenas durante a instalação do Azure AD Connect)
5. Instalação do Azure AD Connect
    * Desinstalar o DirSync
    * Instalar o Azure AD Connect
    * Opcionalmente, iniciar a sincronização

São necessários passos adicionais se:

* Estiver a utilizar atualmente o SQL Server total, local ou remoto
* Tiver mais de 50 000 objetos no âmbito da sincronização

## Atualização no local

1. Inicie o instalador do Azure AD Connect (MSI).
2. Leia e aceite os termos de licenciamento e o aviso de privacidade.
![Bem-vindo ao Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Welcome.png)
3. Clique em seguinte para iniciar uma análise da instalação existente do DirSync.
![Analisar a instalação existente da Sincronização de Diretórios](./media/active-directory-aadconnect-dirsync-upgrade-get-started/Analyze.png)
4. Quando a análise estiver concluída, verá as recomendações sobre como proceder.  
    - Se utilizar o SQL Server Express e tiver menos de 50 000 objetos, é apresentado o ecrã seguinte: ![Análise concluída, pronto para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReady.png)
    - Se utilizar um SQL Server total para o DirSync, verá, em vez disso, esta página: ![Análise concluída, pronto para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisReadyFullSQL.png)  
São apresentadas as informações sobre o servidor de base de dados existente do SQL Server que está a ser utilizado pelo DirSync. Efetue os ajustes adequados, se necessário. Clique em **Seguinte** para continuar a instalação.
    - Se tiver mais de 50 000 objetos, verá, em vez disso, este ecrã: ![Análise concluída, pronto para atualizar a partir do DirSync](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)  
Para continuar com uma atualização no local, clique na caixa de verificação junto a esta mensagem: **Continuar a atualização do DirSync neste computador.**
Para fazer, em vez disso, uma [implementação paralela](#parallel-deployment), terá de exportar e mover para o novo servidor as definições de configuração do DirSync.
5. Forneça a palavra-passe da conta que utiliza atualmente para ligar ao Azure AD. Tem de ser a conta utilizada atualmente pelo DirSync.
![Introduzir as credenciais do Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToAzureAD.png)  
Se receber um erro e tiver problemas com a conectividade, consulte [Resolver problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).
6. Forneça uma conta de administrador da empresa do Active Directory.
![Introduzir as credenciais do ADDS](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ConnectToADDS.png)
7. Está agora pronto para a configuração. Quando clicar em **Atualizar**, o DirSync será desinstalado e o Azure AD Connect será configurado e começará a sincronização.
![Preparado para configurar](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ReadyToConfigure.png)
8. Após a conclusão da instalação, termine e inicie a sessão novamente no Windows antes de utilizar o Synchronization Service Manager, o Editor de Regras de Sincronização ou tentar efetuar outras alterações de configuração.

## Implementação paralela

### Exportar a configuração do DirSync
**Implementação paralela com mais de 50 000 objetos**

Se tiver mais de 50 000 objetos, a instalação do Azure AD Connect recomenda uma implementação paralela.

É apresentado um ecrã semelhante ao seguinte:

![Análise completa](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AnalysisRecommendParallel.png)

Se pretender continuar com a implementação paralela, terá de efetuar os seguintes passos:

- Clique no botão **Exportar definições**. Ao instalar o Azure AD Connect num servidor separado, estas definições serão importadas para migrar as definições do DirSync atual para a nova instalação do Azure AD Connect.

Assim que as definições forem exportadas com êxito, pode sair do assistente do Azure AD Connect no servidor do DirSync. Continue com o passo seguinte para [instalar o Azure AD Connect num servidor separado](#installation-of-azure-ad-connect-on-separate-server)

**Implementação paralela com menos de 50 000 objetos**

Se tiver menos de 50 000 objetos mas quiser, mesmo assim, fazer uma implementação paralela, efetue o seguinte:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando vir o ecrã **Bem-vindo ao Azure AD Connect**, saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra uma linha de comandos.
4. Na localização da instalação do Azure AD Connect (predefinição: C:\Programas\Microsoft Azure Active Directory Connect), execute o seguinte comando:  `AzureADConnect.exe /ForceExport`.
5. Clique no botão **Exportar definições**.  Ao instalar o Azure AD Connect num servidor separado, estas definições serão importadas para migrar as definições do DirSync atual para a nova instalação do Azure AD Connect.

![Análise completa](./media/active-directory-aadconnect-dirsync-upgrade-get-started/forceexport.png)

Assim que as definições forem exportadas com êxito, pode sair do assistente do Azure AD Connect no servidor do DirSync. Continue com o passo seguinte para [instalar o Azure AD Connect num servidor separado](#installation-of-azure-ad-connect-on-separate-server)

### Instalar o Azure AD Connect num servidor separado

Ao instalar o Azure AD Connect num servidor novo, este partirá do princípio de que pretende efetuar uma instalação de raiz do Azure AD Connect. Uma vez que pretende utilizar a configuração do DirSync, há alguns passos adicionais:

1. Execute o instalador do Azure AD Connect (MSI).
2. Quando vir o ecrã **Bem-vindo ao Azure AD Connect**, saia do assistente de instalação clicando no “X” no canto superior direito da janela.
3. Abra uma linha de comandos.
4. Na localização da instalação do Azure AD Connect (predefinição: C:\Programas\Microsoft Azure Active Directory Connect), execute o seguinte comando:  `AzureADConnect.exe /migrate`.
    O assistente de instalação do Azure AD Connect arranca e apresenta o seguinte ecrã: ![Introduzir as credenciais do Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/ImportSettings.png)
5. Selecione o ficheiro de definições que exportou na instalação do DirSync.
6. Configure as opções avançadas, incluindo:
    - Uma localização de instalação personalizada para o Azure AD Connect.
    - Uma instância existente do SQL Server (predefinição: o Azure AD Connect instala o SQL Server 2012 Express). Não utilize a mesma instância de base de dados do servidor do DirSync.
    - Uma conta de serviço utilizada para ligar ao SQL Server (se a base de dados do SQL Server for remota, esta conta tem de ser uma conta de serviço de domínio).
Estas opções podem ser vistas neste ecrã: ![Introduzir as credenciais do Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/advancedsettings.png)
7. Clique em **Seguinte**.
8. Na página **Preparado para configurar**, deixe marcada a opção **Inicie o processo de sincronização assim que a configuração for concluída**. O servidor estará em [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode), ou seja, as alterações não serão exportadas para o Azure AD neste momento.
9. Clique em **Instalar**.
10. Após a conclusão da instalação, termine e inicie a sessão novamente no Windows antes de utilizar o Synchronization Service Manager, o Editor de Regras de Sincronização ou tentar efetuar outras alterações de configuração.

>[AZURE.NOTE] Será iniciada a sincronização entre o Windows Server Active Directory e o Azure Active Directory, mas as alterações não serão exportadas para o Azure AD.  Só uma ferramenta de sincronização de cada vez pode exportar ativamente alterações. Chama-se a isto [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode).

### Verificar se o Azure AD Connect está pronto para iniciar a sincronização

Para verificar se o Azure AD Connect está pronto para substituir o DirSync, terá de abrir o **Synchronization Service Manager** no grupo **Azure AD Connect** do menu Iniciar.

Na aplicação, terá de ver o separador **Operations**. Neste separador, deverá confirmar se as seguintes operações forma concluídas:

- Importar no Conector AD
- Importar no Conector Azure AD
- Sincronização completa no Conector AD
- Sincronização completa no Conector Azure AD

![Importação e sincronização concluídas](./media/active-directory-aadconnect-dirsync-upgrade-get-started/importsynccompleted.png)

Consulte o resultado destas operações e certifique-se de que não contém erros.

Se pretender ver e inspecionar as alterações que estão prestes a ser exportadas para o Azure AD, deverá ler como verificar a configuração em [modo de teste](active-directory-aadconnectsync-operations.md#staging-mode). Efetue as alterações de configuração necessárias até já não ver nada inesperado.

Quando estas 4 operações estiverem concluídas, não houver erros e estiver satisfeito com as alterações que estão prestes a serem exportadas, estará pronto para desinstalar o DirSync e ativar a sincronização do Azure AD Connect. Execute os dois passos seguintes para concluir a migração.

### Desinstalar o DirSync (servidor antigo)

- Em **Programas e funcionalidades**, localize **Ferramenta de sincronização do Windows Azure Active Directory**
- Desinstalar a **Ferramenta de sincronização do Windows Azure Active Directory**
- Tenha em atenção que a desinstalação pode levar até 15 minutos para ficar concluída.

Com o DirSync desinstalado,deixa de existir um servidor ativo a exportar para o Azure AD. O passo seguinte tem de ser concluído para que quaisquer alterações no Active Directory no local continuem a ser sincronizadas para o Azure AD.

### Ativar o Azure AD Connect (novo servidor)
Após a instalação, voltar a abrir Azure AD Connect irá permitir-lhe efetuar alterações de configuração adicionais. Inicie o **Azure AD Connect** a partir do menu Iniciar ou do atalho no ambiente de trabalho. Certifique-se de que não está a tentar executar novamente a instalação do MSI.

Deverá ver o seguinte:

![Tarefas adicionais](./media/active-directory-aadconnect-dirsync-upgrade-get-started/AdditionalTasks.png)

- Selecione **Configurar o modo de teste**.
- Desative o teste desmarcando a caixa de verificação **Modo de teste ativado**.

![Introduzir as credenciais do Azure AD](./media/active-directory-aadconnect-dirsync-upgrade-get-started/configurestaging.png)

- Clique no botão **Seguinte**
- Na página de confirmação, clique no botão **instalar**.

O Azure AD Connect é agora o seu servidor ativo.

## Passos seguintes
Agora que já tem o Azure AD Connect instalado, pode [verificar a instalação e atribuir licenças](active-directory-aadconnect-whats-next.md).

Saiba mais acerca destas novas funcionalidades que foram ativadas com a instalação: [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md), [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) e [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Saiba mais acerca destes tópicos comuns: [agendador e como acionar a sincronização](active-directory-aadconnectsync-feature-scheduler.md).

Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).

## Documentação relacionada

Tópico |  
--------- | ---------
Visão geral do Azure AD Connect | [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md)
Atualizar de uma versão do Connect anterior | [Atualizar de uma versão anterior do Connect](active-directory-aadconnect-upgrade-previous-version.md)
Instalar utilizando as definições rápidas | [Instalação rápida do Azure AD Connect](active-directory-aadconnect-get-started-express.md)
Instalar utilizando as definições personalizadas | [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Contas utilizadas para a instalação | [Mais acerca de contas e permissões do Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)



<!--HONumber=Jun16_HO2-->


