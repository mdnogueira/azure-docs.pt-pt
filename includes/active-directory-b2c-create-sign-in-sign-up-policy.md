Para ativar o início de sessão na sua aplicação, terá de criar uma política de início de sessão. Esta política descreve as experiências que os consumidores terão durante o início de sessão e os conteúdos de tokens que a aplicação receberá nos inícios de sessão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Na secção de políticas de definições, selecione **Políticas de inscrição ou início de sessão** e clique em **+ Adicionar**.

![Selecione as políticas de inscrição ou início de sessão e clique no botão Adicionar](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-policy.png)

Introduza um **Nome** de política para a aplicação como referência. Por exemplo, introduza `SiUpIn`.

Selecione **Fornecedores de identidade** e selecione **Inscrição de e-mail**. Opcionalmente, também pode selecionar fornecedores de identidade social, se já estiverem configurados. Clique em **OK**.

![Selecione Inscrição de e-mail como fornecedor de identidade e clique no botão OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-identity-providers.png)

Selecione **Atributos de inscrição**. Escolha os atributos que quer recolher do consumidor durante a inscrição. Por exemplo, selecione **País/Região**, **Nome a Apresentar**, e **Código Postal**. Clique em **OK**.

![Selecione alguns atributos e clique no botão OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-sign-up-attributes.png)

Selecione **Afirmações de aplicação**. Escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação depois de uma experiência de inscrição ou início de sessão com êxito. Por exemplo, selecione **Nome a Apresentar**, **Fornecedor de Identidade**, **Código Postal**, **O utilizador é novo** e **ID de Objeto do Utilizador**.

![Selecione algumas afirmações de aplicação e clique no botão OK](media/active-directory-b2c-create-sign-in-sign-up-policy/add-b2c-signup-signin-application-claims.png)

Clique em **Criar** para adicionar a política. A política está listada como **B2C_1_SiUpIn**. O prefixo **B2C_1_** é acrescentado ao nome.

Abra a política ao selecionar **B2C_1_SiUpIn**. Verifique as definições especificadas na tabela e clique em **Executar agora**.

![Selecione a política e execute-a](media/active-directory-b2c-create-sign-in-sign-up-policy/run-b2c-signup-signin-policy.png)

| Definição      | Valor  |
| ------------ | ------ |
| **Aplicações** | Aplicação Contoso B2C |
| **Selecionar o URL de resposta** | `https://localhost:44316/` |

É aberto um novo separador do browser e pode verificar a experiência de consumidor de inscrição ou início de sessão, conforme configurado.

> [!NOTE]
> A criação da política e as atualizações demoram até um minuto a serem aplicadas.
>