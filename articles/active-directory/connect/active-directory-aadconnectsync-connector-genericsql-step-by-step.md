---
title: "Genérica SQL conector passo-a passo | Microsoft Docs"
description: "Este artigo é walking, através de um sistema simples de RH passo a passo através do conector do SQL Server genérico."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 28c1cc60-24fd-4d0d-a36d-b4aba6de86e7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 3fdc1b405b95180d031aa4ad45b406f7fc149d8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="generic-sql-connector-step-by-step"></a>Conector do SQL genérico passo a passo
Este tópico é um guia passo a passo. Cria uma base de dados de RH de exemplo simples e utilizá-lo para importar alguns utilizadores e a respetiva associação a grupos.

## <a name="prepare-the-sample-database"></a>Preparar a base de dados de exemplo
Num servidor com o SQL Server, execute o script SQL encontrada no [anexo A](#appendix-a). Este script cria uma base de dados de exemplo com o nome GSQLDEMO. O modelo de objeto para a base de dados criada aspeto nesta imagem:  
![Modelo de objeto](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/objectmodel.png)

Também crie um utilizador que pretende utilizar para ligar à base de dados. Esta explicação passo a passo, o utilizador é chamado FABRIKAM\SQLUser e localizado no domínio.

## <a name="create-the-odbc-connection-file"></a>Criar o ficheiro de ligação de ODBC
O conector genérico do SQL Server está a utilizar ODBC para ligar ao servidor remoto. Primeiro é preciso criar um ficheiro com as informações de ligação de ODBC.

1. Inicie o utilitário de gestão de ODBC no seu servidor:  
   ![ODBC](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc.png)
2. Selecione o separador **ficheiro DSN**. Clique em **adicionar...** .  
   ![ODBC1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc1.png)
3. O controlador de out-of-box funciona ajustar, por isso, selecione-o e clique em **seguinte >**.  
   ![ODBC2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc2.png)
4. Dê um nome, o ficheiro como **GenericSQL**.  
   ![ODBC3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc3.png)
5. Clique em **Concluir**.  
   ![ODBC4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc4.png)
6. Tempo para configurar a ligação. Atribua a origem de dados uma boa descrição e forneça o nome do servidor a executar o SQL Server.  
   ![ODBC5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc5.png)
7. Selecione a forma de autenticação do SQL Server. Neste caso, vamos utilizar a autenticação do Windows.  
   ![ODBC6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc6.png)
8. Forneça o nome da base de dados de exemplo, **GSQLDEMO**.  
   ![ODBC7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc7.png)
9. Manter tudo predefinido neste ecrã. Clique em **Concluir**.  
   ![ODBC8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc8.png)
10. Para verificar que tudo está a funcionar conforme esperado, clique em **origem de dados de teste**.  
    ![ODBC9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc9.png)
11. Certifique-se de que o teste está concluída com êxito.  
    ![ODBC10](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc10.png)
12. O ficheiro de configuração de ODBC deve agora estar visível no ficheiro DSN.  
    ![ODBC11](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/odbc11.png)

Temos, agora, o ficheiro é necessário e pode começar a criar o conector.

## <a name="create-the-generic-sql-connector"></a>Criar o conector do SQL Server genérico
1. Na IU do Gestor do serviço de sincronização, selecione **conectores** e **criar**. Selecione **SQL genérico (Microsoft)** e atribua-lhe um nome descritivo.  
   ![Connector1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector1.png)
2. Localize o ficheiro DSN que criou na secção anterior e carregá-la para o servidor. Forneça as credenciais para ligar à base de dados.  
   ![Connector2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector2.png)
3. Nestas instruções, iremos são facilitando para nos e indicar que existem dois tipos de objeto, **utilizador** e **grupo**.
   ![Connector3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector3.png)
4. Para obter os atributos, queremos o conector para detetar esses atributos observando a tabela em si. Uma vez que **utilizadores** é uma palavra reservada no SQL, temos de fornecê-lo no Parênteses Retos [-].  
   ![Connector4](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector4.png)
5. Tempo até ao definir o atributo âncora e o atributo DN. Para **utilizadores**, utilizamos a combinação de nome de utilizador de dois atributos e campo IDdeEmpregado. Para **grupo**, utilizamos GroupName (realistas não reais, mas para estas instruções funciona).
   ![Connector5](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector5.png)
6. Nem todos os tipos de atributo podem ser detetados numa base de dados do SQL Server. O tipo de atributo de referência não pode em particular. Para o tipo de objeto de grupo, é necessário alterar o OwnerID e MemberID para efeitos de referência.  
   ![Connector6](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector6.png)
7. Os atributos que selecionamos como atributos de referência no passo anterior requerem o tipo de objeto estes valores são uma referência. No nosso caso, o tipo de objeto de utilizador.  
   ![Connector7](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector7.png)
8. Na página de parâmetros globais, selecione **marca d'água** como a estratégia de diferenças. Escreva também o formato de data/hora **aaaa-MM-dd hh: mm:**.
   ![Connector8](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector8.png)
9. No **configurar partições e hierarquias** página, selecione ambos os tipos de objeto.
   ![Connector9](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/connector9.png)
10. No **selecionar tipos de objeto** e **selecionar atributos**, selecione os tipos de objeto e todos os atributos. No **configurar âncoras** página, clique em **concluir**.

## <a name="create-run-profiles"></a>Criar perfis de execução
1. Na IU do Gestor do serviço de sincronização, selecione **conectores**, e **configurar perfis de execução**. Clique em **novo perfil**. Iremos começar com **importação completa**.  
   ![Runprofile1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile1.png)
2. Selecione o tipo **importação completa (apenas fase)**.  
   ![Runprofile2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile2.png)
3. Selecione a partição **OBJETO = utilizador**.  
   ![Runprofile3](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile3.png)
4. Selecione **tabela** e tipo **[utilizadores]**. Desloque para baixo para a secção de tipo de objeto com múltiplos valores e introduza os dados como a imagem seguinte. Selecione **concluir** para guardar o passo.  
   ![Runprofile4a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4a.png)  
   ![Runprofile4b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile4b.png)  
5. Selecione **novo passo**. De momento, selecione **OBJETO = grupo**. Na última página, utilize a configuração como a imagem seguinte. Clique em **Concluir**.  
   ![Runprofile5a](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5a.png)  
   ![Runprofile5b](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/runprofile5b.png)  
6. Opcional: Se pretender, pode configurar perfis de execução adicional. Nestas instruções, é utilizada apenas a importação completa.
7. Clique em **OK** para concluir a alteração de perfis de execução.

## <a name="add-some-test-data-and-test-the-import"></a>Adicionar alguns dados de teste e a importação de teste
Preencha alguns dados de teste na sua base de dados de exemplo. Quando estiver pronto, selecione **executar** e **completa importação**.

Segue-se um utilizador com dois números de telefone e um grupo com alguns membros.  
![cs1](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs1.png)  
![CS2](./media/active-directory-aadconnectsync-connector-genericsql-step-by-step/cs2.png)  

## <a name="appendix-a"></a>Anexo A
**Script SQL para criar a base de dados de exemplo**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
