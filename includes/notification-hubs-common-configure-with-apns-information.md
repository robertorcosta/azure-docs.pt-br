---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 537c86512db4706077d0089a4f71ee945623c26b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095128"
---
### <a name="configure-your-notification-hub-with-apns-information"></a>Configure seu hub de notificações com as informações de APNS

Em **Serviços de Notificação**, selecione **Apple** e siga as etapas apropriadas com base na abordagem que você selecionou antes na seção [Criar um certificado para os Hubs de Notificação](#creating-a-certificate-for-notification-hubs).  

> [!NOTE]
> Use **Produção** no **Modo de Aplicativo** somente caso queira enviar notificações por push aos usuários que tenham comprado seu aplicativo da loja.

### <a name="option-1-using-a-p12-push-certificate"></a>OPÇÃO UM: Usando um certificado push .p12

1. Selecione **Certificado**.

1. Selecione o ícone arquivo.

1. Selecione o arquivo .p12 que você exportou anteriormente e, em seguida, clique em **Abrir**.

1. Se necessário, especifique a senha correta.

1. Selecione o modo **Sandbox**.

1. Clique em **Salvar**.

### <a name="option-2-using-token-based-authentication"></a>OPÇÃO DOIS: Usando a autenticação baseada em token

1. Selecione **Token**.
1. Insira os seguintes valores que você adquiriu antes:

    - **ID da Chave**
    - **ID do Pacote**
    - **ID da Equipe**
    - **Token**

1. Selecione **Área restrita**.
1. Clique em **Salvar**.
