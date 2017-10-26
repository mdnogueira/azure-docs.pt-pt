# Descrição geral
## [O que é o Azure Active Directory?](active-directory-whatis.md)
## [Sobre a gestão de identidade do Azure](identity-fundamentals.md)
## [Compreender as soluções de identidade do Azure](understand-azure-identity-solutions.md)
## [Escolher uma solução de identidade híbrida](choose-hybrid-identity-solution.md)
## [Associar as subscrições Azure](active-directory-how-subscriptions-associated-directory.md)
## [FAQs](active-directory-faq.md)
## [Novidades](whats-new.md)


# Introdução
## [Introdução ao Azure AD](get-started-azure-ad.md)
## [Inscreva-se no Azure AD Premium](active-directory-get-started-premium.md)
## [Adicionar um nome de domínio personalizado](add-custom-domain.md)
## [Configurar o branding da empresa](customize-branding.md)
## [Adicionar utilizadores ao Azure AD](add-users-azure-active-directory.md)
## [Atribuir licenças a utilizadores](license-users-groups.md)
## [Configurar a reposição personalizada de palavra-passe](active-directory-passwords-getting-started.md)


# Procedimento
## Planear e conceber
### [Compreender a arquitetura do Azure AD](active-directory-architecture.md)
### [Implementar uma solução de identidade híbrida](active-directory-hybrid-identity-design-considerations-overview.md)
### [Mapeamento de afirmações no Azure Active Directory](active-directory-claims-mapping.md)
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
### [Utilizar grupos para atribuir licenças ](active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licenças num grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar utilizadores licenciados individuais para o licenciamento baseado no grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Cenários adicionais para licenciamento baseado no grupo](active-directory-licensing-group-advanced.md)
#### [Exemplos do Azure PowerShell para licenciamento baseado no grupo](active-directory-licensing-ps-examples.md)
#### [Referência para produtos e planos de serviço em Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Gerir perfis de utilizador](active-directory-users-profile-azure-portal.md)
### [Repor uma palavra-passe](active-directory-users-reset-password-azure-portal.md)
### [Gerir as informações de trabalho do utilizador](active-directory-users-work-info-azure-portal.md)
### [Contas de partilha](active-directory-sharing-accounts.md)



## [Gerir grupos e membros](active-directory-manage-groups.md)
### Gerir grupos
#### [Portal do Azure](active-directory-groups-create-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-manage-groups.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Gerir membros do grupo](active-directory-groups-members-azure-portal.md)
### [Gerir proprietários do grupo](active-directory-accessmanagement-managing-group-owners.md)
### [Gerir associação ao grupo](active-directory-groups-membership-azure-portal.md)
### [Utilizar grupos para atribuir licenças ](active-directory-licensing-whatis-azure-portal.md)
#### [Atribuir licenças a um grupo](active-directory-licensing-group-assignment-azure-portal.md)
#### [Identificar e resolver problemas de licenças num grupo](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrar utilizadores licenciados individuais para o licenciamento baseado no grupo](active-directory-licensing-group-migration-azure-portal.md)
#### [Cenários adicionais para licenciamento baseado no grupo](active-directory-licensing-group-advanced.md)
#### [Exemplos do Azure PowerShell para licenciamento baseado no grupo](active-directory-licensing-ps-examples.md)
#### [Referência para produtos e planos de serviço em Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Configurar a expiração de grupos do Office 365](active-directory-groups-lifecycle-azure-portal.md)
### [Ver todos os grupos](active-directory-groups-view-azure-portal.md)
### [Ativar grupos dedicados](active-directory-accessmanagement-dedicated-groups.md)
### [Adicionar acesso do grupo às aplicações SaaS](active-directory-accessmanagement-group-saasapps.md)
### [Restaurar um grupo do Office 365 eliminado](active-directory-groups-restore-azure-portal.md)
### Gerir definições do grupo
#### [Portal do Azure](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Criar regras avançadas
#### [Portal do Azure](active-directory-groups-dynamic-membership-azure-portal.md)
#### [Portal clássico](active-directory-accessmanagement-groups-with-advanced-rules.md)
### [Configurar grupos de self-service](active-directory-accessmanagement-self-service-group-management.md)
### [Resolução de problemas](active-directory-accessmanagement-troubleshooting.md)

## [Gerir relatórios](active-directory-reporting-azure-portal.md)
### [Atividade de inícios de sessão](active-directory-reporting-activity-sign-ins.md)
### [Atividade de auditoria](active-directory-reporting-activity-audit-logs.md)
### [Utilizadores em risco](active-directory-reporting-security-user-at-risk.md)
### [Inícios de sessão de risco](active-directory-reporting-security-risky-sign-ins.md)
### [Eventos de risco](active-directory-reporting-risk-events.md)
### [FAQ](active-directory-reporting-faq.md)
### Tarefas
#### [Configurar localizações com nome](active-directory-named-locations.md)
#### [Encontrar relatórios de atividades](active-directory-reporting-migration.md)
#### [Utilizar o Pacote de Conteúdos do Power BI para o Azure Active Directory](active-directory-reporting-power-bi-content-pack-how-to.md)
### Referência
#### [Retenção](active-directory-reporting-retention.md)
#### [Latências](active-directory-reporting-latencies-azure-portal.md)
#### [Notificações](active-directory-reporting-notifications.md)
#### [Códigos de erro de atividade de início de sessão](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)
### Resolução de problemas
#### [Dados de auditoria em falta](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Dados em falta em transferências](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Erros do pacote de conteúdos dos registos de Atividade do Azure Active Directory](active-directory-reporting-troubleshoot-content-pack.md)
### [Acesso Programático](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Referência de auditoria](active-directory-reporting-api-audit-reference.md)
#### [Referência de início de sessão](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Pré-requisitos](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Amostras de auditoria](active-directory-reporting-api-audit-samples.md)
#### [Amostras de início de sessão](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Com certificados](active-directory-reporting-api-with-certificates.md)

## [Gerir palavras-passe](active-directory-passwords-overview.md)
### Documentos do utilizador
#### [Repor ou alterar a palavra-passe](active-directory-passwords-update-your-own-password.md)
#### [Melhores práticas de palavra-passe](active-directory-secure-passwords.md)
#### [Registar-se na reposição personalizada de palavra-passe](active-directory-passwords-reset-register.md)
### [Licenciar a SSPR](active-directory-passwords-licensing.md)
### [Implementar a SSPR](active-directory-passwords-best-practices.md)
### Administradores de TI: repor palavras-passe
#### [Portal do Azure](active-directory-users-reset-password-azure-portal.md)
#### [Portal Clássico do Azure](active-directory-create-users-reset-password.md)
### [Compreender as políticas de SSPR](active-directory-passwords-policy.md)
### [Compreender a reposição de palavra-passe](active-directory-passwords-how-it-works.md)
### [Personalizar a SSPR](active-directory-passwords-customize.md)
### [Dados utilizados pela SSPR](active-directory-passwords-data.md)
### [Relatórios na SSPR](active-directory-passwords-reporting.md)
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### [Repetição de escrita de palavras-passe](active-directory-passwords-writeback.md)
### [Sincronização de hash de palavra-passe](./connect/active-directory-aadconnectsync-implement-password-synchronization.md#how-password-synchronization-works)
### [Resolução de problemas](active-directory-passwords-troubleshoot.md)
### [FAQ](active-directory-passwords-faq.md)


## Gerir dispositivos
### [Introdução](device-management-introduction.md)
### [Com o Portal do Azure](device-management-azure-portal.md)
### [Planear a Associação do Azure AD](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [FAQs](device-management-faq.md)
### Tarefas
#### [Configurar dispositivos Windows 10 registados no Azure AD](device-management-azuread-registered-devices-windows10-setup.md)
#### [Configurar dispositivos associados ao Azure AD](device-management-azuread-joined-devices-setup.md)
#### [Configurar dispositivos associados ao Azure AD híbridos](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Implementar no local](active-directory-device-registration-on-premises-setup.md)
#### [Associação ao Azure AD durante a experiência de primeira execução do Windows 10](device-management-azuread-joined-devices-frx.md)
### Resolução de problemas
#### [O Azure AD híbrido associou-se a dispositivos do Windows 10 e do Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [O Azure AD híbrido associou-se a dispositivos legados do Windows](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Gerir aplicações
### [Descrição geral](active-directory-enable-sso-scenario.md)
### [Introdução](active-directory-integrating-applications-getting-started.md)
### [Tutoriais de integração de aplicação SaaS](active-directory-saas-tutorial-list.md)
### [Cloud App Discovery](cloudappdiscovery-get-started.md)
#### [Criar relatórios de instantâneos](cloudappdiscovery-set-up-snapshots.md)
#### [Configurar os relatórios contínuos](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Utilizar um analisador de registos personalizados](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
#### Deteção baseada no agente
##### [O que é o Cloud App Discovery?](active-directory-cloudappdiscovery-whatis.md)
##### [Atualizar definições de registo](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [Compreender a segurança e privacidade](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)


### [Aceder a aplicações remotamente com o Proxy de Aplicações](active-directory-application-proxy-get-started.md)
#### Introdução
##### [Ativar Proxy de aplicações](active-directory-application-proxy-enable.md)
##### [Publicar aplicações](application-proxy-publish-azure-portal.md)
##### [Domínios personalizados](active-directory-application-proxy-custom-domains.md)
#### [Início de sessão único](application-proxy-sso-overview.md)
##### [SSO com KCD](active-directory-application-proxy-sso-using-kcd.md)
##### [SSO com cabeçalhos](application-proxy-ping-access.md)
##### [SSO com colocação de palavras-passe em cofres](application-proxy-sso-azure-portal.md)
#### Conceitos
##### [Conectores](application-proxy-understand-connectors.md)
##### [Segurança](application-proxy-security-considerations.md)
##### [Redes](application-proxy-network-topology-considerations.md)


##### [Atualizar a partir de TMG ou UAG](application-proxy-transition-from-uag-tmg.md)

#### Configurações avançadas
##### [Publicar em redes separadas](active-directory-application-proxy-connectors-azure-portal.md)
##### [Servidores proxy](application-proxy-working-with-proxy-servers.md)
##### [Aplicações com suporte para afirmações](active-directory-application-proxy-claims-aware-apps.md)
##### [Aplicações clientes nativas](active-directory-application-proxy-native-client.md)
##### [Instalação silenciosa](active-directory-application-proxy-silent-installation.md)
##### [Página inicial personalizada](application-proxy-office365-app-launcher.md)
##### [Converter ligações inline](application-proxy-link-translation.md)
#### Instruções de publicação
##### [Ambiente de Trabalho Remoto](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Equipas da Microsoft](application-proxy-teams.md)
#### [Resolução de problemas](active-directory-application-proxy-troubleshoot.md)
#### Utilizar o portal clássico
##### [Transferir conectores](application-proxy-enable-classic-portal.md)
##### [Publicar aplicações](active-directory-application-proxy-publish.md)
##### [Utilizar conectores](active-directory-application-proxy-connectors.md)
##### [Acesso condicional](active-directory-application-proxy-conditional-access.md)

### Gerir aplicações empresariais
#### [Atribuir utilizadores](active-directory-coreapps-assign-user-azure-portal.md)
#### [Personalizar a imagem corporativa](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Desativar inícios de sessão do utilizador](active-directory-coreapps-disable-app-azure-portal.md)
#### [Remover utilizadores](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Ver todas as minhas aplicações](active-directory-coreapps-view-azure-portal.md)
#### [Gerir o aprovisionamento de contas de utilizador](active-directory-enterprise-apps-manage-provisioning.md)
#### [Gerir o início de sessão único para aplicações empresariais](active-directory-enterprise-apps-manage-sso.md)
#### [Assinatura de certificado avançada para aplicações SAML](active-directory-enterprise-apps-advance-certificate-options.md)
####[ Ocultar a experiência do utilizador no Azure Active Directory](active-directory-coreapps-hide-third-party-app.md)

### [Gerir o acesso a aplicações](active-directory-managing-access-to-apps.md)
#### [Acesso ao Self-service](active-directory-self-service-application-access.md)
#### [Acesso SSO](active-directory-appssoaccess-whatis.md)
#### [Certificados para SSO](active-directory-sso-certs.md)
#### [Restrições de inquilino](active-directory-tenant-restrictions.md)
#### [Utilizar utilizadores de aprovisionamento SCIM](active-directory-scim-provisioning.md)

### [Resolução de problemas](active-directory-application-troubleshoot-content-map.md)
#### [Desenvolvimento de Aplicações](active-directory-application-dev-troubleshoot-content-map.md)
##### [Configuração e Registo](active-directory-application-dev-config-content-map.md)
##### [Desenvolvimento](active-directory-application-dev-development-content-map.md)
#### [Gestão de Aplicações](active-directory-application-management-troubleshoot-content-map.md)
##### [Configuração](active-directory-application-config-content-map.md)
##### [Iniciar sessão](active-directory-application-sign-in-content-map.md)
##### [Aprovisionamento](active-directory-application-provisioning-content-map.md)
##### [Gerir o Acesso](active-directory-application-access-content-map.md)
##### [Painel de Acesso](active-directory-application-access-panel-content-map.md)
##### [Proxy da Aplicação](active-directory-application-proxy-content-map.md)
##### [Acesso condicional](active-directory-application-conditional-access-content-map.md)
### [Programar aplicações](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Biblioteca de documentos](active-directory-apps-index.md)

## Gerir o seu diretório
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Nomes de domínio personalizados
#### [Descrição geral](active-directory-add-domain-concepts.md)
#### Gerir nomes de domínio
##### [Portal do Azure](active-directory-domains-manage-azure-portal.md)
##### [Portal clássico](active-directory-add-manage-domain-names.md)
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
#### [API e personalização](active-directory-b2b-api.md)
#### [Exemplos de códigos e do Azure PowerShell](active-directory-b2b-code-samples.md)
#### [Exemplo de portal de inscrição self-service](active-directory-b2b-self-service-portal.md)
#### [E-mail de convite](active-directory-b2b-invitation-email.md)
#### [Resgate de convite](active-directory-b2b-redemption-experience.md)
#### [Adicionar utilizadores B2B sem convite](active-directory-b2b-add-user-without-invite.md)
#### [Acesso condicional para B2B](active-directory-b2b-mfa-instructions.md)
#### [Políticas de partilha B2B](active-directory-b2b-delegate-invitations.md)
#### [Adicionar um utilizador B2B a uma função](active-directory-b2b-add-guest-to-role.md)
#### [Grupos dinâmicos e utilizadores B2B](active-directory-b2b-dynamic-groups.md)
#### [Auditoria e relatórios](active-directory-b2b-auditing-and-reporting.md)
#### [Partilha externa do Office 365 e B2B](active-directory-b2b-o365-external-user.md)
#### [Licenciamento](active-directory-b2b-licensing.md)
#### [Limitações atuais](active-directory-b2b-current-limitations.md)
#### [FAQ](active-directory-b2b-faq.md)
#### [Resolução de problemas B2B](active-directory-b2b-troubleshooting.md)
#### [Compreender o utilizador B2B](active-directory-b2b-user-properties.md)
#### [Token de utilizador B2B](active-directory-b2b-user-token.md)
#### [B2B para aplicações integradas do Azure AD](active-directory-b2b-configure-saas-apps.md)
#### [Mapeamento das afirmações de utilizador B2B](active-directory-b2b-claims-mapping.md)
#### [Comparar colaboração B2B com B2C](active-directory-b2b-compare-b2c.md)
#### [Obter suporte para B2B](active-directory-b2b-support.md)

### [Integrar identidades no local com o Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Gerir o acesso ao Azure](toc.yml)

## Delegar o acesso a recursos
### [Funções de administrador](active-directory-assign-admin-roles.md)
#### [Atribuir funções de administrador](active-directory-users-assign-role-azure-portal.md)
### [Unidades administrativas](active-directory-administrative-units-management.md)
### [Configurar durações de token](active-directory-configurable-token-lifetimes.md)

## Revisões de acesso
### [Descrição geral da revisão de acesso](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Concluir uma revisão de acesso](active-directory-azure-ad-controls-complete-access-review.md)
### [Criar uma revisão de acesso](active-directory-azure-ad-controls-create-access-review.md)
### [Como realizar uma revisão de acesso](active-directory-azure-ad-controls-perform-access-review.md)
### [Como rever o seu acesso](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Acesso de convidado com revisões de acesso](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Gerir o acesso dos utilizadores com revisões](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Gerir programas e controlos](active-directory-azure-ad-controls-manage-programs-controls.md)


## Proteger as suas identidades
### [Acesso condicional](active-directory-conditional-access-azure-portal.md)
#### [Controlos](active-directory-conditional-access-controls.md)
#### [Introdução](active-directory-conditional-access-azure-portal-get-started.md)
#### [Melhores práticas](active-directory-conditional-access-best-practices.md)
#### [Compreender as políticas de dispositivo para serviços do Office 365](active-directory-conditional-access-device-policies.md)
#### Tarefas
##### [Migrar políticas clássicas](active-directory-conditional-access-migration.md)
##### [Configurar o acesso condicional baseado no dispositivo](active-directory-conditional-access-policy-connected-applications.md)
##### [Configurar o acesso condicional baseado na aplicação](active-directory-conditional-access-mam.md)
##### [Fornecer termos de utilização para utilizadores e aplicações](active-directory-tou.md)
##### [Configurar ligação VPN](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [Configurar o SharePoint e Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Remediação](active-directory-conditional-access-device-remediation.md)
#### [Referência técnica](active-directory-conditional-access-technical-reference.md)
#### [FAQs](active-directory-conditional-faqs.md)
#### [Portal clássico](active-directory-conditional-access.md)
##### [Introdução](active-directory-conditional-access-azuread-connected-apps.md)

### Windows Hello
#### [Autenticar sem palavras-passe](active-directory-azureadjoin-passport.md)
#### [Ativar o Windows Hello para Empresas](active-directory-azureadjoin-passport-deployment.md)
### Autenticação baseada em certificados
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Introdução](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Ativar](active-directory-identityprotection-enable.md)
#### [Detetar vulnerabilidades](active-directory-identityprotection-vulnerabilities.md)
#### [Eventos de risco](active-directory-identity-protection-risk-events.md)
#### [Notificações](active-directory-identityprotection-notifications.md)
#### [Experiência de início de sessão](active-directory-identityprotection-flows.md)
#### [Simular eventos de risco](active-directory-identityprotection-playbook.md)
#### [Desbloquear utilizadores](active-directory-identityprotection-unblock-howto.md)
#### [FAQs](active-directory-identity-protection-faqs.md)
#### [Glossário](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## [Integrar outros serviços no Azure AD]()
### [Ativar a integração do LinkedIn](linkedin-integration.md)

## [Implementar o AD DS em VMs do Azure](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory em VMs do Azure](active-directory-deploying-ws-ad-guidelines.md)
### [Controlador de domínio de réplica numa rede virtual do Azure](active-directory-install-replica-active-directory-domain-controller.md)
### [Floresta nova numa rede virtual do Azure](active-directory-new-forest-virtual-machine.md)



## [Implementar os AD FS no Azure](active-directory-aadconnect-azure-adfs.md)
### [Elevada disponibilidade](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Algoritmo de alteração de hash de assinatura](active-directory-federation-sha256-guidance.md)

## [Resolução de problemas](active-directory-troubleshooting-support-howto.md)
### [O item de resolução de problemas do Active Directory está em falta ou não está disponível](active-directory-troubleshooting.md)

## Implementar a Prova de Conceito (PoC) do Azure AD
### [Manual PoC: Introdução](active-directory-playbook-intro.md)
### [Manual PoC: Ingredientes](active-directory-playbook-ingredients.md)
### [Manual PoC: Implementação](active-directory-playbook-implementation.md)
### [Manual PoC: Blocos Modulares](active-directory-playbook-building-blocks.md)


# Referência
## [Exemplos de código](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [cmdlets do Azure PowerShell](/powershell/azure/overview)
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
## [Fórum de comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Mapa do Azure](https://azure.microsoft.com/roadmap/?category=security-identity)
## [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Preços](https://azure.microsoft.com/pricing/details/active-directory/)
## [Calculadora de preços](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=active-directory)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
