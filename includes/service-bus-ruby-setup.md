## <a name="create-a-ruby-application"></a>Criar uma aplicação Ruby
Para obter instruções, consulte [criar uma aplicação Ruby no Azure](../articles/virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar a sua aplicação ao utilizar o Service Bus
Para utilizar o Service Bus, transfira e utilize o pacote do Azure Ruby, que inclui um conjunto de bibliotecas de conveniência que comunicam com os serviços REST do storage.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote
1. Utilize uma interface de linha de comandos, como **PowerShell** (Windows), **Terminal** (Mac), ou **Bash** (Unix).
2. Escreva "azure de instalação gem" na janela de comando para instalar o gem e dependências.

### <a name="import-the-package"></a>Importar o pacote
Utilizar o editor de texto favorito, adicione o seguinte na parte superior do ficheiro Ruby em que pretende utilizar o armazenamento:

```ruby
require "azure"
```

## <a name="set-up-a-service-bus-connection"></a>Configurar uma ligação de barramento de serviço
Utilize o seguinte código para definir os valores de espaço de nomes, nome da chave, chave, signatário e anfitrião:

```ruby
Azure.configure do |config|
  config.sb_namespace = '<your azure service bus namespace>'
  config.sb_sas_key_name = '<your azure service bus access keyname>'
  config.sb_sas_key = '<your azure service bus access key>'
end
signer = Azure::ServiceBus::Auth::SharedAccessSigner.new
sb_host = "https://#{Azure.sb_namespace}.servicebus.windows.net"
```

Defina o valor de espaço de nomes para o valor que criou em vez do URL completo. Por exemplo, utilizar **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".
