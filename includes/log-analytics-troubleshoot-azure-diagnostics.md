### <a name="troubleshoot-azure-diagnostics"></a>Resolver Problemas do Diagnóstico do Azure

Se receber a seguinte mensagem de erro, o fornecedor de recursos Microsoft.insights não está registado:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Para registar o fornecedor de recursos, execute os seguintes passos no portal do Azure:

1.  No painel de navegação à esquerda, clique em *Subscrições*
2.  Selecione a subscrição identificada na mensagem de erro
3.  Clique em *Fornecedores de Recursos*
4.  Encontre o fornecedor *Microsoft.insights*
5.  Clique na ligação *Registar*

![Registe o fornecedor de recursos do microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

Assim que o fornecedor de recursos do *Microsoft.insights* estiver registado, repita o diagnóstico de configuração.


<!--HONumber=Feb17_HO2-->


