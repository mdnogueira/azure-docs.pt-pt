<properties
    pageTitle="Adicionar a imagem corporativa às suas páginas de início de sessão e Painel de Acesso"
    description="Saiba como adicionar uma imagem corporativa à página de início de sessão do Azure e à página de painel de acesso"
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="04/16/2016" 
    ms.author="MarkVi"/>

# Adicionar a imagem corporativa às suas páginas de início de sessão e Painel de Acesso


Para evitar confusões, muitas empresas pretendem aplicar um aspeto e funcionalidade consistentes em todos os Web sites e serviços que gerem. O Azure Active Directory fornece esta capacidade ao permitir-lhe personalizar o aspeto das seguintes páginas web com o logótipo da sua empresa e esquemas de cores personalizados:

- **Página de início de sessão** - Esta é a página que aparece quando inicia sessão Office 365 ou outras aplicações baseadas na web que estão a utilizar o Azure AD como o seu fornecedor de identidade. Interage com esta página durante uma Deteção de Realm Inicial ou ao introduzir as suas credenciais. A Deteção de Realm Inicial permite ao sistema redirecionar utilizadores federados para as respetivas STS no local (por exemplo, o AD FS).

- **Página Painel de Acesso** - o Painel de Acesso é um portal baseado na web que lhe permite ver e iniciar as aplicações baseadas na nuvem a que o seu administrador do Azure AD lhe concedeu acesso. Para aceder ao Painel de Acesso, utilize o seguinte URL: [https://myapps.microsoft.com](https://myapps.microsoft.com).

Este tópico explica como pode personalizar a página de início de sessão e a página de painel de acesso.

> [AZURE.NOTE]
>
- Imagem corporativa é uma funcionalidade que só estará disponível se tiver efetuado a atualização para a edição Premium ou Basic do Azure Active Directory. Para obter mais informações, consulte [Edições do Azure Active Directory](active-directory-editions.md).
- As edições Premium e Basic do Azure Active Directory estão disponíveis para clientes na China que utilizem a instância mundial do Azure Active Directory. As edições Basic e Premium do Azure Active Directory não são atualmente suportadas pelo serviço Microsoft Azure operado pela 21Vianet na China. Para mais informações, contacte-nos através do [Fórum do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).



## Personalizar a página de início de sessão

Normalmente, se precisa de acesso baseado em browser para as suas aplicações e serviços na nuvem que a sua organização subscreve, utiliza a página de início de sessão.

Se tiver efetuado alterações na sua página de início de sessão, pode demorar até uma hora para que estas apareçam.

Uma página de início de sessão com marca corporativa só é apresentada quando visita um serviço com um URL de inquilino específico como https://outlook.com/**contoso**. com ou https://mail.**contoso**. com.

Quando visita um serviço com URLs específicos de não inquilino (por exemplo, https://mail.office365.com), é apresentada uma página de início de sessão sem uma imagem corporativa. Neste caso, a sua imagem corporativa aparece depois de introduzir o seu ID de utilizador ou de ter selecionado um mosaico de utilizador.

> [AZURE.NOTE]
>

- O nome de domínio tem de aparecer como “Ativo” na secção **Domínios** > **do Diretório** > **do Active Directory** do Portal Clássico do Azure onde tiver configurado uma imagem corporativa.

- A imagem corporativa na página de início de sessão de imagem não passa para a página de sessão do consumidor da Microsoft. Se iniciar sessão com uma conta Microsoft pessoal, poderá ver uma lista de mosaicos de utilizador com marca corporativa composto pelo Azure AD, mas a aplicação de imagem corporativa da sua organização não se aplica à página de início de sessão da Microsoft.


Se pretender mostrar nesta página a marca, cores e outros elementos personalizáveis da sua empresa,, consulte as seguintes imagens para entender a diferença entre as duas experiências.

A seguinte captura de ecrã mostra um exemplo da página de início de sessão do Office 365 num computador de secretária **antes** da personalização:

![Página de início de sessão do Office 365 antes da personalização][1]

A seguinte captura de ecrã mostra um exemplo da página de início de sessão do Office 365 num computador de secretária **depois** da personalização:

![Página de início de sessão do Office 365 após a personalização][2]

A seguinte captura de ecrã mostra um exemplo da página de início de sessão do Office 365 num dispositivo móvel **antes** da personalização:

![Página de início de sessão do Office 365 antes da personalização][3]


A seguinte captura de ecrã mostra um exemplo da página de início de sessão do Office 365 num dispositivo móvel **após** a personalização:

![Página de início de sessão do Office 365 após a personalização][4]


Quando redimensionar a janela do browser, ilustrações grandes, como a apresentada anteriormente, são muitas vezes recortadas para acomodar proporções de ecrã diferentes. Com isto em mente, deve tentar manter os elementos visuais chave na ilustração, para que sejam sempre apresentados no canto superior esquerdo (superior direito para idiomas da direita para a esquerda). Isto é importante porque o redimensionamento ocorre normalmente a partir do canto inferior direito em direção ao topo / esquerda ou a partir da parte inferior para o topo.

A imagem seguinte mostra como a ilustração é recortada no browser quando este é redimensionado para a esquerda:

![][6]

Eis como aparece depois do browser ser redimensionado em direção ao topo:

![][7]

## Que elementos na página posso personalizar?

Pode personalizar os seguintes elementos na página de início de sessão:

![][5]

 Elemento da página  | Localização na página
    ------------- | -------------
Logótipo de Faixa | Mostrado na parte superior direita da página. Substitui o logótipo que apresenta o site de destino em que está a iniciar sessão (por exemplo. Office 365 ou do Azure).
Ilustração Grande / Cor de Fundo | Apresentada à esquerda da página. Substitui a imagem que apresenta o site de destino que está a iniciar sessão. A Cor de Fundo poderá ser apresentada em vez da Ilustração Grande em ligações de largura de banda baixa ou em ecrãs estreitos.
Texto da Página de Início de Sessão | Mostrado acima do rodapé da página quando precisar de transmitir informações úteis antes de um início de sessão com uma conta escolar ou profissional. Por exemplo, pode querer incluir o número de telefone para o suporte técnico ou uma instrução legal.

> [AZURE.NOTE]
Todos os elementos são opcionais. Por exemplo, se especificar um Logótipo de Faixa, mas não uma Ilustração Grande, a página de início de sessão mostra o seu logótipo e a ilustração do site de destino (ou seja, a imagem de autoestrada da Califórnia do Office 365).

Também pode localizar todos os elementos nesta página. Depois de configurar um conjunto “predefinido” de elementos de personalização, pode configurar mais versões para diferentes regiões. Também pode misturar e combinar os vários elementos. Pode, por exemplo:

- Criar uma Ilustração Grande “predefinida” que funciona para todas as culturas e criar depois versões específicas em inglês e francês. Quando definir os seus browsers para um destes dois idiomas, aparece a imagem específica, ao passo que a ilustração predefinida é apresentada para todos os outros idiomas.

- Configurar logótipos diferentes para a sua organização (por exemplo, versões em japonês ou hebraico).



## Personalização de página do painel de acesso

A página do Painel de Acesso é essencialmente uma página de portal para um acesso rápido às aplicações da nuvem a que o seu administrador lhe concedeu acesso. Nesta página, as suas aplicações são apresentados como mosaicos clicáveis de aplicações. 


A seguinte captura de ecrã mostra um exemplo de uma página do painel de acesso após a personalização.

![][8]

## Configurar o diretório da sua empresa com a imagem corporativa

Pode configurar um conjunto predefinido de elementos personalizáveis por diretório no Portal Clássico do Azure. Uma vez guardadas as predefinições, um administrador pode adicionar versões localizadas de cada elemento, para idiomas / regiões diferentes. Todos os elementos personalizáveis são opcionais.

Por exemplo, se configurar um Logótipo de Faixa predefinido, mas não uma Ilustração Grande, a página de início de sessão apresenta o seu logótipo no canto superior direito. É no entanto apresentada a ilustração predefinida do site. 

Imagine a seguinte configuração:

- Um Logótipo de Faixa e Texto da Página de Início de Sessão predefinidos em inglês 
- Um Texto da Página de Início de Sessão específico para o alemão 

Se a sua preferência de idioma é alemão, verá o Logótipo de Faixa predefinido, mas o texto em alemão. 

Embora possa tecnicamente configurar um conjunto diferente para cada idioma suportado pelo Azure AD, recomendamos que mantenha o número de variações baixo, por motivos de manutenção e desempenho.

**Para adicionar a imagem corporativa da sua empresa ao seu diretório, execute os seguintes passos:**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global do diretório que pretende personalizar.
2. Selecione o diretório que pretende personalizar.
3. Na barra de ferramentas no canto superior, clique em **Configurar**.
4. Clique em **Personalizar a Imagem Corporativa**.
4. Modifique os elementos que pretende personalizar. Todos os campos são opcionais.
5. Clique em **Guardar**.

Pode demorar até uma hora para que apareça a nova alteração que efetuou na imagem corporativa da página de início de sessão.

**Para adicionar uma imagem corporativa específica do idioma, execute os seguintes passos:**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global do diretório que pretende personalizar.
2. Selecione o diretório que pretende personalizar.
3. Na barra de ferramentas no canto superior, clique em **Configurar**.
4. Clique em **Personalizar a Imagem Corporativa**.
2. Clique em **Adicionar imagem corporativa para um idioma específico**.
3. Selecione o idioma para que pretende personalizar o logótipo e clique em **Seguinte**.
3. Edite apenas os elementos para que pretende configurar substituições específicas do idioma. Todos os campos são opcionais. Se um campo for deixado em branco, é apresentado o valor predefinido personalizado (ou a predefinição da Microsoft, se não tiver configurado um personalizado predefinido).
4. Clique em **Guardar**.

**Para remover a imagem corporativa da sua empresa do seu diretório, execute os seguintes passos:**

1. Inicie sessão no [Portal Clássico do Azure](https://manage.windowsazure.com) como administrador global do diretório que pretende personalizar.
2. Selecione o diretório que pretende personalizar.
3. Na barra de ferramentas no canto superior, clique em **Configurar**.
4. Clique em **Personalizar a Imagem Corporativa**.
5. Na página Personalizar uma Imagem Corporativa, selecione **Editar Definições da Imagem Corporativa Existente** e vá para a página seguinte.
3. Dependendo dos elementos que pretende remover, efetue um ou mais dos seguintes procedimentos:

    a. Em **Logótipo de Faixa**, selecione **Remover o logótipo carregado**.

    b. Em **Logótipo de Mosaico**, selecione **Remover o logótipo carregado**.

    c. Remova o texto de todas as caixas de texto.

    d. Clique em **Seguinte**.

    e. Remova o texto de todas as caixas de texto.

4. Clique em **Guardar** para remover os elementos.
5. Se necessário, clique novamente em **Personalizar uma Imagem Corporativa** e repita estes passos para todas as imagens corporativas específicas do idioma que têm de ser removidas.
    Todas as definições de imagem corporativa foram removidas quando clicar em **Personalizar uma Imagem Corporativa** e vir o formulário **Personalizar Imagem Corporativa Predefinida** sem definições existentes configuradas.

## Testar e exemplos

Recomendamos que experimente com um inquilino de teste antes de efetuar alterações no seu ambiente de produção.

**Para verificar se a imagem corporativa foi aplicada:**

1. Abra uma sessão de browser InPrivate ou Incognito. 
2. Visite https://outlook.com/contoso.com, substituindo contoso.com com o domínio que tenha personalizado. 

Isto também funciona com domínios com o aspeto de contoso.onmicrosoft.com.

Para ajudar a criar conjuntos eficazes de personalização, personalizámos as duas páginas de início de sessão fictícias seguintes:

- [http://aka.ms/aaddemo001](http://aka.ms/aaddemo001)
- [http://aka.ms/aaddemo002](http://aka.ms/aaddemo002)

Para testar as definições específicas do idioma, tem de modificar as preferências de idioma predefinidas no seu browser para um idioma que definiu na sua personalização. No Internet Explorer, pode configurá-lo no menu **Opções da Internet**.

## Elementos personalizáveis

Alguns elementos personalizáveis no Azure AD tem vários casos de utilização. Pode configurar uma vez por diretório logótipos da empresa e este é utilizado na página de início de sessão e na do Painel de Acesso. Alguns elementos personalizáveis são específicos apenas da página de início de sessão. A tabela seguinte fornece detalhes para os diferentes elementos personalizáveis.

Nome | Descrição | Restrições | Recomendações
    ------------- | ------------- | ------------- | -------------
Logótipo de Faixa | O Logótipo de Faixa será apresentado na página de início de sessão e no Painel de Acesso. | <p>JPG ou PNG</p><p>60 x 280 pixels</p><p>10 KB</p> | <p>Utilize o logótipo completo da sua organização (incluindo pictograma e logótipo)</p><p>Mantenha uma altura inferior a 30 pixeis para evitar a introdução de barras de deslocamento em dispositivos móveis</p><p>Mantenha-a inferior a 4 KB</p><p>Utilize um PNG transparente (não parta do princípio de que a página de início de sessão tem sempre um fundo branco)</p>
Logótipo de Mosaico | (atualmente não utilizado na página de início de sessão) De futuro, este texto poderá ser utilizado para substituir o pictograma genérico de “conta escolar ou profissional” em locais diferentes da experiência. | <p>JPG ou PNG</p><p>120 x 120 pixéis</p><p>10 KB</p> | <p>Mantenha-o simples (sem texto pequeno), uma vez que esta imagem poderá ser redimensionada para 50%
</p> |
Etiqueta de Nome de Utilizador da Página de Início de Sessão | (atualmente não utilizada na página de início de sessão) No futuro, este texto poderá ser utilizado para substituir a cadeia genérica “conta escolar ou profissional” em locais diferentes da experiência. Pode configurá-lo para algo como “Conta de Contoso” ou “ID Contoso”. | <p>Texto Unicode, até 50 carateres</p><p>Apenas texto simples (sem ligações ou tags de HTML)</p> | <p>Mantenha-o curto e simples</p><p>Peça aos seus utilizadores como se costumam referir à conta profissional ou escolar que lhes fornece.</p>
Texto da Página de Início de Sessão | Este texto “automático” aparece por baixo do formulário da página de início de sessão e pode ser utilizado para comunicar instruções adicionais ou onde obter ajuda e suporte. | <p>Texto Unicode, até 256 carateres</p><p>Apenas texto simples (sem ligações ou tags de HTML)</p> | Mantenha-o abaixo dos 250 carateres (cerca de 3 linhas de texto)
Ilustração da Página de Início de Sessão | A ilustração é uma imagem grande que é apresentada na página de início de sessão à esquerda do formulário da página de início de sessão. | <p>JPG ou PNG</p><p>1420 x 1200</p><p>500 KB</p> | <p>1420 x 1200 pixéis</p><p>Importante: mantê-la o mais pequena possível, idealmente abaixo de 200 KB. Se esta imagem for demasiado grande, o desempenho da página de Início de Sessão é afetado se a imagem não estiver em cache</p><p>Esta imagem é frequentemente recortada, para suportar aspetos de ecrã diferentes. Mantenha os elementos visuais principais na parte superior esquerda canto (canto superior direito para linguagens RTL), porque redimensionamento ocorre a partir do canto inferior/direita, em direção à parte superior / esquerda, à medida que diminui a janela do browser.</p>
Cor de Fundo da Página de Início de Sessão | A cor de fundo de página de início de sessão é utilizada na área à esquerda do formulário da página de início de sessão. | Tem de ser uma cor RGB numa forma hexadecimal (exemplo: #FFFFFF) | <p>A cor de fundo poderá ser apresentada em vez da Ilustração Grande em ligações de largura de banda baixa</p><p>Sugerimos que escolha a cor primária do Logótipo de Faixa</p>


## Passos Seguintes

- [Introdução ao Azure Active Directory Premium](active-directory-get-started-premium.md)
- [Ver os relatórios de acesso e utilização](active-directory-view-access-usage-reports.md)

<!--Image references-->
[1]: ./media/active-directory-add-company-branding/SignInPage_beforecustomization.png
[2]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization.png
[3]: ./media/active-directory-add-company-branding/SignInPage_mobile_beforecustomization.png
[4]: ./media/active-directory-add-company-branding/SignInPage_mobile_aftercustomization.png
[5]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_elements.png
[6]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedleft.png
[7]: ./media/active-directory-add-company-branding/SignInPage_aftercustomization_croppedtop.png
[8]: ./media/active-directory-add-company-branding/APBranding.png



<!--HONumber=Jun16_HO2-->


