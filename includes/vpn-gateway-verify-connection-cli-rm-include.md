Pode verificar se a sua ligação foi concluída com êxito com o comando [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Configure os valores para corresponder aos seus. No exemplo, --name refere-se ao nome da ligação que criou e quer testar.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

Quando a ligação ainda estiver a ser estabelecida, o respetivo estado de ligação mostra "A ligar". No exemplo abaixo, o estado da ligação é apresentado como "Ligado" e pode ver bytes de entrada e de saída.