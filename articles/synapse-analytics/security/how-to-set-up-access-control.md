---
title: Proteja seu espaço de trabalho Sinapse (visualização)
description: Este artigo ensinará como usar funções e controle de acesso para controlar atividades e acesso a dados no espaço de trabalho Sinapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 94699f2153fa8d1df3ab85c184f32792c7ae0b59
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428869"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteja seu espaço de trabalho Sinapse (visualização)

Este artigo ensinará como usar funções e controle de acesso para controlar atividades e acesso a dados. Seguindo estas instruções, o controle de acesso no Azure Synapse Analytics é simplificado. Você só precisa adicionar e remover usuários em um dos três grupos de segurança.

## <a name="overview"></a>Visão geral

Para garantir um espaço de trabalho Sinapse (visualização), você seguirá um padrão de configuração dos seguintes itens:

- Funções do Azure (como as incorporadas como Proprietário, Contribuinte, etc.)
- Papéis sinapses – esses papéis são exclusivos da Sinapse e não são baseados em papéis do Azure. Há três desses papéis:
  - Admin espaço de trabalho sinapse
  - Sinapse SQL admin
  - Sinapse Spark admin
- Controle de acesso para dados no Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controle de acesso para bancos de dados Synapse SQL e Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Etapas para garantir um espaço de trabalho sinapse

Este documento usa nomes padrão para simplificar as instruções. Substitua-os por qualquer nome de sua escolha.

|Configuração | Valor de exemplo | Descrição |
| :------ | :-------------- | :---------- |
| **Espaço de trabalho sinapse** | WS1 |  O nome que o espaço de trabalho sinapse terá. |
| **Conta ADLSGEN2** | STG1 | A conta ADLS para usar com seu espaço de trabalho. |
| **Contêiner** | CNT1 | O contêiner no STG1 que o espaço de trabalho usará por padrão. |
| **Inquilino ativo do diretório** | contoso | o nome do inquilino ativo do diretório.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSO 1: Configurar grupos de segurança

Crie e preencha três grupos de segurança para o seu espaço de trabalho:

- **WS1\_WSAdmins** – para usuários que precisam de controle completo sobre o espaço de trabalho
- **WS1\_SparkAdmins** – para aqueles usuários que precisam de controle completo sobre os aspectos da Faísca do espaço de trabalho
- **WS1\_SQLAdmins** – para usuários que precisam de controle completo sobre os aspectos SQL do espaço de trabalho
- Adicionar **admins WS1\_** aos **SQLAdmins WS1\_**
- Adicionar **admins\_WS1** aos **SparkAdmins WS1\_**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>PASSO 2: Prepare sua conta Data Lake Storage Gen2

Identifique essas informações sobre seu armazenamento:

- A conta ADLSGEN2 para usar no seu espaço de trabalho. Este documento chama-o de STG1.  O STG1 é considerado a conta de armazenamento "principal" do seu espaço de trabalho.
- O recipiente dentro do WS1 que seu espaço de trabalho Sinapse usará por padrão. Este documento chama-o cnt1.  Este recipiente é usado para:
  - Armazenamento dos arquivos de dados de backup para tabelas Spark
  - Registros de execução para trabalhos de Faísca

- Usando o portal Azure, atribua aos grupos de segurança as seguintes funções no CNT1

  - Atribuir **WSAdmins WS1\_** à função **de contribuinte de dados blob de armazenamento**
  - Atribuir **SparkAdmins WS1\_** à função **de contribuinte de dados blob de armazenamento**
  - Atribuir **SQLAdmins WS1\_** à função **de contribuinte de dados blob de armazenamento**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSO 3: Crie e configure seu espaço de trabalho sinapse

No portal Azure, crie um espaço de trabalho sinapse:

- Nomeie o espaço de trabalho WS1
- Escolha o STG1 para a conta armazenamento
- Escolha CNT1 para o contêiner que está sendo usado como "sistema de arquivos".
- Abra o WS1 no Estúdio Sinapse
- Selecione **Gerenciar** > **controle de acesso** atribuir os grupos de segurança às seguintes funções do Sinapse.
  - Atribuir **WS1\_WSAdmins** aos admins do espaço de trabalho sinapse
  - Atribuir **SparkAdmins WS1\_** aos admins sinapse spark
  - Atribuir **sqladmins WS1\_** para admins Synapse SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>PASSO 4: Configuração do Data Lake Storage Gen2 para uso pelo espaço de trabalho Sinapse

O espaço de trabalho Sinapse precisa de acesso ao STG1 e CNT1 para que possa executar pipelines e executar tarefas do sistema.

- Abrir o portal do Azure
- Localizar STG1
- Navegue até CNT1
- Certifique-se de que o MSI para WS1 seja atribuído à função **contribuinte de dados do Azure Blob** no CNT1
  - Se você não vê-lo atribuído, atribua-o.
  - O MSI tem o mesmo nome do espaço de trabalho. Neste caso, seria &quot;O WS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>PASSO 5: Configure o acesso de administrador para pools SQL

- Abrir o portal do Azure
- Navegue até WS1
- Em **Configurações,** clique **em SQL Active Directory admin**
- Clique **em Definir admin** \_e escolha WS1 SQLAdmins

## <a name="step-6-maintaining-access-control"></a>PASSO 6: Manutenção do controle de acesso

A configuração está concluída.

Agora, para gerenciar o acesso aos usuários, você pode adicionar e remover usuários para os três grupos de segurança.

Embora você possa atribuir manualmente usuários a funções sinapse, se o fizer, ele não configurará as coisas de forma consistente. Em vez disso, adicione ou remova apenas os usuários aos grupos de segurança.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>PASSO 7: Verifique o acesso dos usuários nas funções

Os usuários de cada função precisam concluir as seguintes etapas:

|   | Etapa | Administradores do workspace | Admins de centelha | Admins SQL |
| --- | --- | --- | --- | --- |
| 1 | Carregue um arquivo parquet no CNT1 | YES | YES | YES |
| 2 | Leia o arquivo parquet usando SQL sob demanda | YES | Não | YES |
| 3 | Criar uma piscina de faíscas | SIM [1] | SIM [1] | Não  |
| 4 | Lê o arquivo parquet com um Notebook | YES | YES | Não |
| 5 | Crie um pipeline a partir do Notebook e acione o pipeline para executar agora | YES | Não | Não |
| 6 | Crie um Pool SQL e execute um &quot;script SQL, como SELECT 1&quot; | SIM [1] | Não | SIM[1] |

> [!NOTE]
> [1] Para criar pools SQL ou Spark, o usuário deve ter pelo menos a função contribuinte no espaço de trabalho Sinapse.
> [!TIP]
>
> - Algumas etapas não serão permitidas deliberadamente dependendo do papel.
> - Tenha em mente que algumas tarefas podem falhar se a segurança não estiver totalmente configurada. Essas tarefas são anotadas na tabela.

## <a name="step-8-network-security"></a>PASSO 8: Segurança de rede

Para configurar o firewall do espaço de trabalho, a rede virtual e [o Private Link](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>PASSO 9: Conclusão

Seu espaço de trabalho está agora totalmente configurado e protegido.

## <a name="how-roles-interact-with-synapse-studio"></a>Como os papéis interagem com o Synapse Studio

O Synapse Studio se comportará de forma diferente com base nas funções do usuário. Alguns itens podem ser ocultos ou desativados se um usuário não for atribuído a funções que dão o acesso apropriado. A tabela a seguir resume o efeito no Synapse Studio.

| Tarefa | Admins do espaço de trabalho | Admins de centelha | Admins SQL |
| --- | --- | --- | --- |
| Abra o Estúdio Sinapse | YES | YES | YES |
| Ver home hub | YES | YES | YES |
| Exibir data hub | YES | YES | YES |
| Data Hub / Ver contas e contêineres ADLSGen2 vinculados | SIM [1] | SIM[1] | SIM[1] |
| Data Hub / Ver bancos de dados | YES | YES | YES |
| Data Hub / Ver objetos em bancos de dados | YES | YES | YES |
| Data Hub / Dados de acesso em bancos de dados sql pool | YES   | Não   | YES   |
| Data Hub / Dados de acesso em bancos de dados sob demanda SQL | SIM [2]  | Não  | SIM [2]  |
| Data Hub / Dados de acesso em bancos de dados Spark | SIM [2] | SIM [2] | SIM [2] |
| Use o hub Desenvolver | YES | YES | YES |
| Desenvolver scripts SQL hub / autor | YES | Não | YES |
| Desenvolver hub / autor Spark Job Definições | YES | YES | Não |
| Desenvolver Hub / notebooks autor | YES | YES | Não |
| Desenvolver fluxos de dados do Hub/autor | YES | Não | Não |
| Use o hub Orchestrate | YES | YES | YES |
| Centro de orquestração / uso de Pipelines | YES | Não | Não |
| Use o Manage Hub | YES | YES | YES |
| Gerenciar pools Hub / SQL | YES | Não | YES |
| Gerenciar piscinas hub / spark | YES | YES | Não |
| Gerenciar hub / gatilhos | YES | Não | Não |
| Gerenciar serviços hub/vinculados | YES | YES | YES |
| Gerenciar hub / controle de acesso (atribuir usuários às funções de espaço de trabalho Synapse) | YES | Não | Não |
| Gerenciar tempos de execução do Hub /Integração | YES | YES | YES |

> [!NOTE]
> [1] O acesso aos dados em contêineres depende do controle de acesso em tabelas ADLSGen2 [2] Tabelas SQL OD e tabelas Spark armazenam seus dados no ADLSGen2 e o acesso requer as permissões apropriadas no ADLSGen2.

## <a name="next-steps"></a>Próximas etapas

Criar um [espaço de trabalho sinapse](../quickstart-create-workspace.md)
