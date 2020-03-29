---
title: Defina as preferências do portal Azure | Microsoft Docs
description: Você pode alterar as configurações padrão do portal Azure para atender às suas próprias preferências. As configurações incluem tempo de sessão inativo, exibição padrão, modo de menu, contraste, tema, notificações e formatos de idioma e regionais
services: azure-portal
keywords: configurações, tempo, idioma, regional
author: mgblythe
ms.author: mblythe
ms.date: 12/19/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 7bcfdeec832b14eb53c0dab6cb2f53970d85c804
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310672"
---
# <a name="set-your-azure-portal-preferences"></a>Definir suas preferências do portal do Azure

Você pode alterar as configurações padrão do portal Azure para atender às suas próprias preferências. Cada uma das configurações listadas abaixo pode ser alterada:

* [Tempo de sessão inativo](#change-the-idle-duration-for-inactive-sign-out)
* [Exibição padrão](#choose-your-default-view)
* [Modo menu portal](#choose-a-portal-menu-mode)
* [Tema de cor e alto contraste](#choose-a-theme)
* [Notificações pop-up](#enable-or-disable-pop-up-notifications)
* [Idioma e formato de região](#change-language-and-regional-settings)

## <a name="change-general-portal-settings"></a>Alterar configurações gerais do portal

1. Faça login no [portal Azure](https://portal.azure.com).
2. Selecione **Configurações** no cabeçalho global da página.

    ![Captura de tela mostrando ícones globais de cabeçalho de página com configurações destacadas](./media/set-preferences/header-settings.png)

### <a name="change-the-idle-duration-for-inactive-sign-out"></a>Alterar a duração ociosa para a saída inativa

A configuração de tempo livre de inatividade ajuda a proteger os recursos contra acesso não autorizado se você esquecer de proteger sua estação de trabalho. Depois de ficar ocioso por um tempo, você está automaticamente fora da sessão do portal Azure.

Selecione o drop-down em **Sign me out quando estiver inativo**. Escolha a duração após a qual a sessão do portal Do Zure é assinada se você estiver ocioso.

   ![Captura de tela mostrando configurações do portal com configurações de tempo inativo destacadas](./media/set-preferences/inactive-signout-user.png)

A mudança é salva automaticamente. Se você estiver ocioso, a sessão do portal Azure sairá após a duração definida.

Essa configuração também pode ser feita por um admin no nível do diretório para impor um tempo máximo de ociosidade. Se um admin tiver feito uma configuração de tempo out no nível do diretório, você ainda pode definir sua própria duração de saída inativa. Escolha uma configuração de tempo menor do que a definida no nível do diretório.

Se o seu admin habilitou uma política de tempo de tempo de inatividade, selecione a caixa de seleção de **política de tempo de inatividade** do diretório. Defina um intervalo de tempo menor do que a configuração da diretiva.

   ![Captura de tela mostrando configurações do portal com a substituição da configuração de diretiva de política de tempo de inatividade de inatividade destacada](./media/set-preferences/inactive-signout-override.png)


> [!NOTE]
> Se você é um administrador e deseja impor uma configuração de tempo inativo para todos os usuários do portal Azure, consulte [Set directory-level inactivity timeout para usuários do portal Azure](admin-timeout.md)
>

### <a name="choose-your-default-view"></a>Escolha sua exibição padrão 

Você pode alterar a página que abre por padrão quando seu login no portal Azure.

   ![Captura de tela mostrando configurações do portal Azure com exibição padrão destacada](./media/set-preferences/default-view.png)

A configuração de exibição padrão controla a exibição do portal Azure quando você faz login. Você pode optar por abrir o Azure Home por padrão ou pela exibição do Painel de Controle.

* **O** lar não pode ser personalizado.  Ele exibe atalhos para serviços populares do Azure e lista os recursos que você usou mais recentemente. Também damos links úteis para recursos como o Microsoft Learn e o roteiro do Azure.
* Os painéis podem ser personalizados para criar um espaço de trabalho projetado apenas para você. Por exemplo, você pode construir um painel focado em projeto, tarefa ou função. Se você selecionar **Dashboard,** sua exibição padrão irá para o painel de instrumentos usado mais recentemente.

### <a name="choose-a-portal-menu-mode"></a>Escolha um modo de menu de portal

O modo padrão do menu portal controla quanto espaço o menu do portal ocupa na página.

* Quando o menu portal está no modo **flyout,** ele está escondido até que você precise dele. Selecione o ícone do menu para abrir ou fechar o menu.
* Se você escolher o modo **docked** para o menu portal, ele está sempre visível. Você pode desabar o menu para fornecer mais espaço de trabalho. 

### <a name="choose-a-theme"></a>Escolher um tema

O tema escolhido afeta as cores de fundo e fonte que aparecem no portal Azure. Você pode selecionar entre um dos quatro temas de cor predefinidos. Selecione cada miniatura para encontrar o tema que melhor combina com você.

   ![Captura de tela mostrando configurações do portal Azure com temas destacados](./media/set-preferences/theme.png)

Você pode escolher um dos temas de alto contraste em vez disso. As configurações de alto contraste tornam o portal Azure mais fácil de ler para usuários com deficiência visual e anulam todas as outras seleções temáticas. Para obter mais informações, consulte [Ativar o alto contraste ou alterar o tema](azure-portal-change-theme-high-contrast.md).

### <a name="enable-or-disable-pop-up-notifications"></a>Habilite ou desative notificações pop-up

Notificações são mensagens do sistema relacionadas à sua sessão atual. Eles fornecem informações como o seu saldo de crédito atual, quando os recursos que você acabou de criar ficam disponíveis, ou confirmam sua última ação, por exemplo. Quando as notificações pop-up são ligadas, as mensagens são exibidas brevemente no canto superior da tela. 

Para ativar ou desativar notificações pop-up, selecione ou desselecione a **caixa de seleção Ativar notificações pop-up.**

   ![Captura de tela mostrando configurações do portal Azure com notificações pop-up destacadas](./media/set-preferences/popup-notifications.png)

Para ler todas as notificações recebidas durante a sessão atual, **selecione Notificações** do cabeçalho global.

   ![Captura de tela mostrando cabeçalho global do portal Azure com notificações destacadas](./media/set-preferences/read-notifications.png)

Se você quiser ler notificações de sessões anteriores, procure eventos no registro de atividades. Para saber mais, leia [Exibir e recuperar eventos de registro de atividades do Azure](/azure/azure-monitor/platform/activity-log-view).

### <a name="settings-under-useful-links"></a>Configurações em links úteis

Se você fez alterações nas configurações do portal Azure e deseja descartá-las, selecione **Restaurar configurações padrão**. Quaisquer alterações que você fez nas configurações do portal serão perdidas. Essa opção não afeta as personalizações do painel.

Para obter mais informações sobre **Exportar todas as configurações** ou **excluir todas as configurações e painéis privados,** consulte [Exportar ou excluir as configurações do usuário](azure-portal-export-delete-settings.md).

## <a name="change-language-and-regional-settings"></a>Alterar a linguagem e as configurações regionais

Existem duas configurações que controlam como o texto no portal Azure aparece. A **configuração de Idioma** controla o idioma que você vê para texto no portal Azure. **O formato regional** controla a forma como as datas, a hora, os números e a moeda são mostrados.

Para alterar o idioma usado no portal Azure, use o down-down para selecionar na lista de idiomas disponíveis.

A seleção do formato regional muda para exibir opções regionais apenas para o idioma selecionado. Para alterar essa seleção automática, use o drop-down para escolher o formato regional que deseja.

Por exemplo, se você selecionar o inglês como seu idioma e, em seguida, selecionar os Estados Unidos como o formato regional, a moeda é mostrada em dólares americanos. Se você selecionar o inglês como idioma e, em seguida, selecionar a Europa como o formato regional, a moeda é mostrada em euros.

Selecione **Aplicar** para atualizar as configurações do seu idioma e formato regional.

   ![Captura de tela mostrando configurações de idioma e formato regional](./media/set-preferences/language.png)

>[!NOTE]
>Essas configurações linguísticas e regionais afetam apenas o portal Azure. Os links de documentação abertos em uma nova guia ou janela usarão as configurações de idioma do seu navegador para determinar o idioma a ser exibido.
>

## <a name="next-steps"></a>Próximas etapas

* [Criar e compartilhar painéis personalizados](azure-portal-dashboards.md)
* [Série de vídeos explicativos sobre o portal do Azure](azure-portal-video-series.md)
