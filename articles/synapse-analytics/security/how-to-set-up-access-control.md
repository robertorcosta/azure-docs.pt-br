---
title: Proteger seu workspace do Synapse (versão prévia)
description: Este artigo ensinará como usar funções e o controle de acesso para controlar atividades e o acesso a dados no workspace do Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 080e56a5b6be8ba68c901509fe87421632144643
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312047"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteger seu workspace do Synapse (versão prévia) 

Este artigo ensinará como usar funções e o controle de acesso para controlar as atividades e o acesso a dados. Seguindo essas instruções, o controle de acesso no Azure Synapse Analytics é simplificado. Você só precisa adicionar usuários a um dos três grupos de segurança e removê-los de lá.

## <a name="overview"></a>Visão geral

Para proteger um workspace do Synapse (versão prévia), você seguirá um padrão de configuração dos seguintes itens:

- Funções do Azure (como as internas, por exemplo, Proprietário, Colaborador etc.)
- Funções do Synapse – essas funções são exclusivas do Synapse e não são baseadas em funções do Azure. Há três dessas funções:
  - Administrador do workspace do Synapse
  - Administrador do SQL do Synapse
  - Administrador do Apache Spark para Azure Synapse Analytics
- Controle de acesso para dados no Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controle de acesso para bancos de dados do SQL do Synapse e Spark
- 
## <a name="steps-to-secure-a-synapse-workspace"></a>Etapas para proteger um workspace do Synapse

Este documento usa nomes padrão para simplificar as instruções. Substitua-os pelos nomes de sua escolha.

|Configuração | Valor de exemplo | Descrição |
| :------ | :-------------- | :---------- |
| **Workspace do Synapse** | WS1 |  O nome que o workspace do Synapse terá. |
| **Conta do ADLSGEN2** | STG1 | A conta do ADLS a ser usada com seu workspace. |
| **Contêiner** | CNT1 | O contêiner no STG1 que o workspace usará por padrão. |
| **Locatário do Active Directory** | contoso | o nome do locatário do Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>ETAPA 1: Configurar grupos de segurança

Crie e preencha três grupos de segurança para seu workspace:

- **WS1\_WSAdmins** – para usuários que precisam ter controle completo sobre o workspace
- **WS1\_SparkAdmins** – para aqueles usuários que precisam ter controle completo sobre os aspectos do workspace do Spark
- **WS1\_SQLAdmins** – para usuários que precisam ter controle completo sobre os aspectos do SQL do workspace

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>ETAPA 2: Preparar sua conta do Data Lake Storage Gen2

Identifique estas informações sobre seu armazenamento:

- A conta do ADLSGEN2 a ser usada para seu workspace. Este documento a chama de STG1.  O STG1 é considerado a conta de armazenamento "primária" para seu workspace.
- O contêiner dentro de WS1 que o workspace do Synapse usará por padrão. Este documento a chama de CNT1.  Esse contêiner é usado para:
  - Armazenar os arquivos de dados de backup para tabelas do Spark
  - Logs de execução para trabalhos do Spark

- Usar o portal do Azure, atribua os grupos de segurança às funções a seguir no CNT1

  - Atribua **WS1\_WSAdmins** à função **Colaborador de Dados do Blob de Armazenamento**
  - Atribua **WS1\_SparkAdmins** à função **Colaborador de Dados do Blob de Armazenamento**
  - Atribua **WS1\_SQLAdmins** à função **Colaborador de Dados do Blob de Armazenamento**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>ETAPA 3: Criar e configurar o workspace do Synapse

 No portal do Azure, crie um workspace do Synapse:

- Selecionar sua assinatura
- Selecione seu grupo de recursos-você precisa ter acesso a um grupo de recursos para o qual você recebeu a função **proprietário** .
- Dê ao workspace o nome WS1
- Escolha STG1 para a conta de armazenamento-. Escolha CNT1 para o contêiner que está sendo usado como "filesystem".
- Abra WS1 no Synapse Studio
- Selecione **Gerenciar** > **Controle de Acesso** e atribua os grupos de segurança às funções do Synapse a seguir.
  - Atribua **WS1\_WSAdmins** a administradores do workspace do Synapse
  - Atribua **WS1\_SparkAdmins** a administradores do Spark do Synapse
  - Atribua **WS1\_SQLAdmins** aos administradores do SQL do Synapse

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>ETAPA 4: Configurar Data Lake Storage Gen2 para uso pelo espaço de trabalho Synapse

O workspace do Synapse precisa de acesso ao STG1 e CNT1 para que possa executar pipelines e executar tarefas do sistema.

- Abrir o portal do Azure
- Localize o STG1
- Navegue até o CNT1
- Verifique se a MSI (Identidade de Serviço Gerenciado) para WS1 foi atribuída à função **Colaborador de Dados do Blob de Armazenamento** no CNT1
  - Se você não a vir atribuída, atribua-a.
  - A MSI tem o mesmo nome que o workspace. Nesse caso, seria &quot;WS1&quot;.

## <a name="step-5-configure-admin-access-for-synapse-sql"></a>ETAPA 5: configurar o acesso de administrador para Synapse SQL

- Abrir o portal do Azure
- Navegar até WS1
- Em **configurações** , selecione **administrador do SQL Active Directory**
- Selecione **definir administrador** e escolha WS1 \_ sqladmins

## <a name="step-6-maintain-access-control"></a>ETAPA 6: manter o controle de acesso

A configuração foi concluída.

Agora, para gerenciar o acesso para usuários, você pode adicionar usuários aos três grupos de segurança e removê-los de lá.

Embora você possa atribuir usuários manualmente às funções do Synapse, se fizer isso, ele não vai configurar as coisas de maneira consistente. Em vez disso, apenas adicione usuários aos grupos de segurança ou remova-os de lá.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>ETAPA 7: Verificar o acesso de usuários nas funções

Os usuários em cada função precisam concluir as seguintes etapas:

| Número | Etapa | Administradores do workspace | Administradores do Spark | Administradores do SQL |
| --- | --- | --- | --- | --- |
| 1 | Carregar um arquivo parquet no CNT1 | YES | YES | YES |
| 2 | Ler o arquivo parquet usando o pool SQL sem servidor | YES | Não | YES |
| 3 | Criar um pool de Apache Spark sem servidor | SIM [1] | SIM [1] | Não  |
| 4 | Ler o arquivo parquet com um Notebook | YES | YES | Não |
| 5 | Criar um pipeline com base no Notebook e disparar o pipeline para executar agora | YES | Não | Não |
| 6 | Criar um pool SQL dedicado e executar um script SQL, como &quot; Select 1&quot; | SIM [1] | Não | SIM [1] |

> [!NOTE]
> [1] Para criar pools do SQL ou do Spark, o usuário deve ter pelo menos a função Colaborador no workspace do Synapse.
>
 
>[!TIP]
> - Algumas etapas não serão permitidas deliberadamente dependendo da função.
> - Tenha em mente que algumas tarefas poderão falhar se a segurança não tiver sido totalmente configurada. Essas tarefas estão anotadas na tabela.

## <a name="step-8-network-security"></a>ETAPA 8: Segurança de rede

Configurar o firewall do workspace, a rede virtual e o [Link Privado](../../azure-sql/database/private-endpoint-overview.md).

## <a name="step-9-completion"></a>ETAPA 9: Completion

Agora seu workspace está totalmente configurado e protegido.

## <a name="how-roles-interact-with-synapse-studio"></a>Como as funções interagem com o Synapse Studio

O Synapse Studio se comportará de maneira diferente com base nas funções de usuário. Alguns itens poderão ser ocultados ou desabilitados se um usuário não estiver atribuído a funções que fornecem o acesso apropriado. A tabela a seguir resume o efeito no Synapse Studio.

| Tarefa | Administradores do workspace | Administradores do Spark | Administradores do SQL |
| --- | --- | --- | --- |
| Abrir o Synapse Studio | YES | YES | YES |
| Exibir hub inicial | YES | YES | YES |
| Exibir hub de dados | YES | YES | YES |
| Hub de dados/Confira contas e contêineres do ADLS Gen2 vinculados | SIM [1] | SIM [1] | SIM [1] |
| Hub de dados/Confira Bancos de Dados | YES | YES | YES |
| Hub de dados/Confira objetos em bancos de dados | YES | YES | YES |
| Dados de data Hub/acesso em bancos de dados SQL do Synapse | YES   | Não   | YES   |
| Dados de data Hub/acesso em bancos de dados de pool SQL sem servidor | SIM [2]  | Não  | SIM [2]  |
| Hub de dados/Acessar dados em bancos de dados do Spark | SIM [2] | SIM [2] | SIM [2] |
| Usar Desenvolver hub | YES | YES | YES |
| Desenvolver hub/criar scripts SQL | YES | Não | YES |
| Desenvolver hub/criar definições de trabalho do Spark | YES | YES | Não |
| Desenvolver hub/criar notebooks | YES | YES | Não |
| Desenvolver hub/criar fluxos de dados | YES | Não | Não |
| Usar o Orquestrar hub | YES | YES | YES |
| Orquestrar hub/usar pipelines | YES | Não | Não |
| Usar o Gerenciar hub | YES | YES | YES |
| Gerenciar Hub/Synapse SQL | YES | Não | YES |
| Gerenciar hub/pools do Spark | YES | YES | Não |
| Gerenciar hub/gatilhos | YES | Não | Não |
| Gerenciar o Hub/serviços vinculados | YES | YES | YES |
| Gerenciar o Hub/controle de acesso (atribuir usuários a funções de workspace do Synapse) | YES | Não | Não |
| Gerenciar o Hub/runtimes de integração | YES | YES | YES |
| Usar o Monitorar hub | YES | YES | YES |
| Monitorar hub/orquestração/execuções de pipeline  | YES | Não | Não |
| Monitorar hub/orquestração/execuções de gatilho  | YES | Não | Não |
| Monitorar hub/orquestração/runtimes de integração  | YES | YES | YES |
| Monitorar hub/atividades/aplicativos Spark | YES | YES | Não  |
| Monitorar hub/atividades/solicitações de SQL | YES | Não | YES |
| Monitorar hub/atividades/pools do Spark | YES | YES | Não  |
| Monitorar hub/gatilhos | YES | Não | Não |
| Gerenciar o Hub/serviços vinculados | YES | YES | YES |
| Gerenciar o Hub/controle de acesso (atribuir usuários a funções de workspace do Synapse) | YES | Não | Não |
| Gerenciar o Hub/runtimes de integração | YES | YES | YES |


> [!NOTE]
> [1] O acesso a dados em contêineres depende do controle de acesso no ADLS Gen2. </br>
> [2] As tabelas OD do SQL e as tabelas do Spark armazenam os dados no ADLS Gen2, e o acesso requer as permissões apropriadas no ADLS Gen2.

## <a name="next-steps"></a>Próximas etapas

Criar um [Workspace do Synapse](../quickstart-create-workspace.md)
