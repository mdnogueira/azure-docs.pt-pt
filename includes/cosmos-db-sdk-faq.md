**1. Como os clientes receberá do retiring SDK?**

A Microsoft vai fornecer mês 12 notificação antecipada ao fim do suporte do retiring SDK para facilitar uma transição tranquila para um SDK suportado. Além disso, os clientes serão notificados através de vários comunicação canais – o Portal de gestão do Azure, Developer Center, mensagem de blogue, e comunicação direta para atribuídos os administradores de serviço.

**2. Podem aos clientes criar aplicações com um "restaurar" extinto Azure Cosmos DB SDK durante o período de 12 meses?** 

Sim, os clientes terão acesso total para criar, implementar e modificação das aplicações utilizando o "restaurar" extinto Azure Cosmos DB SDK durante o período de tolerância de 12 meses. Durante o período de tolerância de 12 meses, os clientes aconselha-se para migrar para uma versão suportada mais recente do Azure SDK de BD do Cosmos conforme apropriado.

**3. Os clientes podem criar e modificar aplicações utilizando uma base de dados SDK extinto do Azure Cosmos após o período de notificação de 12 meses?**

Após o período de notificação de 12 meses, o SDK será descontinuado. Qualquer acesso à base de dados do Azure Cosmos por um aplicações utilizando um SDK extinto não será permitido pela plataforma do Azure Cosmos DB. Além disso, a Microsoft não vai fornecer suporte ao cliente sobre o SDK extinto.

**4. O que acontece ao cliente da execução de aplicações que estão a utilizar a versão não suportada do SDK de BD do Cosmos do Azure?**

Quaisquer tentativas de ligar para o serviço de base de dados do Azure Cosmos com uma versão do SDK extinto serão rejeitadas. 

**5. Novas funcionalidades e funções são aplicadas a todos os SDKs de não extinto?**

Novas funcionalidades e funções apenas serão adicionadas novas versões. Se estiver a utilizar uma versão antiga não-extinto, do SDK pedidos para a base de dados do Azure Cosmos continuará a funcionar como anterior, mas não terão acesso a quaisquer novas capacidades.  

**6. O que posso fazer se não é possível atualizar a minha aplicação antes de uma data de truncado?**

Recomendamos que Atualize para o SDK mais recente antecipadamente quanto possível. Depois de um SDK tem sido etiquetado para extinção terá de atualizar a aplicação de 12 meses. Se, por qualquer motivo, não é possível concluir a atualização da aplicação durante este período de tempo, contacte o [Cosmos DB equipa](mailto:askcosmosdb@microsoft.com) e pedir os seus assistência antes da data de serem.

