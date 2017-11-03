É importante manter a sua máquina virtual (VM) segura para as aplicações que executar. Proteger as VMs pode incluir um ou mais serviços do Azure e as funcionalidades que abrangem o acesso seguro para as VMs e armazenamento seguro dos seus dados. Este artigo fornece informações que lhe permitem manter segura a VM e aplicações.

## <a name="antimalware"></a>Antimalware

O cenário das ameaças modernas para ambientes de nuvem for dinâmico, aumentando a pressão para manter a proteção eficaz para cumprir os requisitos de segurança e conformidade. [Antimalware da Microsoft para o Azure](../articles/security/azure-security-antimalware.md) é uma funcionalidade de proteção em tempo real livre que ajuda a identificar e remover vírus, spyware e outro software malicioso. Alertas podem ser configurados para o notificar ao conhecido software malicioso ou indesejável tenta instalação ou execução na VM.

## <a name="azure-security-center"></a>Centro de Segurança do Azure

[Centro de segurança do Azure](../articles/security-center/security-center-intro.md) ajuda a evitar, detetar e responder a ameaças para as suas VMs. Centro de segurança fornece gestão de políticas e monitorização de segurança integrada nas suas subscrições do Azure, ajuda a detetar ameaças que caso contrário podem passar despercebidas e funciona com um ecossistema abrangente de soluções de segurança.

## <a name="encryption"></a>Encriptação

Para melhorado [VM do Windows](../articles/virtual-machines/windows/encrypt-disks.md) e [VM com Linux](../articles/virtual-machines/linux/encrypt-disks.md) podem ser encriptadas de segurança e conformidade, discos virtuais no Azure. Discos virtuais em VMs do Windows são encriptados em descanso ao utilizar o Bitlocker. Discos virtuais em VMs do Linux são encriptados em descanso ao utilizar dm-crypt. 

Não há sem encargos de encriptação de discos virtuais no Azure. As chaves criptográficas são armazenadas no Cofre de chaves do Azure utilizando a proteção de software, ou pode importar ou gerar as suas chaves nos módulos de segurança de Hardware (HSMs) com certificação FIPS 140-2 normas de nível 2. Estas chaves criptográficas são utilizados para encriptar e desencriptar discos virtuais anexados à VM. Manter o controlo destas chaves criptográficas e pode auditar a sua utilização. Um principal de serviço do Azure Active Directory fornece um mecanismo seguro para emitir estas chaves criptográficas como VMs estão ligados à corrente ou desligar.

## <a name="key-vault-and-ssh-keys"></a>Cofre de chaves e as chaves SSH

Os segredos e certificados podem ser modelados como recursos e fornecidos pelo [Cofre de chaves](../articles/key-vault/key-vault-whatis.md). Pode utilizar o Azure PowerShell para criar cofres de chaves para [VMs do Windows](../articles/virtual-machines/windows/key-vault-setup.md) e a CLI do Azure para [VMs com Linux](../articles/virtual-machines/linux/key-vault-setup.md). Também pode criar chaves de encriptação.

As políticas de acesso do Cofre de chaves conceder permissões aos certificados, chaves e segredos separadamente. Por exemplo, pode dar a um utilizador acesso só a chaves, mas não permissões para segredos. No entanto, as permissões para aceder a chaves, segredos ou certificados são ao nível do cofre. Por outras palavras, [política de acesso do Cofre de chaves](../articles/key-vault/key-vault-secure-your-key-vault.md) não suporta permissões ao nível do objeto.

Quando se liga a VMs, deve utilizar a criptografia de chave pública para fornecer uma forma mais segura para iniciar sessão nos mesmos. Este processo envolve uma troca de chaves pública e privada utilizando o comando de secure shell (SSH) para autenticar si em vez de um nome de utilizador e palavra-passe. As palavras-passe estão vulneráveis a ataques, especialmente em VMs de acesso à Internet, tais como servidores web de força bruta. Com um par de chaves secure shell (SSH), pode criar um [VM com Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) chaves SSH que utiliza para autenticação, eliminando a necessidade de palavras-passe iniciar sessão. Também pode utilizar chaves SSH para ligar a partir de um [VM do Windows](../articles/virtual-machines/linux/ssh-from-windows.md) para uma VM com Linux.

## <a name="policies"></a>Políticas

[As políticas do Azure Resource Manager](../articles/azure-resource-manager/resource-manager-policy.md) pode ser utilizado para definir o comportamento pretendido para a sua organização [VMs do Windows](../articles/virtual-machines/windows/policy.md) e [VMs com Linux](../articles/virtual-machines/linux/policy.md). Ao utilizar políticas, uma organização pode aplicar vários convenções e regras em toda a empresa. Imposição do comportamento pretendido pode ajudar a mitigar o risco ao contribuir para o êxito da organização.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Utilizar [controlo de acesso baseado em funções (RBAC)](../articles/active-directory/role-based-access-control-what-is.md), pode segregar funções na sua equipa e conceder apenas a quantidade de acesso aos utilizadores na sua VM que precisam para desempenhar as suas funções. Em vez de dar everybody sem restrições permissões na VM, pode permitir que apenas determinadas ações. Pode configurar o controlo de acesso para a VM com o [portal do Azure](../articles/active-directory/role-based-access-control-configure.md), utilizando o [CLI do Azure](https://docs.microsoft.com/cli/azure/role), ou[Azure PowerShell](../articles/active-directory/role-based-access-control-manage-access-powershell.md).


## <a name="next-steps"></a>Passos seguintes
- Siga os passos para monitorizar a segurança da máquina virtual, utilizando o Centro de segurança do Azure para [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) ou [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).