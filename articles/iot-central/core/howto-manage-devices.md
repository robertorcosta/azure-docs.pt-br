---
title: Gerenciar os dispositivos no aplicativo Azure IoT Central | Microsoft Docs
description: Como um operador, saiba como gerenciar dispositivos no aplicativo Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: c3d07c95592c1c43a070afbbf5091693ce0a6611
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950347"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerenciar dispositivos no aplicativo Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como operador, gerenciar dispositivos no seu Microsoft IoT Central. Como um operador, você pode:

- Use a página **Device Explorer** para exibir, adicionar e excluir dispositivos conectados ao seu Microsoft IoT Central.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do dispositivo atualizados, alterando os valores armazenados nas propriedades de dispositivo.
- Controle o comportamento dos dispositivos, atualizando uma configuração em um dispositivo específico na página **Configurações**.

## <a name="view-your-devices"></a>Exibir dispositivos

Para exibir um dispositivo individual:

1. Escolha **Device Explorer** no painel esquerdo. Aqui você visualiza uma lista dos [modelos de dispositivos](howto-set-up-template.md).

1. Escolha um modelo de dispositivo na lista **Modelos**.

1. No painel direito da página do **Device Explorer**, você vê uma lista de dispositivos criados com base nesse modelo de dispositivo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para o dispositivo:

    ![Página Detalhes do Dispositivo](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo ao aplicativo Azure IoT Central:

1. Escolha **Device Explorer** no painel esquerdo.

1. Escolha o modelo de dispositivo, a partir do qual você deseja criar um dispositivo.

1. Escolha + **Novo**.

1. Escolha **Real** ou **Simulado**. Um dispositivo real é para um dispositivo físico que você conecta ao aplicativo Azure IoT Central. Um dispositivo simulado tem dados de exemplo gerados a você pelo Azure IoT Central.

## <a name="import-devices"></a>Importar dispositivos

Para conectar um grande número de dispositivos ao seu aplicativo, você pode importar em massa os dispositivos de um arquivo CSV. O arquivo CSV deve ter as seguintes colunas e cabeçalhos:

* **IOTC_DeviceID** – a ID do dispositivo deve estar toda em letras maiúsculas ou toda em letras minúsculas.
* **IOTC_DeviceName** – essa coluna é opcional.

Para dispositivos de registro em massa no aplicativo:

1. Escolha **Device Explorer** no painel esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja criar os dispositivos em massa.

    > [!NOTE]
    > Se você ainda não tiver um modelo de dispositivo, poderá importar dispositivos em **Dispositivos não associados** e registrá-los sem um modelo. Depois que dispositivos foram importados, você pode associá-los a um modelo.

1. Selecione **Importar**.

    ![Ação Importar](./media/howto-manage-devices/bulkimport1a.png)

1. Selecione o arquivo CSV que contém a lista de IDs dos Dispositivos a serem importados.

1. A importação de dispositivo inicia assim que o arquivo é carregado. É possível acompanhar o status de importação na parte superior da grade do dispositivo.

1. Depois que a importação estiver concluída, uma mensagem de êxito será exibida na grade do dispositivo.

    ![Importação com êxito](./media/howto-manage-devices/bulkimport3a.png)

Se a operação de importação do dispositivo falhar, uma mensagem de erro será exibida na grade do dispositivo. É gerado um arquivo de log captura todos os erros que você pode baixar.

**Associando dispositivos a um modelo**

Se você registrar dispositivos iniciando a importação em **Dispositivos não associados**, os dispositivos serão criados sem associação de modelos de dispositivo. Os dispositivos devem estar associados a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estas etapas para associar dispositivos a um modelo:

1. Escolha **Device Explorer** no painel esquerdo.

1. No painel esquerdo, escolha **Dispositivos não associados**:

    ![Dispositivos não associados](./media/howto-manage-devices/unassociateddevices1a.png)

1. Selecione os dispositivos que você deseja associar a um modelo:

1. Selecione **associar**:

    ![Associar Dispositivos](./media/howto-manage-devices/unassociateddevices2a.png)

1. Escolha o modelo na lista de modelos disponíveis e selecione **associar**.

1. Os dispositivos selecionados estão associados ao modelo de dispositivo que você escolheu.

> [!NOTE]
> Depois que um dispositivo tiver sido associado a um modelo, não poderá ser desassociado ou associado a um modelo diferente.

## <a name="export-devices"></a>Exportar dispositivos

Para conectar um dispositivo real à IoT Central, você precisa de sua cadeia de conexão. Você pode exportar detalhes do dispositivo em massa para obter as informações necessárias para criar cadeias de conexão de dispositivo. O processo de exportação cria um arquivo CSV com a identidade do dispositivo, o nome do dispositivo e as chaves para todos os dispositivos selecionados.

Para exportar em massa os dispositivos do seu aplicativo:

1. Escolha **Device Explorer** no painel esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja exportar os dispositivos.

1. Selecione os dispositivos que você deseja exportar e, em seguida, selecione a ação **Exportar** .

    ![Exportação](./media/howto-manage-devices/export1a.png)

1. O processo de exportação tem início. Você pode acompanhar o status na parte superior da grade.

1. Quando a exportação for concluída, será mostrada uma mensagem com um link para baixar o arquivo gerado.

1. Selecione a **mensagem de êxito** para baixar o arquivo em uma pasta local no disco.

    ![Exportação bem-sucedida](./media/howto-manage-devices/export2a.png)

1. O arquivo CSV exportado contém as seguintes colunas: ID do dispositivo, nome do dispositivo, chaves de dispositivo e impressões digitais de certificado X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Consulte [conectividade do dispositivo no Azure IOT central](concepts-connectivity.md), para obter mais informações sobre cadeias de conexão e conectar dispositivos reais ao seu aplicativo IOT central.

## <a name="delete-a-device"></a>Excluir um dispositivo

Para excluir um dispositivo real ou simulado do aplicativo Azure IoT Central:

1. Escolha **Device Explorer** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo que você deseja excluir.

1. Marque a caixa próxima ao dispositivo a ser excluído.

1. Clique em **Excluir**.

## <a name="change-a-device-setting"></a>Alterar uma configuração de dispositivo

As configurações controlam o comportamento de um dispositivo. Em outras palavras, elas permitem que você forneça entradas ao dispositivo. É possível exibir e atualizar as configurações do dispositivo na página **Detalhes do Dispositivo**.

1. Escolha **Device Explorer** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo cujas configurações você quer alterar.

1. Escolha a guia **configurações** . Aqui você vê todas as configurações que seu dispositivo tem e seus valores atuais. Para cada configuração é possível ver se o dispositivo ainda está sendo sincronizado.

1. Modifique as configurações para os valores de que você precisa. É possível modificar várias configurações de uma só vez e atualizá-las todas ao mesmo tempo.

1. Escolha **Atualizar**. Os valores são enviados ao dispositivo. Quando o dispositivo confirma a alteração da configuração, o status da configuração retorna para **sincronizado**.

## <a name="change-a-property"></a>Alterar uma propriedade

Propriedades são os metadados do dispositivo associados ao dispositivo, como cidade e número de série. É possível exibir e atualizar as propriedades na página **Detalhes do Dispositivo**.

1. Escolha **Device Explorer** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades você quer alterar.

1. Escolha a guia **Propriedades**, na qual todas as propriedades são exibidas.

1. Modificar as propriedades do aplicativo para os valores que necessários. É possível modificar várias propriedades de uma vez e atualizá-las todas ao mesmo tempo. Escolha **Atualizar**.

> [!NOTE]
> Não é possível alterar o valor das _propriedades do dispositivo_. As propriedades do dispositivo são definidas pelo dispositivo e são somente leitura no aplicativo Azure IoT Central.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu como gerenciar dispositivos no aplicativo Azure IoT Central, a próxima etapa sugerida é apresentada:

> [!div class="nextstepaction"]
> [Como usar conjuntos de dispositivos](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
