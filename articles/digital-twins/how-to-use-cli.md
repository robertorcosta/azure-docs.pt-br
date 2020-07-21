---
title: Usar a CLI dos Gêmeos Digitais do Azure
titleSuffix: Azure Digital Twins
description: Veja como começar a usar a CLI do Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.date: 05/25/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5272babf794529e5e9bd87a3c4a96e6df5758fb8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537455"
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

Os comandos do gêmeos digital do Azure fazem parte da [extensão do Azure IOT para CLI do Azure](https://github.com/Azure/azure-iot-cli-extension). Você pode exibir a documentação de referência para esses comandos como parte do `az iot` conjunto de comandos: [AZ DT](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest).

## <a name="deploy-and-validate"></a>Implantar e validar

Além de geralmente gerenciar sua instância, a CLI também é uma ferramenta útil para implantação e validação.
* Os comandos de plano de controle podem ser usados para tornar a implantação de uma nova instância repetível ou automatizada.
* Os comandos do plano de dados podem ser usados para verificar rapidamente os valores em sua instância e verificar se as operações foram concluídas conforme o esperado.

## <a name="next-steps"></a>Próximas etapas

Para obter uma alternativa aos comandos da CLI, consulte como gerenciar uma instância do gêmeos digital do Azure usando APIs e SDKs:
* [*Como: usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md)
