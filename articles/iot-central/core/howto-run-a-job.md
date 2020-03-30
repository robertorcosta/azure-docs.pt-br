---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem recursos de gerenciamento de dispositivos em massa, como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 03/03/2020
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: c9d5a0daa364b09e45699e898511c28d4b4d92ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157748"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Crie e execute um trabalho no seu aplicativo Azure IoT Central

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. Os trabalhos permitem que você faça atualizações em massa nas propriedades do dispositivo e execute comandos. Este artigo mostra como começar a usar trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Ele mostra como definir o limiar de luz para um grupo de dispositivos de gateway logístico.

1. Navegue até **Jobs** pelo painel esquerdo.

2. Selecione **+ Novo** para criar um novo emprego:

    ![Criar novo trabalho](./media/howto-run-a-job/createnewjob.png)

3. Digite um nome e descrição para identificar o trabalho que você está criando.

4. Selecione o grupo de dispositivo de destino ao que deseja que seu trabalho se inscreva. Você pode ver a quantidade de dispositivos a que sua configuração de trabalho se aplica na seção **Resumo.**

5. Em seguida, escolha **a propriedade Cloud,** **Propriedade** ou **Comando** como o tipo de trabalho a ser configurado. Para configurar uma configuração de trabalho **de propriedade,** selecione uma propriedade e defina seu novo valor. Para configurar um **comando,** escolha o comando a ser executado. Um trabalho de propriedade pode definir várias propriedades:

    ![Configurar trabalho](./media/howto-run-a-job/configurejob.png)

6. Depois de criar seu trabalho, escolha **Executar** ou **Salvar**. O trabalho agora aparece na página principal **de Empregos.** Nesta página, você pode ver seu trabalho em execução atual e o histórico de quaisquer trabalhos anteriormente executados ou salvos. Seu trabalho salvo pode ser aberto novamente a qualquer momento para continuar editando-o ou executá-lo:

    ![Exibir trabalho](./media/howto-run-a-job/viewjob.png)

    > [!NOTE]
    > Você pode ver até 30 dias de história para seus trabalhos anteriormente executados.

7. Para obter uma visão geral do seu trabalho, selecione o trabalho a ser visualizado na lista. Esta visão geral contém os detalhes do trabalho, dispositivos e valores de status do dispositivo. A partir desta visão geral, você também pode selecionar **Baixar Detalhes de Trabalho** para baixar um arquivo CSV de seus detalhes de trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para solução de problemas:

    ![Exibir status do dispositivo](./media/howto-run-a-job/downloaddetails.png)

### <a name="manage-a-job"></a>Gerencie um trabalho

Para parar um de seus trabalhos em execução, abra-o e selecione **Stop**. A mudança de status do trabalho para refletir o trabalho está parada. A seção **Resumo** mostra quais dispositivos foram concluídos, falharam ou ainda estão pendentes.

Para executar um trabalho que está parado no momento, selecione-o e selecione **Executar**. As mudanças no status do trabalho para refletir o trabalho estão agora funcionando novamente. A seção **Resumo** continua a ser atualizada com o progresso mais recente.

![Gerenciar o trabalho](./media/howto-run-a-job/managejob.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um de seus trabalhos existentes, selecione-o na página **Empregos** e selecione **Copiar**. Uma cópia da configuração do trabalho é aberta para você editar e **o Copy** é anexado ao nome do trabalho. Você pode salvar ou executar o novo trabalho:

![Trabalho de cópia](./media/howto-run-a-job/copyjob.png)

## <a name="view-the-job-status"></a>Exibir o status do trabalho

Depois que um trabalho é criado, a coluna **Status** é atualizada com a última mensagem de status do trabalho. A tabela a seguir lista os possíveis valores de status:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Falhou               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Este trabalho ainda não começou a ser executado em dispositivos.         |
| Executando              | Esse trabalho está sendo executado nos dispositivos.             |
| Parado              | Este trabalho foi interrompido manualmente por um usuário.           |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os possíveis valores de status do dispositivo:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Teve êxito            | O número de dispositivos em que o trabalho foi executado com sucesso.       |
| Falhou               | O número de dispositivos que o trabalho falhou em executar.       |

### <a name="view-the-device-status"></a>Exibir o status do dispositivo

Para visualizar o status do trabalho e todos os dispositivos afetados, abra o trabalho. Para baixar um arquivo CSV que inclua os detalhes do trabalho, incluindo a lista de dispositivos e seus valores de status, selecione **Baixar detalhes de trabalho**. Ao lado do nome de cada dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Falhou               | A execução do trabalho falhou neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

> [!NOTE]
> Se um dispositivo foi excluído, você não poderá selecionar o dispositivo. Ele é exibido como excluído com o ID do dispositivo.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar empregos no seu aplicativo Azure IoT Central, aqui estão alguns passos seguintes:

- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-device-template.md)
