---
title: Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure
description: Saiba como integrar o Key Vault com a Grade de Eventos do Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 3b24da4d988554da240baba2984df44ff4744aaf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464095"
---
# <a name="how-to-receive-and-respond-to-key-vault-notifications-with-azure-event-grid-preview"></a>Como: Receber notificações do cofre de chaves e responder a elas com a Grade de Eventos do Azure (versão prévia)

A integração do Key Vault com a Grade de Eventos do Azure, atualmente em versão prévia, permite que os usuários sejam notificados quando o status de um segredo armazenado no cofre de chaves é alterado. Para obter uma visão geral do recurso, confira [Monitoramento do Key Vault com a Grade de Eventos do Azure](event-grid-overview.md).

Esse guia mostrará como receber notificações de Key Vault por meio da Grade de Eventos do Azure e como responder a alterações de status com a Automação do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
- Um cofre de chaves na assinatura do Azure. Você pode criar rapidamente um novo cofre de chaves seguindo as etapas em [Definir e recuperar um segredo do Azure Key Vault usando a CLI do Azure](quick-create-cli.md)

## <a name="concepts"></a>Conceitos

A Grade de Eventos do Azure é um serviço de eventos para a nuvem. Neste guia, você assinará eventos para o cofre de chaves e roteará eventos para a Automação do Azure. Quando um dos segredos no cofre de chaves está prestes a expirar, a Grade de Eventos é notificada sobre a alteração de status e faz um HTTP POST para o ponto de extremidade. Um web hook dispara então uma execução do script do PowerShell da Automação do Azure.

![image](media/image1.png)

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

Crie uma conta da Automação do Azure por meio do [portal do Azure](https://portal.azure.com).

1.  Acesse portal.azure.com e faça logon em sua assinatura.

1.  Na caixa de pesquisa, digite "Contas de Automação".

1.  Na seção "Serviços" do menu suspenso da barra de pesquisa, selecione "Contas de Automação".

1.  Clique em Adicionar.

    ![](media/image2.png)

1.  Preencha as informações necessárias na folha "Adicionar Conta de Automação" e selecione "Criar".

## <a name="create-a-runbook"></a>Criar um runbook

Depois que sua conta da Automação do Azure estiver pronta, crie um runbook.

![](media/image3.png)

1.  Selecione a conta de Automação que você acabou de criar.

1.  Selecione "Runbooks" na seção Automação de Processos.

1.  Clique em "Criar um runbook".

1.  Atribua um nome ao runbook e selecione "PowerShell" como o tipo do runbook.

1.  Clique no runbook que você criou e selecione o botão "Editar".

1.  Insira o código a seguir (para fins de teste) e clique no botão "Publicar". Isso produzirá o resultado da solicitação POST recebida.

```azurepowershell
param
(
[Parameter (Mandatory = $false)]
[object] $WebhookData
)

#If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData) {

#rotate secret:
#generate new secret version in key vault
#update db/service with generated secret

#Write-Output "WebhookData <$WebhookData>"
Write-Output $WebhookData.RequestBody
}
else
{
# Error
write-Error "No input data found." 
}
```

![](media/image4.png)

## <a name="create-a-webhook"></a>Criar um webhook

Agora, crie um webhook para disparar o runbook recém-criado.

1.  Selecione "Webhooks" na seção de recursos do runbook que você acabou de publicar.

1.  Clique em "Adicionar Webhook".

    ![](media/image5.png)

1.  Selecione "Criar novo Webhook".

1. Nomeie o webhook, defina uma data de validade e **copie a URL**.

    > [!IMPORTANT] 
    > Você não pode exibir a URL depois de criá-la. Lembre-se de salvar uma cópia em uma localização segura, em que você possa acessá-la no restante deste guia.

1. Clique em "Parâmetros e configurações de execução" e selecione "OK". Não insira nenhum parâmetro. Isso habilitará o botão "Criar".

1. Selecione "OK" e, então, selecione "Criar".

    ![](media/image6.png)

## <a name="create-an-event-grid-subscription"></a>Criar uma assinatura na Grade de Eventos

Crie uma assinatura de Grade de Eventos por meio do [portal do Azure](https://portal.azure.com).

1.  Abra o portal do Azure usando o seguinte link: https://portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true

1.  Vá para o cofre de chaves e selecione a guia "Eventos". Se você não conseguir ver a guia Eventos, verifique se você está usando a [versão prévia do portal](https://ms.portal.azure.com/?Microsoft_Azure_KeyVault_ShowEvents=true&Microsoft_Azure_EventGrid_publisherPreview=true).

    ![](media/image7.png)

1.  Clique no botão "+ Assinatura de Evento".

1.  Crie um nome descritivo para a assinatura.

1.  Escolha "Esquema da Grade de Eventos".

1.  O "Recurso de Tópico" deve ser o cofre de chaves cujas alterações de status você deseja monitorar.

1.  Para "Filtrar para Tipos de Evento", deixe todos os itens marcados ("9 selecionados").

1.  Para "Tipo de Ponto de Extremidade", selecione "Webhook".

1.  Selecione "Selecionar um ponto de extremidade". No novo painel de contexto, cole a URL do webhook da etapa [Criar um webhook](#create-a-webhook) no campo "Ponto de Extremidade do Assinante".

1.  Selecione "Confirmar Seleção" no painel de contexto.

1.  Selecione "Criar".

    ![](media/image8.png)

## <a name="test-and-verify"></a>Testar e verificar

Verifique se a sua assinatura da Grade de Eventos está definida adequadamente.  Esse teste pressupõe que você assinou a notificação "Nova Versão Secreta Criada" em [Criar uma assinatura de Grade de Eventos](#create-an-event-grid-subscription) e que você tem os privilégios necessários para criar uma nova versão de um segredo em um cofre de chaves.

![](media/image9.png)

![](media/image10.png)

1.  Vá para o seu cofre de chaves no portal do Azure

1.  Crie um novo segredo. Para fins de teste, defina a data de vencimento como o dia seguinte.

1.  Navegue até a guia eventos em seu cofre de chaves.

1.  Selecione a assinatura da Grade de Eventos que você criou.

1.  Em métricas, confira se um evento foi capturado. Dois eventos são esperados: SecretNewVersion e SecretNearExpiry. Isso valida que a Grade de Eventos capturou com êxito a alteração de status do segredo em seu cofre de chaves.

    ![](media/image11.png)

1.  Vá para sua conta da Automação do Azure.

1.  Selecione a guia "Runbooks" e selecione o runbook que você criou.

1.  Selecione a guia "Webhooks" e confirme se o carimbo de data/hora "disparado pela última vez" está dentro de 60 segundos de quando você criou o novo segredo.  Isso confirma que a Grade de Eventos fez um POST no webhook com os detalhes do evento da alteração de status no cofre de chaves e o webhook foi disparado.

    ![](media/image12.png)

1. Retorne ao seu runbook e selecione a guia "Visão geral".

1. Examine a lista Trabalhos Recentes. Você deverá ver que um trabalho foi criado e que o status dele é concluído.  Isso confirma que o webhook disparou o runbook para iniciar a execução do respectivo script.

    ![](media/image13.png)

1. Selecione o trabalho recente e examine a solicitação POST enviada da Grade de Eventos para o webhook. Examine o JSON e verifique se os parâmetros para o cofre de chaves e o tipo de evento estão corretos. Se o parâmetro "tipo de evento" no objeto JSON corresponder ao evento que ocorreu no cofre de chaves (neste exemplo, Microsoft.KeyVault.SecretNearExpiry), o teste foi bem-sucedido.

## <a name="troubleshooting"></a>solução de problemas

### <a name="unable-to-create-event-subscription"></a>Não é possível criar uma assinatura de evento

Registre novamente a grade de eventos e o provedor do Key Vault em seus provedores de recursos de assinatura do Azure. Consulte [Provedores e tipos de recursos do Azure](../azure-resource-manager/resource-manager-supported-services.md).

## <a name="next-steps"></a>Próximas etapas

Parabéns! Se você seguiu todas as etapas acima, agora você está pronto para responder programaticamente às alterações de status dos segredos armazenados no seu cofre de chaves.

Migre para o uso desse recurso de notificação caso você esteja usando um sistema baseado em sondagem para procurar alterações de status nos segredos em seus cofres de chaves. Você também poderá substituir o script de teste em seu runbook por código para renovar programaticamente seus segredos quando eles estiverem prestes a expirar.

Saiba mais:

- [Visão geral do Azure Key Vault](key-vault-overview.md)
- [Visão geral da Grade de Eventos do Azure](../event-grid/overview.md)
- [Monitoramento do Key Vault com a Grade de Eventos do Azure (versão prévia)](event-grid-overview.md)
- [Esquema de eventos da Grade de Eventos do Azure para o Azure Key Vault (versão prévia)](../event-grid/event-schema-key-vault.md)
- [Visão geral da Automação do Azure](../automation/index.yml)
