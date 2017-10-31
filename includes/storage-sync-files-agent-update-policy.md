As atualizações para o agente de sincronização de ficheiros do Azure serão lançadas regularmente para adicionar novas funcionalidades e resolver quaisquer problemas encontrados. Recomendamos que ative o Microsoft Update para obter todas as atualizações para o agente de sincronização de ficheiros do Azure à medida que as lançamos. Dito isto, compreendemos que algumas organizações gostam de controlar e testar rigorosamente as atualizações. Para implementações que utilizam versões mais antigas do agente de Sincronização de Ficheiros do Azure:

- O Serviço de Sincronização de Armazenamento respeitará a versão principal anterior durante três meses após o lançamento inicial de uma versão principal nova. Por exemplo, a versão 1.\* seria suportada pelo Serviço de Sincronização de Armazenamento até três meses após o lançamento da versão 2.\*.
- Após os três meses, o Serviço de Sincronização de Armazenamento irá começar a bloquear a sincronização dos Servidores Registados que utilizem a versão expirada com os respetivos grupos de sincronização.
- No prazo dos três meses de uma versão principal anterior, todas as correções de erros só passarão para a versão principal atual.

> [!Note]  
> Será notificado através de notificação de alerta no portal do Azure se estiver a utilizar uma versão de Sincronização de Ficheiros do Azure que irá expirar no prazo dos três meses seguintes.