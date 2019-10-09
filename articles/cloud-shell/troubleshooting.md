---
title: Solução de problemas do Azure Cloud Shell | Microsoft Docs
description: Solucionando problemas do Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 91dc87cd6bda93663fb4b4eae3d498ae56ba4b3e
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169593"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Solução de problemas e limitações do Azure Cloud Shell

As resoluções conhecidas para solucionar problemas no Azure Cloud Shell incluem:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Solução de problemas gerais

### <a name="early-timeouts-in-firefox"></a>Tempos limites iniciais no FireFox

- **Detalhes**: o Cloud Shell utiliza um websocket aberto para passar a entrada/saída para o navegador. FireFox tem políticas predefinidas que podem fechar o websocket prematuramente, causando tempos limites iniciais no Cloud Shell.
- **Resolução**: abra o FireFox e acesse "about:config" na caixa URL. Procure por "network.websocket.timeout.ping.request" e altere o valor de 0 para 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Desabilitando o Cloud Shell em um ambiente de rede bloqueado

- **Detalhes**: os administradores podem querer desabilitar o acesso ao Cloud Shell para seus usuários. O Cloud Shell utiliza o acesso ao domínio `ux.console.azure.com`, que pode ser negado, interrompendo qualquer acesso aos ponto de entrada de Cloud Shell, incluindo portal.azure.com, shell.azure.com, Visual Studio Code extensão de conta do Azure e docs.microsoft.com.
- **Resolução**: restrinja o acesso ao `ux.console.azure.com` por meio das configurações de rede para seu ambiente. Ícone do Cloud Shell ainda existirá no portal.azure.com, mas não se conectará com êxito ao serviço.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Diálogo de armazenamento – erro: 403 RequestDisallowedByPolicy

- **Detalhes**: a criação de uma conta de armazenamento por meio do Cloud Shell falha devido a uma política do Azure imposta pelo seu administrador. A mensagem de erro incluirá: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Resolução**: contate o administrador do Azure para remover ou atualizar a política do Azure que nega a criação de armazenamento.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Diálogo de armazenamento – erro: 400 DisallowedOperation

- **Detalhes**: ao usar uma assinatura do Azure Active Directory, não será possível criar armazenamento.
- **Resolução**: use uma assinatura do Azure capaz de criar recursos de armazenamento. Assinaturas do Azure AD não são capazes de criar recursos do Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Saída do terminal – erro: Falha ao conectar o terminal: não é possível estabelecer o websocket. Pressione `Enter` para reconectar.
- **Detalhes**: o Cloud Shell requer a capacidade de estabelecer uma conexão websocket com a infraestrutura do Cloud Shell.
- **Resolução**: verifique se você definiu as configurações de rede para permitir o envio de solicitações HTTPS e solicitações de websocket para domínios em *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Definir sua conexão de Cloud Shell para dar suporte ao uso do TLS 1.2
 - **Detalhes**: Para definir a versão do TLS para sua conexão com o Cloud Shell, você deve definir configurações específicas do navegador.
 - **Resolução**: navegue até as configurações de segurança do navegador e marque a caixa de seleção ao lado de “Usar TLS 1.2”.

## <a name="bash-troubleshooting"></a>Solução de problemas de Bash

### <a name="cannot-run-the-docker-daemon"></a>Não é possível executar o daemon do Docker

- **Detalhes**: o Cloud Shell utiliza um contêiner para hospedar o ambiente de shell e, como resultado, a execução do daemon deixa de ser permitida.
- **Resolução**: utilize o [docker-machine](https://docs.docker.com/machine/overview/), que está instalado por padrão, para gerenciar contêineres do Docker de um host remoto do Docker.

## <a name="powershell-troubleshooting"></a>Solução de problemas do PowerShell

### <a name="gui-applications-are-not-supported"></a>Não há suporte para aplicativos GUI

- **Detalhes**: se um usuário inicia um aplicativo GUI, o prompt não retorna. Por exemplo, quando um usuário clona um repositório do GitHub privado com autenticação de dois fatores habilitada, uma caixa de diálogo é exibida para completar a autenticação de dois fatores.
- **Resolução**: Feche e reabra o shell.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Solucionando problemas de gerenciamento remoto de máquinas virtuais do Azure
> [!NOTE]
> As VMs do Azure devem ter um endereço IP voltado para o público.

- **Detalhes**: devido às configurações do Firewall do Windows para WinRM, o usuário pode ver o seguinte erro: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Resolução**:  execute `Enable-AzVMPSRemoting` para habilitar todos os aspectos da comunicação remota do PowerShell no computador de destino.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` não atualiza o resultado na unidade do Azure

- **Detalhes**: por padrão, para otimizar a experiência do usuário, os resultados de `dir` são armazenados em cache na unidade do Azure.
- **Resolução**: depois de criar, atualizar ou remover um recurso do Azure, execute `dir -force` para atualizar os resultados na unidade do Azure.

## <a name="general-limitations"></a>Limitações gerais

O Azure Cloud Shell tem as seguintes limitações conhecidas:

### <a name="system-state-and-persistence"></a>Estado do sistema e persistência

O computador que fornece a sessão do Cloud Shell é temporário e é reciclado após a sessão ficar inativa por 20 minutos. O Cloud Shell exige a montagem de um compartilhamento de arquivos do Azure. Como resultado, sua assinatura deve ser capaz de configurar recursos de armazenamento para acessar o Cloud Shell. Outras considerações incluem:

- Com o armazenamento montado, apenas as modificações no diretório `clouddrive` persistem. No Bash, seu `$HOME` diretório também persiste.
- Os compartilhamentos de arquivo do Azure só podem ser montados em sua [região atribuída](persisting-shell-storage.md#mount-a-new-clouddrive).
  - No Bash, execute `env` para localizar sua região definida como `ACC_LOCATION`.
- Os Arquivos do Azure oferece suporte apenas a armazenamento com redundância local e a contas de armazenamento com redundância geográfica.

### <a name="browser-support"></a>Suporte ao navegador

O Cloud Shell é compatível com as versões mais recentes dos navegadores a seguir:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Não há suporte para o Safari no modo particular.

### <a name="copy-and-paste"></a>Copiar e colar

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limites de uso

O Cloud Shell destina-se a casos de uso interativos. Como resultado, quaisquer sessões não interativo e de longa execução são finalizadas sem aviso.

### <a name="user-permissions"></a>Permissões de usuário

As permissões são definidas como usuários regulares sem acesso sudo. Qualquer instalação fora do seu diretório `$Home` não será persistida.

## <a name="bash-limitations"></a>Limitações do Bash

### <a name="editing-bashrc"></a>Edição de .bashrc

Tome cuidado ao editar .bashrc, uma vez que isso pode causar erros inesperados no Cloud Shell.

## <a name="powershell-limitations"></a>Limitações do PowerShell

### <a name="preview-version-of-azuread-module"></a>Versão prévia do módulo AzureAD

Atualmente, `AzureAD.Standard.Preview`, uma versão prévia baseada no .NET Standard, o módulo está disponível. Esse módulo fornece funcionalidade como `AzureAD`.

### <a name="sqlserver-module-functionality"></a>Funcionalidade do módulo `SqlServer`

O módulo `SqlServer` incluído no Cloud Shell tem suporte apenas de pré-lançamento para o PowerShell Core. Especificamente, `Invoke-SqlCmd` ainda não está disponível.

### <a name="default-file-location-when-created-from-azure-drive"></a>Local padrão do arquivo quando criado da unidade do Azure

Usando cmdlets do PowerShell, os usuários não podem criar arquivos na unidade do Azure. Quando os usuários criam novos arquivos usando outras ferramentas como vim ou nano, os arquivos são salvos em `$HOME` por padrão.

### <a name="tab-completion-can-throw-psreadline-exception"></a>O preenchimento de guia pode lançar uma exceção de PSReadline

Se o EditMode do usuário no PSReadline estiver definido como Emacs, o usuário tentar exibir todas as possibilidades por meio do preenchimento com tab e o tamanho da janela for muito pequeno para exibir todas as possibilidades, o PSReadline lançará exceção em tratamento.

### <a name="large-gap-after-displaying-progress-bar"></a>Intervalo grande depois de exibir a barra de progresso

Se um comando ou usuário exibe uma barra de progresso, por exemplo, um preenchimento de guia na unidade `Azure:`, é possível que o cursor não esteja definido corretamente e uma lacuna apareça onde antes estava a barra de progresso.

### <a name="random-characters-appear-inline"></a>Caracteres aleatórios aparecem embutidos

Os códigos de sequência de posição do cursor, por exemplo `5;13R`, podem aparecer na entrada do usuário. Os caracteres podem ser removidos manualmente.

## <a name="personal-data-in-cloud-shell"></a>Dados pessoais no Cloud Shell

O Azure Cloud Shell leva seus dados pessoais a sério, os dados capturados e armazenados pelo serviço do Azure Cloud Shell são usados para fornecer padrões para a sua experiência, como o seu shell usado mais recentemente, tamanho da fonte preferencial, tipo de fonte preferencial e detalhes de compartilhamento de arquivos que deem suporte à unidade de nuvem. Se desejar exportar ou excluir esses dados, use as instruções a seguir.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Exportação
Para **exportar** as configurações de usuário Cloud Shell salvas para você, como o shell preferencial, o tamanho da fonte e o tipo de fonte, execute os seguintes comandos.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Inicializar o Azure Cloud Shell")](https://shell.azure.com)
2. Execute os seguintes comandos no PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Excluir
Para **excluir** as configurações de usuário Cloud Shell salvas para você, como o shell preferencial, o tamanho da fonte e o tipo de fonte, execute os seguintes comandos. Na próxima vez que você iniciar o Cloud Shell você deverá carregar um compartilhamento de arquivos novamente. 

>[!Note]
> Se você excluir as configurações de usuário, vá para arquivos do Azure para concluir essa ação. Vá para os seus arquivos do Azure para concluir essa ação.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Inicializar o Azure Cloud Shell")](https://shell.azure.com)
2. Execute os seguintes comandos no PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Limitações do Azure governamental
Azure Cloud Shell no Azure governamental só é acessível por meio do portal do Azure.
