---
title: Integração de controle do código-fonte
description: Experiência de DevOps de banco de dados de classe empresarial para o pool de SQL com integração do controle do código-fonte nativo usando Azure Repos (Git e GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 28ce56675b8ba6b0f699b93af4411d360e1e582d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80148055"
---
# <a name="source-control-integration-for-sql-pool"></a>Integração de controle do código-fonte para o pool de SQL

Este tutorial descreve como integrar seu projeto de banco de dados SSDT (SQL Server Data Tools) com controle do código-fonte.  A integração do controle do código-fonte é a primeira etapa na criação de seu pipeline de implantação e integração contínua com o recurso de pool de SQL no Azure Synapse Analytics. 

## <a name="before-you-begin"></a>Antes de começar

- Inscreva-se para obter uma [organização do Azure DevOps](https://azure.microsoft.com/services/devops/)
- Percorra o tutorial [Criar e Conectar](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Instale o Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Configurar e conectar ao Azure DevOps

1. Em sua Organização do Azure DevOps, crie um projeto que hospedará seu projeto de banco de dados do SSDT por meio de um Repositório do Azure

   ![Criar Projeto](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Criar projeto")

2. Abra o Visual Studio e conecte-se à sua organização e projeto do Azure DevOps da etapa 1 selecionando "Gerenciar Conexões"

   ![Gerenciar Conexões](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gerenciar conexões")

   ![Connect](./media/sql-data-warehouse-source-control-integration/3-connect.png "Conectar")

3. Clone seu repositório Azure Repo de seu projeto para o computador local

   ![Clonar repositório](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Clonar repositório")

## <a name="create-and-connect-your-project"></a>Criar e conectar o projeto

1. No Visual Studio, crie um novo Projeto de Banco de Dados do SQL Server com um diretório e um repositório Git local em seu **repositório clonado local**

   ![Criar projeto](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Criar um novo projeto")  

2. Clique com o botão direito do mouse em seu sqlproject vazio e importe seu data warehouse para o projeto de banco de dados

   ![Importar Projeto](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importar projeto")  

3. No Team Explorer no Visual Studio, confirme suas alterações no repositório Git local 

   ![Confirmar](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. Agora que você tem as alterações confirmadas localmente no repositório clonado, sincronize e envie por push suas alterações para o repositório Azure Repo em seu projeto do Azure DevOps.

   ![Sincronizar e efetuar push – preparo](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronizar e efetuar push – preparo")

   ![Sincronizar e efetuar push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizar e efetuar push")  

## <a name="validation"></a>Validação

1. Verifique se as alterações foram enviadas por push para o Repositório do Azure atualizando uma coluna de tabela em seu projeto de banco de dados no SSDT (SQL Server Data Tools) do Visual Studio

   ![Validar coluna de atualização](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Validar coluna de atualização")

2. Confirme e envie por push a alteração do repositório local para seu Azure Repo

   ![Efetuar push das alterações](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Enviar alterações por push")

3. Verifique se a alteração foi enviada por push em seu repositório Azure Repo

   ![Verificar](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificar alterações")

4. (**Opcional**) Use a Comparação de Esquemas e atualize as alterações de seu data warehouse de destino usando o SSDT para garantir que as definições de objeto no repositório Azure Repo e no repositório local reflitam seu data warehouse

## <a name="next-steps"></a>Próximas etapas

- [Como desenvolvedor para o pool de SQL](sql-data-warehouse-overview-develop.md)
