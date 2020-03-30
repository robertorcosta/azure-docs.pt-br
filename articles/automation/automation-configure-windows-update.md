---
title: Configure as configurações do Windows Update para trabalhar com o Azure Update Management
description: Este artigo descreve as configurações do Windows Update que você configura para trabalhar com o Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279020"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Configure as configurações do Windows Update para gerenciamento de atualizações

O Azure Update Management conta com o [cliente Windows Update](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) para baixar e instalar atualizações do Windows. Existem configurações específicas que são usadas pelo cliente Windows Update ao se conectar ao Windows Server Update Services (WSUS) ou ao Windows Update. Muitas dessas configurações podem ser gerenciadas com:

- Editor de Política de Grupo Local
- Política de Grupo
- PowerShell
- Editando diretamente o Registro

O Gerenciamento de atualizações respeita muitas das configurações especificadas para controlar o cliente Windows Update. Se você usar configurações para habilitar atualizações não-Windows, o Gerenciamento de atualizações também gerenciará essas atualizações. Se você quiser habilitar o download de atualizações antes que uma implantação de atualização ocorra, a implantação da atualização pode ser mais rápida, mais eficiente e menos provável de exceder a janela de manutenção.

## <a name="pre-download-updates"></a>Atualizações de pré-download

Para configurar o download automático de atualizações, mas não instalá-las automaticamente, você pode usar a Diretiva de Grupo para definir a [configuração Configure Automatic Updates](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) para **3**. Esta configuração permite o download das atualizações necessárias em segundo plano e notifica que as atualizações estão prontas para serem instaladas. Desta forma, o Update Management permanece no controle dos horários, mas as atualizações podem ser baixadas fora da janela de manutenção do Update Management. Esse comportamento evita que **a janela de manutenção exceda** os erros no Gerenciamento de Atualizações.

Você pode habilitar essa configuração usando o PowerShell, executando o seguinte comando:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Configurar configurações de reinicialização

As chaves de registro listadas na [Configuração de Atualizações Automáticas editando as](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) chaves de registro e [registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem fazer com que suas máquinas sejam reiniciadas, mesmo se você especificar **Never Reboot** nas configurações De implantação de **atualização.** Configure essas chaves de registro para melhor se adequar ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, o cliente Windows Update está configurado para fornecer atualizações apenas para o Windows. Se você ativar as **atualizações do Give me para outros produtos da Microsoft quando atualizo a** configuração do Windows, você também receberá atualizações para outros produtos, incluindo patches de segurança para o Microsoft SQL Server e outros softwares da Microsoft. Esta opção pode ser configurada se você tiver baixado e copiado os [arquivos de modelo administrativo](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) mais recentes disponíveis para windows 2016 ou superior.

Se você estiver executando o Windows Server 2012 R2, essa configuração não poderá ser configurada pela Diretiva de Grupo. Execute o seguinte comando PowerShell nessas máquinas. O Gerenciamento de Atualização está em conformidade com esta configuração.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Configurações de configuração WSUS

O Update Management suporta as configurações do WSUS. As configurações do WSUS que você pode configurar para trabalhar com o Gerenciamento de Atualizações estão listadas abaixo.

### <a name="intranet-microsoft-update-service-location"></a>Intranet Microsoft atualizar local de serviço

Você pode especificar fontes para digitalizar e baixar atualizações em [Especificar o local de serviço da Intranet Microsoft Update](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Por padrão, o cliente Windows Update está configurado para baixar atualizações do Windows Update. Quando você especifica um servidor WSUS como fonte para suas máquinas, se as atualizações não forem aprovadas no WSUS, a implantação de atualização falhará. 

Para restringir as máquinas apenas a esse serviço de atualização interna, configure [Não conecte-se a nenhum local da Internet do Windows Update](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Próximas etapas

Depois de configurar as configurações do Windows Update, você pode agendar uma implantação de atualização seguindo as instruções em [Gerenciar atualizações e patches para suas VMs do Azure](automation-tutorial-update-management.md).
