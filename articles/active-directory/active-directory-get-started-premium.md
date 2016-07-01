<properties
    pageTitle="Introdução ao Azure Active Directory Premium"
    description="Um tópico que explica como se inscrever na edição Premium do Azure Active Directory através do site Licenciamento em Volume."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila" 
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/25/2016"
    ms.author="markvi"/>

# Introdução ao Azure Active Directory Premium


Tem várias opções para se inscrever no Active Directory Premium: 

**Azure ou Office 365** – na qualidade de subscritor do Azure ou do Office 365, pode comprar o Active Directory Premium online. Para obter os passos detalhados, consulte [Como Comprar o Azure Active Directory Premium – Clientes Existentes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-Existing-Customer) ou [Como Comprar o Azure Active Directory Premium – Novos Clientes](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/How-to-Purchase-Azure-Active-Directory-Premium-New-Customers).  

**Enterprise Mobility Suite** – o Enterprise Mobility Suite é uma forma económica de as organizações utilizarem os seguintes serviços sob um único plano de licenciamento: Active Directory Premium, Azure Rights Management, Microsoft Intune. Para obter mais informações, consulte o site do [Enterprise Mobility Suite](https://www.microsoft.com/en-us/server-cloud/enterprise-mobility/overview.aspx). Para obter a avaliação gratuita de 30 dias, clique [aqui](https://portal.office.com/Signup/Signup.aspx?OfferId=2E63A04D-BE0B-4A0F-A8CF-407C1C299221&dl=EMS&ali=1#0).


**Licenciamento em Volume da Microsoft** – o Azure Active Directory Premium está disponível através de um [Contrato Enterprise da Microsoft](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx) (250 licenças ou mais) ou do programa [Licenciamento em Volume Aberto](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx) (5 a 250 licenças).


Este tópico mostra como começar com o Azure Active Directory Premium que adquiriu através do programa de Licenciamento em Volume. Se não estiver familiarizado com as várias edições do Azure Active Directory, consulte [Edições do Azure Active Directory](active-directory-editions.md).  

> [AZURE.NOTE]
As edições Premium e Basic do Azure Active Directory estão disponíveis para clientes na China que utilizem a instância mundial do Azure Active Directory. As edições Basic e Premium do Azure Active Directory não são atualmente suportadas pelo serviço Microsoft Azure operado pela 21Vianet na China. Para mais informações, contacte-nos através do [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).




## Passo 1: Inscrever-se no Active Directory Premium

Para se inscrever, consulte [Como comprar através do Licenciamento em Volume](http://www.microsoft.com/en-us/licensing/how-to-buy/how-to-buy.aspx).



## Passo 2: Ativar o plano de licenciamento

Esta é a sua primeira compra de plano através do programa de Licenciamento em Volume para Empresas da Microsoft?
Neste caso, receberá um e-mail de confirmação quando a compra for concluída.
Esse e-mail será necessário para ativar o primeiro plano de licenciamento.

Em qualquer compra subsequente deste diretório, as licenças serão automaticamente ativadas no mesmo diretório.



**Para ativar o plano de licenciamento, efetue um dos seguintes passos:**


1. Para iniciar a ativação, clique em **Iniciar sessão** ou **Inscrever-se**.

    ![Iniciar sessão][1]



    - Se tiver um inquilino existente, clique em **Iniciar sessão** para iniciar sessão com a conta de administrador existente. Tem de iniciar sessão com as credenciais de administrador global do diretório onde as licenças devem ser ativadas.

    - Se pretender criar um novo inquilino do Azure Active Directory para utilizar com o plano de licenciamento, clique em **Inscrever-se** para abrir a caixa de diálogo **Criar Perfil de Conta**.

        ![Criar perfil de conta][2]

Quando tiver terminado, aparece a seguinte caixa de diálogo como confirmação da ativação do plano de licença do inquilino.

![Confirmação][3]

## Passo 3: Ativar o acesso ao Azure Active Directory

Se já tiver utilizado o Microsoft Azure, pode avançar para o [Passo 4](#step-4-assign-license-to-user-accounts). 

Ser-lhe-á enviado um **e-mail de boas-vindas** quando forem aprovisionadas licenças ao diretório. O e-mail confirma que pode começar a gerir as licenças e funcionalidades do Azure Active Directory Premium ou do Enterprise Mobility Suite. 

Se tentar ativar o acesso ao Azure Active Directory sem ter recebido o e-mail de boas-vindas, será apresentada a seguinte mensagem de erro. 

![Acesso não disponível][9]

Tente novamente dentro de alguns minutos quando tiver recebido o e-mail.

Os novos administradores da subscrição também podem ativar o respetivo acesso ao Portal Clássico do Azure através desta ligação.






**Para ativar o acesso ao Azure Active Directory, siga os seguintes passos:**

1. No **e-mail de boas-vindas**, clique em **Iniciar Sessão**. 
    
    ![E-mail de boas-vindas][4]

2. Quando tiver iniciado a sessão com êxito, terá de concluir uma segunda autenticação de fator sob forma de verificação móvel:

    ![Verificação móvel][5]

A ativação pode demorar alguns minutos. Assim que o acesso estiver ativo, a barra castanha desaparece e poderá clicar em **Portal**.

![Aguarde durante a configuração][6]

Neste caso, o acesso ao Azure está limitado ao Azure Active Directory.

![Capacidades do Azure][7]

É provável que já tenha acedido ao Azure a partir de uma utilização anterior; além disso, pode atualizar o acesso ao Azure Active Directory para o acesso completo ao Azure ativando subscrições do Azure adicionais. Nestes casos, o Portal Clássico do Azure tem mais funcionalidades.

![Capacidades do Azure][8]



## Passo 4: Atribuir licenças a contas de utilizador

Antes de começar a utilizar o plano que comprou, terá de atribuir manualmente licenças às contas de utilizador da sua organização para que possam utilizar as funcionalidades avançadas disponibilizadas pela conta Premium. Utilize os seguintes passos para atribuir licenças aos utilizadores para que possam utilizar as funcionalidades do Azure Active Directory Premium.

**Para atribuir licenças aos utilizadores, execute os seguintes passos:**

1. Inicie sessão no Portal Clássico do Azure como administrador global do diretório que pretende personalizar.
2. Clique em **Active Directory** e selecione o diretório onde pretende atribuir licenças.
3. Selecione o separador **Licenças**, selecione **Active Directory Premium** ou **Enterprise Mobility Suite** e clique em **Atribuir**.

    ![Planos de licença][10]

4. Na caixa de diálogo, selecione os utilizadores a quem pretende atribuir as licenças e clique no ícone de marca de verificação para guardar as alterações.

    ![Atribuir licenças][11]

### Restrições de licença

Alguns planos de licença são subconjuntos ou conjuntos mais amplos de outros planos de licença. Normalmente, não é possível atribuir uma licença que já tenha sido atribuída ao utilizador. Se pretender atribuir um plano de licença que for um conjunto mais amplo, terá primeiro de remover o plano de licença do conjunto mais amplo.

### Requisitos de licença

Quando atribui uma licença a um utilizador, pode especificar uma localização principal de utilização nas propriedades da respetiva conta. Se não for especificada uma localização de utilização, a localização do inquilino é automaticamente atribuída ao utilizador.

![Localização do utilizador][12]

A disponibilidade dos serviços e funcionalidades para um serviço em nuvem da Microsoft varia consoante o país ou a região. Um serviço, como o Voice over Internet Protocol (VoIP), poderá estar disponível num país ou numa região e não estar disponível noutro. Por motivos legais, as funcionalidades dentro de um serviço poderão ser restringidas em determinados países ou regiões. Para ver se um serviço ou funcionalidade está disponível com ou sem restrições, procure o seu país ou a sua região no site de restrições de licença de um serviço.

## Passos seguintes

- [Adicionar a imagem corporativa às páginas Iniciar Sessão e Painel de Acesso](active-directory-add-company-branding.md)
- [Ver os relatórios de acesso e utilização](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-get-started-premium/MOLSEmail.png
[2]: ./media/active-directory-get-started-premium/MOLSAccountProfile.png
[3]: ./media/active-directory-get-started-premium/MOLSThankYou.png
[4]: ./media/active-directory-get-started-premium/AADEmail.png
[5]: ./media/active-directory-get-started-premium/SignUppage.png
[6]: ./media/active-directory-get-started-premium/Subscriptionspage.png
[7]: ./media/active-directory-get-started-premium/Premiuminportal.png
[8]: ./media/active-directory-get-started-premium/Premiuminportal_large.png
[9]: ./media/active-directory-get-started-premium/Signuppage_oops.png
[10]: ./media/active-directory-get-started-premium/contosolicenseplan.png
[11]: ./media/active-directory-get-started-premium/Assignlicensespicker.png
[12]: ./media/active-directory-get-started-premium/Usagelocation.png



<!--HONumber=Jun16_HO2-->


