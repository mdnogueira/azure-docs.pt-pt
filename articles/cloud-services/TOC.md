# Descrição geral
## [O que é um Serviço Cloud?](cloud-services-choose-me.md)
## [Ficheiros de configuração do serviço cloud e empacotamento](cloud-services-model-and-package.md)

# Introdução
## [Exemplo de Serviço Cloud .NET](cloud-services-dotnet-get-started.md)
## [Exemplo de Serviço Cloud Python para o Visual Studio](cloud-services-python-ptvs.md)
## [Configurar um cluster HPC híbrido com o Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Procedimento
## Planear
### [Tamanhos de máquinas virtuais](cloud-services-sizes-specs.md)
### [Atualizações](cloud-services-update-azure-service.md)

## Programar
### [Criar funções de Web PHP e de trabalho](../cloud-services-php-create-web-role.md)
### [Compilar e implementar uma aplicação Node.js](cloud-services-nodejs-develop-deploy-app.md)
### [Criar uma aplicação Web Node.js com o Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Armazenamento e o Visual Studio
#### [Armazenamento de blobs e serviços ligados](../storage/vs-storage-cloud-services-getting-started-blobs.md)
#### [Armazenamento de filas e serviços ligados](../storage/vs-storage-cloud-services-getting-started-queues.md)
#### [Armazenamento de tabelas e serviços ligados](../storage/vs-storage-cloud-services-getting-started-tables.md)
### Configurar pacotes para compilação e implementação contínua
#### [Visual Studio Team Services e Git](cloud-services-continuous-delivery-use-vso-git.md)
#### [Visual Studio Team Services](cloud-services-continuous-delivery-use-vso.md)
#### [TFS e Compilação de Equipa](cloud-services-dotnet-continuous-delivery.md)
### [Configurar regras de tráfego para uma função](cloud-services-enable-communication-role-instances.md)
### [Processar eventos de ciclo de vida do Serviço Cloud](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Utilizar o Twilio para fazer uma chamada telefónica (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)
### [New Relic](../store-new-relic-cloud-services-dotnet-application-performance-management.md)

### Configurar tarefas de arranque
#### [Criar tarefas de arranque](cloud-services-startup-tasks.md)
#### [Tarefas de arranque comuns](cloud-services-startup-tasks-common.md)
#### [Utilizar uma tarefa para Instalar o .NET numa função de Serviço Cloud](cloud-services-dotnet-install-dotnet.md)

### Configurar o Ambiente de Trabalho Remoto
#### [Visual Studio](cloud-services-role-enable-remote-desktop.md)
#### [Node.js](cloud-services-nodejs-enable-remote-desktop.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)

## Implementação
### Criar e implementar um serviço cloud no portal
#### [Portal](cloud-services-how-to-create-deploy-portal.md)
#### [Portal clássico](cloud-services-how-to-create-deploy.md)
### [Criar um contentor de serviço cloud vazio no PowerShell](cloud-services-powershell-create-cloud-container.md)
### Configurar um nome de domínio personalizado
#### [Portal](cloud-services-custom-domain-name-portal.md)
#### [Portal clássico](cloud-services-custom-domain-name.md)
### [Testar uma implementação do Serviço Cloud (Node.js)](cloud-services-nodejs-stage-application.md)
### [Ligar a um Controlador de Domínio personalizado](cloud-services-connect-to-custom-domain.md)

## Gerir serviço
### Tarefas comuns de gestão
#### [Portal](cloud-services-how-to-manage-portal.md)
#### [Portal clássico](cloud-services-how-to-manage.md)
### Configurar Serviço Cloud
#### [Portal](cloud-services-how-to-configure-portal.md)
#### [Portal clássico](cloud-services-how-to-configure.md)
### [Gerir um Serviço Cloud com a Automatização do Azure](automation-manage-cloud-services.md)
### Configurar o dimensionamento automático
#### [Portal](cloud-services-how-to-scale-portal.md)
#### [Portal clássico](cloud-services-how-to-scale.md)
### [Utilizar Python para gerir os Recursos do Azure](cloud-services-python-how-to-use-service-management.md)

### [Correções do SO convidado](cloud-services-guestos-msrc-releases.md)
### Extinção do SO convidado
#### [Política de extinção](cloud-services-guestos-retirement-policy.md)
#### [Aviso de extinção da Família 1](cloud-services-guestos-family1-retirement.md)
### [Notícias de lançamento do SO Convidado](cloud-services-guestos-update-matrix.md)
### [Notas de XPath para Configuração de Função dos Serviços Cloud](cloud-services-role-config-xpath.md)

## Gerir certificados
### [Serviços Cloud em e gestão de certificados](cloud-services-certs-create.md)
### Configurar o SSL 
#### [Portal](cloud-services-configure-ssl-certificate-portal.md)
#### [Portal clássico](cloud-services-configure-ssl-certificate.md)

## Monitorizar
### [Monitorizar serviço cloud](cloud-services-how-to-monitor.md)
### [Testar o desempenho](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Testar com o Gerador de Perfis do Visual Studio](cloud-services-performance-testing-visual-studio-profiler.md)
### Ativar diagnóstico
#### [PowerShell](cloud-services-diagnostics-powershell.md)
#### [.NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Utilizar contadores de desempenho no Diagnóstico do Azure](cloud-services-dotnet-diagnostics-performance-counters.md)
### [Armazenar e ver dados de diagnósticos no Armazenamento do Azure](cloud-services-dotnet-diagnostics-storage.md)
### [Rastrear Serviço Cloud com os Diagnósticos](cloud-services-dotnet-diagnostics-trace-flow.md)
### [Enviar dados de diagnóstico às Informações da Aplicação](cloud-services-dotnet-diagnostics-applicationinsights.md)

## Resolução de problemas
### Depurar 
#### [Ativar a depuração remota com entrega contínua](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)
#### [Opções para um Serviço Cloud](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Serviço Cloud local com o Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Serviço Cloud publicado com o Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Falha de alocação do Serviço Cloud](cloud-services-allocation-failures.md)
### [Causas comuns da reciclagem de funções do Serviço Cloud](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [O tamanho da pasta TEMP predefinida demasiado pequeno para a função](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Problemas de implementação comuns](cloud-services-troubleshoot-deployment-problems.md)
### [Função falhou ao iniciar](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Documentação de orientação da recuperação](cloud-services-disaster-recovery-guidance.md)
### [FAQ dos Serviços Cloud](cloud-services-faq.md)

# Referência
## [Esquema XML .csdef](https://msdn.microsoft.com/library/azure/ee758711)
## [Esquema XML .cscfg](https://msdn.microsoft.com/library/azure/ee758710)
## [REST](https://msdn.microsoft.com/library/azure/ee460812)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Percurso de aprendizagem](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
