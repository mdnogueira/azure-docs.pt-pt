Para ativar o início de sessão na sua aplicação, terá de criar uma política de início de sessão. Esta política descreve as experiências que os consumidores terão durante o início de sessão e os conteúdos de tokens que a aplicação receberá nos inícios de sessão com êxito.

[!INCLUDE [active-directory-b2c-portal-navigate-b2c-service](active-directory-b2c-portal-navigate-b2c-service.md)] Clique em **Políticas de início de sessão**.

Clique em **+ Adicionar** na parte superior do painel.

O **Nome** determina o nome da política de início de sessão utilizado pela sua aplicação. Por exemplo, introduza **SiIn**.

Clique em **Fornecedores de identidade** e selecione **Início de Sessão da Conta Local**. Opcionalmente, também pode selecionar fornecedores de identidade social, se já estiverem configurados. Clique em **OK**.

Clique em **Afirmações de aplicação**. Aqui, pode escolher as afirmações que quer que sejam devolvidas nos tokens enviados para a sua aplicação depois de uma experiência de início de sessão com êxito. Por exemplo, selecione **Nome a Apresentar**, **Fornecedor de Identidade**, **Código Postal** e **ID de Objeto do Utilizador**. Clique em **OK**.

Clique em **Criar**. Tenha em atenção que a política que acabou de criar é apresentada como **B2C_1_SiIn** (o fragmento **B2C\_1\_**  é adicionado automaticamente) no painel **Políticas de início de sessão**.

Abra a política ao clicar em **B2C_1_SiIn**.

Selecione **Aplicação Contoso B2C** no menu pendente **Aplicações** e `https://localhost:44321/` no menu pendente **URL de Resposta/URI de Redirecionamento**.

Clique em **Executar agora**. É aberto um novo separador do browser e pode percorrer a experiência de consumidor de início de sessão na sua aplicação.

> [!NOTE]
> A criação da política e as atualizações demoram até um minuto a serem aplicadas.
>