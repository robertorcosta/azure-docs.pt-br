---
title: Implantar a máquina virtual do Windows 7 Windows Virtual Desktop - Azure
description: Como configurar e implantar uma máquina virtual do Windows 7 no Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 158dd9d6a38cc1aaf7667c0b16518e23b3f7cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366676"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop"></a>Implantar uma máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows

O processo para implantar uma máquina virtual do Windows 7 (VM) no Windows Virtual Desktop é ligeiramente diferente do que para VMs que executam versões posteriores do Windows. Este guia lhe dirá como implantar o Windows 7.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Criar um pool de host com o PowerShell](create-host-pools-powershell.md) para criar um pool de host. Depois disso, siga as instruções em [Criar pools de host no Azure Marketplace](create-host-pools-azure-marketplace.md#optional-assign-additional-users-to-the-desktop-application-group) para atribuir um ou mais usuários ao grupo de aplicativos de desktop.

## <a name="configure-a-windows-7-virtual-machine"></a>Configure uma máquina virtual do Windows 7

Depois de fazer os pré-requisitos, você está pronto para configurar sua VM do Windows 7 para implantação no Windows Virtual Desktop.

Para configurar um VM do Windows 7 no Windows Virtual Desktop:

1. Faça login no portal Azure e procure a imagem do Windows 7 Enterprise ou carregue sua própria imagem personalizada do Windows 7 Enterprise (x64).  
2. Implante uma ou várias máquinas virtuais com o Windows 7 Enterprise como seu sistema operacional host. Certifique-se de que as máquinas virtuais permitem rdp (Remote Desktop Protocol) (a porta TCP/3389).
3. Conecte-se ao host Windows 7 Enterprise usando o RDP e autentique com as credenciais definidas enquanto configura sua implantação. 
4. Adicione a conta usada ao se conectar ao host com RDP ao grupo "Usuário de desktop remoto". Se você não fizer isso, você pode não ser capaz de se conectar à VM depois de se juntar a ele ao domínio do Active Directory.
5. Vá para Windows Update em sua VM.
6. Instale todas as atualizações do Windows na categoria Importante.
7. Instale todas as atualizações do Windows na categoria Opcional (excluindo pacotes de idiomas). Isso instala a atualização remote desktop protocol 8.0[(KB2592687)](https://www.microsoft.com/download/details.aspx?id=35387)que você precisa para completar essas instruções.
8. Abra o Editor de Políticas de Grupo Local e navegue até**modelos administrativos** > de **configuração** > de computador Os componentes do**Windows Serviços remotos** > **da** > área de trabalho**remota saem do** > **ambiente remoto da sessão de sessão de desktop**.
9. Habilite a diretiva Remote Desktop Protocol 8.0.
10. Junte-se a esta VM ao domínio Active Directory.
11. Reinicie a máquina virtual executando o seguinte comando:
    
     ```cmd
     shutdown /r /t 0
     ```
    
12. Siga as instruções [aqui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) para obter um token de registro.
13. [Baixe o Windows Virtual Desktop Agent para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Baixe o Gerenciador de Agente de Desktop Virtual do Windows para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Abra o instalador do Agente de Desktop Virtual do Windows e siga as instruções. Quando solicitado, dê a chave de registro que você criou na etapa 12.
16. Abra o instalador de desktop virtual do Windows e siga as instruções.
17. Opcionalmente, bloqueie a porta TCP/3389 para remover o acesso direto do Protocolo de Área de Trabalho Remota à VM.

## <a name="next-steps"></a>Próximas etapas

Sua implantação do Windows Virtual Desktop está agora pronta para uso. [Baixe a versão mais recente do cliente Windows Virtual Desktop](https://aka.ms/wvd/clients/windows) para começar.

Para obter uma lista de problemas conhecidos e instruções de solução de problemas para o Windows 7 no Windows Virtual Desktop, consulte nosso artigo de solução de problemas em [máquinas virtuais do Windows 7 no Windows Virtual Desktop](troubleshoot-windows-7-vm.md).
