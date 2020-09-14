---
title: Instalar a atualização no dispositivo da série Gateway do Azure Data Box | Microsoft Docs
description: Descreve como aplicar atualizações usando o portal do Azure e a interface do usuário da Web local para o dispositivo Gateway do Azure Data Box Series
services: databox
author: alkohli
ms.service: databox
ms.topic: article
ms.date: 06/30/2020
ms.author: alkohli
ms.openlocfilehash: 1b3f0faa2b5f67a23317935f0ad868e3872cf86e
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055956"
---
# <a name="update-your-azure-data-box-gateway"></a>Atualizar seu Gateway do Azure Data Box

Este artigo descreve as etapas necessárias para instalar a atualização no seu Gateway do Azure Data Box por meio da interface do usuário da Web local e por meio do portal do Azure. Aplique as atualizações de software ou hotfixes para manter seu dispositivo Gateway do Data Box atualizado.

> [!IMPORTANT]
>
> - A atualização **1911** corresponde à versão do **1.6.1049.786** software em seu dispositivo. Para obter informações sobre essa atualização, acesse [notas de versão](data-box-gateway-1911-release-notes.md).
>
> - Tenha em mente que instalar uma atualização ou um hotfix reinicia seu dispositivo. Considerando que o Data Box Gateway é um dispositivo de nó único, qualquer E/S em andamento será interrompida, e o dispositivo apresentará um tempo de inatividade de até 30 minutos para a atualização de software do dispositivo.

Cada uma dessas etapas é descrita nas seções a seguir.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure

Recomendamos que você instale as atualizações por meio do portal do Azure. O dispositivo verifica automaticamente se há atualizações uma vez por dia. Depois que as atualizações estiverem disponíveis, você verá uma notificação no Portal. Em seguida, você pode baixar e instalar as atualizações.

> [!NOTE]
> Verifique se o dispositivo está íntegro e se o status aparece como **online** antes de prosseguir com a instalação das atualizações.

1. Quando as atualizações estiverem disponíveis para seu dispositivo, você verá uma notificação. Selecione a notificação ou na barra de comandos superior, **Atualizar dispositivo**. Isso permitirá que você aplique atualizações de software do dispositivo.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-01a.png)

2. Na folha **atualizações do dispositivo** , verifique se você analisou os termos de licença associados aos novos recursos nas notas de versão.

    Você pode optar por **baixar e instalar** as atualizações ou apenas **baixar** as atualizações. Em seguida, você pode optar por instalar essas atualizações mais tarde.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-02.png)

    Se você quiser baixar e instalar as atualizações, verifique a opção que as atualizações instalam automaticamente após a conclusão do download.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-03.png)

3. O download das atualizações é iniciado. Você verá uma notificação informando que o download está em andamento.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-05.png)

    Uma faixa de notificação também é exibida no portal do Azure. Isso indica o progresso do download.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-08a.png)

    Você pode selecionar essa notificação ou selecionar **Atualizar dispositivo** para ver o status detalhado da atualização.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-09.png)

4. Depois que o download for concluído, a faixa de notificação será atualizada para indicar a conclusão. Se você optar por baixar e instalar as atualizações, a instalação será iniciada automaticamente.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-10a.png)

    Se você optar por baixar apenas as atualizações, selecione a notificação para abrir a folha **atualizações do dispositivo** . Selecione **Instalar**.
  
    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-11a.png)

5. Você verá uma notificação de que a instalação está em andamento.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-12a.png)

    O portal também exibe um alerta informativo para indicar que a instalação está em andamento. <!-- The device goes offline and is in maintenance mode.-->

    <!-- ![Software version after update](./media/data-box-gateway-apply-updates/update-13.png)-->

6. Como esse é um dispositivo de 1 nó, o dispositivo será reiniciado depois que as atualizações forem instaladas. O alerta crítico durante a reinicialização indicará que a pulsação do dispositivo será perdida.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-19a.png)

    Selecione o alerta para ver o evento de dispositivo correspondente.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-20a.png)

7. O status do dispositivo é atualizado para **online** depois que as atualizações são instaladas.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-23a.png)

    Na barra de comandos superior, selecione **atualizações de dispositivo**. Verifique se a atualização foi instalada com êxito e se a versão do software do dispositivo reflete isso.

    ![Versão do software após a atualização](./media/data-box-gateway-apply-updates/portal-apply-update-24.png)

## <a name="use-the-local-web-ui"></a>Usar a interface do usuário da Web local

Há duas etapas ao usar a interface do usuário da Web local:

- Baixe a atualização ou o hotfix
- Instale a atualização ou o hotfix

Cada uma dessas etapas é descrita mais detalhadamente nas seções a seguir.

### <a name="download-the-update-or-the-hotfix"></a>Baixe a atualização ou o hotfix

Execute as etapas a seguir para baixar a atualização. Você pode baixar a atualização do local fornecido pela Microsoft ou do catálogo Microsoft Update.

Execute as etapas a seguir para baixar a atualização do catálogo Microsoft Update.

1. Inicie o navegador e navegue até [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com) .

   ![Pesquisar o catálogo](./media/data-box-gateway-apply-updates/download-update-1.png)

2. Na caixa de pesquisa do catálogo Microsoft Update, insira o número da base de dados de conhecimento (KB) do hotfix ou os termos da atualização que você deseja baixar. Por exemplo, digite **gateway do Azure data Box**e clique em **Pesquisar**.

   A listagem de atualização aparece como **Gateway do Azure Data Box 1911**.

   ![Pesquisar o catálogo](./media/data-box-gateway-apply-updates/download-update-2.png)

3. Selecione **Baixar**. Há um único arquivo para download chamado *SoftwareUpdatePackage.exe* que corresponde à atualização de software do dispositivo. Baixe o arquivo em uma pasta no sistema local. Você também pode copiar a pasta para um compartilhamento de rede que é acessível do dispositivo.

   ![Pesquisar o catálogo](./media/data-box-gateway-apply-updates/download-update-3.png)

### <a name="install-the-update-or-the-hotfix"></a>Instale a atualização ou o hotfix

Antes da instalação de atualização ou hotfix, certifique-se de que:

- Você tem a atualização ou hotfix baixado localmente em seu host ou acessível por meio de um compartilhamento de rede.
- O status do dispositivo é íntegro, conforme mostrado na página **visão geral** da interface do usuário da Web local.

   ![atualizar dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-1.png)

Esse procedimento leva cerca de 20 minutos para ser concluído. Execute as etapas a seguir para instalar a atualização ou o hotfix.

1. Na interface do usuário da Web local, vá para **manutenção**  >  **atualização de software**. Anote a versão do software que você está executando.

   ![atualizar dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-2.png)

2. Forneça o caminho para o arquivo de atualização. Você também pode navegar até o arquivo de instalação de atualização, se colocado em um compartilhamento de rede. Selecione o arquivo de atualização de software com o sufixo *SoftwareUpdatePackage.exe* .

   ![atualizar dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-3.png)

3. Selecione **Aplicar**.

   ![atualizar dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-4.png)

4. Quando a confirmação for solicitada, selecione **Sim** para continuar. Considerando que o dispositivo é um dispositivo de nó único, depois que a atualização é aplicada, o dispositivo é reiniciado e há um tempo de inatividade.
   ![atualizar dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-5.png)

5. A atualização será iniciada. Depois que for atualizado com êxito, o dispositivo será reiniciado. A interface do usuário local não estará acessível durante esse tempo.

6. Depois que a reinicialização for concluída, você será levado à página **Entrar** . Para verificar se o software do dispositivo foi atualizado, na interface do usuário da Web local, vá para **manutenção**  >  **atualização de software**. A versão de software exibida neste exemplo é **1.6.1049.786**.

   ![atualizar dispositivo](./media/data-box-gateway-apply-updates/local-ui-update-6.png)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como [administrar seu gateway do Azure data Box](data-box-gateway-manage-users.md).
