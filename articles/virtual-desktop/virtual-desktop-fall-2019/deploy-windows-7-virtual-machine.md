---
title: Implantar a máquina virtual do Windows 7 Windows Desktop-Azure
description: Como configurar e implantar uma máquina virtual do Windows 7 na área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 0cb5b2ee8b8391dc4fcb78cc1d3bd212c44f1803
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82615403"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Implantar uma máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows

>[!IMPORTANT]
>Esse conteúdo se aplica à versão 2019 do outono que não dá suporte a Azure Resource Manager objetos da área de trabalho virtual do Windows.

O processo para implantar uma VM (máquina virtual) do Windows 7 na área de trabalho virtual do Windows é um pouco diferente do para VMs que executam versões posteriores do Windows. Este guia lhe dirá como implantar o Windows 7.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [criar um pool de hosts com o PowerShell](create-host-pools-powershell-2019.md) para criar um pool de hosts. Depois disso, siga as instruções em [criar pools de hosts no Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) para atribuir um ou mais usuários ao grupo de aplicativos da área de trabalho.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurar uma máquina virtual do Windows 7

Depois de concluir os pré-requisitos, você estará pronto para configurar sua VM do Windows 7 para implantação na área de trabalho virtual do Windows.

Para configurar uma VM do Windows 7 na área de trabalho virtual do Windows:

1. Entre no portal do Azure e pesquise a imagem do Windows 7 Enterprise ou carregue sua própria imagem personalizada do Windows 7 Enterprise (x64).  
2. Implante uma ou várias máquinas virtuais com o Windows 7 Enterprise como seu sistema operacional de host. Verifique se as máquinas virtuais permitem protocolo RDP (RDP) (a porta TCP/3389).
3. Conecte-se ao host do Windows 7 Enterprise usando o RDP e autentique com as credenciais que você definiu ao configurar a implantação. 
4. Adicione a conta usada ao conectar-se ao host com RDP para o grupo "Área de Trabalho Remota usuário". Se você não fizer isso, talvez não consiga se conectar à VM depois de associá-la ao seu domínio de Active Directory.
5. Vá para Windows Update em sua VM.
6. Instale todas as atualizações do Windows na categoria importante.
7. Instale todas as atualizações do Windows na categoria opcional (excluindo os pacotes de idiomas). Isso instala a atualização do protocolo RDP 8,0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) que você precisa para concluir estas instruções.
8. Abra o editor de política de grupo local e navegue até **configuração** > do computador**modelos administrativos** > **componentes** > do Windows**serviços de área de trabalho remota** > **host da sessão da área de trabalho remota** > **ambiente de sessão remota**.
9. Habilite a política protocolo RDP 8,0.
10. Ingresse essa VM em seu domínio de Active Directory.
11. Reinicie a máquina virtual executando o seguinte comando:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Siga as instruções [aqui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) para obter um token de registro.
13. [Baixe o agente de área de trabalho virtual do Windows para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Baixe o Gerenciador de agentes de área de trabalho virtual do Windows para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Abra o instalador do agente de área de trabalho virtual do Windows e siga as instruções. Quando solicitado, forneça a chave de registro que você criou na etapa 12.
16. Abra o instalador de área de trabalho virtual do Windows e siga as instruções.
17. Opcionalmente, bloqueie a porta TCP/3389 para remover o acesso direto de protocolo RDP à VM.

## <a name="next-steps"></a>Próximas etapas

Sua implantação de área de trabalho virtual do Windows agora está pronta para uso. [Baixe a versão mais recente do cliente de área de trabalho virtual do Windows](https://aka.ms/wvd/clients/windows) para começar.

Para obter uma lista de problemas conhecidos e instruções de solução de problemas do Windows 7 na área de trabalho virtual do Windows, consulte nosso artigo de solução de problemas em [solução de problemas de máquinas virtuais do Windows 7 na área de trabalho virtual do Windows](troubleshoot-windows-7-vm.md).
