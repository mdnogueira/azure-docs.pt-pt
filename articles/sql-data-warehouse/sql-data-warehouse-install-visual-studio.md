<properties
   pageTitle="Instalar o Visual Studio e o SSDT para SQL Data Warehouse | Microsoft Azure"
   description="Instalar o Visual Studio e SQL Server Development Tools (SSDT) para o Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/01/2016"
   ms.author="sonyama;barbkess"/>

# Instalar o Visual Studio 2015 e o SSDT para SQL Data Warehouse

Para desenvolver aplicações para o SQL Data Warehouse, é recomendável utilizar o Visual Studio 2015 com a versão mais recente de SQL Server Data Tools (SSDT).  O Visual Studio 2013 com SSDT também é suportado para compatibilidade com versões anteriores.  

Utilizar o Visual Studio com SSDT irá permitir a utilização do SQL Server Object Explorer para explorar visualmente tabelas, vistas, procedimentos armazenados e muitos mais objetos no SQL Data Warehouse, bem como executar consultas.

> [AZURE.NOTE] O SQL Data Warehouse ainda não suporta Projetos de Base de Dados do Visual Studio.  Esta funcionalidade será adicionada numa versão futura.

## Passo 1: Instalar o Visual Studio 2015

Siga estas ligações para transferir e instalar o Visual Studio 2015. Se já tiver instalado o Visual Studio 2013 ou 2015, pode avançar para o passo 2, instalar o SSDT.

1. [Transferir o Visual Studio 2015][].
2. Siga o guia [Instalar o Visual Studio][] na MSDN e escolha as configurações predefinidas.

## Passo 2: Instalar o SSDT

Para instalar o SSDT para Visual Studio, basta procurar uma atualização do SSDT a partir do Visual Studio, seguindo estes passos.

1. No Visual Studio, clique em **Ferramentas** / **Extensões e Atualizações…** / **Atualizações**
2. Selecione **Atualizações de Produtos** e, em seguida, procure **Atualização do Microsoft SQL Server para Ferramentas de Base de Dados**

Se não for encontrada uma atualização, deve ter a versão mais recente instalada.  Para confirmar que o SSDT está instalado, clique em **Ajuda** / **Sobre o Microsoft Visual Studio** e procure SQL Server Data Tools na lista.  A versão mais recente do SSDT é 14.0.60525.0.  Se a opção de instalação não estiver disponível a partir do Visual Studio, em alternativa, pode visitar a página [Transferência do SSDT][] para transferir e instalar manualmente o SSDT.

## Passos seguintes

Agora que tem a versão mais recente do SSDT, está pronto para [ligar][] ao SQL Data Warehouse.

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[ligar]: ./sql-data-warehouse-get-started-connect.md

<!--Other-->
[Transferir o Visual Studio 2015]: https://www.visualstudio.com/downloads/
[Instalar o Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[Transferência do SSDT]: https://msdn.microsoft.com/library/mt204009.aspx



<!--HONumber=Jun16_HO2-->


