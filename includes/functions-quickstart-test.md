
Uma vez que os inícios rápidos das Funções do Azure contêm código funcional, pode testar a nova função imediatamente.

1. No separador **Desenvolver**, reveja a janela **Código** e tenha em atenção que este código Node.js espera um pedido HTTP com um valor *nome* transmitido no corpo da mensagem ou numa cadeia de consulta. Quando a função é executada, este valor é devolvido na mensagem de resposta.
   
2. Clique em **Testar** para apresentar o painel de pedido de teste HTTP da função.
 
    ![](./media/functions-quickstart-test/function-app-develop-tab-testing.png)

3. Na caixa de texto **Corpo do pedido**, altere o valor da propriedade *nome* para o seu nome e clique em **Executar**. Verá que a execução é acionada por um pedido HTTP de teste, as informações são escritas nos registos e a resposta "olá..." é apresentada no **Resultado**. 

4. Para acionar a execução da mesma função a partir de uma ferramenta de teste HTTP ou a partir de outra janela do browser, copie o valor **URL da Função** do separador **Desenvolver** e cole-o na ferramenta ou numa barra de endereço do browser. Acrescente o valor da cadeia de consulta `&name=yourname` ao URL e execute o pedido. Tenha em atenção que as mesmas informações são escritas nos registos e a mesma cadeia está no corpo da mensagem de resposta.

    ![](./media/functions-quickstart-test/function-app-browser-testing.png)


<!--HONumber=Feb17_HO2-->


