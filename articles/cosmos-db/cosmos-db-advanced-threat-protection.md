---
title: Proteção Avançada contra Ameaças do Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece criptografia de dados em repouso e como ela é implementada.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: 0b37f9be5eb3313cc451e65f90d7f2713c68806d
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/26/2020
ms.locfileid: "83845977"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Proteção Avançada contra Ameaças do Azure Cosmos DB (versão prévia)

A Proteção Avançada contra Ameaças do Azure Cosmos DB oferece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar as contas do Azure Cosmos DB. Essa camada de proteção permite que você resolva as ameaças, mesmo sem ser um especialista em segurança e integrá-las aos sistemas centrais de monitoramento de segurança.

Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados à [Central de segurança do Azure](https://azure.microsoft.com/services/security-center/) e também são enviados por email para administradores de assinatura, com detalhes da atividade suspeita e recomendações sobre como investigar e corrigir as ameaças.

> [!NOTE]
>
> * A Proteção Avançada contra Ameaças do Azure Cosmos DB está disponível no momento apenas para a API do SQL.
> * A Proteção Avançada contra Ameaças do Azure Cosmos DB não está disponível no momento nas regiões de nuvem soberana e do Azure Governamental.

Para uma experiência de investigação completa dos alertas de segurança, recomendamos habilitar o [log de diagnóstico no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), que registra as operações no próprio banco de dados, incluindo operações CRUD em todos os documentos, contêineres e bancos de dados.

## <a name="threat-types"></a>Tipos de ameaça

A Proteção Avançada contra Ameaças do Azure Cosmos DB detecta atividades anômalas que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. Ela pode disparar os alertas a seguir simultaneamente:

- **Acesso de locais incomuns**: Este alerta é acionado quando há uma alteração no padrão de acesso a uma conta do Azure Cosmos, em que alguém se conectou ao ponto de extremidade do Azure Cosmos DB de um local geográfico incomum. Em alguns casos, o alerta detecta uma ação legítima, ou seja, um novo aplicativo ou operação de manutenção do desenvolvedor. Em outros casos, o alerta detecta uma ação mal-intencionada, ex-funcionário, invasor externo etc.

- **Extração de dados incomuns**: Esse alerta é acionado quando um cliente está extraindo uma quantidade incomum de dados de uma conta do Azure Cosmos DB. Isso pode ser o sintoma do vazamento de alguns dados executados para transferir todos os dados armazenados na conta para um repositório de dados externo.



## <a name="configure-advanced-threat-protection"></a>Configurar a Proteção Avançada contra Ameaças

Você pode configurar a proteção avançada contra ameaças de várias maneiras, descritas nas seções a seguir.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Na conta do Azure Cosmos DB, no menu **Configurações**, selecione **Segurança avançada**.

    ![Configurar o ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Na folha de configuração **Segurança avançada**:

    * Clique na opção **Proteção Avançada contra Ameaças** para defini-la como **ATIVADA**.
    * Clique em **Salvar** para salvar a política de Proteção avançada contra ameaças nova ou atualizada.   

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Use os comandos da API REST para criar, atualizar ou obter a configuração de Proteção Avançada contra Ameaças para uma conta específica do Azure Cosmos DB.

* [Proteção Avançada contra Ameaças - Criar](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Proteção Avançada contra Ameaças - Obter](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use os seguintes cmdlets do PowerShell:

* [Habilitar a Proteção Avançada contra Ameaças](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obter a Proteção Avançada contra Ameaças](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Desabilitar a Proteção Avançada contra Ameaças](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="arm-template"></a>[Modelo de ARM](#tab/arm-template)

Use um modelo do Azure Resource Manager (ARM) para configurar o Cosmos DB com a Proteção Avançada contra Ameaças habilitada.
Para obter mais informações, consulte [Criar uma conta do CosmosDB com a Proteção Avançada contra Ameaças](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/).

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Use uma Azure Policy para habilitar a Proteção Avançada contra Ameaças do Cosmos DB.

1. Inicie a página **Política - Definições** do Azure e procure a política **Implantar a Proteção Avançada contra Ameaças do Cosmos DB**.

    ![Pesquisar política](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Clique na política **Implantar a Proteção Avançada contra Ameaças do CosmosDB** e em **Atribuir**.

    ![Selecione Assinatura ou Grupo](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. No campo **Escopo**, clique nos três pontos, selecione uma assinatura ou grupo de recursos do Azure e clique em **Selecionar**.

    ![Página Definições de Política](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Insira os outros parâmetros e clique em **Atribuir**.




## <a name="manage-atp-security-alerts"></a>Gerenciar alertas de segurança da ATP do Azure

Quando ocorrem anomalias de atividade no Azure Cosmos DB, um alerta de segurança é acionado com informações sobre o evento de segurança suspeito. 

 Na Central de Segurança do Azure, é possível examinar e gerenciar os [alertas de segurança](../security-center/security-center-alerts-overview.md) atuais.  Clique em um determinado alerta na [Central de Segurança](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para ver as possíveis causas e ações recomendadas para investigar e atenuar a ameaça potencial. A imagem a seguir mostra um exemplo de detalhes de alerta fornecidos na Central de Segurança.

 ![Detalhes das ameaças](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Uma notificação por email também é enviada com os detalhes do alerta e as ações recomendadas. A imagem a seguir mostra um exemplo de um alerta de email.

 ![Detalhes do Alerta](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Alertas de ATP do Cosmos DB

 Para ver uma lista dos alertas gerados durante o monitoramento de contas do Azure Cosmos DB, consulte a seção [Alertas do Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) na documentação da Central de Segurança do Azure.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [Log de diagnósticos no Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
