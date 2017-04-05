# Descrição geral
## [O que é o Azure AD Connect?](active-directory-aadconnect.md)
## [O que é o Azure AD Connect Sync?](active-directory-aadconnectsync-whatis.md)
### [Utilizadores e contactos](active-directory-aadconnectsync-understanding-users-and-contacts.md)
### [Arquitetura](active-directory-aadconnectsync-understanding-architecture.md)
### [Aprovisionamento Declarativo](active-directory-aadconnectsync-understanding-declarative-provisioning.md)
#### [Expressões de Aprovisionamento Declarativas](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)
### [Configuração predefinida](active-directory-aadconnectsync-understanding-default-configuration.md)
## [O que é o Azure AD Connect e a Federação?](active-directory-aadconnectfed-whatis.md)


# Introdução
## [Pré-requisitos](active-directory-aadconnect-prerequisites.md)
## [Instalar o Azure AD Connect](active-directory-aadconnect-select-installation.md)
### [Definições rápidas](active-directory-aadconnect-get-started-express.md)
### [Definições personalizadas](active-directory-aadconnect-get-started-custom.md)
### [Atualizar do DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
### [Atualizar de uma versão anterior](active-directory-aadconnect-upgrade-previous-version.md)


# Procedimento
## Planear e conceber
### [Conceitos de design](active-directory-aadconnect-design-concepts.md)
### [Topologias do Azure AD Connect](active-directory-aadconnect-topologies.md)
### [Início de Sessão Único](active-directory-aadconnect-sso.md)
### [Serviço de Federação do Active Directory no Azure](active-directory-aadconnect-azure-adfs.md)
### [Considerações especiais sobre instâncias](active-directory-aadconnect-instances.md)
### [Se já tiver o Azure AD](active-directory-aadconnect-existing-tenant.md)
## [Gerir o Azure AD Connect](active-directory-aadconnect-whats-next.md)
### [Renovar certificados para o Office 365 e o Azure AD](active-directory-aadconnect-o365-certs.md)
### [Ativar a repetição de escrita do dispositivo](active-directory-aadconnect-feature-device-writeback.md)
### [Opções de início de sessão do utilizador](active-directory-aadconnect-user-signin.md)
### [Suporte para vários domínios para federação](active-directory-aadconnect-multiple-domains.md)
### [Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md)



## Gerir o Azure AD Connect Sync
### [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
### [Sincronização de palavras-passe](active-directory-aadconnectsync-implement-password-synchronization.md)
### [Autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md)
### [Conta de serviço do AD FS](active-directory-aadconnectsync-howto-azureadaccount.md)
### [Assistente de instalação](active-directory-aadconnectsync-installation-wizard.md)
### [Alterar a configuração predefinida](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)
### [Configurar a Filtragem](active-directory-aadconnectsync-configure-filtering.md)
### [Scheduler](active-directory-aadconnectsync-feature-scheduler.md)
### [Extensões de diretórios](active-directory-aadconnectsync-feature-directory-extensions.md)

### [Alterar a palavra-passe da conta do serviço Azure AD Sync](active-directory-aadconnectsync-change-serviceacct-pass.md)
### [Ativar a reciclagem do AD](active-directory-aadconnectsync-recycle-bin.md)

### [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md)
#### [Operações](active-directory-aadconnectsync-service-manager-ui-operations.md)
#### [Conectores](active-directory-aadconnectsync-service-manager-ui-connectors.md)
#### [Estruturador de metaversos](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md)
#### [Pesquisa de metaversos](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)


## Gerir os Serviços de Federação
### [Gerir e personalizar](active-directory-aadconnect-federation-management.md)


## Resolução de problemas
### [Conetividade](active-directory-aadconnect-troubleshoot-connectivity.md)
### [Erros durante a sincronização](active-directory-aadconnect-troubleshoot-sync-errors.md)
### [O objeto não está sincronizado](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md)
### [Sincronização de palavras-passe](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)
### [Como recuperar a partir do limite de 10 GB de LocalDB](active-directory-aadconnect-recover-from-localdb-10gb-limit.md)

# Referência
## [Sincronização de identidades e resiliência de atributos duplicados](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md)
## [Portas e Protocolos Necessários para a Identidade Híbrida](active-directory-aadconnect-ports.md)
## [Funcionalidades em pré-visualização](active-directory-aadconnect-feature-preview.md)
## [Histórico de Versões](active-directory-aadconnect-version-history.md)
## [Contas e permissões](active-directory-aadconnect-accounts-permissions.md)

## Azure AD Connect Sync
### [Atributos sincronizados com o Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md)
### [Histórico de Versões do Conector](active-directory-aadconnectsync-connector-version-history.md)
### [Referência das Funções](active-directory-aadconnectsync-functions-reference.md)
### [Tarefas operacionais e consideração](active-directory-aadconnectsync-operations.md)
### [Lista de compatibilidades de federação do Azure AD](active-directory-aadconnect-federation-compatibility.md)
### [Conceitos Técnicos](active-directory-aadconnectsync-technical-concepts.md)
### [Funcionalidades do serviço](active-directory-aadconnectsyncservice-features.md)




# Relacionado
## [Monitorizar a infraestrutura de identidade no local e os serviços de sincronização na cloud](../connect-health/active-directory-aadconnect-health.md)
## [Hybrid Identity Design Guide (Guia de Design da Identidade Híbrida)](https://azure.microsoft.com/documentation/articles/active-directory-hybrid-identity-design-considerations-overview/)


# Recursos
##[FAQ do Azure AD Connect](active-directory-aadconnect-faq.md)
##[Descontinuação de Deprecation](active-directory-aadconnect-dirsync-deprecated.md)
