---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem recursos de gerenciamento de dispositivos em massa, como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 07/08/2019
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 2dd1eddc841cc484957c2124de3419799c4e59b7
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206769"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar um trabalho em seu aplicativo de IoT Central do Azure

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. Os trabalhos permitem que você faça atualizações em massa nas propriedades do dispositivo e execute comandos. Este artigo mostra como começar a usar trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Ele mostra como definir o limite de luz para um grupo de dispositivos de gateway logística.

1. Navegue até **trabalhos** no painel esquerdo.

2. Selecione **+ novo** para criar um novo trabalho:

    > [!div class="mx-imgBorder"]
    > ![criar novo trabalho](./media/howto-run-a-job/createnewjob.png)

3. Insira um nome e uma descrição para identificar o trabalho que você está criando.

4. Selecione o grupo de dispositivos de destino ao qual você deseja que seu trabalho seja aplicado. Você pode ver quantos dispositivos sua configuração de trabalho aplica na seção de **Resumo** .

5. Em seguida, escolha uma das **Propriedades** ou **comando** como o tipo de trabalho a ser configurado. Para definir uma configuração de trabalho de **Propriedade** , selecione uma propriedade e defina seu novo valor. Para configurar um **comando**ou escolha o comando a ser executado. Um trabalho de propriedade pode definir várias propriedades:

    > [!div class="mx-imgBorder"]
    > ![configurar o trabalho](./media/howto-run-a-job/configurejob.png)

6. Depois de selecionar os dispositivos, escolha **executar** ou **salvar**. O trabalho agora aparece na página de **trabalhos** principais. Nessa página, você pode ver seu trabalho em execução no momento e o histórico de qualquer trabalho executado anteriormente ou salvo. Seu trabalho salvo pode ser aberto novamente a qualquer momento para continuar a editá-lo ou executá-lo:

    > [!div class="mx-imgBorder"]
    > ![Exibir trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Você pode exibir até 30 dias de histórico para os trabalhos executados anteriormente.

7. Para obter uma visão geral do seu trabalho, selecione o trabalho a ser exibido na lista. Esta visão geral contém os detalhes do trabalho, dispositivos e valores de status do dispositivo. Nesta visão geral, você também pode selecionar **baixar detalhes do trabalho** para baixar um arquivo CSV dos detalhes do trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para a solução de problemas:

    > [!div class="mx-imgBorder"]
    > ![exibir o status do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="stop-a-running-job"></a>Parar um trabalho em execução

Para interromper um de seus trabalhos em execução, abra-o e selecione **parar**. O status do trabalho muda para refletir que o trabalho foi interrompido. A seção de **Resumo** mostra quais dispositivos foram concluídos, com falha ou ainda estão pendentes:

    > [!div class="mx-imgBorder"]
    > ![Stop job](./media/howto-run-a-job/stopjob.png)

### <a name="run-a-stopped-job"></a>Executar um trabalho interrompido

Para executar um trabalho que está parado no momento, selecione-o e, em seguida, selecione **executar**. O status do trabalho muda para refletir que o trabalho agora está em execução novamente. A seção de **Resumo** continua a ser atualizada com o progresso mais recente:

    > [!div class="mx-imgBorder"]
    > ![Resumed job](./media/howto-run-a-job/resumejob.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um de seus trabalhos existentes, abra-o e selecione **copiar**. Uma cópia da configuração do trabalho é aberta para você editar e a **cópia** é anexada ao final do nome do trabalho. Você pode salvar ou executar o novo trabalho:

    > [!div class="mx-imgBorder"]
    > ![Copy job](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Exibir o status do trabalho

Depois que um trabalho é criado, a coluna **status** é atualizada com a mensagem de status mais recente do trabalho. A tabela a seguir lista os valores de status possíveis:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Falhou               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não começou a ser executado em dispositivos.         |
| Executando              | Esse trabalho está sendo executado nos dispositivos.             |
| Parado              | Este trabalho foi interrompido manualmente por um usuário.           |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os possíveis valores de status do dispositivo:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Teve êxito            | O número de dispositivos em que o trabalho foi executado com êxito.       |
| Falhou               | O número de dispositivos nos quais o trabalho falhou ao ser executado.       |

### <a name="view-the-device-status"></a>Exibir o status do dispositivo

Para exibir o status do trabalho e de todos os dispositivos afetados, selecione o trabalho. Para baixar um arquivo CSV que inclui os detalhes do trabalho, incluindo a lista de dispositivos e seus valores de status, selecione **baixar detalhes do trabalho**. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Falhou               | A execução do trabalho falhou neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo tiver sido excluído, você não poderá selecionar o dispositivo. Ele é exibido como excluído com a ID do dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar trabalhos em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-device-template.md)
