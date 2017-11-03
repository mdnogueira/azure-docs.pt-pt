Armazenamento georredundante (GRS) replica os dados para uma região secundária centenas de quilómetros região primária. Se a sua conta do storage tiver GRS ativada, em seguida, os dados são duráveis mesmo em caso de uma falha regional completa ou de um desastre no qual a região primária não é recuperável.

Para uma conta de armazenamento com a GRS ativada, uma atualização pela primeira vez é consolidada na região primária, em que estes são replicados três vezes. Em seguida, a atualização é replicada de forma assíncrona para a região secundária, onde também são replicado três vezes.

Com a GRS, regiões primários e secundários gerir réplicas em vários domínios de falhas separada e atualizar domínios dentro de uma unidade de escala de armazenamento, conforme descrito com LRS.

Considerações:

* Uma vez que a replicação assíncrona envolve um atraso, na eventualidade de ocorrer um desastre regional é possível que as alterações que ainda não tem sido replicadas para a região secundária serão perdidas se não não possível recuperar os dados da região primária.
* A réplica não está disponível, a menos que a Microsoft inicia a ativação pós-falha para a região secundária. Se o Microsoft inicie uma ativação pós-falha para a região secundária, será leu e acesso de escrita para que os dados após a ativação pós-falha foi concluída. Para obter mais informações, consulte [orientações de recuperação após desastre](../articles/storage/common/storage-disaster-recovery-guidance.md). 
* Se pretender uma aplicação ao ler a partir da região secundária, o utilizador deve ativar RA-GRS.

Quando cria uma conta de armazenamento, selecione a região primária para a conta. A região secundária é determinada com base na região primária e não pode ser alterada. A tabela seguinte mostra os emparelhamentos região primária e secundária.

| Primária | Secundária |
| --- | --- |
| EUA Centro-Norte | EUA Centro-Sul |
| EUA Centro-Sul | EUA Centro-Norte |
| EUA Leste | EUA Oeste |
| EUA Oeste | EUA Leste |
| Este dos EUA 2 | EUA Central |
| EUA Central | Este dos EUA 2 |
| Europa do Norte | Europa Ocidental |
| Europa Ocidental | Europa do Norte |
| Sudeste Asiático | Ásia Oriental |
| Ásia Oriental | Sudeste Asiático |
| Leste da China | Norte da China |
| Norte da China | Leste da China |
| Leste do Japão | Oeste do Japão |
| Oeste do Japão | Leste do Japão |
| Sul do Brasil | EUA Centro-Sul |
| Leste da Austrália | Sudeste da Austrália |
| Sudeste da Austrália | Leste da Austrália |
| Índia do Sul | Índia Central |
| Índia Central | Índia do Sul |
| Índia Ocidental | Índia do Sul |
| Gov (US) - Iowa | Gov (US) - Virginia |
| Gov (US) - Virginia | Gov (US) - Texas |
| Gov (US) - Texas | Gov (US) - Arizona |
| Gov (US) - Arizona | Gov (US) - Texas |
| Canadá Central | Leste do Canadá |
| Leste do Canadá | Canadá Central |
| Reino Unido Oeste | Reino Unido Sul |
| Reino Unido Sul | Reino Unido Oeste |
| Alemanha Central | Alemanha Nordeste |
| Alemanha Nordeste | Alemanha Central |
| Oeste dos E.U.A 2 | EUA Centro-Oeste |
| EUA Centro-Oeste | Oeste dos E.U.A 2 |

Para obter informações atualizadas sobre regiões suportadas pelo Azure, consulte [regiões do Azure](https://azure.microsoft.com/regions/).

>[!NOTE]  
> Região secundária do US Virginia EUA é Texas us-nos. Anteriormente, US-nos Virginia utilizados Iowa us-nos de como uma região secundária. As contas do Storage ainda tirar partido dos EUA us Iowa como uma região secundária estão a ser migradas para Texas us-nos como uma região secundário;. 
> 
> 