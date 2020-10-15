---
title: Exibir e gerenciar sensores integrados no Azure defender para IoT
description: Este artigo descreve como exibir e excluir sensores integrados, bem como baixar novos arquivos de ativação para sensores integrados no Azure defender para IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094231"
---
# <a name="view-and-manage-onboarded-sensors"></a>Exibir e gerenciar sensores integrados

Este artigo descreve como exibir e excluir sensores integrados, bem como baixar novos arquivos de ativação para sensores integrados.

## <a name="update-sensor-management-mode"></a>Atualizar o modo de gerenciamento do sensor

Talvez você queira atualizar o modo que o sensor é gerenciado. Ao fazer isso, você precisa remover o sensor atual da página de gerenciamento do sensor e carregar um novo arquivo de ativação.

- **Trabalhe no modo gerenciado na nuvem em vez de no modo gerenciado localmente**: Atualize o arquivo de ativação para o sensor gerenciado localmente com um arquivo de ativação para um sensor gerenciado na nuvem. Após a reativação, as detecções do sensor são exibidas no sensor e no Azure defender para o portal de IoT. Depois que o arquivo de reativação for carregado com êxito, as informações de alerta recém detectadas serão enviadas para o Azure.

- **Trabalhe no modo gerenciado localmente em vez do modo gerenciado na nuvem**: Atualize o arquivo de ativação para um sensor gerenciado na nuvem com um arquivo de ativação para um sensor gerenciado localmente. Após a reativação, as informações de detecção de sensor são exibidas apenas no sensor.

- **Associar o sensor a um novo hub IOT**: Talvez você queira associar o sensor a um novo hub IOT. Para fazer isso, registre novamente o sensor e carregue um novo arquivo de ativação.

**Para reativar o sensor:**

1. Navegue até a página de gerenciamento do Azure defender para IoT, **sensor** .

2. Selecione o sensor para o qual você deseja carregar um novo arquivo de ativação.

3. Exclua-a.

4. Integre o sensor novamente da página de **integração** no modo novo ou com um novo hub IOT.

5. Baixe o arquivo de ativação na página **baixar arquivo de ativação** .

6. Faça logon no console do sensor do Azure defender para IoT.

7. No console do sensor, selecione **configurações do sistema** e, em seguida, selecione **reativação**.

   ![Captura de tela da exibição de reativação](media/updates/image14.png)

8. Selecione **carregar** e selecione o arquivo que você salvou.

9. Selecione **Ativar**.
 
## <a name="delete-sensors"></a>Excluir sensores

Se você excluir um sensor gerenciado na nuvem, as informações não serão enviadas para o Hub IoT.

Exclua os sensores gerenciados localmente quando você não estiver mais trabalhando com eles.

**Para excluir sensores:**

1. Navegue até a página de gerenciamento do **sensor** do Azure defender para IOT.

2. Selecione os sensores que você deseja excluir.

3. Selecione **excluir sensor**.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as opções de configuração, vá para o guia de instruções para configuração de módulo.
> [!div class="nextstepaction"]
> [Guia de instruções de configuração de módulo](./how-to-agent-configuration.md)
