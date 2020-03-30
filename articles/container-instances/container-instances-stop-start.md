---
title: Pare ou inicie manualmente o grupo de contêineres
description: Aprenda a parar ou iniciar manualmente um grupo de contêineres em Instâncias de Contêineres Do Azure.
ms.topic: article
ms.date: 04/15/2019
ms.openlocfilehash: c9f8afea33c65df940d02823ec394697d2786d6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533416"
---
# <a name="manually-stop-or-start-containers-in-azure-container-instances"></a>Pare ou inicie manualmente contêineres em instâncias de contêineres azure

A configuração de diretiva de [reinicialização](container-instances-restart-policy.md) de um grupo de contêineres determina como as instâncias de contêiner começam ou param por padrão. Você pode substituir a configuração padrão parando ou iniciando manualmente um grupo de contêineres.

## <a name="stop"></a>Stop

Interrompa manualmente um grupo de contêineres em execução - por exemplo, usando o comando [az container stop][az-container-stop] ou o portal Azure. Para determinadas cargas de trabalho de contêiner, você pode querer parar um grupo de contêineres de longa duração após um período definido para economizar custos. 

*Quando um grupo de contêineres entra no estado Parado, ele termina e recicla todos os recipientes do grupo. Não preserva o estado do contêiner.*

Quando os contêineres são reciclados, os [recursos](container-instances-container-groups.md#resource-allocation) são desalocados e as paradas de faturamento para o grupo de contêineres.

A ação stop não tem efeito se o grupo de contêineres já tiver terminado (está em um estado bem sucedido ou falho). Por exemplo, um grupo de contêineres com tarefas de contêiner executadas que foram executadas com sucesso termina no estado bem sucedido. As tentativas de deter o grupo naquele estado não mudam o estado. 

## <a name="start"></a>Iniciar

Quando um grupo de contêineres é interrompido - ou porque os contêineres terminaram por conta própria ou você parou manualmente o grupo - você pode iniciar os contêineres. Por exemplo, use o comando [az container start][az-container-start] ou a portal Azure para iniciar manualmente os contêineres no grupo. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será extraída. 

Iniciar um grupo de contêineres inicia uma nova implantação com a mesma configuração de contêiner. Essa ação pode ajudar você a reutilizar rapidamente uma configuração de grupo de contêineres conhecido que funciona conforme o esperado. Você não precisa criar um novo grupo de contêineres para executar a mesma carga de trabalho.

Todos os contêineres de um grupo de contêineres são iniciados por esta ação. Você não pode iniciar um contêiner específico no grupo.

Após você iniciar ou reiniciar manualmente um grupo de contêineres, o grupo de contêineres é executado de acordo com a política de reinicialização configurada.
  
## <a name="restart"></a>Reiniciar

Você pode reiniciar um grupo de contêineres enquanto ele está em execução - por exemplo, usando o comando [az container restart.][az-container-restart] Essa ação reinicia todos os contêineres no grupo de contêineres. Se a imagem de contêiner de qualquer contêiner for atualizada, uma nova imagem será extraída. 

Reiniciar um grupo de contêineres é útil quando você deseja solucionar um problema de implantação. Por exemplo, se uma limitação de recursos temporária impedir que seus contêineres sejam executados com êxito, reiniciar o grupo poderá resolver o problema.

Todos os contêineres de um grupo de contêineres são reiniciados por esta ação. Você não pode reiniciar um contêiner específico no grupo.

Depois de reiniciar manualmente um grupo de contêineres, o grupo de contêineres será executado de acordo com a política de reinicialização configurada.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [as configurações de diretiva de reinicialização](container-instances-restart-policy.md) em Instâncias de contêiner do Azure.

Além de parar e iniciar manualmente um grupo de [contêineres](container-instances-update.md) com a configuração existente, você pode atualizar as configurações de um grupo de contêineres em execução.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[az-container-restart]: /cli/azure/container?view=azure-cli-latest#az-container-restart
[az-container-start]: /cli/azure/container?view=azure-cli-latest#az-container-start
[az-container-stop]: /cli/azure/container?view=azure-cli-latest#az-container-stop
