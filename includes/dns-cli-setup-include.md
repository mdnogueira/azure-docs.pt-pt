## <a name="set-up-azure-cli-for-azure-dns"></a>Configurar CLI do Azure para o Azure DNS

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração, verifique se tem os seguintes itens.

* Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativar os [Benefícios de subscritor do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se numa [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Instalar a versão mais recente da CLI do Azure, disponível para Windows, Linux ou Mac. Existem mais informações disponíveis em [Instalar a CLI do Azure](../articles/cli-install-nodejs.md).

### <a name="sign-in-to-your-azure-account"></a>Inicie sessão na sua conta do Azure

Abra uma janela de consola e autentique com as suas credenciais. Para obter mais informações, veja [Iniciar sessão no Azure a partir da CLI do Azure](../articles/xplat-cli-connect.md)

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>Alternar o modo da CLI

O DNS do Azure utiliza o Azure Resource Manager. Certifique-se de que altera o modo da CLI para utilizar comandos do Azure Resource Manager.

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>Selecionar a subscrição

Verifique as subscrições da conta.

```azurecli
azure account list
```

Escolha qual das suas subscrições do Azure utilizar.

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure Resource Manager requer que todos os grupos de recursos especifiquem uma localização. Isto é utilizado como a localização predefinida para recursos nesse grupo de recursos. No entanto, uma vez que todos os recursos DNS são globais, não regionais, a opção de localização do grupo de recursos não tem impacto no DNS do Azure.

Pode ignorar este passo se estiver a utilizar um grupo de recursos existente.

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>Registar o fornecedor de recursos

O serviço do DNS do Azure é gerido pelo fornecedor de recursos Microsoft.Network. A subscrição do Azure tem de estar registada para utilizar este fornecedor de recursos antes de poder utilizar o DNS do Azure. Esta é uma operação única para cada subscrição.

```azurecli
azure provider register --namespace Microsoft.Network
```

