## <a name="push-to-azure-from-git"></a>Enviar para o Azure a partir do Git

Adicione um Azure remoto para o seu repositório do Git local.

```bash
git remote add azure <URI from previous step>
```

Envie para o Azure remoto para implementar a sua aplicação. É-lhe pedida a palavra-passe que indicou quando criou o utilizador de implementação, anteriormente. Confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```

O comando anterior apresenta informações semelhantes ao exemplo seguinte:
