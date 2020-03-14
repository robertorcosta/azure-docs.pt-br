---
title: Criar um aplicativo de funções no portal do Azure
description: Crie um novo aplicativo de funções no Azure por meio do Portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368733"
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

Ao criar um aplicativo de funções, você deve criar ou vincular a uma conta de armazenamento do Azure de uso geral que dá suporte ao armazenamento de BLOBs, filas e tabelas. Internamente, o Functions usa o Armazenamento para operações como gerenciamento de gatilhos e log de execuções de função. Algumas contas de armazenamento não dão suporte a filas e tabelas, como contas de armazenamento somente blob, Armazenamento Premium do Azure e contas de armazenamento de uso geral com replicação ZRS. Essas contas são filtradas na folha Conta de Armazenamento durante a criação de um aplicativo de funções.

>[!NOTE]
>Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado.

Para saber mais sobre tipos de conta de armazenamento, confira [Introdução aos serviços de Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Próximas etapas

Embora o portal do Azure torna fácil criar e testar funções, é recomendável usar o [desenvolvimento local](functions-develop-local.md). Depois de criar um aplicativo de funções no portal, você ainda precisa adicionar uma função. 

> [!div class="nextstepaction"]
> [Adicionar uma função disparada por HTTP](functions-create-first-azure-function.md#create-function)
