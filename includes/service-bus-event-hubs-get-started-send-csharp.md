## Enviar mensagens para os Event Hubs

Nesta secção, irá escrever uma aplicação de consola do Windows para enviar eventos para o seu Hub de Eventos.

1. No Visual Studio, crie um novo projeto de Aplicação de Ambiente de Trabalho do Visual C# com o modelo de projeto **Aplicação de Consola**. Dê ao projeto o nome **Remetente**.

    ![][7]

2. No Explorador de Soluções, clique com o botão direito do rato na solução e, em seguida, clique em **Gerir Pacotes NuGet para Solução**. 

3. Clique no separador **Procurar** e procure `Microsoft Azure Service Bus`. Verifique se o nome do projeto (**Remetente**) está especificado na caixa **Versões**. Clique em **Instalar** e aceite os termos de utilização. 

    ![][8]

    O Visual Studio transfere, instala e adiciona uma referência ao [Pacote NuGet da biblioteca do Service Bus do Azure](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

4. Adicione as seguinte declarações `using` na parte superior do ficheiro **Program.cs**:

    ```
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Adicione os campos seguintes à classe **Programa**, substituindo os valores dos marcadores de posição pelo nome do Hub de Eventos que criou na secção anterior e pela cadeia de ligação de ao nível do espaço de nomes que guardou anteriormente.

    ```
    static string eventHubName = "{Event Hub name}";
    static string connectionString = "{send connection string}";
    ```

6. Adicione o seguinte método à classe **Programa**:

    ```
    static void SendingRandomMessages()
    {
        var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
        while (true)
        {
            try
            {
                var message = Guid.NewGuid().ToString();
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                Console.ResetColor();
            }

            Thread.Sleep(200);
        }
    }
    ```

    Este método envia continuamente eventos para o Event Hub com um atraso de 200 ms.

7. Por fim, adicione as seguintes linhas ao método **Main**:

    ```
    Console.WriteLine("Press Ctrl-C to stop the sender process");
    Console.WriteLine("Press Enter to start now");
    Console.ReadLine();
    SendingRandomMessages();
    ```


<!-- Images -->
[7]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png
[8]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp2.png


<!--HONumber=ago16_HO4-->


