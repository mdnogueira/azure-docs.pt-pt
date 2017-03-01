## <a name="delete-a-recovery-services-vault-powershell"></a>Eliminar um cofre dos Serviços de Recuperação (PowerShell)

1. Obter o cofre dos Serviços de Recuperação

        $vault = Get-AzureRmRecoveryServicesVault -Name "ContosoVault"

2. Eliminar o cofre

        Remove-AzureRmRecoveryServicesVault -Vault $vault

>[!WARNING]
>
> Utilize o comando acima com muito cuidado, uma vez que se eliminar qualquer cofre por engano, perderá todos os dados. Esta é uma ação permanente e não é possível para invertê-la.  




<!--HONumber=Feb17_HO3-->


