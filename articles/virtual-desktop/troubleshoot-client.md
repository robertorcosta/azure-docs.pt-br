---
title: Solucionar problemas cliente de desktop remoto Windows Virtual Desktop - Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de inquilino do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 595762e6e8f22dddff30f1cff8c4bb79e89624b1
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473855"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Solucionar problemas do cliente de desktop remoto

Este artigo descreve problemas comuns com o cliente do Remote Desktop e como corrigi-los.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Cliente de desktop remoto para Windows 7 ou Windows 10 pára de responder ou não pode ser aberto

A partir da versão 1.2.790, você pode redefinir os dados do usuário da página Sobre ou usando um comando.

Use o comando a seguir para remover os dados do usuário, restaurar as configurações padrão e cancelar a assinatura de todos os espaços de trabalho.

```cmd
msrdcw.exe /reset [/f]
```

Se você estiver usando uma versão anterior do cliente Remote Desktop, recomendamos que você desinstale e reinstale o cliente.

## <a name="web-client-wont-open"></a>O cliente web não abrirá

Primeiro, teste sua conexão com a internet abrindo outro site no seu navegador; por exemplo, [www.bing.com](https://www.bing.com).

Use **nslookup** para confirmar que o DNS pode resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente se conectar com outro cliente, como cliente de desktop remoto para Windows 7 ou Windows 10, e verifique se você pode abrir o cliente web.

### <a name="opening-another-site-fails"></a>A abertura de outro site falha

Isso geralmente é causado por problemas de conexão de rede ou uma paralisação da rede. Recomendamos que entre em contato com o suporte da rede.

### <a name="nslookup-cannot-resolve-the-name"></a>Nslookup não pode resolver o nome

Isso geralmente é causado por problemas de conexão de rede ou uma paralisação da rede. Recomendamos que entre em contato com o suporte da rede.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>Seu cliente não pode se conectar, mas outros clientes em sua rede podem se conectar

Se o seu navegador começar a funcionar ou parar de funcionar enquanto estiver usando o cliente web, siga estas instruções para soluciona-lo:

1. Reinicie o navegador.
2. Limpe os cookies do navegador. [Veja como excluir arquivos de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe o cache do navegador. Consulte [o cache do navegador claro para o seu navegador](https://binged.it/2RKyfdU).
4. Abra o navegador no modo Privado.

## <a name="web-client-stops-responding-or-disconnects"></a>O cliente web pára de responder ou desconecta

Tente se conectar usando outro navegador ou cliente.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Outros navegadores e clientes também não funcionam ou não conseguem abrir

Se os problemas continuarem mesmo depois de você ter trocado de navegador, o problema pode não ser com seu navegador, mas com sua rede. Recomendamos que entre em contato com o suporte da rede.

## <a name="web-client-keeps-prompting-for-credentials"></a>O cliente web continua solicitando credenciais

Se o cliente Web continuar solicitando credenciais, siga estas instruções:

1. Confirme se a URL do cliente web está correta.
2. Confirme se as credenciais que você está usando são para o ambiente de desktop virtual do Windows vinculado à URL.
3. Limpe os cookies do navegador. Para obter mais detalhes, consulte [Como excluir arquivos de cookies no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe o cache do navegador. Para obter mais detalhes, consulte [Limpar o cache do navegador para o seu navegador](https://binged.it/2RKyfdU).
5. Abra seu navegador no modo Privado.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral sobre a solução de problemas do Windows Virtual Desktop e as faixas de escalonamento, consulte [Visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de solução de problemas.
- Para solucionar problemas ao criar um inquilino e um pool de host em um ambiente de Desktop Virtual do Windows, consulte [Criação de inquilino e pool de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte [Configuração da máquina virtual do host session](troubleshoot-vm-configuration.md).
- Para solucionar problemas ao usar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para passar por um tutorial de solução de problemas, consulte Tutorial: Implantação de [modelo do Gerenciador de recursos de solução de problemas](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).