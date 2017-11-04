Na janela de terminal local, adicione um remoto do Azure ao seu repositório Git local. Este remoto do Azure foi criado por si no [criar uma aplicação web](#create-a-web-app).

```bash
git remote add azure <URI from previous step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for pedida uma palavra-passe, confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```

O comando anterior apresenta informações semelhantes ao exemplo seguinte:
