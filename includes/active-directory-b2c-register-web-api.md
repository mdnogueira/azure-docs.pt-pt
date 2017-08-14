[!INCLUDE [active-directory-b2c-portal-add-application](active-directory-b2c-portal-add-application.md)]

Para registar a API Web, utilize as definições especificadas na tabela.

![Definições de registo de exemplo para a nova API Web](./media/active-directory-b2c-register-web-api/b2c-new-web-api-settings.png)

| Definição      | Valor da amostra  | Descrição                                        |
| ------------ | ------- | -------------------------------------------------- |
| **Nome** | API Contoso B2C | Introduza um **Nome** para a aplicação que descreva a API aos consumidores. | 
| **Incluir aplicação/API Web** | Sim | Selecione **Sim** para uma API Web. |
| **Permitir fluxo implícito** | Sim | Selecione **Sim** se a sua aplicação utilizar o [Início de sessão OpenID Connect](../articles/active-directory-b2c/active-directory-b2c-reference-oidc.md) |
| **URL de resposta** | `https://localhost:44316/` | Os URLs de resposta são pontos finais para onde o Azure AD B2C devolve quaisquer tokens que a aplicação solicite. Introduza [um](../articles/active-directory-b2c/active-directory-b2c-app-registration.md#choosing-a-web-app-or-api-reply-url) **URL de resposta** adequado. Neste exemplo, a API Web é local e está a escutar na porta 44316. |
| **URI do ID da Aplicação** | api | O URI do ID da Aplicação é o identificador utilizado para a API Web. O URI identificador completo, incluindo o domínio, é gerado por si. |

Clique em **Criar** para registar a aplicação.

A aplicação recentemente registada é apresentada na lista de aplicações para o inquilino do B2C. Selecione a API Web na lista. É apresentado o painel de propriedades da API.

![Propriedades da API Web](./media/active-directory-b2c-register-web-api/b2c-web-api-properties.png)

Anote o **ID de Cliente da Aplicação** globalmente exclusivo. Utilize o ID no código da aplicação.