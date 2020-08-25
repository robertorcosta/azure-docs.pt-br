---
title: Criar e executar trabalhos em seu aplicativo Azure IoT Central | Microsoft Docs
description: Os trabalhos do Azure IoT Central permitem recursos de gerenciamento de dispositivos em massa, como atualizar propriedades ou executar um comando.
ms.service: iot-central
services: iot-central
author: sarahhubbard
ms.author: sahubbar
ms.date: 06/08/2020
ms.topic: how-to
ms.openlocfilehash: 406881a9131aae35b91dcab248745bb426cecf0e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88797829"
---
# <a name="create-and-run-a-job-in-your-azure-iot-central-application"></a>Criar e executar um trabalho em seu aplicativo de IoT Central do Azure

Você pode usar Microsoft Azure IoT Central para gerenciar seus dispositivos conectados em escala por meio de trabalhos. Os trabalhos permitem que você faça atualizações em massa nas propriedades do dispositivo e execute comandos. Este artigo mostra como começar a usar trabalhos em seu próprio aplicativo.

## <a name="create-and-run-a-job"></a>Criar e executar um trabalho

Esta seção mostra como criar e executar um trabalho na forma de definir o limite de luz para um grupo de dispositivos de gateway logística.

1. No painel esquerdo, selecione **trabalhos**.

2. Selecione **+ Novo**.

   ![Captura de tela que mostra seleções para criar um trabalho.](./media/howto-run-a-job/create-new-job.png)

3. Insira um nome e uma descrição para identificar o trabalho que você está criando.

4. Selecione o grupo de dispositivos de destino ao qual você deseja que seu trabalho se aplique. Você pode ver quantos dispositivos sua configuração de trabalho aplica na seção de **Resumo** .

5. Escolha **propriedade de nuvem**, **Propriedade**ou **comando** como o tipo de trabalho a ser configurado. 

   Para definir uma configuração de trabalho de **Propriedade** , selecione uma propriedade e defina seu novo valor. Para definir uma configuração de trabalho de **comando** , escolha o comando a ser executado. Um trabalho de propriedade pode definir várias propriedades.

   ![Captura de tela que mostra seleções para criar um trabalho de propriedade chamado definir limite claro.](./media/howto-run-a-job/configure-job.png)

6. Selecione **executar** ou **salvar**. O trabalho agora aparece na página de **trabalhos** principais. Nessa página, você pode ver seu trabalho em execução no momento e o histórico de qualquer trabalho executado anteriormente ou salvo. Você pode reabrir o trabalho salvo a qualquer momento para continuar editá-lo ou executá-lo.

   ![Captura de tela que mostra o nome, o status e a descrição de um trabalho criado.](./media/howto-run-a-job/view-job.png)

   > [!NOTE]
   > Você pode exibir até 30 dias de histórico para os trabalhos executados anteriormente.

7. Selecione o trabalho salvo e execute-o selecionando o botão **executar** . 

   A caixa de diálogo **executar seu trabalho?** é exibida. Confirme selecionando o botão **Executar trabalho** . 

   ![Captura de tela da caixa de diálogo que confirma que você deseja executar um trabalho.](./media/howto-run-a-job/run-job.png)

8. Um trabalho passa pelas fases de pendente, em execução e concluído. Os detalhes de execução do trabalho contêm métricas de resultado, detalhes de duração e uma grade de lista de dispositivos. 

   Nesta visão geral, você também pode selecionar **log de resultados** para baixar um arquivo CSV dos detalhes do seu trabalho, incluindo os dispositivos e seus valores de status. Essas informações podem ser úteis para a solução de problemas.

   ![Captura de tela que mostra o status do dispositivo.](./media/howto-run-a-job/download-details.png)

## <a name="manage-jobs"></a>Gerenciar trabalhos

Para interromper um trabalho em execução, abra-o e selecione **parar**. O status do trabalho é alterado para refletir que o trabalho foi interrompido. A seção de **Resumo** mostra quais dispositivos foram concluídos, falharam ou ainda estão pendentes.

![Captura de tela que mostra um trabalho em execução e o botão para parar um trabalho.](./media/howto-run-a-job/manage-job.png)

Depois que um trabalho estiver em um estado parado, você poderá selecionar **continuar** para retomar a execução do trabalho. O status do trabalho é alterado para refletir que o trabalho agora está em execução novamente. A seção de **Resumo** continua a ser atualizada com o progresso mais recente.

![Captura de tela que mostra um trabalho parado e o botão para continuar um trabalho.](./media/howto-run-a-job/stopped-job.png)

## <a name="copy-a-job"></a>Copiar um trabalho

Para copiar um de seus trabalhos existentes, selecione-o na página **trabalhos** e selecione **detalhes do trabalho**. A página de **detalhes do trabalho** é exibida. 

![Captura de tela que mostra a página de detalhes do trabalho.](./media/howto-run-a-job/job-details.png)

Selecione **Copiar**.

![Captura de tela que mostra o botão de cópia.](./media/howto-run-a-job/job-details-copy.png)

Uma cópia da configuração do trabalho é aberta para você editar e a **cópia** é anexada ao nome do trabalho. Você pode salvar ou executar o novo trabalho.

![Captura de tela que mostra uma cópia da configuração do trabalho.](./media/howto-run-a-job/copy-job.png)

## <a name="view-job-status"></a>Exibir status do trabalho

Depois que um trabalho é criado, a coluna **status** é atualizada com a mensagem de status mais recente para o trabalho. A tabela a seguir lista os possíveis valores de status de trabalho:

| Mensagem de status       | Significado do status                                          |
| -------------------- | ------------------------------------------------------- |
| Concluído            | Esse trabalho foi executado em todos os dispositivos.              |
| Com falha               | Esse trabalho falhou e não foi totalmente executado nos dispositivos.  |
| Pendente              | Esse trabalho ainda não começou a ser executado em dispositivos.         |
| Executando              | Este trabalho está sendo executado atualmente em dispositivos.             |
| Parado              | Um usuário interrompeu manualmente este trabalho.           |

A mensagem de status é seguida por uma visão geral dos dispositivos no trabalho. A tabela a seguir lista os possíveis valores de status do dispositivo:

| Mensagem de status       | Significado do status                                                     |
| -------------------- | ------------------------------------------------------------------ |
| Com sucesso            | O número de dispositivos em que o trabalho foi executado com êxito.       |
| Com falha               | O número de dispositivos em que o trabalho falhou em execução.       |

Para exibir o status do trabalho e de todos os dispositivos afetados, abra o trabalho. Ao lado de cada nome de dispositivo, você verá uma das seguintes mensagens de status:

| Mensagem de status       | Significado do status                                                                |
| -------------------- | ----------------------------------------------------------------------------- |
| Concluído            | O trabalho foi executado neste dispositivo.                                     |
| Com falha               | Falha ao executar o trabalho neste dispositivo. A mensagem de erro mostra mais informações.  |
| Pendente              | O trabalho ainda não foi executado neste dispositivo.                                   |

Para baixar um arquivo CSV que inclui os detalhes do trabalho e a lista de dispositivos e seus valores de status, selecione **baixar**.

## <a name="filter-the-device-list"></a>Filtrar a lista de dispositivos

Você pode filtrar a lista de dispositivos na página de **detalhes do trabalho** selecionando o ícone de filtro. Você pode filtrar o campo **ID do dispositivo** ou **status** .

:::image type="content" source="media/howto-run-a-job/filter.png" alt-text="Captura de tela que mostra seleções para filtrar uma lista de dispositivos.":::

## <a name="customize-columns-in-the-device-list"></a>Personalizar colunas na lista de dispositivos

Você pode escolher colunas adicionais para exibir na lista de dispositivos selecionando o ícone de opções de coluna.

:::image type="content" source="media/howto-run-a-job/column-options.png" alt-text="Captura de tela que mostra o ícone para as opções de coluna.":::

Uma caixa de diálogo permite que você escolha as colunas a serem exibidas na lista de dispositivos. Selecione as colunas que você deseja exibir, selecione a seta para a direita e, em seguida, selecione **OK**. Para selecionar todas as colunas disponíveis, marque **selecionar tudo**.

:::image type="content" source="media/howto-run-a-job/column-picker-popup.png" alt-text="Captura de tela que mostra a caixa de diálogo para escolher as colunas a serem exibidas.":::

As colunas selecionadas aparecem na lista de dispositivos.

:::image type="content" source="media/howto-run-a-job/column-picker-column-selected.png" alt-text="Captura de tela que mostra as colunas selecionadas na lista de dispositivos.":::

As colunas selecionadas são mantidas em uma sessão de usuário ou em sessões de usuário que têm acesso ao aplicativo.

## <a name="rerun-jobs"></a>Executar trabalhos novamente

Você pode executar novamente um trabalho que tenha dispositivos com falha. Selecione **executar novamente em falha**.

:::image type="content" source="media/howto-run-a-job/rerun.png" alt-text="Captura de tela que mostra o botão para executar novamente um trabalho em dispositivos com falha.":::

Insira um nome de trabalho e uma descrição e, em seguida, selecione **executar novamente o trabalho**. Um novo trabalho é enviado para repetir a ação em dispositivos com falha.

:::image type="content" source="media/howto-run-a-job/rerun-failed.png" alt-text="Captura de tela que mostra a caixa de diálogo para a reexecução de dispositivos com falha.":::

> [!NOTE]
> Não é possível executar mais de cinco trabalhos ao mesmo tempo de um aplicativo de IoT Central do Azure.
>
> Quando um trabalho é concluído e você exclui um dispositivo que está na lista de dispositivos do trabalho, a entrada do dispositivo aparece como excluído no nome do dispositivo. O link detalhes não está disponível para o dispositivo excluído.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como criar trabalhos em seu aplicativo IoT Central do Azure, aqui estão algumas próximas etapas:

- [Gerenciar seus dispositivos](howto-manage-devices.md)
- [Versão de seu modelo de dispositivo](howto-version-device-template.md)
