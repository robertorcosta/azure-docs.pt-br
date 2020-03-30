---
title: Gerenciar os dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como gerenciar dispositivos no aplicativo Azure IoT Central.
author: sarahhubbard
ms.author: sahubbar
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 8725a822c575ce80b9810d56bfd072241ded4c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157935"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerenciar dispositivos no aplicativo Azure IoT Central



Este artigo descreve como, como operador, gerenciar dispositivos no seu Microsoft IoT Central. Como um operador, você pode:

- Use a página **Dispositivos** para visualizar, adicionar e excluir dispositivos conectados ao aplicativo Azure IoT Central.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do dispositivo atualizados alterando os valores armazenados nas propriedades do dispositivo a partir de suas exibições.
- Controle o comportamento de seus dispositivos atualizando uma configuração em um dispositivo específico a partir de suas visualizações.

## <a name="view-your-devices"></a>Exibir dispositivos

Para exibir um dispositivo individual:

1. Escolha **dispositivos** no painel esquerdo. Aqui você vê uma lista de todos os dispositivos e modelos de seus dispositivos.

1. Escolha um modelo de dispositivo.

1. No painel direito da página **Dispositivos,** você vê uma lista de dispositivos criados a partir desse modelo de dispositivo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para o dispositivo:

    ![Página Detalhes do Dispositivo](./media/howto-manage-devices/devicelist.png)


## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo ao aplicativo Azure IoT Central:

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo, a partir do qual você deseja criar um dispositivo.

1. Escolha + **Novo**.

1. Ligue ou desligue o **alternador simulado** **para ligar** ou **desligar**. Um dispositivo real é para um dispositivo físico que você conecta ao aplicativo Azure IoT Central. Um dispositivo simulado tem dados de exemplo gerados a você pelo Azure IoT Central.

1. Clique em **Criar**.

1. Este dispositivo agora aparece na lista de dispositivos para este modelo. Selecione o dispositivo para ver a página de detalhes do dispositivo que contém todas as visualizações do dispositivo.

## <a name="import-devices"></a>Importar dispositivos

Para conectar um grande número de dispositivos ao seu aplicativo, você pode importar em massa os dispositivos de um arquivo CSV. O arquivo CSV deve ter as seguintes colunas e cabeçalhos:

* **IOTC_DeviceID** – a ID do dispositivo deve estar toda em letras maiúsculas ou toda em letras minúsculas.
* **IOTC_DeviceName** – essa coluna é opcional.

Para dispositivos de registro em massa no aplicativo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja criar os dispositivos em massa.

    > [!NOTE]
    > Se você ainda não tem um modelo de dispositivo, então você pode importar dispositivos em Todos os dispositivos e **registrá-los** sem um modelo. Depois que os dispositivos tiverem sido importados, você pode migrar para um modelo.

1. Selecione **Importar**.

    ![Ação Importar](./media/howto-manage-devices/bulkimport1a.png)


1. Selecione o arquivo CSV que contém a lista de IDs dos Dispositivos a serem importados.

1. A importação de dispositivo inicia assim que o arquivo é carregado. Você pode rastrear o status de importação no painel Operações do dispositivo. Este painel aparece automaticamente após o início da importação ou você pode acessá-lo através do ícone do sino no canto superior direito.

1. Uma vez que a importação seja concluída, uma mensagem de sucesso é mostrada no painel Operações do dispositivo.

    ![Importação com êxito](./media/howto-manage-devices/bulkimport3a.png)


Se a operação de importação do dispositivo falhar, você verá uma mensagem de erro no painel Operações do dispositivo. É gerado um arquivo de log captura todos os erros que você pode baixar.

**Migração de dispositivos para um modelo**

Se você registrar dispositivos iniciando a importação em **Todos os dispositivos,** os dispositivos serão criados sem qualquer associação de modelos de dispositivos. Os dispositivos devem estar associados a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estas etapas para associar dispositivos a um modelo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha **Todos os dispositivos:**

    ![Dispositivos não associados](./media/howto-manage-devices/unassociateddevices1a.png)


1. Use o filtro na grade para determinar se o valor na coluna **Modelo do dispositivo** é "Não associado" para qualquer um de seus dispositivos.

1. Selecione os dispositivos que você deseja associar a um modelo:

1. Selecione **Migrar**:

    ![Associar Dispositivos](./media/howto-manage-devices/unassociateddevices2a.png)


1. Escolha o modelo na lista de modelos disponíveis e **selecione Migrar**.

1. Os dispositivos selecionados estão associados ao modelo de dispositivo que você escolheu.


## <a name="export-devices"></a>Exportar dispositivos

Para conectar um dispositivo real à IoT Central, você precisa de sua cadeia de conexão. Você pode exportar detalhes do dispositivo em massa para obter as informações necessárias para criar seqüências de conexão do dispositivo. O processo de exportação cria um arquivo CSV com a identidade do dispositivo, o nome do dispositivo e as chaves de todos os dispositivos selecionados.

Para exportar em massa os dispositivos do seu aplicativo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo do dispositivo do qual deseja exportar os dispositivos.

1. Selecione os dispositivos que deseja exportar e selecione a ação **Exportar.**

    ![Exportação](./media/howto-manage-devices/export1a.png)


1. O processo de exportação tem início. Você pode rastrear o status usando o painel Operações do dispositivo.

1. Quando a exportação for concluída, será mostrada uma mensagem com um link para baixar o arquivo gerado.

1. Selecione o link **'Baixar arquivo'** para baixar o arquivo para uma pasta local no disco.

    ![Exportação bem-sucedida](./media/howto-manage-devices/export2a.png)


1. O arquivo CSV exportado contém as seguintes colunas: ID do dispositivo, nome do dispositivo, chaves de dispositivo e impressões digitais de certificado X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Para obter mais informações sobre as strings de conexão e a conexão de dispositivos reais ao seu aplicativo IoT Central, consulte [a conectividade do dispositivo no Azure IoT Central](concepts-get-connected.md).

## <a name="delete-a-device"></a>Excluir um dispositivo

Para excluir um dispositivo real ou simulado do aplicativo Azure IoT Central:

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo que você deseja excluir.

1. Use as ferramentas de filtro para filtrar e procurar por seus dispositivos. Verifique a caixa ao lado dos dispositivos para excluir.

1. Escolha **Excluir**. Você pode rastrear o status desta exclusão no painel Operações do dispositivo.

## <a name="change-a-property"></a>Alterar uma propriedade

As propriedades da nuvem são os metadados do dispositivo associados ao dispositivo, como cidade e número de série. Propriedades graváveis controlam o comportamento de um dispositivo. Em outras palavras, elas permitem que você forneça entradas ao dispositivo.  As propriedades do dispositivo são definidas pelo dispositivo e são somente leitura dentro da IoT Central. Você pode visualizar e atualizar propriedades nas exibições **detalhes** do dispositivo para o seu dispositivo.

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo do dispositivo cujas propriedades você deseja alterar e selecione o dispositivo de destino.

1. Escolha a exibição que contém propriedades para o seu dispositivo, esta exibição permite que você insira valores e selecione **Salvar** na parte superior da página. Aqui você vê as propriedades que seu dispositivo tem e seus valores atuais. Propriedades na nuvem e propriedades graváveis têm campos editáveis, enquanto as propriedades do dispositivo são somente leitura. Para propriedades graváveis, você pode ver seu status de sincronização na parte inferior do campo. 

1. Modifique as propriedades para os valores necessários. Você pode modificar várias propriedades ao mesmo tempo e atualizá-las todas ao mesmo tempo.

1. Escolha **Salvar**. Se você salvou propriedades graváveis, os valores serão enviados para o seu dispositivo. Quando o dispositivo confirmar a alteração para a propriedade gravável, o status volta a **ser sincronizado**. Se você salvou uma propriedade em nuvem, o valor será atualizado.


## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como gerenciar dispositivos no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como usar grupos de dispositivos](tutorial-use-device-groups.md)

<!-- Next how-tos in the sequence -->
