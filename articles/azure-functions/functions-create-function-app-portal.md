---
title: Criar um aplicativo de funções no portal do Azure
description: Crie um novo aplicativo de funções no Azure por meio do Portal.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 8d19a269903de309bf219c2546fa70c3abe7be10
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093580"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar um aplicativo de funções no portal do Azure

Este tópico mostra como usar Azure Functions para criar um aplicativo de funções no portal do Azure. Um aplicativo de funções é o contêiner que hospeda a execução de funções individuais. 

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Depois de criar o aplicativo de funções, é possível criar funções individuais em uma ou mais linguagens diferentes. Crie funções [usando o portal](functions-create-first-azure-function.md#create-function), [a implantação contínua](functions-continuous-deployment.md) ou [carregando com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos de serviço

Azure Functions tem três planos de serviço diferentes: plano de consumo, plano Premium e plano dedicado (serviço de aplicativo). Você deve escolher seu plano de serviço quando seu aplicativo de funções for criado e não puder ser alterado posteriormente. Para obter mais informações, consulte [Escolher um plano de hospedagem do Azure Functions](functions-scale.md).

Se você estiver planejando executar funções JavaScript em um plano dedicado (serviço de aplicativo), escolha um plano com menos núcleos. Para obter mais informações, consulte a [Referência do JavaScript para funções](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

Quando você cria um aplicativo de funções, é necessário criar ou vincular uma conta de armazenamento do Azure de uso geral que oferece suporte ao armazenamento de Tabelas, Blobs e Filas. Internamente, o Functions usa o Armazenamento para operações como gerenciamento de gatilhos e log de execuções de função. Algumas contas de armazenamento não dão suporte a filas e tabelas, como contas de armazenamento somente blob, Armazenamento Premium do Azure e contas de armazenamento de uso geral com replicação ZRS. 

As contas de um tipo sem suporte são filtradas quando você cria um aplicativo de funções no portal do Azure. O portal também permite que você use apenas uma conta de armazenamento existente quando essa conta está na mesma região que o aplicativo de funções que você está criando. Se, por alguma razão, você quiser violar a prática recomendada de desempenho de ter a conta de armazenamento usada pelo seu aplicativo de funções na mesma região, deverá criar seu aplicativo de funções fora do Portal. 

>[!NOTE]
>Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado. 

Para saber mais sobre tipos de conta de armazenamento, confira [Introdução aos serviços de Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md#core-storage-services). 

## <a name="next-steps"></a>Próximas etapas

Embora o portal do Azure torna fácil criar e testar funções, é recomendável usar o [desenvolvimento local](functions-develop-local.md). Depois de criar um aplicativo de funções no portal, você ainda precisa adicionar uma função. 

> [!div class="nextstepaction"]
> [Adicionar uma função disparada por HTTP](functions-create-first-azure-function.md#create-function)
