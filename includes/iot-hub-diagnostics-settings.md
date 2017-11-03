### <a name="enable-logging-with-diagnostics-settings"></a>Ativar o registo com as definições de diagnóstico

1. Iniciar sessão para o [portal do Azure] [ lnk-portal] e navegue até ao seu IoT Hub.
1. Selecione **definições de diagnóstico**.
1. Selecione **ative os diagnósticos**.

   ![Ative os diagnósticos][1]

1. Dê um nome das definições de diagnóstico.
1. Selecione onde pretende enviar os registos. Pode selecionar qualquer combinação de três opções:
   * Para uma conta de armazenamento de arquivo
   * Fluxo para um hub de eventos
   * Enviar ao Log Analytics
1. Escolha as operações que pretende monitorizar e ativar os registos para essas operações. As operações que definições de diagnóstico podem reportar são:
   * Ligações
   * Telemetria do dispositivo
   * Mensagens da nuvem para dispositivo
   * Operações de identidade de dispositivo
   * Carregamentos de ficheiros
   * Encaminhamento de mensagens
   * Operações de nuvem para o dispositivo duplo
   * Operações de dispositivo para nuvem duplo
   * Operações de duplo
   * Operações de tarefa
   * Métodos diretos  
1. Guarde as novas definições. 

Se pretender ativar as definições de diagnóstico com o PowerShell, utilize o seguinte código:

```
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <subscription that includes your IoT Hub>
Set-AzureRmDiagnosticSetting -ResourceId <your resource Id> -ServiceBusRuleId <your service bus rule Id> -Enabled $true
```

Novas definições entram em vigor no cerca de 10 minutos. Depois disso, os registos são apresentados no destino arquivo configurado no **as definições de diagnóstico** painel. Para obter mais informações sobre a configuração de diagnósticos, consulte [recolher e consumir dados de registo dos seus recursos do azure][lnk-diagnostics-settings].

<!-- Images -->
[1]: ./media/iot-hub-diagnostics-settings/turnondiagnostics.png

<!-- Links -->
[lnk-portal]: https://portal.azure.com
[lnk-diagnostics-settings]: ../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md
