---
title: Definir configurações de Windows Update para trabalhar com o Azure Gerenciamento de Atualizações
description: Este artigo descreve as configurações de Windows Update que você configura para trabalhar com o Gerenciamento de Atualizações do Azure.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226272"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Definir configurações de Windows Update para Gerenciamento de Atualizações

O Azure Gerenciamento de Atualizações conta com [Windows Update cliente](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) para baixar e instalar atualizações do Windows. Há configurações específicas que são usadas pelo cliente do Windows Update ao se conectar ao Windows Server Update Services (WSUS) ou Windows Update. Muitas dessas configurações podem ser gerenciadas com:

- Editor de Política de Grupo Local
- Diretiva de grupos
- PowerShell
- Editando o registro diretamente

Gerenciamento de Atualizações respeita muitas das configurações especificadas para controlar o cliente de Windows Update. Se você usar configurações para habilitar atualizações não Windows, Gerenciamento de Atualizações também gerenciará essas atualizações. Se você quiser habilitar o download de atualizações antes que ocorra uma implantação de atualização, a implantação da atualização poderá ser mais rápida, mais eficiente e menos provável de exceder a janela de manutenção.

## <a name="pre-download-updates"></a>Atualizações de pré-download

Para configurar o download automático de atualizações, mas não instalá-las automaticamente, você pode usar Política de Grupo para definir a [configuração definir atualizações automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) como **3**. Essa configuração permite downloads das atualizações necessárias em segundo plano e notifica que as atualizações estão prontas para serem instaladas. Dessa forma, Gerenciamento de Atualizações permanece no controle de agendas, mas as atualizações podem ser baixadas fora da janela de manutenção Gerenciamento de Atualizações. Esse comportamento impede que a **janela de manutenção exceda** erros no gerenciamento de atualizações.

Você pode habilitar essa configuração usando o PowerShell, executando o seguinte comando:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Definir configurações de reinicialização

As chaves do registro listadas na [configuração de atualizações automáticas editando o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [as chaves do registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem fazer com que os computadores sejam reinicializados, mesmo se você especificar **nunca reinicializar** nas configurações de **implantação de atualização** . Configure essas chaves do registro para se adequar melhor ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, Windows Update cliente é configurado para fornecer atualizações somente para o Windows. Se você habilitar a configuração **fornecer atualizações para outros produtos da Microsoft ao atualizar o Windows** , também receberá atualizações para outros produtos, incluindo patches de segurança para Microsoft SQL Server e outros softwares da Microsoft. Essa opção pode ser configurada se você tiver baixado e copiado os [arquivos de modelo administrativo](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) mais recentes disponíveis para o Windows 2016 e superior.

Se você estiver executando o Windows Server 2012 R2, essa configuração não poderá ser definida por Política de Grupo. Execute o seguinte comando do PowerShell nesses computadores. Gerenciamento de Atualizações está em conformidade com essa configuração.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Definições de configuração do WSUS

Gerenciamento de Atualizações dá suporte a configurações do WSUS. As configurações do WSUS que você pode configurar para trabalhar com Gerenciamento de Atualizações estão listadas abaixo.

### <a name="intranet-microsoft-update-service-location"></a>Local do serviço de atualização da intranet da Microsoft

Você pode especificar as fontes para verificação e download de atualizações em [especificar local do serviço de Microsoft Update de intranet](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Por padrão, Windows Update cliente está configurado para baixar atualizações do Windows Update. Quando você especifica um servidor do WSUS como uma fonte para seus computadores, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará. 

Para restringir os computadores apenas ao serviço de atualização interno, configure o [não se conecte a nenhum local Windows Update Internet](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Depois de definir as configurações de Windows Update, você pode agendar uma implantação de atualização seguindo as instruções em [gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
