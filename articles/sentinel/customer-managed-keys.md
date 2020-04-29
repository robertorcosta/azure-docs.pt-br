---
title: Configurar chaves gerenciadas pelo cliente no Azure Sentinel | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461626"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Configurar a chave gerenciada pelo cliente do Azure Sentinel


Este artigo fornece informações básicas e etapas para configurar uma chave gerenciada pelo cliente (CMK) para o Azure Sentinel. O CMK permite que todos os dados salvos ou enviados ao Azure Sentinel sejam criptografados em todos os recursos de armazenamento relevantes com uma chave de Azure Key Vault criada ou pertencente a você.

> [!NOTE]
> -   O recurso de CMK do Azure Sentinel é fornecido somente para clientes que são **novos** e o acesso a esse recurso é controlado pelo registro de recursos do Azure.Você pode solicitar acesso entrando em contato azuresentinelCMK@microsoft.come, à medida que a capacidade estiver disponível, solicitações pendentes serão aprovadas.
> -   O recurso CMK do Azure Sentinel está disponível apenas nas regiões leste dos EUA, oeste dos EUA 2 e EUA Central do Sul.
> -   O recurso CMK só está disponível para clientes que enviam 1 TB por dia ou mais. Você receberá informações sobre preços adicionais quando aplicar à Microsoft para provisionar o CMK em sua assinatura do Azure. Saiba mais sobre o carregamento de [log Analytics](../azure-monitor/platform/customer-managed-keys.md#disclaimers) .

## <a name="how-cmk-works"></a>Como o CMK funciona 

A solução Sentinela do Azure usa vários recursos de armazenamento para a coleta e os recursos de log, incluindo Log Analytics e outros recursos de armazenamento. Como parte da configuração do Azure Sentinel CMK, você precisará definir as configurações de CMK nos recursos de armazenamento relacionados também. Os dados salvos em recursos de armazenamento diferentes de Log Analytics também serão criptografados.

> [!NOTE]
> Se você habilitar o CMK no Azure Sentinel, qualquer recurso de visualização pública que não ofereça suporte a CMK não será habilitado.

## <a name="enable-cmk"></a>Habilitar CMK 

Para provisionar o CMK, siga estas etapas: 

1.  Criar um Azure Key Vault e armazenar a chave.

2.  Habilite CMK em seu espaço de trabalho Log Analytics.

3.  Registre-se para Cosmos DB.

4.  Adicione uma política de acesso à sua instância de Azure Key Vault.

5.  Habilite o CMK no Azure Sentinel.

6.  Habilite o Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>ETAPA 1: criar um Azure Key Vault e armazenar a chave

1.  [Crie Azure Key Vault recurso](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal?view=azs-1910)e, em seguida, gere ou importe uma chave para ser usada para criptografia de dados.
    > [!NOTE]
    >  Azure Key Vault deve ser configurado como recuperável para proteger sua chave e o acesso.

1.  [Ativar opções de recuperação:](../key-vault/general/best-practices.md#turn-on-recovery-options)

    -   Verifique se a [exclusão reversível](../key-vault/general/overview-soft-delete.md) está ativada.

    -   Ative a [proteção de limpeza](../key-vault/general/overview-soft-delete.md#purge-protection) para proteger contra a exclusão forçada do segredo/cofre mesmo após a exclusão reversível.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>ETAPA 2: habilitar o CMK em seu espaço de trabalho Log Analytics

Siga as instruções em [Azure monitor configuração de chave gerenciada pelo cliente](../azure-monitor/platform/customer-managed-keys.md) para criar um espaço de trabalho CMK que será usado como o espaço de trabalho do Azure Sentinel nas etapas a seguir.

### <a name="step-3-register-for-cosmos-db"></a>ETAPA 3: registrar-se para Cosmos DB

O Azure Sentinel funciona com Cosmos DB como um recurso de armazenamento adicional. Certifique-se de se registrar no Cosmos DB.

Siga as instruções de Cosmos DB para [registrar o](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) provedor de recursos de Azure Cosmos DB para sua assinatura do Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>ETAPA 4: adicionar uma política de acesso à sua instância do Azure Key Vault

Certifique-se de adicionar acesso de Cosmos DB à instância de Azure Key Vault. Siga a instrução de Cosmos DB para [Adicionar uma política de acesso à instância de Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) com a entidade de segurança Azure Cosmos DB.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>ETAPA 5: habilitar o CMK no Azure Sentinel

O recurso CMK do Azure Sentinel é fornecido para novos clientes somente após o recebimento do acesso diretamente do grupo de produtos do Azure. Use seus contatos na Microsoft para receber aprovação da equipe do Sentinela do Azure para habilitar o CMK em sua solução.

Depois de obter aprovação, você será solicitado a fornecer as informações a seguir para habilitar o recurso CMK.

-  ID do espaço de trabalho na qual você deseja habilitar CMK

-  URL de Key Vault: Copie o "identificador de chave" da chave até a última barra:  
    

    ![identificador de chave](./media/customer-managed-keys/key-identifier.png)

    A equipe do Azure Sentinel habilitará o recurso CMK do Azure Sentinel para seu espaço de trabalho fornecido.

-  Verificação da equipe de produto do Azure Sentinel que você aprovou para usar esse recurso. Você deve ter isso antes de continuar.

### <a name="step-6-enable-azure-sentinel"></a>ETAPA 6: habilitar o Azure Sentinel


Vá para a portal do Azure e habilite o Azure Sentinel no espaço de trabalho no qual você configurou o CMK. Para obter mais informações, consulte [integração do Azure Sentinel](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Revogação ou exclusão da chave de criptografia de chave


No caso de um usuário revogar a chave de criptografia de chave, excluindo-a ou removendo o acesso para o Azure Sentinel, em uma hora, o Azure Sentinel honrará a alteração e se comportará como se os dados não esficarem mais disponíveis. Neste ponto, qualquer operação executada que use recursos de armazenamento persistentes, como ingestão de dados, alterações de configuração persistente e criação de incidentes, será impedida. Os dados armazenados anteriormente não serão excluídos, mas permanecerão inacessíveis. Os dados inacessíveis são regidos pela política de retenção de dados e serão limpos de acordo com essa política.

A única operação possível depois que a chave de criptografia é revogada ou excluída é a exclusão da conta.

Se o acesso for restaurado após a revogação, o Azure Sentinel irá restaurar o acesso aos dados em uma hora.

Para entender mais sobre como isso funciona em Azure Monitor, consulte [Azure monitor revogação de CMK](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-revocation).

## <a name="key-encryption-key-rotation"></a>Rotação da chave de criptografia de chave


O Azure Sentinel e Log Analytics oferecem suporte à rotação de chaves. Quando um usuário executa a rotação de chaves no Key Vault, o Azure Sentinel dá suporte à nova chave em uma hora.

No Key Vault, você pode executar a rotação de chaves criando uma nova versão da chave:

![rotação de chaves](./media/customer-managed-keys/key-rotation.png)

Você pode desabilitar a versão anterior da chave após 24 horas ou depois que os logs de auditoria do Azure Key Vault não mostram mais nenhuma atividade que usa a versão anterior.

Se você usar a mesma chave no Azure Sentinel e no Log Analytics, é necessário executar a rotação de chaves para atualizar explicitamente o recurso de cluster no Log Analytics com a nova versão de chave Azure Key Vault. Para obter mais informações, consulte [Azure monitor rotação CMK](../azure-monitor/platform/customer-managed-keys.md#cmk-kek-rotation).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu a configurar uma chave gerenciada pelo cliente no Azure Sentinel. Para saber mais sobre o Azure Sentinel, consulte os seguintes artigos:
- Saiba como [obter visibilidade dos seus dados e possíveis ameaças](quickstart-get-visibility.md).
- Comece a [detectar ameaças com o Azure Sentinel](tutorial-detect-threats.md).
- [Use pastas de trabalho](tutorial-monitor-your-data.md) para monitorar seus dados.

