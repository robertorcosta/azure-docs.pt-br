---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem recursos de gerenciamento de dispositivos em massa, como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 6073f71eb21ba4a6739647964d4888044d6ee59a
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87283564"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar um trabalho em seu aplicativo de IoT Central do Azure

É possível usar o Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala usando trabalhos. Os trabalhos permitem que você faça atualizações em massa nas propriedades do dispositivo e execute comandos. Este artigo mostra como começar a usar trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho. Ele mostra como definir o limite de luz para um grupo de dispositivos de gateway logística.

1. Navegue até **trabalhos** no painel esquerdo.

2. Selecione **+ novo** para criar um novo trabalho:

    ![Criar novo trabalho](./media/howto-run-a-job/create-new-job.png)

3. Insira um nome e uma descrição para identificar o trabalho que você está criando.

4. Selecione o grupo de dispositivos de destino ao qual você deseja que seu trabalho seja aplicado. Você pode ver quantos dispositivos sua configuração de trabalho aplica na seção de **Resumo** .

5. Em seguida, escolha a **propriedade de nuvem**, a **Propriedade**ou o **comando** como o tipo de trabalho a ser configurado. Para definir uma configuração de trabalho de **Propriedade** , selecione uma propriedade e defina seu novo valor. Para configurar um **comando**, escolha o comando a ser executado. Um trabalho de propriedade pode definir várias propriedades:

    ![Configurar trabalho](./media/howto-run-a-job/configure-job.png)

6. Depois de criar seu trabalho, escolha **executar** ou **salvar**. O trabalho agora aparece na página de **trabalhos** principais. Nessa página, você pode ver seu trabalho em execução no momento e o histórico de qualquer trabalho executado anteriormente ou salvo. Seu trabalho salvo pode ser aberto novamente a qualquer momento para continuar a editá-lo ou executá-lo:

    ![Exibir trabalho](./media/howto-run-a-job/view-job.png)

    > [!NOTE]
    > Você pode exibir até 30 dias de histórico para os trabalhos executados anteriormente.

7. Clique no trabalho salvo e execute o trabalho clicando no botão Executar. Executar um pop-up de trabalho será exibido. Confirme clicando no botão Executar trabalho. 

    ![Executar um trabalho](./media/howto-run-a-job/run-job.png)

8. O trabalho passa por diferentes fases de pendente, em execução e concluído. Os detalhes de execução do trabalho contêm métricas de resultados, detalhes de duração e grade de lista de dispositivos. Nesta visão geral, você também pode selecionar **log de resultados** para baixar um arquivo CSV dos detalhes do seu trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para a solução de problemas.

    ![Exibir status do dispositivo](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gerenciar trabalhos

Para interromper um de seus trabalhos em execução, abra-o e selecione **parar**. O status do trabalho muda para refletir que o trabalho foi interrompido. A seção de **Resumo** mostra quais dispositivos foram concluídos, com falha ou ainda estão pendentes.

![Gerenciar trabalho](./media/howto-run-a-job/manage-job.png)

Quando o trabalho estiver no estado parado, você poderá clicar em **continuar** para retomar a execução do trabalho. O status do trabalho muda para refletir que o trabalho agora está em execução novamente. A seção de **Resumo** continua a ser atualizada com o progresso mais recente.

![Trabalho parado](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um de seus trabalhos existentes, selecione-o na página **trabalhos** e selecione **detalhes do trabalho**. A página de detalhes do trabalho será exibida. 

![Detalhes do trabalho](./media/howto-run-a-job/job-details.png)

Clique em **copiar**

![Detalhes do trabalho](./media/howto-run-a-job/job-details-copy.png)

Uma cópia da configuração do trabalho é aberta para você editar e a **cópia** é anexada ao nome do trabalho. Você pode salvar ou executar o novo trabalho:

![Trabalho de cópia](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Exibir status do trabalho

Depois que um trabalho é criado, a coluna **status** é atualizada com a mensagem de status mais recente do trabalho. A tabela a seguir lista os valores de status possíveis:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Este trabalho foi executado em todos os dispositivos.              |
| Com falha               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não começou a ser executado em dispositivos.         |
| Executando              | Esse trabalho está sendo executado nos dispositivos.             |
| Parado              | Este trabalho foi interrompido manualmente por um usuário.           |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os possíveis valores de status do dispositivo:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Com sucesso            | O número de dispositivos em que o trabalho foi executado com êxito.       |
| Com falha               | O número de dispositivos nos quais o trabalho falhou ao ser executado.       |

### <a name="view-the-device-status-values"></a>Exibir os valores de status do dispositivo

Para exibir o status do trabalho e de todos os dispositivos afetados, abra o trabalho. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho executado neste dispositivo.                                     |
| Com falha               | Falha ao executar o trabalho neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

Para baixar um arquivo CSV que inclui os detalhes do trabalho e a lista de dispositivos e seus valores de status, selecione **baixar**.

### <a name="filter-the-list-of-devices"></a>Filtrar a lista de dispositivos

Você pode filtrar a lista de dispositivos na página de detalhes do trabalho selecionando o ícone de filtro. Você pode filtrar os campos **ID do dispositivo** ou **status** :

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Filtrar a lista de dispositivos":::

### <a name="customize-columns-in-the-device-list"></a>Personalizar colunas na lista de dispositivos

Você pode escolher colunas adicionais para exibir na lista de dispositivos selecionando o ícone de opções de coluna:

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Opções de Coluna":::

Você verá uma caixa de diálogo que permite escolher as colunas a serem exibidas na lista de dispositivos. Selecione as colunas que você deseja exibir, selecione o ícone de seta para a direita e selecione **OK**. Para selecionar todas as colunas disponíveis, marque **selecionar tudo**:

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Caixa de diálogo Seletor de coluna":::

As colunas selecionadas são exibidas na lista de dispositivos:

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Selecionar colunas":::

As colunas selecionadas são mantidas em uma sessão de usuário ou em sessões de usuário que têm acesso ao aplicativo.

## <a name="rerun-jobs"></a>Executar trabalhos novamente

Você pode executar novamente um trabalho que tenha dispositivos com falha. Selecione **executar novamente**:

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Executar novamente um trabalho":::

Insira um nome de trabalho e uma descrição e, em seguida, selecione **executar novamente o trabalho**. Um novo trabalho é enviado para repetir a ação em dispositivos com falha:

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Executar novamente dispositivos com falha":::

> [!NOTE]
> Você não pode executar mais de cinco trabalhos ao mesmo tempo de um aplicativo IoT Central.

> [!NOTE]
> Quando um trabalho é concluído e você exclui um dispositivo que está na lista de dispositivos do trabalho, a entrada do dispositivo mostra como excluído no link nome do dispositivo e detalhes do dispositivo não está disponível para o dispositivo excluído.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar trabalhos em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-device-template.md)
