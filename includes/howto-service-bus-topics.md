## O que são os tópicos e as subscrições do Service Bus?

Os tópicos e as subscrições do Service Bus suportam um modelo de comunicação de mensagens de *publicação/subscrição*. Ao utilizar tópicos e subscrições, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de um tópico, que funciona como um intermediário.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Ao contrário das filas do Service Bus, em que cada mensagem é processada por um único consumidor, os tópicos e as subscrições proporcionam uma forma de comunicação de “um-para-muitos”, utilizando um padrão de publicação/subscrição. É possível registar várias subscrições num tópico. Quando uma mensagem é enviada para um tópico, é depois disponibilizada para que cada subscrição a processe de forma independente.

Uma subscrição num tópico é semelhante a uma fila virtual que recebe cópias das mensagens que foram enviadas para o tópico. Opcionalmente, pode registar regras de filtragem para um tópico numa base por subscrição, o que lhe permite filtrar ou restringir que mensagens para um tópico são recebidas por que subscrições desse tópico.

Os tópicos e as subscrições do Service Bus permitem ao utilizador dimensionar e processar um número muito grande de mensagens entre muito utilizadores e aplicações.

## Criar um espaço de nomes

Para começar a utilizar os tópicos e as subscrições do Service Bus no Azure, deve começar por criar um *espaço de nomes do serviço*. Um espaço de nomes fornece um contentor de âmbito para enviar os recursos do Service Bus na aplicação.

Para criar um espaço de nomes:

1.  Inicie sessão no [Portal Clássico do Azure][].

2.  No painel de navegação esquerdo do portal, clique em **Service Bus**.

3.  No painel inferior do portal, clique em **Criar**.   
    ![][0]

4.  Na caixa de diálogo **Adicionar um novo espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.   
    ![][2]

5.  Após verificar se o nome do espaço de nomes está disponível, escolha o país ou a região onde será alojado o espaço de nomes (verifique se utiliza o mesmo país/região onde está a implementar os recursos de computação).

    > [AZURE.IMPORTANT] Escolha a **mesma região** que pretende escolher para a implementação da aplicação. Tal proporcionará o melhor desempenho.

6.  Deixe os outros campos na caixa de diálogo com os respetivos valores predefinidos (**Mensagens** e **Escalão Standard**) e clique na marca de verificação OK. O sistema cria o espaço de nomes e ativa-o. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.

    ![][6]

## Obter as credenciais de gestão predefinidas para o espaço de nomes

Para realizar operações de gestão como, por exemplo, criar um tópico ou uma subscrição no novo espaço de nomes, tem de obter as credenciais de gestão para o espaço de nomes. Pode obter estas credenciais no portal.

### Obter as credenciais de gestão no portal

1.  No painel de navegação esquerdo, clique no nó **Service Bus** para mostrar a lista de espaços de nomes disponíveis.   
    ![][0]

2.  Na lista apresentada, selecione o nome do espaço de nomes que acabou de criar:   
    ![][3]

3.  Clique em **Informações da Ligação**.   
    ![][4]

4.  Na caixa de diálogo **Aceder às informações da ligação**, localize a cadeia de ligação com a chave SAS e o nome da chave. Tome nota destes valores, uma vez que irá utilizar esta informação mais tarde para realizar as operações com o espaço de nomes. 


  [Portal Clássico do Azure]: http://manage.windowsazure.com
  [0]: ./media/howto-service-bus-topics/sb-queues-13.png
  [2]: ./media/howto-service-bus-topics/sb-queues-04.png
  [3]: ./media/howto-service-bus-topics/sb-queues-09.png
  [4]: ./media/howto-service-bus-topics/sb-queues-06.png
  
  [6]: ./media/howto-service-bus-topics/getting-started-multi-tier-27.png




<!--HONumber=Jun16_HO2-->


