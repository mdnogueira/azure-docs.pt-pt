## <a name="what-are-service-bus-topics-and-subscriptions"></a>O que são os tópicos e as subscrições do Service Bus?
Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Ao contrário das filas do Service Bus, em que cada mensagem é processada por um único consumidor, os tópicos e as subscrições proporcionam uma forma de comunicação de “um-para-muitos”, utilizando um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente.

Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, pode registar regras de filtragem para um tópico numa base por subscrição, o que lhe permite filtrar ou restringir que mensagens para um tópico são recebidas por que subscrições desse tópico.

Os tópicos e as subscrições do Service Bus permitem ao utilizador dimensionar e processar um número muito grande de mensagens entre muito utilizadores e aplicações.

## <a name="create-a-namespace"></a>Criar um espaço de nomes
Para começar a utilizar os tópicos e as subscrições do Service Bus no Azure, deve começar por criar um *espaço de nomes do serviço*. Um espaço de nomes fornece um contentor de âmbito para abordar os recursos do Service Bus na sua aplicação.

Para criar um espaço de nomes:

1. Inicie sessão no [Portal do Azure][Azure portal].
2. No painel de navegação à esquerda do portal, clique em **Novo** e, em seguida, clique em **Integração do Enterprise** e em **Service Bus**.
3. Na caixa de diálogo **Criar espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
4. Após se certificar de que o espaço de nomes está disponível, selecione o escalão de preço (Básico, Standard ou Premium).
5. No campo **Subscrição**, selecione a subscrição do Azure em que pretende criar o espaço de nomes.
6. No campo **Grupo de recursos**, selecione um grupo de recursos existente em que o espaço de nomes será colocado ou crie um novo.      
7. Em **Localização**, selecione o país ou a região em que o espaço de nomes deverá ser alojado.
   
    ![Criar espaço de nomes][create-namespace]
8. Clique no botão **Criar**. O sistema cria o espaço de nomes e ativa-o. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.

### <a name="obtain-the-credentials"></a>Obter as credenciais
1. Na lista de espaços de nomes, clique no nome do espaço de nomes criado recentemente.
2. No painel **Espaço de nomes do Service Bus**, clique em **Políticas de acesso partilhado**.
3. No painel **Políticas de acesso partilhado**, clique em **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. No painel **Política: RootManageSharedAccessKey**, clique no botão de cópia junto a **Cadeia de ligação – chave primária** para copiar a cadeia de ligação para a sua área de transferência e utilizá-la mais tarde.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png




<!--HONumber=Jan17_HO1-->


