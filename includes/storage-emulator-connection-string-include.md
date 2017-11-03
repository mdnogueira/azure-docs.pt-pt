O emulador do storage suporta uma única conta fixa e uma chave de autenticação conhecidos para a autenticação de chave partilhada. Esta conta e chave são as credenciais de chave partilhada apenas permitidas para utilização com o emulador de armazenamento. São:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> A chave de autenticação suportada pelo emulador do storage destina-se apenas para testar a funcionalidade do seu código de autenticação de cliente. Servem qualquer objetivo de segurança. Não é possível utilizar a conta de armazenamento de produção e a chave com o emulador de armazenamento. Não deve utilizar a conta de desenvolvimento com dados de produção.
> 
> O emulador de armazenamento suporta a ligação através de HTTP apenas. No entanto, o HTTPS é o protocolo recomendado para aceder a recursos na conta do storage do Azure de produção.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Ligar à conta do emulador utilizando um atalho
A forma mais fácil de ligar para o emulador de armazenamento a partir da sua aplicação consiste em configurar uma cadeia de ligação no ficheiro de configuração da aplicação que referencia o atalho `UseDevelopmentStorage=true`. Eis um exemplo de uma cadeia de ligação para o emulador do storage num *App. config* ficheiro: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Ligar à conta do emulador utilizando o nome da conta bem conhecido e a chave
Para criar uma cadeia de ligação que referencia o nome da conta do emulador e a chave, tem de especificar os pontos finais para cada um dos serviços que pretende utilizar o emulador na cadeia de ligação de. Isto é necessário para que a cadeia de ligação será feita referência pontos finais do emulador, que são diferentes dos para uma conta de armazenamento de produção. Por exemplo, o valor da cadeia de ligação terá este aspeto:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Este valor é idêntico ao atalho mostrado acima, `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Especifique um proxy HTTP
Também pode especificar um proxy HTTP a utilizar quando estiver a testar o seu serviço contra o emulador de armazenamento. Isto pode ser útil para observar os pedidos e respostas HTTP enquanto estiver a depuração operações contra os serviços de armazenamento. Para especificar um proxy, adicione o `DevelopmentStorageProxyUri` opção para a cadeia de ligação e defina o respetivo valor para o URI de proxy. Por exemplo, aqui é uma cadeia de ligação que aponta para o emulador do storage e configura um proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

