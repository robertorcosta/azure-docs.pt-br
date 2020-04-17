---
title: Configure chaves gerenciadas pelo cliente no Azure Sentinel| Microsoft Docs
description: Saiba como configurar chaves gerenciadas pelo cliente (CMK) no Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2019
ms.author: yelevin
ms.openlocfilehash: 5eed208ed79aeab4e46ed90dd4d340a8b445be96
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461626"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configure a chave gerenciada pelo cliente do Azure Sentinel


Este artigo fornece informações de fundo e etapas para configurar uma chave gerenciada pelo cliente (CMK) para o Azure Sentinel. O CMK permite que todos os dados salvos ou enviados ao Azure Sentinel sejam criptografados em todos os recursos de armazenamento relevantes com uma chave Azure Key Vault criada ou de propriedade de você.

> [!NOTE]
> -   O recurso CMK do Azure Sentinel é fornecido apenas para clientes que são **novos** e o acesso a esse recurso é controlado pelo registro de recursos do Azure.Você pode solicitar acesso azuresentinelCMK@microsoft.compor contato, e como a capacidade está disponível, solicitações pendentes serão aprovadas.
> -   O azure Sentinel CMK está disponível apenas nas regiões leste dos EUA, Oeste dos EUA 2 e Centro-Sul dos EUA.
> -   O recurso CMK só está disponível para clientes que enviam 1 TB por dia ou mais. Você receberá informações sobre preços adicionais quando solicitar à Microsoft para prover CMK em sua assinatura do Azure. Saiba mais sobre [o carregamento do Log Analytics.](../azure-monitor/platform/customer-managed-keys.md#disclaimers)

## <a name="how-cmk-works"></a>Como funciona o CMK 

A solução do Azure Sentinel usa vários recursos de armazenamento para coleta de log e recursos, estes incluem o Log Analytics e outros recursos de armazenamento. Como parte da configuração cmk do Azure Sentinel, você também terá que configurar as configurações do CMK nos recursos de armazenamento relacionados. Os dados salvos em recursos de armazenamento que não o Log Analytics também serão criptografados.

> [!NOTE]
> Se você habilitar cmk no Azure Sentinel, qualquer recurso de visualização pública que não tenha suporte ao CMK não será habilitado.

## <a name="enable-cmk"></a>Habilitar CMK 

Para prover CMK, siga estas etapas: 

1.  Crie um Cofre de Chaves Azure e armazene a chave.

2.  Habilite o CMK no seu espaço de trabalho do Log Analytics.

3.  Registre-se no Cosmos DB.

4.  Adicione uma política de acesso à instância do Cofre de Chaves do Azure.

5.  Habilite o CMK no Azure Sentinel.

6.  Habilite o Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>PASSO 1: Crie um cofre de chaves azure e armazene a chave

1.  [Crie o recurso Azure Key Vault](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)e, em seguida, gere ou importe uma chave a ser usada para criptografia de dados.
    > [!NOTE]
    >  O Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso.

1.  [Ativar opções de recuperação:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Certifique-se de que [o Soft Delete](../key-vault/general/overview-soft-delete.md) está ligado.

    -   Ligue a [proteção Expurgo](../key-vault/general/overview-soft-delete.md#purge-protection) para se proteger contra a exclusão forçada do cofre secreto mesmo após a exclusão suave.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>PASSO 2: Habilite o CMK no seu espaço de trabalho do Log Analytics

Siga as instruções na [configuração de chave gerenciada pelo cliente do Azure Monitor](../azure-monitor/platform/customer-managed-keys.md) para criar um espaço de trabalho CMK que será usado como espaço de trabalho do Azure Sentinel nas etapas seguintes.

### <a name="step-3-register-for-cosmos-db"></a>PASSO 3: Inscreva-se no Cosmos DB

O Azure Sentinel trabalha com o Cosmos DB como um recurso de armazenamento adicional. Certifique-se de se registrar no Cosmos DB.

Siga a instrução Cosmos DB para registrar o provedor de recursos [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) para sua assinatura do Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>PASSO 4: Adicione uma política de acesso à sua instância do Cofre chave do Azure

Certifique-se de adicionar acesso do Cosmos DB à sua instância do Azure Key Vault. Siga a instrução Cosmos DB para [adicionar uma política de acesso à instância do Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) com o diretor do Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>PASSO 5: Habilite o CMK no Azure Sentinel

O recurso CMK do Azure Sentinel é fornecido a novos clientes somente após receber acesso diretamente do grupo de produtos Azure. Use seus contatos na Microsoft para receber aprovação da equipe do Azure Sentinel para habilitar o CMK em sua solução.

Depois de obter aprovação, você será solicitado a fornecer as seguintes informações para ativar o recurso CMK.

-  ID do espaço de trabalho no qual você deseja ativar o CMK

-  URL do Cofre de Chaves: Copie o "Identificador de Chaves" da chave até a última barra para frente:  
    

    ![identificador de chave](./media/customer-managed-keys/key-identifier.png)

    A equipe do Azure Sentinel habilitará o recurso Azure Sentinel CMK para o seu espaço de trabalho fornecido.

-  Verificação da equipe de produtos do Azure Sentinel de que você foi aprovado para usar este recurso. Você deve ter isso antes de prosseguir.

### <a name="step-6-enable-azure-sentinel"></a>PASSO 6: Habilite o Azure Sentinel


Vá para o portal Azure e habilite o Azure Sentinel no espaço de trabalho no qual você configura o CMK. Para obter mais informações, consulte [Azure Sentinel Onboarding](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revogação ou exclusão da chave de criptografia de chave


No caso de um usuário revogar a chave de criptografia da chave, excluindo-a ou removendo o acesso ao Azure Sentinel, dentro de uma hora, o Azure Sentinel honrará a mudança e se comportará como se os dados não estiverem mais disponíveis. Neste ponto, qualquer operação realizada que use recursos de armazenamento persistentes, como ingestão de dados, alterações persistentes de configuração e criação de incidentes, será evitada. Os dados armazenados anteriormente não serão excluídos, mas permanecerão inacessíveis. Os dados inacessíveis são regidos pela política de retenção de dados e serão eliminados de acordo com essa política.

A única operação possível depois que a chave de criptografia é revogada ou excluída é a exclusão da conta.

Se o acesso for restaurado após a revogação, o Azure Sentinel restaurará o acesso aos dados dentro de uma hora.

Para entender mais sobre como isso funciona no Azure Monitor, consulte [a revogação do Azure Monitor CMK](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Rotação de chave de criptografia de chave


O Azure Sentinel e o Log Analytics suportam a rotação de chaves. Quando um usuário realiza a rotação de chaves no Key Vault, o Azure Sentinel suporta a nova chave dentro de uma hora.

No Key Vault, você pode executar a rotação de chaves criando uma nova versão da chave:

![rotação de chaves](./media/customer-managed-keys/key-rotation.png)

Você pode desativar a versão anterior da chave após 24 horas ou depois que os registros de auditoria do Azure Key Vault não mostrarem mais nenhuma atividade que use a versão anterior.

Se você usar a mesma chave no Azure Sentinel e no Log Analytics, é necessário realizar a rotação de chaves, você deve atualizar explicitamente o recurso de cluster no Log Analytics com a nova versão-chave do Azure Key Vault. Para obter mais informações, consulte [a rotação cmk do Monitor Azure](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu como configurar uma chave gerenciada pelo cliente no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use as cadernetas](tutorial-monitor-your-data.md) para monitorar seus dados.

