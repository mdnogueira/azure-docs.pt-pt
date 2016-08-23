1. Inicie sessão no Portal Clássico do Azure.

2. No painel de navegação esquerdo do portal, clique em **Service Bus**.

3. No painel inferior do portal, clique em **Criar**.

    ![Selecione Criar][select-create]
   
4. Na caixa de diálogo **Adicionar um novo espaço de nomes**, introduza um nome de espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.

    ![Nome do espaço de nomes][namespace-name]
  
5. Depois de se certificar de que o nome do espaço de nomes está disponível, selecione o país ou a região na qual o seu espaço de nomes deverá estar alojado.

6. Deixe os outros campos na caixa de diálogo com os respetivos valores predefinidos (**Mensagens** e **Escalão Standard**) e clique na marca de verificação OK. O sistema cria o espaço de nomes e ativa-o. Poderá ter de aguardar alguns minutos enquanto o sistema aprovisiona recursos para a sua conta.
 
    ![Criado com êxito][created-successfully]

###Obter as credenciais
1. No painel de navegação esquerdo, clique no nó **Service Bus** para ver a lista de espaços de nomes disponíveis:
 
    ![Selecionar barramento de serviço][select-service-bus]
  
2. Na lista apresentada, selecione o nome do espaço de nomes que acabou de criar:
 
    ![Selecionar espaço de nomes][select-namespace]
 
3. Clique em **Informações da Ligação**.

    ![Informações da ligação][connection-information]
  
4. No painel **Aceder às informações de ligação**, localize a cadeia de ligação com a chave SAS e o nome da chave.

    ![Aceder às informações da ligação][access-connection-information]
  
5. Tome nota da chave ou copie-a para a área de transferência.

<!--Image references-->

[select-create]: ./media/service-bus-create-namespace-portal/select-create.png
[namespace-name]: ./media/service-bus-create-namespace-portal/namespace-name.png
[created-successfully]: ./media/service-bus-create-namespace-portal/created-successfully.png
[select-service-bus]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[select-namespace]: ./media/service-bus-create-namespace-portal/select-namespace.png
[connection-information]: ./media/service-bus-create-namespace-portal/connection-information.png
[access-connection-information]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com


<!--HONumber=Aug16_HO1-->


