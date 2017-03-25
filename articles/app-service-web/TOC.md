# Descrição geral    
## [Acerca de Aplicações Web](app-service-web-overview.md)
## [Comparar opções de alojamento](choose-web-site-cloud-service-vm.md)

# Início Rápido    
## [Criar site HTML estático](app-service-web-get-started-html.md)
## [Criar aplicação ASP.NET](app-service-web-get-started-dotnet.md)        
## [Criar aplicação PHP](app-service-web-get-started-php.md) 
## [Criar aplicação Node.js](app-service-web-get-started-nodejs.md) 
## [Criar aplicação Java](app-service-web-get-started-java.md)        
## [Criar aplicação Python](app-service-web-get-started-python.md)    

# Amostras
## [CLI do Azure](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# Tutoriais
## [Adicionar funcionalidade a uma aplicação Web](app-service-web-get-started-2.md)
## [Aplicação ASP.NET com a Base de Dados SQL](web-sites-dotnet-get-started.md)
## [Aplicação Laravel com MySQL](app-service-web-php-get-started.md)
## [Aplicação Sails.js com NOSQL DB](app-service-web-nodejs-sails.md)
## [Aplicação Java com Eclipse](app-service-web-eclipse-create-hello-world-web-app.md)
## [Aplicação Java com IntelliJ](app-service-web-intellij-create-hello-world-web-app.md)
## [Aplicação Django com MySQL](web-sites-python-ptvs-django-mysql.md)

# Conceitos
## [Como funciona o Serviço de Aplicações](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [Planos do Serviço de Aplicações](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [Ambientes do App Service](app-service-app-service-environment-intro.md)
## [Autenticação e autorização](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [Autenticação com o AD local](web-sites-authentication-authorization.md)


# Guias de Procedimentos
## Desenvolver a sua aplicação    
### ASP.NET

#### [Desenvolver uma aplicação ASP.NET Core com o VS Code](web-sites-create-web-app-using-vscode.md)
### PHP
#### [Configurar o seu projeto PHP](web-sites-php-configure.md)
#### [Configurar um Multisite do WordPress](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [Utilizar o io.js](web-sites-nodejs-iojs.md)
#### [Depurar aplicação Node.js](web-sites-nodejs-debug.md)
### Java

#### [Utilizar o SDK do Azure para Java](java-create-azure-website-using-java-sdk.md)
#### [Carregar aplicação existente](web-sites-java-add-app.md)
#### [Depuração remota do Eclipse](app-service-web-debug-java-web-app-in-eclipse.md)
#### [Depuração remota do IntelliJ](app-service-web-debug-java-web-app-in-intellij.md)

### [Enviar e-mails com o SendGrid](sendgrid-dotnet-how-to-send-email.md)

### Configurar o runtime    
#### [PHP no Windows](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Node.js no Linux](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### Configurar aplicação
#### [Utilizar as definições da aplicação](web-sites-configure.md)
            
## [Implementar no Azure](web-sites-deploy.md)
### [Implementar através de FTP](app-service-deploy-content-sync.md)
### [Implementar através da sincronização da cloud](web-sites-deploy.md)
### [Implementar continuamente](app-service-continuous-deployment.md)
### [Implementar na transição](web-sites-staged-publishing.md)
### [Implementar a partir do Git local](app-service-deploy-local-git.md)
### [Implementar com modelo](app-service-deploy-complex-application-predictably.md)
### [Implementação Agile](app-service-agile-software-development.md)
### [Teste beta](app-service-web-test-in-production-controlled-test-flight.md)

### [Definir credenciais de implementação](app-service-deployment-credentials.md)

### Mapear domínio personalizado
#### [Comprar domínio](custom-dns-web-site-buydomains-web-app.md)
#### [Mapear domínio de terceiros](web-sites-custom-domain-name.md)
#### [Mapear domínios com o Gestor de Tráfego](web-sites-traffic-manager-custom-domain-name.md)
#### [Mapear domínios da GoDaddy](web-sites-godaddy-custom-domain-name.md)
#### [Migrar um domínio ativo](app-service-custom-domain-name-migrate.md)

### [Migrar do IIS](web-sites-migration-from-iis-server.md)
### [Testar na produção](app-service-web-test-in-production-get-start.md)

## Ligar a base de dados/recursos        

### [Ligar aos dados no local](web-sites-hybrid-connection-get-started.md) 
### [Ligar ao Azure Vnet](web-sites-integrate-with-vnet.md)
### [Ligar ao Azure VNet com o PowerShell](app-service-vnet-integration-powershell.md)
### [Ligar ao MongoDB na VM do Azure](web-sites-dotnet-store-data-mongodb-vm.md)
            
##Proteger aplicação
### Autenticar utilizadores        
#### [Autenticar com o Azure AD](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o Facebook](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o Google](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com a conta Microsoft](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o Twitter](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [Autenticar com o AD no local](web-sites-authentication-authorization.md)
#### [Aplicação com uma base de dados multi-inquilino](web-sites-dotnet-entity-framework-row-level-security.md)
### Atribuir SSL personalizado
#### [Comprar certificado SSL](web-sites-purchase-ssl-web-site.md)
#### [Configurar certificado SSL de terceiros](web-sites-configure-ssl-certificate.md)
### [Impor HTTPS](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [Configurar a autenticação mútua de TLS](app-service-web-configure-tls-mutual-auth.md)
            
##Dimensionar aplicação        
### [Aumentar verticalmente](web-sites-scale.md)
### [Aumentar horizontalmente](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Balancear carga com o Gestor de Tráfego](web-sites-traffic-manager.md)
### [Alto dimensionamento com Ambientes de Serviço de Aplicações](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Utilizar o Azure CDN para alcance global](cdn-websites-with-cdn.md)
### [Ligar à Cache de Redis através do Memcache](web-sites-connect-to-redis-using-memcache-protocol.md)
### [Criar uma Cache de Redis](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [Gerir estado de sessão com a cache de Redis do Azure](web-sites-dotnet-session-state-caching.md)

## Monitorizar 
### [Monitorizar aplicações](web-sites-monitor.md)
### [Ativar registos](web-sites-enable-diagnostic-log.md)
### [Transmitir registos](web-sites-streaming-logs-and-console.md)

## Realizar cópia de segurança do conteúdo        
### [Criar cópias de segurança da aplicação](web-sites-backup.md)
### [Restaurar a sua aplicação a partir de uma cópia de segurança](web-sites-restore.md)
### [Criar cópia de segurança com API REST](websites-csm-backup.md)

## Gerir recursos de aplicação
### [Clonar aplicação com o PowerShell](app-service-web-app-cloning.md)
### [Clonar aplicação com o portal](app-service-web-app-cloning-portal.md)
### [Mover recursos](app-service-move-resources.md)
### [Utilizar o Azure Resource Manager com o PowerShell](app-service-web-app-azure-resource-manager-powershell.md)
### [Gerir aplicações com a Automatização do Azure](automation-manage-web-app.md)



# Referência    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [API REST](/rest/api/appservice/) 
        
# Recursos    
## Resolução de problemas        
### [Resolver problemas com o Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md)
### [Resolver problemas da aplicação Node.js](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [Resolver erros de HTTP 502 e 503](app-service-web-troubleshoot-http-502-http-503.md)
### [Resolver problemas de desempenho](app-service-web-troubleshoot-performance-degradation.md)
## [Preços](https://azure.microsoft.com/pricing/details/app-service/)     
## [Informações Sobre a Quota](../azure-subscription-service-limits.md#app-service-limits)    
## [Atualizações de Serviços e Notas de Versão](https://azure.microsoft.com/updates/?product=app-service)    
## [Melhores práticas](app-service-best-practices.md)
## [Amostras](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [Vídeos](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## Cookbooks    
### [Arquiteturas de Referência](../guidance/guidance-ra-app-service.md)    
### [Scripts de Implementação](https://azure.microsoft.com/documentation/scripts/)    
