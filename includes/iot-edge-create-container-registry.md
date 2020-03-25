---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: f63510771e4bd71a3ab6cf048bc5fb5296042a4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75564799"
---
## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Neste tutorial, você usa a extensão do Azure IoT Tools para criar um módulo e uma **imagem de contêiner** dos arquivos. Em seguida, você efetua push dessa imagem para um **registro** que armazena e gerencia suas imagens. Finalmente, você implanta a imagem do seu registro para executar no dispositivo IoT Edge.

Você pode usar qualquer registro compatível com o Docker para manter as imagens de contêiner. Dois serviços de registro populares do Docker são o [Registro de Contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) e o [Hub do Docker](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Este tutorial utiliza o Registro de Contêiner do Azure.

Caso ainda não tenha um registro de contêiner, siga estas etapas para criar um novo no Azure:

1. No [portal do Azure](https://portal.azure.com), selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner**.

2. Forneça os seguintes valores para criar o seu registro de contêiner:

   | Campo | Valor |
   | ----- | ----- |
   | Nome do registro | Forneça um nome exclusivo. |
   | Subscription | Selecione uma assinatura na lista suspensa. |
   | Resource group | É recomendável que você use o mesmo grupo de recursos para todos os recursos de teste que foram criados durante os tutoriais e guias de início rápido do IoT Edge. Por exemplo, **IoTEdgeResources**. |
   | Location | Escolha um local perto de você. |
   | Usuário administrador | Definido como **Habilitar**. |
   | SKU | Selecione **Basic**. |

3. Selecione **Criar**.

4. Depois que o registro de contêiner for criado, navegue até ele e, no painel esquerdo, selecione **Chaves de acesso** no menu localizado em **Configurações**.

5. Copie os valores para **Servidor de logon**, **Nome de usuário** e **Senha** e salve-os em um local conveniente. É possível usar esses valores neste tutorial para fornecer acesso ao registro de contêiner.

   ![Copie o servidor de logon, o nome de usuário e a senha para o registro de contêiner](./media/iot-edge-create-container-registry/registry-access-key.png)