---
title: Servidor MFA com Windows Server 2012 R2 AD FS | Microsoft Docs
description: Este artigo descreve como começar a utilizar o Multi-Factor Authentication do Azure e o AD FS no Windows Server 2012 R2.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2016
ms.author: kgremban

---
# Proteger recursos na nuvem e no local através do Servidor Multi-Factor Authentication do Azure com o AD FS no Windows Server 2012 R2
Se a sua organização utiliza os Serviços de Federação do Active Directory (AD FS) e pretende proteger recursos na nuvem ou no local, pode implementar e configurar o Servidor Multi-Factor Authentication do Azure para trabalhar com o AD FS. Esta configuração aciona a autenticação multifator para pontos finais de alto valor.

Neste artigo, discutimos a utilização do Servidor Multi-Factor Authentication do Azure com o AD FS no Windows Server 2012 R2. Para obter mais informações, leia sobre como [proteger recursos na nuvem e no local através do Servidor Multi-Factor Authentication do Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## Proteger o Windows Server 2012 R2 AD FS com o Servidor Multi-Factor Authentication do Azure
Quando instala o Servidor Multi-Factor Authentication do Azure, tem as seguintes opções:

* Instalar o Servidor Multi-Factor Authentication do Azure localmente no mesmo servidor do AD FS
* Instalar o adaptador do Multi-Factor Authentication do Azure localmente no Servidor do AD FS e instalar o Servidor Multi-Factor Authentication num computador diferente

Antes de começar, tenha em atenção as seguintes informações:

* Não é necessário instalar o Servidor Multi-Factor Authentication do Azure no seu servidor AD FS. No entanto, deve instalar o adaptador do Multi-Factor Authentication para AD FS no Windows Server 2012 R2 que esteja a executar o AD FS. Pode instalar o servidor num computador diferente se for uma versão suportada e instalar o adaptador AD FS separadamente no seu servidor de federação do AD FS. Consulte os seguintes procedimentos para saber como instalar o adaptador em separado.
* Quando o adaptador AD FS do Servidor Multi-Factor Authentication foi concebido, foi antecipado que o AD FS poderia transmitir o nome da entidade confiadora ao adaptador, o qual podia ser utilizado como um nome de aplicação. No entanto, acabou por não ser o caso. Se a sua organização utilizar os métodos de autenticação por mensagem de texto ou da aplicação móvel, as cadeias definidas nas Definições da Empresa contêm um marcador de posição <$*application_name*$>. Este marcador de posição não é substituído automaticamente quando utiliza o adaptador AD FS. Recomendamos que remova o marcador de posição das cadeias adequadas ao proteger o AD FS.
* A conta que utiliza para iniciar sessão tem de ter direitos de utilizador para criar grupos de segurança no seu serviço do Active Directory.
* O assistente de instalação do adaptador AD FS do Multi-Factor Authentication cria um grupo de segurança chamado PhoneFactor Admins na sua instância do Active Directory e, em seguida, adiciona a conta de serviço do AD FS do seu serviço de federação a este grupo. Recomendamos que verifique no seu controlador de domínio se o grupo PhoneFactor Admins é efetivamente criado e se a conta de serviço do AD FS faz parte deste grupo. Se necessário, adicione manualmente a conta de serviço do AD FS ao grupo PhoneFactor Admins no seu controlador de domínio.
* Para obter informações sobre como instalar o SDK do Serviço Web com o portal de utilizador, leia sobre como [implementar o portal de utilizador do Servidor Multi-Factor Authentication do Azure.](multi-factor-authentication-get-started-portal.md)

### Instalar o Servidor Multi-Factor Authentication do Azure localmente no servidor AD FS
1. Transfira e instale o Servidor Multi-Factor Authentication do Azure no seu servidor de federação AD FS. Para obter informações de instalação, consulte a [introdução ao Servidor Multi-Factor Authentication do Azure](multi-factor-authentication-get-started-server.md).
2. Na consola de gestão do Servidor Multi-Factor Authentication do Azure, clique no ícone **AD FS** e, em seguida, selecione as opções **Permitir a inscrição de utilizadores** e **Permitir que os utilizadores selecionem o método**.
3. Selecione outras opções adicionais que pretende especificar para a sua organização.
4. Clique em **Instalar Adaptador AD FS**.
   <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se o computador estiver associado a um domínio e a configuração do Active Directory para proteger a comunicação entre o adaptador AD FS e o serviço Multi-Factor Authentication estiver incompleta, a janela **Active Directory** será apresentada. Clique em **Seguinte** para concluir esta configuração automaticamente ou selecione a caixa de verificação **Ignorar configuração automática do Active Directory e configurar as definições manualmente** e, em seguida, clique em **Seguinte**.
6. Se o computador não estiver associado a um domínio e a configuração do grupo local para proteger a comunicação entre o adaptador AD FS e o serviço Multi-Factor Authentication estiver incompleta, a janela **Grupo Local** será apresentada. Clique em **Seguinte** para concluir esta configuração automaticamente ou selecione a caixa de verificação **Ignorar configuração automática do Grupo Local e configurar as definições manualmente** e, em seguida, clique em **Seguinte**.
7. No assistente de instalação, clique em **Seguinte**. O Servidor Multi-Factor Authentication do Azure cria o grupo PhoneFactor Admins e adiciona a conta de serviço do AD FS ao mesmo.
   <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Na página **Iniciar o Instalador**, clique em **Seguinte**.
9. No instalador do adaptador AD FS do Multi-Factor Authentication, clique em **Seguinte**.
10. Clique em **Fechar** quando a instalação estiver terminada.
11. Quando o adaptador estiver instalado, precisa de o registar no AD FS. Abra o Windows PowerShell e execute o seguinte comando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
    <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Edite a política de autenticação global do AD FS para utilizar o adaptador recém-registado. Na consola de gestão do AD FS, aceda ao nó **Políticas de Autenticação**. Na secção **Multi-Factor Authentication**, clique na ligação **Editar** junto à secção **Definições Globais**. Na janela **Editar Política de Autenticação Global**, selecione **Multi-Factor Authentication** como método de autenticação adicional e, em seguida, clique em **OK**. O adaptador está registado como WindowsAzureMultiFactorAuthentication. Tem de reiniciar o serviço AD FS para o registo ser aplicado.

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Neste momento, o Servidor Multi-Factor Authentication está configurado para ser um fornecedor de autenticação adicional para utilização com o AD FS.

## Instalar uma instância autónoma do adaptador AD FS com o SDK do Serviço Web
1. Instale o SDK do Serviço Web no servidor que está a executar o Servidor Multi-Factor Authentication.
2. Copie os seguintes ficheiros a partir do diretório do Servidor \Program Files\Multi-Factor Authentication para o servidor no qual pretende instalar o adaptador AD FS:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Execute o ficheiro de instalação MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No instalador do adaptador AD FS do Multi-Factor Authentication, clique em **Seguinte** para efetuar a instalação.
5. Clique em **Fechar** quando a instalação estiver terminada.

## Editar o ficheiro MultiFactorAuthenticationAdfsAdapter.con fig
Siga estes passos para editar o ficheiro MultiFactorAuthenticationAdfsAdapter.config:

1. Defina o nó **UseWebServiceSdk** como **true**.  
2. Defina o valor de **WebServiceSdkUrl** para o URL do SDK do Serviço Web do Multi-Factor Authentication. Por exemplo: **https://contoso.com/&lt;certificatename&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx**, em que “certificatename” é o nome do certificado.  
3. Edite o ficheiro Register-MultiFactorAuthenticationAdfsAdapter.ps1 ao adicionar *-ConfigurationFilePath &lt;path&gt;* ao final do comando `Register-AdfsAuthenticationProvider`, em que *&lt;path&gt;* é o caminho completo para o ficheiro MultiFactorAuthenticationAdfsAdapter.config.

### Configurar o SDK do Serviço Web com um nome de utilizador e uma palavra-passe
Existem duas opções para configurar o SDK do serviço Web. A primeira consiste em utilizar um nome de utilizador e uma palavra-passe e a segunda um certificado de cliente. Siga estes passos para a primeira opção ou ignore-os para ir para a segunda.  

1. Defina o valor de **WebServiceSdkUsername** para uma conta que faça parte do grupo de segurança PhoneFactor Admins. Utilize o formato &lt;domínio&gt;&#92;&lt;nome de utilizador&gt;.  
2. Defina o valor de **WebServiceSdkPassword** para a palavra-passe da conta adequada.

### Configurar o SDK do Serviço Web com um certificado de cliente
Se não quiser utilizar um nome de utilizador e uma palavra-passe, siga estes passos para configurar o SDK do Serviço Web com um certificado de cliente.

1. Obtenha um certificado de cliente de uma autoridade de certificação para o servidor que está a executar o SDK do Serviço Web. Saiba como [obter certificados de cliente](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importe o certificado de cliente para o arquivo de certificados pessoais do computador local no servidor que está a executar o SDK do Serviço Web. Nota: certifique-se de que o certificado público da autoridade de certificação está no arquivo de Certificados de Raiz Fidedigna.  
3. Exporte as chaves públicas e privadas do certificado de cliente para um ficheiro .pfx.  
4. Exporte a chave pública no formato Base64 para um ficheiro .cer.  
5. No Gestor de Servidores, certifique-se de que a funcionalidade Servidor Web (IIS)\Servidor Web\Segurança\Autenticação de Mapeamento de Certificados de Cliente IIS está instalada. Se não estiver instalada, selecione **Adicionar Funções e Funcionalidades** para adicionar esta funcionalidade.  
6. No Gestor do IIS, faça duplo clique em **Editor de Configuração** no site que contém o diretório virtual do SDK do Serviço Web. Nota: é muito importante fazê-lo ao nível do site e não ao nível do diretório virtual.  
7. Aceda à secção **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Defina **enabled** como **true**.  
9. Defina **oneToOneCertificateMappingsEnabled** como **true**.  
10. Clique no botão **…** junto a **oneToOneMappings** e, em seguida, clique na ligação **Adicionar**.  
11. Abra o ficheiro .cer de Base64 que exportou anteriormente. Remova *-----BEGIN CERTIFICATE-----*, *-----END CERTIFICATE-----* e quaisquer quebras de linha. Copie a cadeia resultante.  
12. Defina o **certificado** para a cadeia que copiou no passo anterior.  
13. Defina **enabled** como **true**.  
14. Defina **userName** para uma conta que faz parte do grupo de segurança PhoneFactor Admins. Utilize o formato &lt;domínio&gt;&#92;&lt;nome de utilizador&gt;.  
15. Defina a palavra-passe para a palavra-passe da conta adequada e, em seguida, feche o Editor de Configuração.  
16. Clique na ligação **Aplicar**.  
17. No diretório virtual do SDK do Serviço Web, faça duplo clique em **Autenticação**.  
18. Verifique se a **Representação do ASP.NET** e a **Autenticação Básica** estão definidas como **Ativadas** e todos os outros itens estão **Desativados**.  
19. No diretório virtual do SDK do Serviço Web, faça duplo clique em **Definições de SSL**.  
20. Defina os **Certificados de Cliente** para **Aceitar** e, em seguida, clique em **Aplicar**.  
21. Copie o ficheiro .pfx que exportou anteriormente para o servidor que está a executar o adaptador AD FS.  
22. Importe o ficheiro .pfx para o arquivo de certificados pessoais do computador local.  
23. Clique com o botão direito e selecione **Gerir Chaves Privadas**. Em seguida, conceda acesso de leitura à conta que utilizou para iniciar sessão no serviço do AD FS.  
24. Abra o certificado de cliente e copie o thumbprint a partir do separador **Detalhes**.  
25. No ficheiro MultiFactorAuthenticationAdfsAdapter.config, defina **WebServiceSdkCertificateThumbprint** para a cadeia que copiou no passo anterior.  

Por fim, para registar o adaptador, execute o script \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 no PowerShell. O adaptador está registado como WindowsAzureMultiFactorAuthentication. Tem de reiniciar o serviço AD FS para o registo ser aplicado.

## Tópicos relacionados
Para obter ajuda para resolução de problemas, veja [Azure Multi-Factor Authentication FAQs (FAQ do Multi-Factor Authentication do Azure)](multi-factor-authentication-faq.md)

<!--HONumber=Sep16_HO4-->


