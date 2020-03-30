---
title: Solicitar acesso just-in-time
description: Descreve como os editores de aplicativos gerenciados do Azure solicitam acesso just-in-time a um aplicativo gerenciado.
author: MSEvanhi
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: evanhi
ms.openlocfilehash: 7f475774828bcaecd471e13de994b156041323ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651378"
---
# <a name="enable-and-request-just-in-time-access-for-azure-managed-applications"></a>Habilite e solicite acesso just-in-time para aplicativos gerenciados do Azure

Os consumidores de sua aplicação gerenciada podem estar relutantes em conceder-lhe acesso permanente ao grupo de recursos gerenciados. Como editor de um aplicativo gerenciado, você pode preferir que os consumidores saibam exatamente quando você precisa acessar os recursos gerenciados. Para dar aos consumidores um maior controle sobre a concessão de acesso a recursos gerenciados, o Azure Managed Applications fornece um recurso chamado acesso just-in-time (JIT), que está atualmente em visualização.

O acesso ao JIT permite que você solicite acesso elevado aos recursos de um aplicativo gerenciado para solução de problemas ou manutenção. Você sempre tem acesso somente à leitura aos recursos, mas por um período específico de tempo você pode ter um acesso maior.

O fluxo de trabalho para a concessão de acesso é:

1. Você adiciona um aplicativo gerenciado ao mercado e especifica que o acesso jit está disponível.

1. Durante a implantação, o consumidor permite o acesso ao JIT para essa instância do aplicativo gerenciado.

1. Após a implantação, o consumidor pode alterar as configurações para acesso JIT.

1. Você envia uma solicitação de acesso quando precisa solucionar problemas ou atualizar os recursos gerenciados.

1. O consumidor aprova seu pedido.

Este artigo foca nas ações que os editores tomam para permitir o acesso ao JIT e enviar solicitações. Para saber como aprovar solicitações de acesso ao JIT, consulte [Aprovar acesso just-in-time em aplicativos gerenciados do Azure](approve-just-in-time-access.md).

## <a name="add-jit-access-step-to-ui"></a>Adicionar a etapa de acesso jit à ui

Seu arquivo CreateUiDefinition.json é exatamente como o arquivo de interface do mundo que você cria para acesso permanente, exceto que você deve incluir uma etapa que permite que os consumidores habilitem o acesso ao JIT. Para saber mais sobre a publicação da sua primeira oferta de aplicativos gerenciados no Azure Marketplace, consulte [Aplicativos Gerenciados do Azure no Marketplace](publish-marketplace-app.md).

Para oferecer suporte ao recurso JIT para sua oferta, adicione o seguinte conteúdo ao seu arquivo CreateUiDefinition.json:

Em "passos":

```json
{
    "name": "jitConfiguration",
    "label": "JIT Configuration",
    "subLabel": {
        "preValidation": "Configure JIT settings for your application",
        "postValidation": "Done"
    },
    "bladeTitle": "JIT Configuration",
    "elements": [
        {
          "name": "jitConfigurationControl",
          "type": "Microsoft.Solutions.JitConfigurator",
          "label": "JIT Configuration"
        }
    ]
}
```
 
Em "saídas":

```json
"jitAccessPolicy": "[steps('jitConfiguration').jitConfigurationControl]"
```

> [!NOTE]
> O acesso ao JIT está em pré-visualização. O esquema para configuração JIT pode mudar em iterações futuras.

## <a name="enable-jit-access"></a>Habilite o acesso jit

Ao definir sua oferta no mercado, certifique-se de habilitar o acesso JIT.

1. Faça login no [portal de publicação cloud partner](https://cloudpartner.azure.com).

1. Forneça valores para publicar seu aplicativo gerenciado no mercado. Selecione **Sim** para **Ativar o Acesso JIT?**

   ![Habilitar acesso Just-In-Time](./media/request-just-in-time-access/marketplace-enable.png)

Você adicionou uma etapa de configuração JIT à sua ia e habilitou o acesso jit na oferta de marketplace. Quando os consumidores implantam seu aplicativo gerenciado, eles podem [ativar o acesso JIT para sua instância](approve-just-in-time-access.md#enable-during-deployment).

## <a name="request-access"></a>Solicitar acesso

Quando você precisa acessar os recursos gerenciados do consumidor, você envia uma solicitação para uma função específica, hora e duração. O consumidor deve então aprovar o pedido.

Para enviar uma solicitação de acesso ao JIT:

1. Selecione **o JIT Access** para o aplicativo gerenciado que você precisa acessar.

1. Selecione **Funções Elegíveis**e selecione **Ativar** na coluna ACTION para a função desejada.

   ![Ativar solicitação de acesso](./media/request-just-in-time-access/send-request.png)

1. No formulário **Ativar função,** selecione uma hora de início e duração para que sua função esteja ativa. Selecione **Ativar** para enviar a solicitação.

   ![Ativar acesso](./media/request-just-in-time-access/activate-access.png) 

1. Veja as notificações para ver se a nova solicitação JIT é enviada com sucesso ao consumidor.

   ![Notification](./media/request-just-in-time-access/in-progress.png)

   Agora, você deve esperar que o consumidor [aprove seu pedido.](approve-just-in-time-access.md#approve-requests)

1. Para visualizar o status de todas as solicitações jit para um aplicativo gerenciado, selecione **JIT Access** and **Request History**.

   ![Exibir status](./media/request-just-in-time-access/view-status.png)

## <a name="known-issues"></a>Problemas conhecidos

O ID principal da conta que solicita o acesso ao JIT deve ser explicitamente incluído na definição do aplicativo gerenciado. A conta não pode ser incluída apenas através de um grupo especificado no pacote. Essa limitação será corrigida em uma versão futura.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a aprovação de solicitações de acesso ao JIT, consulte [Aprovar acesso just-in-time em aplicativos gerenciados do Azure](approve-just-in-time-access.md).
