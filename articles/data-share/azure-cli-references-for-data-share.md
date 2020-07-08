---
title: Referências de CLI do Azure para o compartilhamento de dados do Azure
description: Página de aterrissagem de referência do CLI do Azure para o compartilhamento de dados do Azure
services: data-share
author: dbradish-microsoft
manager: barbkess
ms.service: data-share
ms.devlang: azurecli
ms.topic: reference
ms.date: 05/27/2020
ms.author: dbradish
ms.openlocfilehash: 404022b13f44174e4b647f0430a58fac5eeb81df
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84302638"
---
# <a name="azure-cli-for-azure-data-share"></a>CLI do Azure para o compartilhamento de dados do Azure

A [CLI do Azure](/cli/azure/what-is-azure-cli) (Interface de Linha de Comando do Azure) é um conjunto de comandos usados para criar e gerenciar recursos do Azure.  Ele está disponível em vários serviços do Azure, incluindo o compartilhamento de dados do Azure.  Há mais de 65 comandos diferentes para o compartilhamento de dados!  Esses comandos oferecem a capacidade de trabalhar com eficiência com o serviço de uma linha de comando.

## <a name="references-for-data-share"></a>Referências para compartilhamento de dados

Todos os comandos CLI do Azure para o compartilhamento de dados do Azure são atualmente extensões para o CLI do Azure.  Uma extensão fornece acesso a comandos experimentais e de pré-lançamento.  Saiba mais sobre as referências de extensão em [Usar extensões com a CLI do Azure](/cli/azure/azure-cli-extensions-overview).

|Referência de CLI do Azure |Descrição
|-|-|-|
| [AZ DataShare](/cli/azure/ext/datashare/datashare) | Todos os comandos para gerenciar o compartilhamento de dados do Azure.
| [conta de DataShare AZ](/cli/azure/ext/datashare/datashare/account) | Comandos para gerenciar contas de compartilhamento de dados do Azure.
| [AZ DataShare consumidor](/cli/azure/ext/datashare/datashare/consumer) | Comandos para os consumidores gerenciarem o compartilhamento de dados do Azure.
| [AZ DataShare DataSet](/cli/azure/ext/datashare/datashare/dataset) | Comandos para provedores para gerenciar conjuntos de dados de compartilhamento de data do Azure.
| [convite de DataShare AZ](/cli/azure/ext/datashare/datashare/invitation) | Comandos para os consumidores gerenciarem convites de compartilhamento de dados do Azure.
| [AZ DataShare Provider – share-Subscription](/cli/azure/ext/datashare/datashare/provider-share-subscription) | Comandos para provedores para gerenciar assinaturas de compartilhamento de dados do Azure.
| [sincronização AZ DataShare](/cli/azure/ext/datashare/datashare/synchronization)  | Comandos para gerenciar a sincronização de compartilhamento de dados do Azure.
| [sincronização de DataShare de AZ – configuração](/cli/azure/ext/datashare/datashare/synchronization-setting)  | Comandos para provedores para gerenciar configurações de sincronização de compartilhamento de dados do Azure.

## <a name="reference-examples"></a>Exemplos de referência

São fornecidos exemplos com todas as referências da CLI do Azure. Embora você também possa concluir essas tarefas no portal do Azure, usar a CLI do Azure requer uma só linha de comando.  Veja alguns blocos de código para ter uma ideia de como é fácil usar a CLI do Azure.

Para trabalhar com o compartilhamento de dados do Azure, primeiro você precisará de um grupo de recursos.  Os grupos de recursos do Azure são simples de criar e gerenciar com a CLI do Azure.  

```azurecli
#create a resource group
az group create -location westus -name MyResourceGroup
```

```azurecli
#get a list of resource groups for a subscription
az group list --subscription MySubscription --output table
```

É tão simples criar uma conta de compartilhamento de dados.

```azurecli
#create a data share account
az datashare account create --location "West US 2" --tags tag1=Red tag2=White --name MyAccount --resource-group MyResourceGroup
```

## <a name="see-also"></a>Confira também

* [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli) para saber mais sobre a instalação e a conexão.

* Descubra referências [básicas](/cli/azure/reference-index) e de [extensão](/cli/azure/azure-cli-extensions-list) adicionais na documentação do CLI do Azure.
