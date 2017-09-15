### <a name="to-modify-the-local-network-gateway-gatewayipaddress"></a>Para modificar o "gatewayIpAddress" do gateway de rede local

Se o dispositivo VPN que pretende ligar foi alterou o respetivo endereço IP público, tem de modificar o gateway de rede local para que essa alteração seja refletida. O endereço IP do gateway pode ser alterado sem remover uma ligação de gateway de VPN existente (caso tenha um). Para modificar o endereço IP do gateway, substitua os valores "Site2" e "TestRG1" pelos seus utilizando o comando [az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update).

```azurecli
az network local-gateway update --gateway-ip-address 23.99.222.170 --name Site2 --resource-group TestRG1
```

Certifique-se de que o endereço IP está correto na saída:

```
"gatewayIpAddress": "23.99.222.170",
```