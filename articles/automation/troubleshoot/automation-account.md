---
title: Solução de problemas de conta da Automação do Azure
description: Este artigo informa como solucionar problemas com uma conta do Azure.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 03/24/2020
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: bebc89514da7f28015da5cb145ae50bf3fccdb6a
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680090"
---
# <a name="troubleshoot-azure-automation-account-issues"></a>Solução de problemas de conta da Automação do Azure

Este artigo aborda soluções para problemas que você pode encontrar ao usar uma conta de Automação do Azure. Confira informações gerais sobre contas de automação em [Visão geral da autenticação da conta de Automação do Azure](../automation-security-overview.md).

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: não é possível registrar o Provedor de Recursos de Automação para assinaturas

### <a name="issue"></a>Problema

Ao trabalhar em sua conta de automação com recursos de gerenciamento como, por exemplo, o Gerenciamento de Atualizações, você encontra o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions:
```

### <a name="cause"></a>Causa

O Provedor de Recursos de Automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o Provedor de Recursos de Automação, execute as seguintes etapas no portal do Azure:

1. No navegador, acesse o [portal do Azure](https://portal.azure.com).

2. Em **Assinaturas**, selecione sua assinatura.   

3. Em **Configurações**, selecione **Provedores de Recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos **Microsoft.Automation** está registrado.

5. Se o provedor não estiver listado, registre-o conforme descrito em [Resolver erros de registro do provedor de recursos](/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="next-steps"></a>Próximas etapas

Se este artigo não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure nos [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport). Esta é a conta oficial do Microsoft Azure para conectar-se aos recursos certos de respostas, suporte e especialistas da comunidade do Azure.
* Registrar um incidente de suporte do Azure. Acesse o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter suporte**.