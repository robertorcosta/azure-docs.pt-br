---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230782"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Criar um aplicativo de funções no portal do Azure

This topic shows you how to use Azure Functions to create a function app in the Azure portal. Um aplicativo de funções é o contêiner que hospeda a execução de funções individuais. 

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Ao criar um aplicativo de funções, forneça um **Nome de aplicativo** válido, que pode conter apenas letras, números e hifens. Sublinhado ( **_** ) não é um caractere permitido.

Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas. O nome da sua conta de armazenamento deve ser exclusivo no Azure. 

Depois de criar o aplicativo de funções, é possível criar funções individuais em uma ou mais linguagens diferentes. Crie funções [usando o portal](functions-create-first-azure-function.md#create-function), [a implantação contínua](functions-continuous-deployment.md) ou [carregando com FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Planos de serviço

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. Para obter mais informações, consulte [Escolher um plano de hospedagem do Azure Functions](functions-scale.md).

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. Para obter mais informações, consulte a [Referência do JavaScript para funções](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisitos da conta de armazenamento

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Internamente, o Functions usa o Armazenamento para operações como gerenciamento de gatilhos e log de execuções de função. Algumas contas de armazenamento não dão suporte a filas e tabelas, como contas de armazenamento somente blob, Armazenamento Premium do Azure e contas de armazenamento de uso geral com replicação ZRS. Essas contas são filtradas na folha Conta de Armazenamento durante a criação de um aplicativo de funções.

>[!NOTE]
>Ao usar o plano de hospedagem de Consumo, o código da função e os arquivos de configuração da associação são armazenados no armazenamento de Arquivos do Azure na conta de armazenamento principal. Ao excluir a conta de armazenamento principal, esse conteúdo será excluído e não poderá ser recuperado.

Para saber mais sobre tipos de conta de armazenamento, confira [Introdução aos serviços de Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Próximos passos

Embora o portal do Azure torna fácil criar e testar funções, é recomendável usar o [desenvolvimento local](functions-develop-local.md). Depois de criar um aplicativo de funções no portal, você ainda precisa adicionar uma função. 

> [!div class="nextstepaction"]
> [Adicionar uma função disparada por HTTP](functions-create-first-azure-function.md#create-function)
