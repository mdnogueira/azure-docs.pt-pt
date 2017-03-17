# Descrição geral
## [O que é o Azure Active Directory?](active-directory-whatis.md)
## [Escolher edição](active-directory-editions.md)
## [Sobre a gestão de identidade do Azure](fundamentals-identity.md)
## [Pré-visualizar a experiência do portal do Azure AD](active-directory-preview-explainer.md)


# Introdução
## [Obter um inquilino do Azure AD](active-directory-howto-tenant.md)
## [Inscreva-se no Azure AD Premium](active-directory-get-started-premium.md)
## [Associar as subscrições Azure](active-directory-how-subscriptions-associated-directory.md)
## Gerir licenciamento do Azure AD
### [Portal do Azure](active-directory-licensing-get-started-azure-portal.md)
### [Portal clássico](active-directory-licensing-what-is.md)
## [Obter o Azure para a sua organização](sign-up-organization.md)
## [FAQs](active-directory-faq.md)
## [Tutoriais de aplicação SaaS](active-directory-saas-tutorial-list.md)

# Procedimento
## Planear e conceber
### [Implementar uma solução de identidade híbrida](active-directory-hybrid-identity-design-considerations-overview.md)
#### Determinar os requisitos
##### [Identidade](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Sincronização de diretórios](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Multi-factor auth](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Estratégia de ciclo de vida de identidade](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Planear a segurança de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Gestão de conteúdos](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Resposta a incidentes](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planear o ciclo de vida de identidade
##### [Tarefas](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Estratégia de adoção](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Passos seguintes?](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Comparação de ferramentas](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Gerir utilizadores
### Adicionar utilizadores
#### [Portal do Azure](active-directory-users-create-azure-portal.md)
#### [Portal clássico](active-directory-create-users.md)

### [Adicionar utilizadores de outros diretórios (portal clássico)](active-directory-create-users-external.md)
### [Eliminar utilizadores](active-directory-users-delete-user-azure-portal.md)
### [Gerir perfis de utilizador](active-directory-users-profile-azure-portal.md)
### [Repor uma palavra-passe](active-directory-users-reset-password-azure-portal.md)
### [Gerir as informações de trabalho do utilizador](active-directory-users-work-info-azure-portal.md)
### [Contas de partilha](active-directory-sharing-accounts.md)

## [Gerir grupos e membros](active-directory-manage-groups.md)
### Gerir grupos
#### [Portal do Azure](active-directory-groups-create-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-manage-groups.md)
#### [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Gerir membros do grupo](active-directory-groups-members-azure-portal.md)
### [Gerir proprietários do grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Gerir associação ao grupo](active-directory-groups-membership-azure-portal.md)
### [Ver todos os grupos](active-directory-groups-view-azure-portal.md)
### [Ativar grupos dedicados](active-directory-accessmanagement-dedicated-groups.md)
### [Adicionar acesso do grupo às aplicações SaaS](active-directory-accessmanagement-group-saasapps.md)
### Gerir definições do grupo
#### [Portal do Azure](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Criar regras avançadas
#### [Portal do Azure](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Licenciamento baseado no grupo](active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licença para um grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar utilizadores licenciados individuais para o licenciamento baseado no grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Cenários adicionais para licenciamento baseado no grupo](active-directory-licensing-group-advanced.md)
### [Configurar grupos de self-service](active-directory-accessmanagement-self-service-group-management.md)
### [Resolução de problemas](active-directory-accessmanagement-troubleshooting.md)

## [Gerir relatórios](active-directory-reporting-azure-portal.md)
### [Atividade de inícios de sessão](active-directory-reporting-activity-sign-ins.md)
### [Atividade de auditoria](active-directory-reporting-activity-audit-logs.md)
### [Utilizadores em risco](active-directory-reporting-security-user-at-risk.md)
### [Inícios de sessão de risco](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de risco](active-directory-reporting-risk-events.md)
### [Redes com nome](active-directory-known-networks-azure-portal.md)
### [Migração de relatórios](active-directory-reporting-migration.md)
### [Retenção](active-directory-reporting-retention.md)
### [FAQ](active-directory-reporting-faq.md)
### Resolução de problemas
#### [Dados de auditoria em falta](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dados em falta em transferências](active-directory-reporting-troubleshoot-missing-data-download.md)
###    Acesso Programático
#### [Referência de auditoria](active-directory-reporting-api-audit-reference.md)
#### [Amostras de auditoria](active-directory-reporting-api-audit-samples.md)
#### [Pré-requisitos](active-directory-reporting-api-prerequisites.md)
#### [Referência de início de sessão](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Amostras de início de sessão](active-directory-reporting-api-sign-in-activity-samples.md)
### [Portal clássico](active-directory-view-access-usage-reports.md)
#### [Relatórios do Azure AD](active-directory-reporting-getting-started.md)
#### [Guia de relatórios](active-directory-reporting-guide.md)
#### [Redes conhecidas](active-directory-known-networks.md)
#### [API](active-directory-reporting-api-getting-started.md)
#### [Eventos de auditoria](active-directory-reporting-audit-events.md)
#### [Latências](active-directory-reporting-latencies.md)
#### [Notificações](active-directory-reporting-notifications.md)
#### Compreender relatórios
##### [Início de sessão irregular](active-directory-reporting-irregular-sign-in-activity.md)
##### [Várias falhas](active-directory-reporting-sign-ins-after-multiple-failures.md)
##### [Endereços IP suspeitos](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
##### [Várias geografias](active-directory-reporting-sign-ins-from-multiple-geographies.md)
##### [Dispositivos possivelmente infetados](active-directory-reporting-sign-ins-from-possibly-infected-devices.md)
##### [Origens desconhecidas](active-directory-reporting-sign-ins-from-unknown-sources.md)
##### [Inícios de sessão anómalos](active-directory-reporting-users-with-anomalous-sign-in-activity.md)

## [Gerir palavras-passe](active-directory-manage-passwords.md)
### [Atualizar a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md)
### [Configurar a reposição personalizada de palavra-passe](active-directory-passwords.md)
### [Compreender a gestão de palavras-passe](active-directory-passwords-how-it-works.md)
### [Compreender as políticas e restrições](active-directory-passwords-policy.md)
### Repor palavras-passe
#### [Portal do Azure](active-directory-users-reset-password-azure-portal.md)
#### [Portal clássico](active-directory-create-users-reset-password.md)
### [Definir políticas de expiração](active-directory-passwords-set-expiration-policy.md)
### Ativar a gestão de palavras-passe
#### [Introdução](active-directory-passwords-getting-started.md)
#### [Implementar](active-directory-passwords-best-practices.md)
#### [Personalizar](active-directory-passwords-customize.md)
#### [Ver relatórios](active-directory-passwords-get-insights.md)
#### [Saiba mais](active-directory-passwords-learn-more.md)
#### [FAQs](active-directory-passwords-faq.md)
#### [Resolução de problemas](active-directory-passwords-troubleshoot.md)

## Gerir dispositivos
### [Registar dispositivos](active-directory-device-registration-overview.md)
#### [Gerir registo automático](active-directory-device-registration.md)
#### [Configurar](active-directory-conditional-access-automatic-device-registration-setup.md)
#### [Implementar no local](active-directory-device-registration-on-premises-setup.md)
#### [FAQs](active-directory-device-registration-faq.md)
#### Resolução de problemas
##### [Resolução de problemas do Windows 10 e Windows Server 2016](active-directory-device-registration-troubleshoot-windows.md)
##### [Resolução de problemas para clientes Windows de nível inferior](active-directory-device-registration-troubleshoot-windows-legacy.md)
### [Associação ao Azure AD](active-directory-azureadjoin-overview.md)
#### [Planear](active-directory-azureadjoin-deployment-aadjoindirect.md)
#### [Configurar o registo do dispositivo](active-directory-azureadjoin-setup.md)
#### [Registar novos dispositivos](active-directory-azureadjoin-user-frx.md)
#### [Implementar](active-directory-azureadjoin-devices-group-policy.md)
#### [Compreender a integração do Windows 10](active-directory-azureadjoin-windows10-devices-overview.md)
#### [Utilizar os dispositivos do Windows 10](active-directory-azureadjoin-windows10-devices.md)
#### [Associar o seu dispositivo](active-directory-azureadjoin-personal-device.md)
#### [Associar um dispositivo Windows 10](active-directory-azureadjoin-user-upgrade.md)

## Gerir aplicações
### [Descrição geral](active-directory-enable-sso-scenario.md)
### [Introdução](active-directory-integrating-applications-getting-started.md)

### [Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md)
#### [Atualizar definições de registo](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
#### [Compreender a segurança e privacidade](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Conceder acesso remoto às suas aplicações](active-directory-application-proxy-get-started.md)
#### [Ativar Proxy de aplicações](active-directory-application-proxy-enable.md)
#### [Compreender os conectores](application-proxy-understand-connectors.md)

#### Publicar aplicações
##### [Portal do Azure](application-proxy-publish-azure-portal.md)
##### [Portal clássico](active-directory-application-proxy-publish.md)
#### [Segurança](application-proxy-security-considerations.md)
#### [Redes](application-proxy-network-topology-considerations.md)
#### [Ambiente de Trabalho Remoto](application-proxy-publish-remote-desktop.md)
#### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)

#### Publicar em redes separadas
##### [Portal do Azure](active-directory-application-proxy-connectors-azure-portal.md)
##### [Portal clássico](active-directory-application-proxy-connectors.md)
#### [Servidores proxy](application-proxy-working-with-proxy-servers.md)
#### [Domínios personalizados](active-directory-application-proxy-custom-domains.md)
#### [Aplicações do Access](active-directory-appssoaccess-whatis.md)
##### [Portal do Azure](application-proxy-sso-azure-portal.md)
#### [Início de sessão único](active-directory-application-proxy-sso-using-kcd.md)
#### [Aplicações com suporte para afirmações](active-directory-application-proxy-claims-aware-apps.md)
#### [Aplicações clientes nativas](active-directory-application-proxy-native-client.md)
#### [Página inicial personalizada](application-proxy-office365-app-launcher.md)
#### [Acesso condicional](active-directory-application-proxy-conditional-access.md)
#### [Instalação silenciosa](active-directory-application-proxy-silent-installation.md)
#### [Microsoft Forefront](application-proxy-transition-from-uag-tmg.md)
#### [Resolução de problemas](active-directory-application-proxy-troubleshoot.md)

### Gerir aplicações empresariais
#### [Atribuir utilizadores](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personalizar a imagem corporativa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Desativar inícios de sessão do utilizador](active-directory-coreapps-disable-app-azure-portal.md)
#### [Remover utilizadores](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Ver todas as minhas aplicações](active-directory-coreapps-view-azure-portal.md)
#### [Gerir o aprovisionamento de contas de utilizador](active-directory-enterprise-apps-manage-provisioning.md)

### Programar
#### [Atribuir utilizadores](active-directory-applications-guiding-developers-assigning-users.md)
#### [Atribuir grupos](active-directory-applications-guiding-developers-assigning-groups.md)
#### [Exigir a atribuição](active-directory-applications-guiding-developers-requiring-user-assignment.md)
#### [Programar aplicações de LoB](active-directory-applications-guiding-developers-for-lob-applications.md)

### [Gerir o acesso a aplicações](active-directory-managing-access-to-apps.md)
#### [Acesso ao Self-service](active-directory-self-service-application-access.md)
#### [Certificados para SSO](active-directory-sso-certs.md)
#### [Restrições de inquilino](active-directory-tenant-restrictions.md)

### [Utilizar utilizadores de aprovisionamento SCIM](active-directory-scim-provisioning.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Gerir o seu diretório
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomes de domínio personalizados
#### [Descrição geral](active-directory-add-domain-concepts.md)
#### Adicionar o seu nome de domínio
##### [Portal do Azure](active-directory-domains-add-azure-portal.md)
##### [Portal clássico](active-directory-add-domain.md)
##### [Com os AD FS](active-directory-add-domain-federated.md)
#### [Atribuir utilizadores](active-directory-add-domain-add-users.md)
#### Gerir nomes de domínio
##### [Portal do Azure](active-directory-domains-manage-azure-portal.md)
##### [Portal clássico](active-directory-add-manage-domain-names.md)
### Personalizar a página de início de sessão
#### [Portal do Azure](active-directory-branding-custom-signon-azure-portal.md)
#### [Específico do idioma](active-directory-branding-localize-azure-portal.md)
#### [Portal clássico](active-directory-add-company-branding.md)
### [Administrar o seu diretório](active-directory-administer.md)
### [Vários diretórios](active-directory-licensing-directory-independence.md)
### [Diretórios O365](active-directory-manage-o365-subscription.md)
### [Inscrição self-service](active-directory-self-service-signup.md)
### [Roaming de Estado Empresarial](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Ativar](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Definições da política de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Definições do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [FAQs](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

### [Integrar parceiros com o Azure AD B2B](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Administradores que adicionam utilizadores B2B](active-directory-b2b-admin-add-users.md)
#### [Técnicos de informação que adicionam utilizadores B2B](active-directory-b2b-iw-add-users.md)
#### [E-mail de convite](active-directory-b2b-invitation-email.md)
#### [Resgate de convite](active-directory-b2b-redemption-experience.md)
#### [Propriedades de utilizador B2B](active-directory-b2b-user-properties.md)
#### [Adicionar um utilizador convidado a uma função](active-directory-b2b-add-guest-to-role.md)
#### [Auditoria e relatórios](active-directory-b2b-auditing-and-reporting.md)
#### [API e personalização](active-directory-b2b-api.md)
#### [Delegar convites](active-directory-b2b-delegate-invitations.md)
#### [Grupos dinâmicos e B2B](active-directory-b2b-dynamic-groups.md)
#### [Autenticação multifator para B2B](active-directory-b2b-mfa-instructions.md)
#### [Tokens de utilizador B2B](active-directory-b2b-user-token.md)
#### [Mapeamento das afirmações de utilizador B2B](active-directory-b2b-claims-mapping.md)
#### [Partilha externa do Office 365](active-directory-b2b-o365-external-user.md)
#### [Configurar aplicações SaaS para B2B](active-directory-b2b-configure-saas-apps.md)
#### [Exemplos de código e do PowerShell](active-directory-b2b-code-samples.md)
#### [Limitações atuais](active-directory-b2b-current-limitations.md)
#### [Licenciamento](active-directory-b2b-licensing.md)
#### [Resolução de problemas B2B](active-directory-b2b-troubleshooting.md)
#### [Comparar colaboração B2B com B2C](active-directory-b2b-compare-b2c.md)
#### [Obter suporte para B2B](active-directory-b2b-support.md)
#### [FAQ](active-directory-b2b-faq.md)
### [Integrar identidades no local com o Azure AD Connect](./connect/active-directory-aadconnect.md)


## Delegar o acesso a recursos
### [Funções de administrador](active-directory-assign-admin-roles.md)
#### [Atribuir funções de administrador](active-directory-users-assign-role-azure-portal.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Acesso a recursos no Azure](active-directory-understanding-resource-access.md)
### [Controlo de Acesso Baseado em Funções](role-based-access-control-what-is.md)
#### Gerir atribuições de acesso
##### [Por utilizador](role-based-access-control-manage-assignments.md)
##### [Por recurso](role-based-access-control-configure.md)
#### [Funções incorporadas](role-based-access-built-in-roles.md)
#### [Funções personalizadas](role-based-access-control-custom-roles.md)
#### [Relatórios](role-based-access-control-access-change-history-report.md)
#### Mais formas de gerir funções
##### [CLI do Azure](role-based-access-control-manage-access-azure-cli.md)
##### [PowerShell](role-based-access-control-manage-access-powershell.md)
##### [REST](role-based-access-control-manage-access-rest.md)
#### [Resolução de problemas](role-based-access-control-troubleshooting.md)
### [Configurar durações de token](active-directory-configurable-token-lifetimes.md)

## Proteger as suas identidades
### [Acesso condicional](active-directory-conditional-access.md)
#### [Introdução](active-directory-conditional-access-azuread-connected-apps.md)
#### [Aplicações suportadas](active-directory-conditional-access-supported-apps.md)
#### [Compreender políticas dos dispositivos](active-directory-conditional-access-device-policies.md)
#### [Configurar o acesso às aplicações ligadas](active-directory-conditional-access-policy-connected-applications.md)
#### [FAQs](active-directory-conditional-faqs.md)
#### [Resolução de problemas](active-directory-conditional-access-device-remediation.md)
#### [Referência](active-directory-conditional-access-technical-reference.md)
### Windows Hello
#### [Autenticar sem palavras-passe](active-directory-azureadjoin-passport.md)
#### [Ativar o Windows Hello para Empresas](active-directory-azureadjoin-passport-deployment.md)
### Autenticação baseada em certificados
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Ativar](active-directory-identityprotection-enable.md)
#### [Detetar vulnerabilidades](active-directory-identityprotection-vulnerabilities.md)
#### [Eventos de risco](active-directory-identity-protection-risk-events.md)
#### [Notificações](active-directory-identityprotection-notifications.md)
#### [Experiência de início de sessão](active-directory-identityprotection-flows.md)
#### [Simular eventos de risco](active-directory-identityprotection-playbook.md)
#### [Desbloquear utilizadores](active-directory-identityprotection-unblock-howto.md)
#### [Glossário](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Implementar o AD DS em VMs do Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory em VMs do Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controlador de domínio de réplica numa rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Floresta nova numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md)



## [Implementar os AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
### [Elevada disponibilidade](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Algoritmo de alteração de hash de assinatura](active-directory-federation-sha256-guidance.md)

## [Resolução de problemas](active-directory-troubleshooting.md)


# Referência
## [Cmdlets do PowerShell](/powershell/ )
## [Referência de Java API](/java/api)
## [API .NET](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Restrições e limites do serviço](active-directory-service-limits-restrictions.md)

# Relacionado
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD para programadores](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# Recursos
## [Preços](https://azure.microsoft.com/pricing/details/active-directory/)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory)
## [Fórum de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
