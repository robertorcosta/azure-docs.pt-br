---
title: Slots de implantação de funções do Azure
description: Aprenda a criar e usar slots de implantação com funções do Azure
author: craigshoemaker
ms.topic: reference
ms.date: 08/12/2019
ms.author: cshoe
ms.openlocfilehash: 0e8c93ea6d5c2b525ccbea2af900f100afcc3d93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75769210"
---
# <a name="azure-functions-deployment-slots"></a>Slots de implantação de funções do Azure

Os slots de implantação do Azure Functions permitem que seu aplicativo de função execute diferentes instâncias chamadas "slots". Os slots são diferentes ambientes expostos através de um ponto final disponível publicamente. Uma instância do aplicativo é sempre mapeada para o slot de produção, e você pode trocar instâncias atribuídas a um slot demanda. Os aplicativos de função em execução o plano Serviço de Aplicativos podem ter vários slots, enquanto no plano de consumo apenas um slot é permitido.

Os seguintes refletem como as funções são afetadas pela troca de slots:

- O redirecionamento do tráfego é perfeito; nenhum pedido é descartado por causa de uma troca.
- Se uma função estiver sendo executado durante uma troca, a execução continua e os gatilhos subsequentes serão encaminhados para a instância do aplicativo trocada.

> [!NOTE]
> Os slots ainda não estão disponíveis para o plano de consumo Linux.

## <a name="why-use-slots"></a>Por que usar caça-níqueis?

Há uma série de vantagens em usar slots de implantação. Os seguintes cenários descrevem usos comuns para caça-níqueis:

- **Diferentes ambientes para diferentes propósitos**: O uso de diferentes slots lhe dá a oportunidade de diferenciar instâncias do aplicativo antes de trocar para a produção ou um slot de preparação.
- **Pré-aquecimento**: Implantar em um slot em vez de diretamente para a produção permite que o aplicativo aqueça antes de entrar ao vivo. Além disso, o uso de slots reduz a latência para cargas de trabalho acionadas pelo HTTP. As instâncias são aquecidas antes da implantação, o que reduz o início a frio para funções recém-implantadas.
- **Recuos fáceis**: Após uma troca com a produção, o slot com um aplicativo anteriormente encenado agora tem o aplicativo de produção anterior. Se as alterações trocadas no slot de produção não forem como você espera, você poderá reverter imediatamente a troca para obter sua "última boa instância conhecida" de volta.

## <a name="swap-operations"></a>Operações de swap

Durante uma troca, um slot é considerado a fonte e o outro o alvo. O slot de origem tem a instância do aplicativo que é aplicado ao slot de destino. As etapas a seguir garantem que o slot de destino não tenha tempo de inatividade durante uma troca:

1. **Aplicar configurações:** As configurações do slot de destino são aplicadas a todas as instâncias do slot de origem. Por exemplo, as configurações de produção são aplicadas à instância de estadiamento. As configurações aplicadas incluem as seguintes categorias:
    - [Configurações de](#manage-settings) aplicativo específicas para slot e strings de conexão (se aplicável)
    - [Configurações de implantação contínua](../app-service/deploy-continuous-deployment.md) (se ativadas)
    - [Configurações de autenticação do Serviço](../app-service/overview-authentication-authorization.md) de Aplicativos (se ativada)

1. **Aguarde as reinicializações e a disponibilidade:** A troca aguarda todas as instâncias no slot de origem para concluir sua reinicialização e estar disponível para solicitações. Se qualquer instância não for reiniciada, a operação de swap reverte todas as alterações no slot de origem e interrompe a operação.

1. **Roteamento de atualização:** Se todas as instâncias do slot de origem forem aquecidas com sucesso, os dois slots completam a troca coisando as regras de roteamento. Após essa etapa, o slot de destino (por exemplo, o slot de produção) tem o aplicativo que já foi aquecido no slot de origem.

1. **Repetir operação:** Agora que o slot de origem tem o aplicativo de pré-swap anteriormente no slot de destino, execute a mesma operação aplicando todas as configurações e reiniciando as instâncias para o slot de origem.

Tenha em mente os seguintes pontos:

- Em qualquer ponto da operação de swap, a inicialização dos aplicativos trocados acontece no slot de origem. O slot de destino permanece on-line enquanto o slot de origem está sendo preparado, se a troca é bem sucedida ou não.

- Para trocar um slot de preparação com o slot de produção, certifique-se de que o slot de produção é *sempre* o slot de destino. Dessa forma, a operação de swap não afeta seu aplicativo de produção.

- As configurações relacionadas a fontes de eventos e vinculações precisam ser configuradas como [configurações de slot de implantação](#manage-settings) *antes de iniciar uma troca*. Marcá-los como "pegajosos" antes do tempo garante que eventos e saídas sejam direcionados para a instância adequada.

## <a name="manage-settings"></a>Gerenciar configurações

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

### <a name="create-a-deployment-setting"></a>Criar uma configuração de implantação

Você pode marcar configurações como uma configuração de implantação que a torna "pegajosa". Uma configuração pegajosa não troca com a instância do aplicativo.

Se você criar uma configuração de implantação em um slot, certifique-se de criar a mesma configuração com um valor único em qualquer outro slot envolvido em uma troca. Desta forma, enquanto o valor de uma configuração não muda, os nomes de configuração permanecem consistentes entre os slots. Essa consistência de nome garante que seu código não tente acessar uma configuração definida em um slot, mas não em outro.

Use as seguintes etapas para criar uma configuração de implantação:

- Navegue até *slots* no aplicativo de função
- Clique no nome do caça-níquel
- Em *Plataforma recursos > configurações gerais,* clique em **Configuração**
- Clique no nome de configuração que deseja ficar com o slot atual
- Clique na **caixa de slot de implantação**
- Clique em **OK**.
- Uma vez que a lâmina de ajuste desapareça, clique **em Salvar** para manter as alterações

![Configuração do slot de implantação](./media/functions-deployment-slots/azure-functions-deployment-slots-deployment-setting.png)

## <a name="deployment"></a>Implantação

Os slots ficam vazios quando você cria um slot. Você pode usar qualquer uma das tecnologias de [implantação suportadas](./functions-deployment-technologies.md) para implantar seu aplicativo em um slot.

## <a name="scaling"></a>Scaling

Todos os caça-níqueis são dimensionados para o mesmo número de trabalhadores que o slot de produção.

- Para planos de consumo, o slot é dimensionado à medida que o aplicativo de função é dimensionado.
- Para planos de Serviço de Aplicativo, o aplicativo é dimensionado para um número fixo de trabalhadores. Os slots são executados no mesmo número de trabalhadores que o plano do aplicativo.

## <a name="add-a-slot"></a>Adicionar um slot

Você pode adicionar um slot através da [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create) ou através do portal. As etapas a seguir demonstram como criar um novo slot no portal:

1. Navegue até o seu aplicativo de função e clique no **sinal de mais** ao lado de *Slots*.

    ![Adicionar slot de implantação de funções do Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-add.png)

1. Digite um nome na caixa de texto e pressione o botão **Criar.**

    ![Slot de implantação de funções do Nome Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-add-name.png)

## <a name="swap-slots"></a>Slots de troca

Você pode trocar slots através da [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap) ou através do portal. As seguintes etapas demonstram como trocar slots no portal:

1. Navegue até o aplicativo de função
1. Clique no nome do slot de origem que você deseja trocar
1. Na *guia Visão geral,* clique ![no slot de implantação **do Swap** Azure Funções](./media/functions-deployment-slots/azure-functions-deployment-slots-swap.png)
1. Verifique as configurações de configuração para o slot de implantação do **Swap** ![Swap Azure Functions](./media/functions-deployment-slots/azure-functions-deployment-slots-swap-config.png)

A operação pode levar um momento enquanto a operação de troca está sendo executada.

## <a name="roll-back-a-swap"></a>Reverter uma troca

Se uma troca resultar em um erro ou você simplesmente quiser "desfazer" uma troca, você pode reverter para o estado inicial. Para retornar ao estado pré-trocado, faça outra troca para reverter a troca.

## <a name="remove-a-slot"></a>Remova um slot

Você pode remover um slot através da [CLI](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete) ou através do portal. As etapas a seguir demonstram como remover um slot no portal:

1. Navegue até a visão geral do aplicativo de função

1. Clique no botão **Excluir**

    ![Adicionar slot de implantação de funções do Azure](./media/functions-deployment-slots/azure-functions-deployment-slots-delete.png)

## <a name="automate-slot-management"></a>Automatize o gerenciamento de slot

Usando o [Azure CLI,](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest)você pode automatizar as seguintes ações para um slot:

- [Criar](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-create)
- [Excluir](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-delete)
- [Lista](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-list)
- [Trocar](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-swap)
- [auto-swap](https://docs.microsoft.com/cli/azure/functionapp/deployment/slot?view=azure-cli-latest#az-functionapp-deployment-slot-auto-swap)

## <a name="change-app-service-plan"></a>Alterar plano de serviço de aplicativo

Com um aplicativo de função que está sendo executado um plano de Serviço de Aplicativo, você tem a opção de alterar o plano de serviço de aplicativo subjacente para um slot.

> [!NOTE]
> Você não pode alterar o plano de Serviço de Aplicativo de um slot o plano de consumo.

Use as seguintes etapas para alterar o plano de serviço de aplicativo de um slot:

1. Navegue até um slot

1. Em *Recursos de plataforma,* clique em **Todas as configurações**

    ![Alterar o plano de serviço do aplicativo](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-settings.png)

1. Clique no **plano de serviço de aplicativos**

1. Selecione um novo plano de serviço de aplicativos ou crie um novo plano

1. Clique em **OK**.

    ![Alterar o plano de serviço do aplicativo](./media/functions-deployment-slots/azure-functions-deployment-slots-change-app-service-select.png)


## <a name="limitations"></a>Limitações

Os slots de implantação do Azure Functions têm as seguintes limitações:

- O número de vagas disponíveis para um aplicativo depende do plano. O plano de consumo só é permitido um slot de implantação. Há vagas adicionais disponíveis para aplicativos executados o plano App Service.
- Trocar um slot redefine chaves por `AzureWebJobsSecretStorageType` aplicativos que `files`tenham uma configuração de aplicativo igual a .
- Os slots não estão disponíveis para o plano de consumo Linux.

## <a name="support-levels"></a>Níveis de suporte

Existem dois níveis de suporte para slots de implantação:

- **Disponibilidade geral (GA)**: Totalmente suportado e aprovado para uso de produção.
- **Visualização**: Ainda não suportado, mas espera-se que atinja o status de GA no futuro.

| Plano de SISTEMA OPERACIONAL/Hospedagem           | Nível de suporte     |
| ------------------------- | -------------------- |
| Consumo de Windows       | Disponibilidade Geral |
| Windows Premium           | Disponibilidade Geral  |
| Windows Dedicado         | Disponibilidade Geral |
| Consumo de Linux         | Sem suporte          |
| Linux Premium             | Disponibilidade Geral  |
| Linux Dedicado           | Disponibilidade Geral |

## <a name="next-steps"></a>Próximas etapas

- [Tecnologias de implantação em funções do Azure](./functions-deployment-technologies.md)
