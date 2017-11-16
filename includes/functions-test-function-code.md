## <a name="test-the-function"></a>Testar a função

Utilize cURL para testar a função implementada num computador Mac ou Linux ou utilize Bash no Windows. Execute o comando cURL seguinte, substituindo o marcador de posição `<app_name>` pelo nome da sua aplicação Function App. Anexe a cadeia de consulta `&name=<yourname>` ao URL.

```bash
curl http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
```  

![Resposta da função mostrada num browser.](./media/functions-test-function-code/functions-azure-cli-function-test-curl.png)  

Se não tiver cURL disponível na sua linha de comandos, introduza o mesmo URL na barra de endereço do browser. Mais uma vez, substitua o marcador de posição `<app_name>` pelo nome da aplicação de funções, anexe a cadeia de consulta `&name=<yourname>` ao URL e execute o pedido. 

    http://<app_name>.azurewebsites.net/api/HttpTriggerJS1?name=<yourname>
   
![Resposta da função mostrada num browser.](./media/functions-test-function-code/functions-azure-cli-function-test-browser.png)  
