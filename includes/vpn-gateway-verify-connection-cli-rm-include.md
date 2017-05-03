Pode verificar se a sua ligação foi concluída com êxito com o seguinte comando da CLI. Configure os valores para corresponder aos seus. No exemplo, -n refere-se ao nome da ligação que criou e quer testar.

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

Quando a ligação ainda estiver a ser estabelecida, o respetivo estado de ligação mostra "A ligar". No exemplo abaixo, o estado da ligação é apresentado como "Ligado" e pode ver bytes de entrada e de saída.