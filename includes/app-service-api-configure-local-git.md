Configure a implementação local do Git para a aplicação API com o comando [az webapp source-control config-local-git](/cli/azure/appservice/web/source-control#config-local-git).   

O Serviço de Aplicações suporta várias formas de implementar conteúdo numa aplicação Web, como FTP, Git local, GitHub, Visual Studio Team Services e Bitbucket. Para este início rápido, pode implementar com o Git local. Isto significa que implementa com um comando do Git para enviar a partir de um repositório local para um repositório no Azure.  

Use o script a seguir para definir as credenciais de implementação ao nível da conta que será usada com o código, certificando-se de que inclui os seus próprios valores para o nome de utilizador e palavra-passe.   

```azurecli-interactive
az webapp deployment user set --user-name <desired user name> --password <desired password>
```
