---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 2bfa356deeede1c16bd5a464ea7081132a67faf6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994667"
---
## <a name="append-other-apis"></a>Acrescentar outras APIs

Uma API pode ser composta de APIs expostas por diferentes serviços, incluindo a Especificação de OpenAPI, API do SOAP, recurso de Aplicativos de API do Serviço de Aplicativo do Azure, Aplicativo de Funções do Azure, Aplicativos Lógicos do Azure e Azure Service Fabric.

![Importar uma API](./media/api-management-append-apis/import.png)

Para acrescentar uma API diferente da sua API existente, siga as etapas abaixo. Ao importar outra API, as operações são acrescentadas à sua API atual.

1. Vá para sua instância de Gerenciamento de API do Azure no portal do Azure.
2. Selecione **APIs** no menu à esquerda.
3. Clique em **...** ao lado da API à qual você deseja acrescentar outra API.
4. Selecione **Importar** no menu suspenso.
5. Selecione um serviço de onde será importada a API.