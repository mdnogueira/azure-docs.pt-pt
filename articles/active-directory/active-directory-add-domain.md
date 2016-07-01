<properties
    pageTitle="Adicionar o seu nome de domínio personalizado ao Azure Active Directory | Microsoft Azure"
    description="Como adicionar nomes de domínio da sua empresa ao Azure Active Directory e como verificar o nome de domínio."
    services="active-directory"
    documentationCenter=""
    authors="jeffsta"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/20/2016"
    ms.author="curtand;jeffsta"/>

# Adicionar o seu nome de domínio personalizado ao Azure Active Directory

Já tem um ou mais nomes de domínio que a sua organização utiliza para fins comerciais e os seus utilizadores iniciam sessão na rede da sua empresa utilizando o nome de domínio da sua empresa. Agora que está a utilizar o Azure Active Directory (Azure AD), pode também adicionar ao Azure AD o nome de domínio da sua empresa. Isto permite-lhe atribuir nomes de utilizador no diretório com que os seus utilizadores estão familiarizados, como “alice@contoso.com”. O processo é simples:

- Adicionar o seu nome de domínio no nosso assistente **Adicionar Domínio** no Portal Clássico do Azure

- Obter a entrada DNS no Portal Clássico do Azure AD ou a ferramenta do Azure AD Connect

- Adicionar a entrada DNS para o nome de domínio ao ficheiro de zona DNS no Web site para a entidade de registo DNS

- Verificar o nome do domínio no Portal Clássico do Azure AD ou na ferramenta do Azure AD Connect


Até que verifique o seu nome de domínio personalizado, os seus utilizadores terão de iniciar sessão com os nomes de utilizador como “alice@contoso.onmicrosoft.com”, que utilizam o nome de domínio inicial do seu diretório. Se necessitar de vários nomes de domínio personalizados, como “contoso.com” e “contosobank.com”, pode adicioná-los até um máximo de 900 nomes de domínio. Utilize os mesmos passos neste artigo para adicionar cada nome de domínio.

## Adicionar um nome de domínio personalizado ao seu diretório

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com/) com uma conta de utilizador que seja um administrador global do seu diretório do Azure AD.

2. Em **Active Directory**, abra o seu diretório e selecione o separador **Domínios**.

3. Na barra de comandos, selecione **Adicionar** e, em seguida, introduza o nome do seu domínio personalizado, como “contoso.com”. Certifique-se de que inclui .com .net ou outra extensão de nível superior.

4. Se planeia configurar este domínio para [início de sessão federado](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect) com o seu Active Directory no local, selecione a caixa de verificação.

5. Selecione **Adicionar**.

Agora que já adicionou o nome de domínio, o Azure AD tem de verificar que a sua organização é proprietária desse nome de domínio. Para que o Azure AD possa executar esta verificação, terá de adicionar uma entrada DNS no ficheiro de zona DNS para o nome de domínio. Esta tarefa é executada no Web site da entidade de registo de nome de domínio para o nome de domínio.

## Obter as entradas de DNS para o nome de domínio

As entradas de DNS estão na segunda página do assistente **Adicionar domínio**, se não se estiver a federar com um Active Directory Windows Server no local.

Se estiver a configurar o domínio para federação, será direcionado para transferir a ferramenta Azure AD Connect. Execute a ferramenta Azure AD Connect para [obter as entradas de DNS tem de que precisa para adicionar a sua entidade de registo de nome de domínio](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation). A ferramenta Azure AD Connect irá também verificar o nome de domínio para o Azure AD.

## Adicionar a entrada DNS ao ficheiro de zona DNS

1.  Inicie sessão na entidade de registo de nome de domínio para o domínio. Se não tem permissões suficientes para atualizar a entrada DNS, peça à pessoa ou equipa que tem este acesso para adicionar a entrada DNS.

2.  Atualize o ficheiro de zona DNS para o domínio ao adicionar a entrada DNS fornecida pelo Azure AD. Esta entrada DNS permite ao Azure AD verificar a sua propriedade do domínio. A entrada DNS não irá alterar quaisquer comportamentos, como o encaminhamento de correio ou o alojamento web. Pode demorar até uma hora para os registos DNS se propagarem.

[Instruções para adicionar uma entrada DNS em entidades de registo DNS populares](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/)

## Verificar o nome de domínio com o Azure AD

Depois de adicionar a entrada DNS, tem de se certificar de que o nome de domínio é verificado pelo Azure AD. Este é o último passo para ter êxito.

Se ainda tiver o assistente **Adicionar domínio** aberto, selecione **Verificar** na terceira página do assistente. Deve aguardar até uma hora para que a entrada DNS se propague, antes de a verificar.

Se o assistente **Adicionar domínio** já não estiver aberto, pode verificar o domínio no [Portal Clássico do Azure](https://manage.windowsazure.com/):

1.  Inicie sessão com uma conta de utilizador que seja um administrador global do seu diretório do Azure AD.

2.  Abra o seu diretório e selecione o separador **Domínios**.

3.  Selecione o domínio que pretende verificar.

4.  Selecione **Verificar** no comando de barras e, em seguida, selecione **Verificar** na caixa de diálogo.

Parabéns pelo seu sucesso! Já pode [atribuir nomes de utilizador que incluem o seu nome de domínio personalizado](active-directory-add-domain-add-users.md). Se tiver problemas para verificar o nome de domínio, consulte a nossa secção [Resolução de Problemas](#troubleshooting).

## Resolução de problemas
Se não conseguir verificar um nome de domínio personalizado, há algumas causas possíveis. Vamos começar com as mais comuns e prosseguir para as menos comuns.

- Tentou verificar o nome de domínio antes que a entrada DNS se pudesse propagar. Aguarde algum tempo e tente novamente.

- Não chegou a introduzir o registo DNS. Verifique a entrada DNS, aguarde que se propague e, em seguida, tente novamente.

- O nome de domínio já foi verificado noutro diretório. Localize o nome de domínio, elimine-o do outro diretório e tente novamente.

- O registo DNS contém um erro. Corrija o erro e tente novamente.

- Não tem permissão suficiente para atualizar os registos DNS. Partilhe as entradas de DNS com a pessoa ou equipa na sua organização que tem este acesso e peça-lhes para adicionar a entrada DNS.


## Passos seguintes

-   [Atribuir nomes de utilizador que incluem o seu nome de domínio personalizado](active-directory-add-domain-add-users.md)
-   [Gerir nomes de domínio personalizado](active-directory-add-manage-domain-names.md)
-   [Saber mais acerca de conceitos de gestão de domínios no Azure AD](active-directory-add-domain-concepts.md)
-   [Mostrar a imagem corporativa da sua empresa quando os seus utilizadores iniciam sessão](active-directory-add-company-branding.md)
-   [Utilizar o PowerShell para gerir os nomes de domínio no Azure AD](https://msdn.microsoft.com/library/azure/e1ef403f-3347-4409-8f46-d72dafa116e0#BKMK_ManageDomains)



<!--HONumber=Jun16_HO2-->


