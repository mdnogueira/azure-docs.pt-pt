Utilizar a CLI do Azure para obter o URL de implementação remota da sua aplicação API. No comando seguinte, substitua  *\<app_name >* por um nome de aplicação Web.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group myResourceGroup --query url --output tsv
```

Configure sua implantação local do Git para enviar por push para o controlo remoto.

```bash
git remote add azure <URI from previous step>
```

Envie para o Azure remoto para implementar a sua aplicação. É-lhe pedida a palavra-passe que indicou quando criou o utilizador de implementação, anteriormente. Confirme que introduz a palavra-passe que criou anteriormente no início rápido e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```
