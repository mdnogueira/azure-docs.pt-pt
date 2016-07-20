## O que são filas do Service Bus?

As filas do Service Bus suportam um modelo de comunicação de **mensagens mediadas**. Ao utilizar filas, os componentes de uma aplicação distribuída não comunicam diretamente entre si; trocam antes mensagens através de uma fila, que funciona como um intermediário (mediador). Um produtor de mensagens (remetente) entrega uma mensagem à fila e, em seguida, prossegue com o processamento. De modo assíncrono, um consumidor de mensagens (recetor) obtém a mensagem da fila e processa-a. O produtor não tem de aguardar uma resposta do consumidor para poder continuar a processar e a enviar mais mensagens. As filas oferecem uma entrega de mensagens **First In, First Out (FIFO)** para um ou mais consumidores concorrentes. Ou seja, as mensagens são normalmente recebidas e processadas pelos recetores pela ordem em que foram adicionadas à fila e cada mensagem é recebida e processada por apenas um consumidor de mensagens.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

As filas do Service Bus são uma tecnologia para fins gerais que pode ser utilizada para uma vasta gama de cenários:

-   Comunicação entre funções da Web e de trabalho numa aplicação Azure multicamadas.
-   Comunicação entre aplicações no local e aplicações alojadas no Azure numa solução híbrida.
-   Comunicação entre componentes de uma aplicação distribuída em execução no local em diferentes organizações ou departamentos de uma organização.

A utilização de filas permite ao utilizador dimensionar mais facilmente as aplicações e permite mais resiliência na arquitetura.

## Criar um espaço de nomes do serviço

Para começar a utilizar as filas do Service Bus no Azure, deve começar por criar um espaço de nomes do serviço. Um espaço de nomes fornece um contentor de âmbito para enviar os recursos do Service Bus na aplicação.

Para criar um espaço de nomes:

1.  Inicie sessão no [Portal Clássico do Azure][].

2.  No painel de navegação esquerdo do portal, clique em **Service Bus**.

3.  No painel inferior do portal, clique em **Criar**.
    ![](./media/howto-service-bus-queues/sb-queues-03.png)

4.  Na caixa de diálogo **Adicionar um novo espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.   
    ![](./media/howto-service-bus-queues/sb-queues-04.png)

5.  Após verificar se o nome do espaço de nomes está disponível, escolha o país ou a região onde será alojado o espaço de nomes (verifique se utiliza o mesmo país/região onde está a implementar os recursos de computação).

     > [AZURE.IMPORTANT] Escolha a **mesma região** que pretende escolher para a implementação da aplicação. Tal proporcionará o melhor desempenho.

6.  Deixe os outros campos na caixa de diálogo com os respetivos valores predefinidos (**Mensagens** e **Escalão Standard**) e clique na marca de verificação OK. O sistema cria o espaço de nomes e ativa-o. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.

    ![](./media/howto-service-bus-queues/getting-started-multi-tier-27.png)

O espaço de nomes que criou leva alguns instantes até estar ativado, sendo depois apresentado no portal. Aguarde até que o estado do espaço de nomes seja **Ativo** antes de continuar.

## Obter as credenciais de gestão predefinidas para o espaço de nomes

Para realizar operações de gestão como, por exemplo, criar uma fila no novo espaço de nomes, tem de obter as credenciais de gestão para o espaço de nomes. Pode obter estas credenciais no [Portal Clássico do Azure][].

###Para obter as credenciais de gestão no portal

1.  No painel de navegação esquerdo, clique no nó **Service Bus** para ver a lista de espaços de nomes disponíveis:   
    ![](./media/howto-service-bus-queues/sb-queues-13.png)

2.  Na lista apresentada, selecione o nome do espaço de nomes que acabou de criar:   
    ![](./media/howto-service-bus-queues/sb-queues-09.png)

3.  Clique em **Informações da Ligação**.   
    ![](./media/howto-service-bus-queues/sb-queues-06.png)

4.  No painel **Aceder às informações da ligação**, localize a cadeia de ligação com a chave SAS e o nome da chave.   

    ![](./media/howto-service-bus-queues/multi-web-45.png)
    
5.  Tome nota da chave ou copie-a para a área de transferência.

  [Portal Clássico do Azure]: http://manage.windowsazure.com




<!--HONumber=Jun16_HO2-->


