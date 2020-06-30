---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 71e4b0b3b7bf84938123acf70ac18a93c3015a4d
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095132"
---
### <a name="create-a-firebase-project-and-enable-firebase-cloud-messaging-for-android"></a>Criar um projeto de Firebase e habilitar Firebase Cloud Messaging para Android

1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um projeto do Firebase inserindo **PushDemo** como o **Nome do projeto**.

    > [!NOTE]
    > Um nome exclusivo será gerado para você. Por padrão, ele é composto de uma variante de minúsculas do nome fornecido, além de um número gerado, separado por um traço. Você pode alterar isso se desejar, desde que o nome ainda seja globalmente exclusivo.

1. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**.

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)

1. Siga estas etapas na página **Adicionar Firebase ao seu aplicativo Android**.
    1. Para o **nome do pacote do Android**, insira um nome para o pacote. Por exemplo: `com.<organization_identifier>.<package_name>`.

        ![Especificar o nome do pacote](./media/notification-hubs-common-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    1. Selecione **Registrar aplicativo**.  
    1. Selecione **Baixar google-services.json**. Em seguida, salve o arquivo em uma pasta local para uso posterior e selecione **Avançar**.  

        ![Baixar google-services.json](./media/notification-hubs-common-enable-firebase-cloud-messaging/download-google-service-button.png)
    1. Selecione **Avançar**.
    1. Selecione **Continuar no console**

        > [!NOTE]
        > Se o botão **Continuar no console** não estiver habilitado, devido à verificação *verificar instalação*, escolha **Ignorar esta etapa**.

1. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-common-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)

    > [!NOTE]
    > Se você ainda não baixou o arquivo **google-services.json**, é possível fazer isso nesta página.

1. Mude para a guia **Mensagens de Nuvem** na parte superior. Copie e salve a **Chave do servidor** para uso posterior. Você usará esse valor para configurar o hub de notificação.

    ![Copiar a chave do servidor](./media/notification-hubs-common-enable-firebase-cloud-messaging/server-key.png)
