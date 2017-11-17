Na janela de terminal local, adicione um remoto do Azure ao seu repositório Git local. Este remoto do Azure foi criado por si no [criar uma aplicação web](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Envie para o remoto do Azure para implementar a sua aplicação com o comando seguinte. Quando lhe for pedida uma palavra-passe, confirme que introduz a palavra-passe que criou em [Configurar um utilizador de implementação](#configure-a-deployment-user) e não a que utilizou para iniciar sessão no portal do Azure.

```bash
git push azure master
```

Este comando pode demorar alguns minutos para ser executada. Ao ser executado, apresenta informações semelhantes ao seguinte exemplo:
