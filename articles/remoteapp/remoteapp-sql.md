---
title: SQL Azure com o Azure RemoteApp | Microsoft Docs
description: Saiba como utilizar o SQL Azure com o Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
editor: 
ms.assetid: 35f81d75-bfd7-4980-807e-00339f2cb2a4
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 24d41c7eb6b5bd34c07d21318740ecf4c6db2d22
ms.lasthandoff: 03/31/2017


---
# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure com o Azure RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp vai ser descontinuado a 31 de agosto de 2017. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Frequentemente, quando os clientes escolhem alojar as aplicações do Windows na nuvem com o Azure RemoteApp, pretendem também migrar os seus dados, tais como os servidores SQL, para a nuvem para uma implementação integralmente na nuvem. Tal permite uma solução integralmente alojada na nuvem que pode ser acedida por qualquer dispositivo, em qualquer altura e local, através do Azure RemoteApp. A seguir encontram-se ligações e referências, bem como orientações para ajudar neste processo.   

## <a name="migrate-your-sql-data"></a>Migrar os dados SQL
Comece por [Migrar uma base de dados do SQL Server para a SQL Database do Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurar o Azure RemoteApp
Aloje a aplicação do Windows no Azure RemoteApp. A seguir encontra-se uma descrição passo-a-passo muito detalhada:

1. Crie a [VM do modelo do Azure RemoteApp](remoteapp-imageoptions.md). 
2. Instale a aplicação necessária na VM.
3. Configure a aplicação para estabelecer ligação à BD SQL e confirme que a mesma está a funcionar.
4. Prepare o sistema e encerre a VM. Efetue uma captura do encerramento como imagem para utilização com o Azure. **Nota:** deverá certificar-se de que a aplicação consegue manter a informação da conectividade da BD ao longo do processo de preparação do sistema. Caso a aplicação não consiga manter as informações da ligação da BD, poderá solicitar ao fornecedor da aplicação para verificar como pode ser especificada uma cadeia de ligação. 
5. Importe a imagem personalizada para a biblioteca do Azure RemoteApp selecionando a localização geográfica adequada em que reside a implementação do SQL Azure. 
6. Implemente uma coleção do RemoteApp no mesmo datacenter da implementação do SQL Azure utilizando o modelo acima e publique a aplicação. A implementação do Azure RemoteApp no mesmo datacenter da implementação do SQL Azure ajuda a garantir as velocidades de ligação mais rápidas e a reduzir a latência. 

## <a name="app-and-sql-configuration-considerations"></a>Considerações de configuração da aplicação e do SQL:
Existem alguns pontos a considerar na utilização do Azure SQL com o RemoteApp:

Saiba [como configurar uma firewall da SQL Database do Azure](../sql-database/sql-database-firewall-configure.md). Um excerto do artigo refere: "Inicialmente, qualquer acesso ao servidor da Base de Dados SQL do Azure é bloqueado pela firewall. De modo a começar a utilizar o servidor da SQL Database do Azure, tem de aceder ao Portal Clássico e especificar uma ou mais regras de firewall ao nível do servidor que ativam o acesso ao servidor da SQL Database do Azure. Utilize as regras de firewall para especificar quais os intervalos de endereços IP da Internet permitidos e se as aplicações do Azure podem ou não tentar estabelecer ligação ao servidor da Base de Dados SQL do Azure."

Além disso, quando um computador tenta estabelecer ligação ao servidor da base de dados a partir da Internet, a firewall verifica o endereço IP de origem do pedido em relação ao conjunto completo de regras de firewall ao nível do servidor e da base de dados (se necessário). "Se o endereço IP do pedido estiver dentro dos intervalos especificados nas regras de firewall ao nível do servidor, a ligação é concedida pelo servidor da Base de Dados SQL do Azure." Por conseguinte, podem ser utilizados Intervalos de IP e não apenas endereços IP de fontes individuais.

Siga as instruções passo-a-passo disponíveis em [Como configurar as definições de firewall da SQL Database através do Portal do Azure](../sql-database/sql-database-configure-firewall-settings.md) para especificar o intervalo de IP. Durante a configuração das regras da Firewall do SQL, forneça o intervalo IP da sub-rede que está especificado para a coleção do Azure RemoteApp. Tal deve permitir que os servidores do ARA estabeleçam ligação à BD SQL apesar de terem Endereços IP atribuídos dinamicamente.

## <a name="troubleshooting"></a>Resolução de problemas
Caso a experiência de utilização de uma aplicação cliente alojada no Azure RemoteApp que estabeleça ligação à SQL Database alojada no Azure ou no local seja morosa, poderão existir alguns motivos para tal.  

* A latência de rede do dispositivo para o Azure é alta. Mude para a melhor e mais rápida ligação de rede possível para o melhor desempenho. Utilize o [azurespeed.com](http://azurespeed.com/) como uma ferramenta geral para testar a latência dos dispositivos para o datacenter do Azure.  
* A aplicação cliente alojada no Azure RemoteApp está em esforço. Selecionar um plano de faturação como, por exemplo, a faturação Premium melhorará o desempenho. Outro truque consiste em monitorizar os recursos que a aplicação está a consumir: durante uma sessão ativa efetue uma sequência de teclas Ctrl-Alt-End que iniciará o ecrã da SAS, selecione Gestor de Tarefas e observe a utilização de recursos da aplicação.
* O SQL Servidor está em esforço ou não está otimizado. Siga a documentação de orientação do SQL para a resolução de problemas. 


