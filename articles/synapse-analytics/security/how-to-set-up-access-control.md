---
title: Proteger seu espaço de trabalho do Synapse (versão prévia)
description: Este artigo ensinará como usar funções e controle de acesso para controlar atividades e o acesso a dados no espaço de trabalho Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 88e58fbf6acc15b71fd135184cf8806a793140f3
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983203"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteger seu espaço de trabalho do Synapse (versão prévia)

Este artigo ensinará como usar funções e controle de acesso para controlar as atividades e o acesso aos dados. Seguindo estas instruções, o controle de acesso no Azure Synapse Analytics é simplificado. Você só precisa adicionar e remover usuários de um dos três grupos de segurança.

## <a name="overview"></a>Visão geral

Para proteger um espaço de trabalho do Synapse (versão prévia), você seguirá um padrão de configuração dos seguintes itens:

- Funções do Azure (como o proprietário, colaborador, etc.)
- Funções Synapse – essas funções são exclusivas do Synapse e não são baseadas em funções do Azure. Há três dessas funções:
  - Administrador do espaço de trabalho Synapse
  - Administrador do SQL do Synapse
  - Administrador do Spark Synapse
- Controle de acesso para dados no Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controle de acesso para bancos de dados Synapse SQL e Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Etapas para proteger um espaço de trabalho Synapse

Este documento usa nomes padrão para simplificar as instruções. Substitua-os por qualquer nome de sua escolha.

|Configuração | Valor de exemplo | Descrição |
| :------ | :-------------- | :---------- |
| **Espaço de trabalho Synapse** | WS1 |  O nome que o espaço de trabalho Synapse terá. |
| **Conta do ADLSGEN2** | STG1 | A conta ADLS a ser usada com seu espaço de trabalho. |
| **Contêiner** | CNT1 | O contêiner em STG1 que o espaço de trabalho usará por padrão. |
| **Locatário do Active Directory** | contoso | o nome do locatário do Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>ETAPA 1: configurar grupos de segurança

Crie e preencha três grupos de segurança para seu espaço de trabalho:

- **WS1\_WSAdmins** – para usuários que precisam de controle total sobre o espaço de trabalho
- **WS1\_SparkAdmins** – para os usuários que precisam de controle total sobre os aspectos do Spark do espaço de trabalho
- **WS1\_sqladmins** – para usuários que precisam de controle total sobre os aspectos SQL do espaço de trabalho
- Adicionar **WS1\_WSAdmins** ao **WS1\_sqladmins**
- Adicionar **WS1\_WSAdmins** ao **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>ETAPA 2: preparar sua conta de Data Lake Storage Gen2

Identifique essas informações sobre seu armazenamento:

- A conta ADLSGEN2 a ser usada para seu espaço de trabalho. Este documento chama STG1 de ti.  STG1 é considerado a conta de armazenamento "primária" para seu espaço de trabalho.
- O contêiner dentro de WS1 que o seu espaço de trabalho do Synapse usará por padrão. Este documento chama CNT1 de ti.  Esse contêiner é usado para:
  - Armazenando os arquivos de dados de backup para tabelas do Spark
  - Logs de execução para trabalhos do Spark

- Usando o portal do Azure, atribua os grupos de segurança às funções a seguir em CNT1

  - Atribuir **WS1\_WSAdmins** à função **colaborador de dados do blob de armazenamento**
  - Atribuir **WS1\_SparkAdmins** à função **colaborador de dados do blob de armazenamento**
  - Atribuir **WS1\_sqladmins** à função **colaborador de dados do blob de armazenamento**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>ETAPA 3: criar e configurar seu espaço de trabalho do Synapse

No portal do Azure, crie um espaço de trabalho Synapse:

- Nomeie o espaço de trabalho WS1
- Escolha STG1 para a conta de armazenamento
- Escolha CNT1 para o contêiner que está sendo usado como "FileSystem".
- Abrir WS1 no Synapse Studio
- Selecione **gerenciar** > **controle de acesso** atribuir os grupos de segurança às seguintes funções Synapse.
  - Atribuir **WS1\_WSAdmins** aos administradores do espaço de trabalho Synapse
  - Atribuir **WS1\_SparkAdmins** ao Synapse Spark admins
  - Atribuir **WS1\_sqladmins** a Synapse admins SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>ETAPA 4: Configurando Data Lake Storage Gen2 para uso pelo espaço de trabalho Synapse

O espaço de trabalho Synapse precisa de acesso a STG1 e CNT1 para que possa executar pipelines e executar tarefas do sistema.

- Abrir o portal do Azure
- Localizar STG1
- Navegue até CNT1
- Verifique se o MSI (Identidade de Serviço Gerenciada) para WS1 está atribuído à função **colaborador de dados de blob do Azure** no CNT1
  - Se você não o vir atribuído, atribua-o.
  - O MSI tem o mesmo nome que o espaço de trabalho. Nesse caso, seria &quot;WS1.&quot;

## <a name="step-5-configure-admin-access-for-sql-pools"></a>ETAPA 5: configurar o acesso de administrador para pools do SQL

- Abrir o portal do Azure
- Navegue até WS1
- Em **configurações**, clique em **administrador do SQL Active Directory**
- Clique em **definir administrador** e escolha\_WS1 sqladmins

## <a name="step-6-maintaining-access-control"></a>ETAPA 6: mantendo o controle de acesso

A configuração foi concluída.

Agora, para gerenciar o acesso de usuários, você pode adicionar e remover usuários dos três grupos de segurança.

Embora você possa atribuir usuários manualmente às funções Synapse, se fizer isso, ele não configurará as coisas de forma consistente. Em vez disso, somente adicione ou remova usuários dos grupos de segurança.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>ETAPA 7: verificar o acesso de usuários nas funções

Os usuários em cada função precisam concluir as seguintes etapas:

|   | Etapa | Administradores do workspace | Administradores do Spark | Administradores do SQL |
| --- | --- | --- | --- | --- |
| 1 | Carregar um arquivo parquet no CNT1 | YES | YES | YES |
| 2 | Ler o arquivo parquet usando o SQL sob demanda | YES | Não | YES |
| 3 | Criar um pool do Spark | SIM [1] | SIM [1] | Não  |
| 4 | Lê o arquivo parquet com um bloco de anotações | YES | YES | Não |
| 5 | Criar um pipeline do bloco de anotações e disparar o pipeline para executar agora | YES | Não | Não |
| 6 | Criar um pool SQL e executar um script SQL, como &quot;Select 1&quot; | SIM [1] | Não | SIM [1] |

> [!NOTE]
> [1] para criar pools do SQL ou do Spark, o usuário deve ter pelo menos a função de colaborador no espaço de trabalho Synapse.
> [!TIP]
>
> - Algumas etapas não serão permitidas deliberadamente dependendo da função.
> - Tenha em mente que algumas tarefas poderão falhar se a segurança não tiver sido totalmente configurada. Essas tarefas são indicadas na tabela.

## <a name="step-8-network-security"></a>ETAPA 8: segurança de rede

Para configurar o Firewall do espaço de trabalho, a rede virtual e o [link privado](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>ETAPA 9: conclusão

Seu espaço de trabalho agora está totalmente configurado e protegido.

## <a name="how-roles-interact-with-synapse-studio"></a>Como as funções interagem com o Synapse Studio

O Synapse Studio se comportará de maneira diferente com base nas funções de usuário. Alguns itens poderão ser ocultados ou desabilitados se um usuário não estiver atribuído a funções que fornecem o acesso apropriado. A tabela a seguir resume o efeito no Synapse Studio.

| Tarefa | Administradores de espaço de trabalho | Administradores do Spark | Administradores do SQL |
| --- | --- | --- | --- |
| Abrir o Synapse Studio | YES | YES | YES |
| Exibir hub inicial | YES | YES | YES |
| Exibir hub de dados | YES | YES | YES |
| Hub de dados/consulte contêineres e contas de ADLS Gen2 vinculadas | SIM [1] | SIM [1] | SIM [1] |
| Hub de dados/consulte bancos | YES | YES | YES |
| Hub de dados/consulte objetos em bancos | YES | YES | YES |
| Dados de data Hub/acesso em bancos de dados de pool do SQL | YES   | Não   | YES   |
| Dados de data Hub/acesso em bancos de dados SQL sob demanda | SIM [2]  | Não  | SIM [2]  |
| Dados de data Hub/acesso em bancos de dados do Spark | SIM [2] | SIM [2] | SIM [2] |
| Usar o Hub desenvolver | YES | YES | YES |
| Desenvolver scripts SQL de Hub/autor | YES | Não | YES |
| Desenvolver definições de trabalho do Spark de Hub/autor | YES | YES | Não |
| Desenvolver blocos de anotações de Hub/autor | YES | YES | Não |
| Desenvolver fluxos de entrada de Hub/autor | YES | Não | Não |
| Usar o Hub orquestrar | YES | YES | YES |
| Orquestrar o Hub/usar pipelines | YES | Não | Não |
| Usar o Hub gerenciar | YES | YES | YES |
| Gerenciar pools de Hub/SQL | YES | Não | YES |
| Gerenciar pools de Hub/Spark | YES | YES | Não |
| Gerenciar Hub/gatilhos | YES | Não | Não |
| Gerenciar Hub/serviços vinculados | YES | YES | YES |
| Gerenciar o Hub/controle de acesso (atribuir usuários a funções de espaço de trabalho Synapse) | YES | Não | Não |
| Gerenciar tempos de execução de Hub/integração | YES | YES | YES |
| Usar o Hub do monitor | YES | YES | YES |
| Monitorar execuções de Hub/orquestração/pipeline  | YES | Não | Não |
| Monitorar execuções de Hub/orquestração/gatilho  | YES | Não | Não |
| Monitorar tempos de execução de Hub/orquestração/integração  | YES | YES | YES |
| Monitorar aplicativos de Hub/atividades/Spark | YES | YES | Não  |
| Monitorar as solicitações de Hub/atividades/SQL | YES | Não | YES |
| Monitorar Hub/atividades/pools do Spark | YES | YES | Não  |
| Monitorar Hub/gatilhos | YES | Não | Não |
| Gerenciar Hub/serviços vinculados | YES | YES | YES |
| Gerenciar o Hub/controle de acesso (atribuir usuários a funções de espaço de trabalho Synapse) | YES | Não | Não |
| Gerenciar tempos de execução de Hub/integração | YES | YES | YES |


> [!NOTE]
> [1] o acesso a dados em contêineres depende do controle de acesso no ADLS Gen2. </br>
> [2] as tabelas OD do SQL e as tabelas do Spark armazenam seus dados no ADLS Gen2 e o acesso requer as permissões apropriadas em ADLS Gen2.

## <a name="next-steps"></a>Próximas etapas

Criar um [espaço de trabalho Synapse](../quickstart-create-workspace.md)
