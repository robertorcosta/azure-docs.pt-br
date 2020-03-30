---
title: Proteção avançada contra ameaças para Azure Cosmos DB
description: Saiba como o Azure Cosmos DB fornece criptografia de dados em repouso e como ele é implementado.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.author: memildin
author: memildin
manager: rkarlin
ms.openlocfilehash: bcc1c6ffe7cdec4aed325a67969235ae993a5109
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614843"
---
# <a name="advanced-threat-protection-for-azure-cosmos-db-preview"></a>Proteção avançada contra ameaças para Azure Cosmos DB (Preview)

O Advanced Threat Protection for Azure Cosmos DB fornece uma camada adicional de inteligência de segurança que detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas do Azure Cosmos DB. Essa camada de proteção permite que você aborde ameaças, mesmo sem ser um especialista em segurança, e integre-as com sistemas centrais de monitoramento de segurança.

Os alertas de segurança são acionados quando ocorrem anomalias na atividade. Esses alertas de segurança são integrados ao [Azure Security Center](https://azure.microsoft.com/services/security-center/), e também são enviados por e-mail para administradores de assinatura, com detalhes da atividade suspeita e recomendações sobre como investigar e remediar as ameaças.

> [!NOTE]
>
> * O Advanced Threat Protection for Azure Cosmos DB está disponível atualmente apenas para a API SQL.
> * O Advanced Threat Protection for Azure Cosmos DB não está disponível no governo do Azure e nas regiões de nuvem soberanas.

Para uma experiência completa de investigação dos alertas de segurança, recomendamos permitir o [registro de diagnóstico no Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/logging), que registra operações no próprio banco de dados, incluindo operações CRUD em todos os documentos, contêineres e bancos de dados.

## <a name="threat-types"></a>Tipos de ameaça

O Advanced Threat Protection for Azure Cosmos DB detecta atividades anômalas indicando tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados. No momento, ele pode acionar os seguintes alertas:

- **Acesso a partir de locais incomuns**: Este alerta é acionado quando há uma alteração no padrão de acesso a uma conta do Azure Cosmos, onde alguém se conectou ao ponto final do Azure Cosmos DB a partir de uma localização geográfica incomum. Em alguns casos, o alerta detecta uma ação legítima, o que significa uma nova operação de manutenção de aplicativo ou desenvolvedor. Em outros casos, o alerta detecta uma ação maliciosa de um ex-funcionário, atacante externo, etc.

- **Extração de dados incomum**: Este alerta é acionado quando um cliente está extraindo uma quantidade incomum de dados de uma conta Azure Cosmos DB. Isso pode ser o sintoma de alguma exfiltração de dados realizada para transferir todos os dados armazenados na conta para um armazenamento de dados externo.

## <a name="set-up-advanced-threat-protection"></a>Configure proteção avançada contra ameaças

### <a name="set-up-atp-using-the-portal"></a>Configurar ATP usando o portal

1. Inicie o portal Azure em [https://portal.azure.com](https://portal.azure.com/).

2. Na conta Azure Cosmos DB, no menu **Configurações,** selecione **Segurança avançada**.

    ![Configurar ATP](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp.png)

3. Na lâmina de configuração **de segurança avançada:**

    * Clique na opção **Proteção avançada contra ameaças** para defini-la **como ON**.
    * Clique em **Salvar** para salvar a política de Proteção avançada contra ameaças nova ou atualizada.   

### <a name="set-up-atp-using-rest-api"></a>Configurar ATP usando a API REST

Use comandos API rest para criar, atualizar ou obter a configuração de Proteção avançada de ameaças para uma conta DB específica do Azure Cosmos.

* [Proteção avançada contra ameaças - Criar](https://go.microsoft.com/fwlink/?linkid=2099745)
* [Proteção avançada contra ameaças - Obter](https://go.microsoft.com/fwlink/?linkid=2099643)

### <a name="set-up-atp-using-azure-powershell"></a>Configurar atp usando o Azure PowerShell

Use os seguintes cmdlets powershell:

* [Habilitar a Proteção Avançada contra Ameaças](https://go.microsoft.com/fwlink/?linkid=2099607&clcid=0x409)
* [Obtenha proteção avançada contra ameaças](https://go.microsoft.com/fwlink/?linkid=2099608&clcid=0x409)
* [Desativar proteção avançada contra ameaças](https://go.microsoft.com/fwlink/?linkid=2099709&clcid=0x409)

### <a name="using-azure-resource-manager-templates"></a>Usando modelos do Gerenciador de Recursos do Azure

Use um modelo do Azure Resource Manager para configurar o Cosmos DB com o Advanced Threat Protection ativado.
Para obter mais informações, consulte [Criar uma conta do CosmosDB com proteção avançada contra ameaças.](https://azure.microsoft.com/resources/templates/201-cosmosdb-advanced-threat-protection-create-account/)

### <a name="using-azure-policy"></a>Usando a política do Azure

Use uma diretiva do Azure para habilitar a proteção avançada contra ameaças para o Cosmos DB.

1. Inicie a página **Azure Policy - Definições** e procure a política Deploy Advanced Threat Protection for **Cosmos DB.**

    ![Política de pesquisa](./media/cosmos-db-advanced-threat-protection/cosmos-db.png) 

1. Clique na **política Implantar Proteção avançada contra ameaças para cosmosDB** e, em seguida, clique **em Atribuir**.

    ![Selecionar assinatura ou grupo](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-policy.png)


1. No campo **Escopo,** clique nos três pontos, selecione uma assinatura ou grupo de recursos do Azure e clique em **Selecionar**.

    ![Página definições de políticas](./media/cosmos-db-advanced-threat-protection/cosmos-db-atp-details.png)


1. Digite os outros parâmetros e clique **em Atribuir**.

## <a name="manage-atp-security-alerts"></a>Gerenciar alertas de segurança ATP

Quando ocorrem anomalias de atividade do Azure Cosmos DB, um alerta de segurança é acionado com informações sobre o evento de segurança suspeito. 

 A partir do Azure Security Center, você pode revisar e gerenciar seus [alertas](../security-center/security-center-alerts-overview.md)de segurança atuais.  Clique em um alerta específico no [Security Center](https://ms.portal.azure.com/#blade/Microsoft_Azure_Security/SecurityMenuBlade/0) para visualizar possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial. A imagem a seguir mostra um exemplo de detalhes de alerta fornecidos no Security Center.

 ![Detalhes da ameaça](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert-details.png)

Uma notificação por e-mail também é enviada com os detalhes de alerta e ações recomendadas. A imagem a seguir mostra um exemplo de um e-mail de alerta.

 ![Detalhes do Alerta](./media/cosmos-db-advanced-threat-protection/cosmos-db-alert.png)

## <a name="cosmos-db-atp-alerts"></a>Alertas de ATP do Cosmos DB

 Para ver uma lista dos alertas gerados ao monitorar as contas do Azure Cosmos DB, consulte a seção [de alertas do Cosmos DB](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos) na documentação do Azure Security Center.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [o registro de diagnóstico no Azure Cosmos DB](cosmosdb-monitor-resource-logs.md)
* Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
