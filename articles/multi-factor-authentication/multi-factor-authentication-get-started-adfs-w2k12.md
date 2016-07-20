<properties 
    pageTitle="Proteger recursos na nuvem e no local utilizando o Servidor MFA do Azure com o Windows Server 2012 R2 AD FS" 
    description="Esta é a página do Multi-Factor Authentication do Azure que descreve como começar a utilizar o MFA do Azure e o AD FS no Windows Server 2012 R2." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>


# Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o Windows Server 2012 R2 AD FS

Se a sua organização estiver federada com o Azure AD e tiver recursos que estão no local ou na nuvem que pretende proteger, pode fazê-lo utilizando o Servidor Multi-Factor Authentication do Azure e configurando-o para funcionar com o AD FS para que a autenticação multifator seja acionada para pontos finais de valor elevado.

Esta documentação abrange a utilização do Servidor Multi-Factor Authentication do Azure com o AD FS no Windows Server 2012 R2.  Para obter informações sobre como utilizar o Multi-Factor Authentication do Azure com o AD FS 2.0, veja [Proteger recursos na nuvem e no local utilizando o Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Proteger o Windows Server 2012 R2 AD FS com o Servidor Multi-Factor Authentication do Azure

Quando instalar o Servidor Multi-Factor Authentication do Azure, tem as duas opções seguintes:

- Instalar o Servidor Multi-Factor Authentication do Azure localmente no mesmo servidor do AD FS 
- Instalar o Adaptador do Servidor Multi-Factor Authentication do Azure localmente no Servidor do AD FS e instalar o Servidor MFA num computador diferente

Antes de começar, tenha em atenção as seguintes informações:

- Não é um requisito que o Servidor Multi-Factor Authentication do Azure seja instalado no servidor de federação do AD FS. No entanto, o Adaptador do Multi-Factor Authentication para o AD FS tem de ser instalado num Windows Server 2012 R2 com o AD FS. Pode instalar o servidor num computador diferente desde que seja uma versão suportada e instalar o adaptador AD FS separadamente no seu servidor de federação do AD FS. Veja o procedimento abaixo para obter instruções sobre como instalar o adaptador separadamente.
- Quando o Adaptador AD FS do Servidor Multi-Factor Authentication foi concebido, foi antecipado que o AD FS poderia transmitir o nome da entidade confiadora ao adaptador, o qual podia ser utilizado como um nome de aplicação.  No entanto, acabou por não ser o caso.  Se utilizar os métodos de autenticação por mensagem de texto ou da aplicação móvel, as cadeias definidas nas Definições da Empresa contêm um marcador de posição "<$application_name$>".  Este marcador de posição não é substituído quando utilizar o Adaptador AD FS.  Por este motivo, é recomendado remover o marcador de posição das cadeias adequadas quando proteger o AD FS.

- A conta com sessão iniciada tem de ter privilégios para criar grupos de segurança no Active Directory.

- O assistente de instalação do Adaptador AD FS do Multi-Factor Authentication cria um grupo de segurança denominado PhoneFactor Admins no Active Directory e, em seguida, adiciona a conta de serviço do AD FS do seu serviço de federação a este grupo. É recomendado que verifique no seu controlador de domínio se o grupo PhoneFactor Admins está de facto criado e a conta de serviço do AD FS é um membro deste grupo. Se necessário, adicione a conta de serviço do AD FS manualmente ao grupo PhoneFactor Admins no seu controlador de domínio.
- Para obter informações sobre como instalar o SDK do Serviço Web com o portal de utilizador, veja [Implementar o Portal de Utilizador do Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-portal.md).
  

### Para instalar o Servidor Multi-Factor Authentication do Azure localmente no mesmo servidor do AD FS

1. Transfira e instale o Servidor Multi-Factor Authentication do Azure no seu servidor de federação do AD FS. Para obter informações sobre como instalar o Servidor Multi-Factor Authentication do Azure, veja [Introdução ao Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server.md)
2. Na interface de utilizador do Servidor Multi-Factor Authentication do Azure, selecione o ícone do AD FS e as opções para Permitir a inscrição de utilizadores e Permitir que os utilizadores selecionem o método.
3. Selecione quaisquer opções adicionais.
4. Clique em Instalar Adaptador AD FS.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se o computador estiver associado a um domínio e a configuração do Active Directory para proteger a comunicação entre o Adaptador AD FS e o serviço Multi-Factor Authentication estiver incompleta, o passo do Active Directory será apresentado.  Clique no botão Seguinte para concluir esta configuração automaticamente ou selecione a caixa de verificação Ignorar configuração automática do Active Directory e configurar as definições manualmente e clique em Seguinte.
6. Se o computador não estiver associado a um domínio e a configuração do Grupo Local para proteger a comunicação entre o Adaptador AD FS e o serviço Multi-Factor Authentication estiver incompleta, o passo do Grupo Local será apresentado.  Clique no botão Seguinte para concluir esta configuração automaticamente ou selecione a caixa de verificação Ignorar configuração automática do Grupo Local e configurar as definições manualmente e clique em Seguinte.
7. Isto irá apresentar o assistente de instalação, clique em Seguinte para permitir que o Servidor Multi-Factor Authentication do Azure crie o grupo PhoneFactor Admins e adicione a conta de serviço do AD FS ao grupo PhoneFactor Admins.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. No passo Iniciar o Instalador, clique em Seguinte.
9. No instalador do Adaptador AD FS do Multi-Factor Authentication, clique em Seguinte.
10. Clique em Fechar quando a instalação estiver concluída.
11. Agora que o adaptador foi instalado, tem de ser registado no AD FS. Abra o Windows PowerShell e execute o seguinte: C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 <center>![Cloud](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Agora, é necessário editar a Política de Autenticação Global do AD FS para utilizar o adaptador recentemente registado. Na Consola de Gestão do AD FS, navegue para o nó Políticas de Autenticação e, na secção Multi-Factor Authentication, clique na ligação Editar junto à subsecção Definições Globais. Na janela Editar Política de Autenticação Global, selecione Multi-Factor Authentication como método de autenticação adicional e, em seguida, clique em OK. O adaptador está registado como WindowsAzureMultiFactorAuthentication.  Tem de reiniciar o serviço AD FS para o registo ser aplicado.

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Neste momento, o Servidor Multi-Factor Authentication está configurado para ser um fornecedor de autenticação adicional para utilização com o AD FS.

## Para instalar o Adaptador AD FS de forma autónoma utilizando o SDK do Serviço Web
1. Instale o SDK do Serviço Web no servidor com o Servidor Multi-Factor Authentication.
2. Copie os ficheiros MultiFactorAuthenticationAdfsAdapterSetup64.msi, Register-MultiFactorAuthenticationAdfsAdapter.ps1, Unregister-MultiFactorAuthenticationAdfsAdapter.ps1, and MultiFactorAuthenticationAdfsAdapter.config do diretório \Program Files\Multi-Factor Authentication Server para o servidor no qual pretende instalar o Adaptador AD FS.
3. Execute o ficheiro MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No instalador do Adaptador AD FS do Multi-Factor Authentication, clique em Seguinte para efetuar a instalação.
5. Clique no botão Fechar quando a instalação estiver concluída.
6. Edite o ficheiro MultiFactorAuthenticationAdfsAdapter.config efetuando o seguinte procedimento:

Passo MultiFactorAuthenticationAdfsAdapter.config| Subpasso
:------------- | :------------- |
Defina o nó UseWebServiceSdk como verdadeiro.||
Defina WebServiceSdkUrl para o URL do SDK do Serviço Web do Multi-Factor Authentication.||
Opção 1 - Configure o SDK do Serviço Web com um nome de utilizador e uma palavra-passe.|<ol><li>Defina WebServiceSdkUsername para uma conta que seja membro do grupo de segurança PhoneFactor Admins.  Utilize <domain>\<nomedeutilizador> format.<li>Defina WebServiceSdkPassword para a palavra-passe da conta adequada.</li></ol>
Opção 2 - Configure o SDK do Serviço Web com um certificado de cliente.|<ol><li>Obtenha um certificado de cliente de uma autoridade de certificação para o servidor com o SDK do Serviço Web.  Para obter informações sobre como obter um certificado, veja [Obter Certificado de Cliente](https://technet.microsoft.com/library/cc770328(v=ws.10).aspx).</li><li>Importe o certificado de cliente para o arquivo de certificados pessoais do computador local no servidor com o SDK do Serviço Web.  Nota: certifique-se de que o certificado público da autoridade de certificação está nos Certificados de Raiz Fidedigna.</li><li>Exporte as chaves públicas e privadas do certificado de cliente para um ficheiro .pfx.</li><li>Exporte a chave pública no formato de base 64 para um ficheiro .cer.</li><li>No Gestor de Servidores, certifique-se de que a funcionalidade Servidor Web (IIS)\Servidor Web\Segurança\Autenticação de Mapeamento de Certificados de Cliente IIS está instalada.</li><li>Se não estiver instalada, escolha Adicionar Funções e Funcionalidades para adicionar esta funcionalidade.</li><li>No Gestor do IIS, faça duplo clique no Editor de Configuração do site que contém o diretório virtual do SDK do Serviço Web.  Nota: é muito importante fazê-lo ao nível do site e não ao nível do diretório virtual.</li><li>Navegue para a secção system.webServer/security/authentication/iisClientCertificateMappingAuthentication.</li><li>Defina enabled como verdadeiro.</li><li>Defina oneToOneCertificateMappingsEnabled como verdadeiro.</li><li>Clique no botão ... junto a oneToOneMappings.</li><li>Clique na ligação Adicionar.</li><li>Abra o ficheiro .cer de base 64 exportado anteriormente.  Remova -----BEGIN CERTIFICATE-----, -----END CERTIFICATE----- e quaisquer quebras de linha.  Copie a cadeia resultante.</li><li>Defina o certificado para a cadeia que copiou no passo anterior.</li><li>Defina enabled como verdadeiro.</li><li>Defina userName para uma conta que seja membro do grupo de segurança PhoneFactor Admins.  Utilize <domain>\<nomedeutilizador> format.</li><li>Defina a palavra-passe para a conta adequada.</li><li>Feche o Editor da Coleção.</li><li>Clique na ligação Aplicar.</li><li>Navegue para o diretório virtual do SDK do Serviço Web.</li><li>Faça duplo clique em Autenticação.</li><li>Verifique se a Representação do ASP.NET e a Autenticação Básica estão Ativadas e todos os outros itens estão Desativados.</li><li>Navegue novamente para o diretório virtual do SDK do Serviço Web.</li><li>Faça duplo clique em Definições de SSL.</li><li>Defina os Certificados de Cliente a Aceitar e clique em Aplicar.</li><li>Copie o ficheiro .pfx exportado anteriormente para o servidor com o Adaptador AD FS.</li><li>Importe o ficheiro .pfx para o arquivo de certificados pessoais do computador local.</li><li>Escolha Gerir Chaves Privadas a partir do menu de contexto e conceda acesso de leitura à conta na qual o serviço Serviços de Federação do Active Directory tem sessão iniciada.</li><li>Abra o certificado de cliente e copie o thumbprint a partir do separador Detalhes.</li><li>No ficheiro MultiFactorAuthenticationAdfsAdapter.config, defina WebServiceSdkCertificateThumbprint para a cadeia que copiou no passo anterior.</li></ol>
Edite o script Register-MultiFactorAuthenticationAdfsAdapter.ps1 adicionando -ConfigurationFilePath <path> ao final do comando Register-AdfsAuthenticationProvider command, em que <path> é o caminho completo para o ficheiro MultiFactorAuthenticationAdfsAdapter.config.|


Agora, execute o script \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 no PowerShell para registar o adaptador.  O adaptador está registado como WindowsAzureMultiFactorAuthentication.  Tem de reiniciar o serviço AD FS para o registo ser aplicado. 




























 

 


 

 


 





 


 

























































































 


 

 






 


<!--HONumber=Jun16_HO2-->


