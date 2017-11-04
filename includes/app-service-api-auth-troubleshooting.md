A maioria dos erros de autenticação tempo resulta de definições de configuração inconsistentes ou incorretos. Aqui estão algumas sugestões específicos para aspetos a verificar.

* Certifique-se de que não perder o **guardar** botão em qualquer lugar. Isto, muitas vezes, é fácil fazer e o resultado é que que irá ser observar os valores corretos numa página de portal, mas, na verdade, não foram guardadas no ambiente do Azure ou da aplicação do Azure AD.
* Para as definições configuradas no **definições da aplicação** painel do portal do Azure, certifique-se de que a aplicação de API correta ou aplicação web foi selecionada quando as definições foram introduzidas.  Certifique-se de que as definições foram introduzidas como também **as definições de aplicação** e não **cadeias de ligação**, uma vez que o formato das dois secções é semelhante.
* Para a autenticação para um front-end de JavaScript, transfira o manifesto novamente para verificar se `oauth2AllowImplicitFlow` foi alterada com êxito para `true`.
* Certifique-se de que utilizou HTTPS independentemente do local onde configurou URLs:
  
  * No código de projeto
  * No CORS
  * Nas definições de aplicação de ambiente do Azure para cada aplicação API e a aplicação web
  * Nas definições de aplicação do Azure AD.
    
    Tenha em atenção que se copiar o URL de uma aplicação API no portal, muitas vezes tem `http://` e tiver de alterar manualmente a `https://`.
* Certifique-se de que as alterações de código foram implementadas com êxito. Por exemplo, numa solução de vários projeto é possível alterar o código de um projeto e escolha um dos outros acidentalmente quando pretender implementar a alteração.
* Certifique-se de que vai os URLs HTTPS no seu browser, não os URLs de HTTP. Por predefinição, o Visual Studio cria publicar perfis com HTTP URLs e que é o que se abre no browser depois de implementar um projeto.
* Para a autenticação para um front-end de JavaScript, certifique-se de que está corretamente configurada CORS na aplicação API que chama o código JavaScript. Em caso de dúvida sobre se o problema está relacionado com a CORS, tente "*" como o URL de origem permitidas. 
* Para um JavaScript front-end, abra o separador de consola de ferramentas de programador do seu browser para obter mais informações de erro e examinar os pedidos de HTTP na rede. No entanto, poderá enganosa mensagens de erro de consola. Se receber uma mensagem a indicar um erro CORS, o problema real pode ser a autenticação. Pode verificar se é este o caso, executando a aplicação com a autenticação temporariamente temporariamente desativada.
* Para uma aplicação .NET API, certifique-se estão a obter a maior quantidade informações em mensagens de erro possível definindo [customErrors modo desligado](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* Para uma aplicação .NET API, inicie um [sessão de depuração remota](../articles/app-service/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug)e examinar os valores das variáveis que são transmitidos para o código que utiliza a ADAL para adquirir um token de portador ou código que verifica as afirmações com o ID de principal do serviço esperado. Tenha em atenção que o seu código pode recolher valores de configuração a partir de várias origens diferentes, pelo que é possível localizar surpresas desta forma. Por exemplo, se de que escreveu `ida:ClientId` como `ida:ClientID` quando configurar as definições de ambiente de App Service do Azure, pode obter o código de `ida:ClientId` valor está à procura do ficheiro Web. config, a ignorar a definição de serviço de aplicações do Azure. 
* Se as coisas não funcionam numa janela do Internet Explorer normal, uma existente início de sessão pode estar a interferir; Tente InPrivate e tente Chrome ou o Firefox.

