As atualizações do agente de sincronização de ficheiros do Azure serão lançadas regularmente para adicionar novas funcionalidades e a resolver quaisquer problemas que foram detetados. Recomendamos que configure o Microsoft Update para obter todas as atualizações para o agente de sincronização de ficheiros do Azure como Lançamos-los. Compreendemos que algumas organizações, como controlar estritamente e testar as atualizações. 

Para implementações que utilizam versões anteriores do agente de sincronização de ficheiros do Azure:

- O serviço de sincronização de armazenamento honra a versão principal anterior durante três meses após o lançamento inicial de uma versão principal nova. Por exemplo, o serviço de sincronização de armazenamento seria suporta a versão 1. \* até três meses após o lançamento da versão 2. \*.
- Depois de ter decorrido durante três meses, o serviço de sincronização de armazenamento começa a bloquear os servidores registados que estão a utilizar a versão expirada de sincronização com os respetivos grupos de sincronização.
- Dentro os três meses para uma versão principal anterior, todas as correções de erros, visite apenas a versão principal atual.

> [!Note]  
> Será notificado através de notificação de alerta no portal do Azure se estiver a utilizar uma versão de Sincronização de Ficheiros do Azure que irá expirar no prazo dos três meses seguintes.