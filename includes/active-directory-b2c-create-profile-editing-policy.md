Para ativar a edição de perfis na sua aplicação, terá de criar uma política de edição de perfis. Esta política descreve as experiências que os consumidores terão durante a edição de perfis e os conteúdos de tokens que a aplicação receberá após a conclusão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)]

Na secção de políticas de definições, selecione **Políticas de edição de perfis** e clique em **+ Adicionar**.

![Selecione Políticas de edição de perfis e clique no botão Adicionar](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-policy.png)

Introduza um **Nome** de política para a aplicação como referência. Por exemplo, introduza `SiPe`.

Selecione **Fornecedores de identidade** e selecione **Início de Sessão da Conta Local**. Opcionalmente, também pode selecionar fornecedores de identidade social, se já estiverem configurados. Clique em **OK**.

![Selecione Início de Sessão da Conta como fornecedor de identidade e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-identity-providers.png)

Selecione **Atributos do perfil**. Escolha os atributos que o consumidor pode ver e editar no respetivo perfil. Por exemplo, selecione **País/Região**, **Nome a Apresentar**, e **Código Postal**. Clique em **OK**.

![Selecione alguns atributos e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-attributes.png)

Selecione **Afirmações de aplicação**. Escolha as afirmações que quer que sejam devolvidas nos tokens de autorização enviados para a sua aplicação depois de uma experiência de edição de perfis com êxito. Por exemplo, selecione **Nome a Apresentar**, **Código Postal**.

![Selecione algumas afirmações de aplicação e clique no botão OK](media/active-directory-b2c-create-profile-editing-policy/add-b2c-editing-application-claims.png)

Clique em **Criar** para adicionar a política. A política está listada como **B2C_1_SiPe**. O prefixo **B2C_1_** é acrescentado ao nome.

Abra a política ao selecionar **B2C_1_SiPe**. Verifique as definições especificadas na tabela e clique em **Executar agora**.

![Selecione a política e execute-a](media/active-directory-b2c-create-profile-editing-policy/run-b2c-editing-policy.png)

| Definição      | Valor  |
| ------------ | ------ |
| **Aplicações** | Aplicação Contoso B2C |
| **Selecionar o URL de resposta** | `https://localhost:44316/` |

É aberto um novo separador do browser e pode verificar a experiência de consumidor de edição de perfis, conforme configurado.

> [!NOTE]
> A criação da política e as atualizações demoram até um minuto a serem aplicadas.
>