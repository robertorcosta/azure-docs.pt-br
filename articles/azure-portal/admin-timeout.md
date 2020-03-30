---
title: Defina o tempo de inatividade em nível de diretório para os usuários do portal Azure | Microsoft Docs
description: Os admins podem impor o tempo máximo de ociosidade antes de uma sessão ser assinada. A política de tempo de inatividade é definida no nível do diretório.
services: azure-portal
keywords: configurações, tempo hário
author: mgblythe
ms.author: mblythe
ms.date: 02/10/2020
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: e27135d09da7060f2a948e37f6026fe66fbef5b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096616"
---
# <a name="set-directory-level-inactivity-timeout"></a>Definir tempo de inatividade em nível de diretório

A configuração de tempo livre de inatividade ajuda a proteger seus recursos contra acesso não autorizado se os usuários esquecerem de proteger sua estação de trabalho. Quando um usuário está ocioso há algum tempo, sua sessão do portal Azure é automaticamente assinada. Os administradores na [função Administrador Global](../active-directory/users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) podem impor o tempo máximo de ociosidade antes de uma sessão ser assinada. A configuração de tempo de inatividade se aplica ao nível do diretório. Para obter mais informações sobre diretórios, consulte [Active Directory Domain Services Overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="configure-the-inactive-timeout-setting"></a>Configure a configuração de tempo inativo

Se você é um administrador global e deseja impor uma configuração de tempo total ocioso para todos os usuários do portal Azure, siga estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Configurações** no cabeçalho global da página.
3. Selecione o texto de link **Configure o tempo de tempo do nível do diretório**.

    ![Captura de tela mostrando configurações do portal com texto de link destacado](./media/admin-timeout/settings.png)

4. Uma nova página é aberta. Na página **Configurar o tempo de tempo de inatividade do nível do diretório,** **selecione Ativar o tempo de tempo ocioso do nível do diretório para o portal Azure** para ativar a configuração.
5. Em seguida, insira as **Horas** e **Minutos** para o tempo máximo que um usuário pode ficar ocioso antes de sua sessão ser automaticamente assinada.
6. Selecione **Aplicar**.

    ![Captura de tela mostrando página para definir o tempo de inatividade em nível de diretório](./media/admin-timeout/configure.png)

Para confirmar se a diretiva de tempo de inatividade está definida corretamente, **selecione Notificações** no cabeçalho da página global. Verifique se uma notificação de sucesso está listada.

  ![Captura de tela mostrando mensagem de notificação bem-sucedida para tempo de inatividade em nível de diretório](./media/admin-timeout/confirmation.png)

A configuração faz efeito para novas sessões. Não se aplicará imediatamente a nenhum usuário que já esteja conectado.

> [!NOTE]
> Se um administrador global tiver configurado uma configuração de tempo out em nível de diretório, os usuários poderão substituir a diretiva e definir sua própria duração de saída inativa. No entanto, o usuário deve escolher um intervalo de tempo menor do que o definido no nível do diretório pelo Administrador Global.
>

## <a name="next-steps"></a>Próximas etapas

* [Definir suas preferências do portal do Azure](set-preferences.md)
* [Exportar ou excluir configurações do usuário](azure-portal-export-delete-settings.md)
* [Ativar alto contraste ou alterar tema](azure-portal-change-theme-high-contrast.md)
