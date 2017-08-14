Está agora disponível o suporte de duas funcionalidades de depuração no Azure: Saída da Consola e Captura de Ecrã para o modelo de implementação das Máquinas Virtuais do Azure (Resource Manager). 

Quando colocar a sua própria imagem no Azure ou mesmo arrancar uma das imagens da plataforma, podem existir muitos motivos pelos quais uma Máquina Virtual entra num estado de não arranque. Estas funcionalidades permitem-lhe diagnosticar e recuperar facilmente Máquinas Virtuais de falhas de arranque.

Para Máquinas Virtuais do Linux, pode visualizar facilmente a saída do registo da consola a partir do Portal:

![Portal do Azure](./media/virtual-machines-common-boot-diagnostics/screenshot1.png)
 
No entanto, para Máquinas Virtuais do Windows e do Linux, o Azure também permite ver uma captura de ecrã da VM a partir do hipervisor:

![Erro](./media/virtual-machines-common-boot-diagnostics/screenshot2.png)

Estas duas funcionalidades são suportadas para Máquinas Virtuais do Azure em todas as regiões. Tenha em atenção que as capturas de ecrã e a saída podem demorar até 10 minutos a aparecer na sua conta de armazenamento.

## <a name="common-boot-errors"></a>Erros de arranque comuns

- [0xC000000E](https://support.microsoft.com/help/4010129)
- [0xC000000F](https://support.microsoft.com/help/4010130)
- [0xC0000011](https://support.microsoft.com/help/4010134)
- [0xC0000034](https://support.microsoft.com/help/4010140)
- [0xC0000098](https://support.microsoft.com/help/4010137)
- [0xC00000BA](https://support.microsoft.com/help/4010136)
- [0xC000014C](https://support.microsoft.com/help/4010141)
- [0xC0000221](https://support.microsoft.com/help/4010132)
- [0xC0000225](https://support.microsoft.com/help/4010138)
- [0xC0000359](https://support.microsoft.com/help/4010135)
- [0xC0000605](https://support.microsoft.com/help/4010131)
- [Não foi encontrado nenhum sistema operativo](https://support.microsoft.com/help/4010142)
- [Falha de arranque ou INACCESSIBLE_BOOT_DEVICE](https://support.microsoft.com/help/4010143)

## <a name="enable-diagnostics-on-a-new-virtual-machine"></a>Ativar o diagnóstico numa máquina virtual nova
1. Quando criar uma nova Máquina Virtual a partir do Portal de Pré-visualização, selecione **Azure Resource Manager** na lista pendente do modelo de implementação:
 
    ![Resource Manager](./media/virtual-machines-common-boot-diagnostics/screenshot3.jpg)

2. Configure a opção de Monitorização para selecionar a conta de armazenamento onde quer colocar estes ficheiros de diagnóstico.
 
    ![Criar VM](./media/virtual-machines-common-boot-diagnostics/screenshot4.jpg)

3. Se estiver a implementar a partir de um modelo do Azure Resource Manager, navegue para o recurso da Máquina Virtual e acrescente a secção do perfil de diagnóstico. Não se esqueça de utilizar o cabeçalho da versão da API "2015-06-15".

    ```json
    {
          "apiVersion": "2015-06-15",
          "type": "Microsoft.Compute/virtualMachines",
          … 
    ```

4. O perfil de diagnóstico permite-lhe selecionar a conta de armazenamento onde quer colocar estes registos.

    ```json
            "diagnosticsProfile": {
                "bootDiagnostics": {
                "enabled": true,
                "storageUri": "[concat('http://', parameters('newStorageAccountName'), '.blob.core.windows.net')]"
                }
            }
            }
        }
    ```

Para implementar uma Máquina Virtual de exemplo com o diagnóstico de arranque ativado, consulte o nosso repositório aqui.

## <a name="update-an-existing-virtual-machine"></a>Atualizar uma máquina virtual existente ##

Para ativar o diagnóstico de arranque através do Portal, também pode atualizar uma Máquina Virtual existente através do Portal. Selecione as opções Diagnóstico de Arranque e Guardar. Reinicie a VM para entrar em vigor.

![Atualizar VM Existente](./media/virtual-machines-common-boot-diagnostics/screenshot5.png)

