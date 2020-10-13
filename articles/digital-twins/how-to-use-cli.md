---
title: Usar a CLI dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Veja como começar a usar a CLI do Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 65462937db4711066bdb8b31cc22de508321d701
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91950900"
---
# <a name="use-the-azure-digital-twins-cli"></a>Usar a CLI dos Gêmeos Digitais do Azure

Além de gerenciar sua instância do gêmeos digital do Azure no portal do Azure, o gêmeos digital do Azure tem uma **CLI (interface de linha de comando)** que você pode usar para executar a maioria das ações mais importantes com o serviço, incluindo:
* Gerenciando uma instância de gêmeos digital do Azure
* Gerenciando modelos
* Gerenciando o digital gêmeos
* Gerenciando relações de entrelaçamento
* Configurando pontos de extremidade
* Gerenciando [rotas](concepts-route-events.md)
* Configurando a [segurança](concepts-security.md) via RBAC (controle de acesso baseado em função)

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

## <a name="uses-deploy-and-validate"></a>Usos (implantar e validar)

Além de geralmente gerenciar sua instância, a CLI também é uma ferramenta útil para implantação e validação.
* Os comandos de plano de controle podem ser usados para tornar a implantação de uma nova instância repetível ou automatizada.
* Os comandos do plano de dados podem ser usados para verificar rapidamente os valores em sua instância e verificar se as operações foram concluídas conforme o esperado.

## <a name="get-the-extension"></a>Obter a extensão

Os comandos do gêmeos digital do Azure fazem parte da [extensão do Azure IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension). Você pode exibir a lista completa de comandos e seu uso como parte da documentação de referência para o `az iot` conjunto de comandos: [referência de comando *AZ DT* ](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true).

Você pode ter certeza de que tem a versão mais recente da extensão com essas etapas. Você pode executar esses comandos no [Azure cloud Shell](../cloud-shell/overview.md) ou em um [CLI do Azure local](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

[!INCLUDE [digital-twins-cloud-shell-extensions.md](../../includes/digital-twins-cloud-shell-extensions.md)]

## <a name="next-steps"></a>Próximas etapas

Explore a CLI e seu conjunto completo de comandos por meio dos documentos de referência:
* [referência de comando *AZ DT*](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest&preserve-view=true)
