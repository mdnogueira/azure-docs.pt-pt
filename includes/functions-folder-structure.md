
O código para todas as funções de uma aplicação de função especificada se encontra numa pasta de raiz que contenha um ficheiro de configuração de anfitrião e uma ou mais subpastas, cada um dos quais contém o código para uma função separada, como no exemplo seguinte:

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

O *host.json* ficheiro contém uma configuração específica do tempo de execução e se encontra na pasta raiz da aplicação de função. Para obter informações sobre as definições que estão disponíveis, consulte [host.json referência](../articles/azure-functions/functions-host-json.md).

Cada função tem uma pasta que contém um ou mais ficheiros de código, a configuração de function.json e outras dependências.

