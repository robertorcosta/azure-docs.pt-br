---
title: Solucionar problemas falhas na descoberta do VMware vCenter na recuperação do site do Azure
description: Este artigo descreve como solucionar problemas de falha sustiva do VMware vCenter no Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: mayg
ms.openlocfilehash: f00c7b12accde9df9a5708a2b8b378d70428318d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74091246"
---
# <a name="troubleshoot-vcenter-server-discovery-failures"></a>Solucionar problemas de falha na detecção do servidor vCenter

Este artigo ajuda você a solucionar problemas que ocorrem devido a falhas de descoberta do VMware vCenter.

## <a name="non-numeric-values-in-the-maxsnapshots-property"></a>Valores não numéricos na propriedade maxSnapShots

Em versões anteriores ao 9.20, o vCenter se desconecta `snapshot.maxSnapShots` quando recupera um valor não numérico para a propriedade em uma VM.

Este problema é identificado pelo Erro ID 95126.

    ERROR :: Hit an exception while fetching the required informationfrom vCenter/vSphere.Exception details:
    System.FormatException: Input string was not in a correct format.
       at System.Number.StringToNumber(String str, NumberStyles options, NumberBuffer& number, NumberFormatInfo info, Boolean parseDecimal)
       at System.Number.ParseInt32(String s, NumberStyles style, NumberFormatInfo info)
       at VMware.VSphere.Management.InfraContracts.VirtualMachineInfo.get_MaxSnapshots()
    
Como resolver o problema:

- Identifique a VM e defina o valor como um valor numérico (configurações de edição de VM no vCenter).

Ou

- Atualize seu servidor de configuração para a versão 9.20 ou posterior.

## <a name="proxy-configuration-issues-for-vcenter-connectivity"></a>Problemas de configuração proxy para conectividade vCenter

o vCenter Discovery honra as configurações de proxy padrão do sistema configuradas pelo usuário do sistema. O serviço DRA honra as configurações de proxy fornecidas pelo usuário durante a instalação do servidor de configuração usando o modelo de instalador de configuração unificada ou OVA. 

Em geral, o proxy é usado para se comunicar com redes públicas; como se comunicar com o Azure. Se o proxy estiver configurado e o vCenter estiver em um ambiente local, ele não será capaz de se comunicar com o DRA.

As seguintes situações ocorrem quando este problema é encontrado:

- O servidor \<vCenter vCenter> não pode ser alcançado por causa do erro: o servidor remoto retornou um erro: (503) Servidor Indisponível
- O servidor \<vCenter vCenter> não pode ser alcançado devido ao erro: o servidor remoto retornou um erro: incapaz de se conectar ao servidor remoto.
- Não é possível conectar-se ao servidor vCenter/ESXi.

Como resolver o problema:

Baixe a [ferramenta PsExec](https://aka.ms/PsExec). 

Use a ferramenta PsExec para acessar o contexto do usuário do sistema e determinar se o endereço proxy está configurado. Em seguida, você pode adicionar vCenter à lista de desviousando os seguintes procedimentos.

Para configuração proxy do Discovery:

1. Abra o IE no contexto do usuário do sistema usando a ferramenta PsExec.
    
    psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"

2. Modifique as configurações de proxy no Internet Explorer para ignorar o endereço IP do vCenter.
3. Reinicie o serviço tmanssvc.

Para configuração proxy DRA:

1. Abra um prompt de comando e abra a pasta Microsoft Azure Site Recovery Provider.
 
    **cd C:\Arquivos do programa\Microsoft Azure Provedor de recuperação do site**

3. A partir do prompt de comando, execute o seguinte comando.
   
   **DRCONFIGURATOR. EXE /configure /AddBypassUrls [Endereço IP/FQDN do vCenter Server fornecido no momento da adição do vCenter]**

4. Reinicie o serviço do provedor DRA.

## <a name="next-steps"></a>Próximas etapas

[Gerenciar o servidor de configuração para recuperação de desastres de VM do VMware](https://docs.microsoft.com/azure/site-recovery/vmware-azure-manage-configuration-server#refresh-configuration-server) 
