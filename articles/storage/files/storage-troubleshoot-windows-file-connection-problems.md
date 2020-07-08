---
title: Solucionar problemas de Arquivos do Azure no Windows | Microsoft Docs
description: Solução de problemas de Arquivos do Azure no Windows
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 05/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 89a5fa0be104c3a7b7e035f82d2fed80d4781701
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85511987"
---
# <a name="troubleshoot-azure-files-problems-in-windows"></a>Solucionar problemas de Arquivos do Azure no Windows

Este artigo lista os problemas comuns relacionados aos Arquivos do Microsoft Azure quando você se conecta de clientes Windows. Também fornece as possíveis causas e resoluções para esses problemas. Além das etapas de solução de problemas neste artigo, você também pode usar o [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5)   para garantir que o ambiente de cliente do Windows tenha pré-requisitos corretos. O AzFileDiagnostics automatiza a detecção da maioria dos sintomas mencionados neste artigo e ajuda a configurar seu ambiente para obter um desempenho ideal. Você também pode encontrar essas informações no [solucionador de problemas de compartilhamentos de arquivos do Azure](https://support.microsoft.com/help/4022301/troubleshooter-for-azure-files-shares) que fornece etapas para ajudá-lo com problemas de conexão/mapeamento/montagem de compartilhamentos de arquivos do Azure.

<a id="error5"></a>
## <a name="error-5-when-you-mount-an-azure-file-share"></a>Erro 5 ao montar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos, pode receber o erro a seguir:

- Ocorreu um erro de sistema 5. O acesso foi negado.

### <a name="cause-1-unencrypted-communication-channel"></a>Causa 1: Canal de comunicação não criptografado

Por motivos de segurança, as conexões para compartilhamentos de arquivos do Azure são bloqueadas se o canal de comunicação não está criptografado e a tentativa de conexão não é feita do mesmo datacenter onde residem os compartilhamentos de arquivos do Azure. As conexões não criptografadas dentro do mesmo datacenter também podem ser bloqueadas se o [transferência segura obrigatória](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está habilitada na conta de armazenamento. Um canal de comunicação criptografado é fornecido somente quando o sistema operacional do cliente do usuário dá suporte à criptografia SMB.

O Windows 8, o Windows Server 2012 e versões posteriores de cada solicitação de negociação de sistema que inclua o SMB 3.0, que dá suporte à criptografia.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

1. Conecte a partir de um cliente com suporte à criptografia SMB (Windows 8, Windows Server 2012 ou posterior) ou conecte a partir de uma máquina virtual que está no mesmo datacenter da conta de armazenamento do Azure usada para o compartilhamento de arquivos do Azure.
2. Verifique se a configuração [Transferência segura obrigatória](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está desabilitada na conta de armazenamento se o cliente não oferecer suporte à criptografia SMB.

### <a name="cause-2-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 2: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento 

Se as regras de firewall e de VNET (rede virtual) estiverem configuradas na conta de armazenamento, o tráfego de rede terá acesso negado a menos que o endereço IP do cliente ou da rede virtual tenha permissão de acesso.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-3-share-level-permissions-are-incorrect-when-using-identity-based-authentication"></a>Causa 3: permissões de nível de compartilhamento incorretas ao usar a autenticação baseada em identidade

Se os usuários estiverem acessando o compartilhamento de arquivos do Azure usando a autenticação Active Directory (AD) ou Azure Active Directory Domain Services (Azure AD DS), o acesso ao compartilhamento de arquivos falhará com o erro "acesso negado" se as permissões de nível de compartilhamento estiverem incorretas. 

### <a name="solution-for-cause-3"></a>Solução para a causa 3

Para atualizar as permissões de nível de compartilhamento, consulte [atribuir permissões de acesso a uma identidade](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable#2-assign-access-permissions-to-an-identity).

<a id="error53-67-87"></a>
## <a name="error-53-error-67-or-error-87-when-you-mount-or-unmount-an-azure-file-share"></a>Erro 53, Erro 67 ou Erro 87 ao montar ou desmontar um compartilhamento de arquivos do Azure

Quando você tenta montar um compartilhamento de arquivos do local ou de um datacenter diferente, você poderá receber os seguintes erros:

- Ocorreu um erro de sistema 53. O caminho da rede não foi encontrado.
- Ocorreu um erro de sistema 67. O nome de rede não foi encontrado.
- Ocorreu um erro de sistema 87. O parâmetro está incorreto.

### <a name="cause-1-port-445-is-blocked"></a>Causa 1: a porta 445 está bloqueada

Pode ocorrer um erro de sistema 53 ou erro de sistema 67 se a comunicação de saída na porta 445 para o datacenter de Arquivos do Azure estiver bloqueada. Para ver o resumo de ISPs que permitem ou proíbem o acesso a partir da porta 445, vá para [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Para verificar se o firewall ou ISP está bloqueando a porta 445, use a ferramenta [AzFileDiagnostics](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) ou o cmdlet `Test-NetConnection`. 

Para usar o `Test-NetConnection` cmdlet, o módulo Azure PowerShell deve ser instalado, consulte [instalar Azure PowerShell módulo](/powershell/azure/install-Az-ps) para obter mais informações. Lembre-se de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes referentes a sua conta de armazenamento.

   
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    
Se a conexão foi bem-sucedida, você verá a seguinte saída:
    
  
    ComputerName     : <your-storage-account-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
 

> [!Note]  
> O comando acima retorna o endereço IP atual da conta de armazenamento. Não há garantia de que esse endereço IP será sempre o mesmo; ele pode mudar a qualquer momento. Não codifique esse endereço IP em um script ou em uma configuração de firewall.

### <a name="solution-for-cause-1"></a>Solução para a causa 1

#### <a name="solution-1---use-azure-file-sync"></a>Solução 1 - Usar a Sincronização de Arquivos do Azure
Sincronização de Arquivos do Azure pode transformar seu Windows Server local em um cache rápido do seu compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. A Sincronização de Arquivos do Azure funciona na porta 443 e, portanto, pode ser usada como uma solução alternativa para acessar os arquivos do Azure de clientes que têm a porta 445 bloqueada. [Saiba como configurar o sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-extend-servers).

#### <a name="solution-2---use-vpn"></a>Solução 2 - Usar a VPN
Ao configurar uma VPN para sua conta de armazenamento específica, o tráfego passará por um túnel seguro em oposição à Internet. Siga as [instruções para configurar a VPN](storage-files-configure-p2s-vpn-windows.md) para acessar os Arquivos do Azure no Windows.

#### <a name="solution-3---unblock-port-445-with-help-of-your-ispit-admin"></a>Solução 3 - Desbloquear a porta 445 com a ajuda de seu ISP / administrador de TI
Trabalhe com seu departamento de ti ou ISP para abrir a porta 445 de saída para [intervalos de IP do Azure](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="solution-4---use-rest-api-based-tools-like-storage-explorerpowershell"></a>Solução 4 – Usar a API REST com base em ferramentas como o Gerenciador de armazenamento/Powershell
Os arquivos do Azure também dão suporte ao REST, além do SMB. O acesso a REST funciona pela porta 443 (tcp padrão). Há várias ferramentas que são escritas usando a API REST que possibilitam uma experiência avançada de interface do usuário. [Gerenciador de armazenamento](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) é um deles. [Baixe e instale o Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e conecte-se ao compartilhamento de arquivo respaldado pelos Arquivos do Azure. Você também pode usar o [PowerShell](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-powershell) que também a API REST do usuário.

### <a name="cause-2-ntlmv1-is-enabled"></a>Causa 2: NTLMv1 está habilitado

O erro de sistema 53 ou erro de sistema 87 também pode ocorrer se a comunicação NTLMv1 está habilitada no cliente. Os Arquivos do Azure dão suporte apenas a autenticação NTLMv2. Ter NTLMv1 habilitado faz com que o cliente esteja menos seguro. Portanto, a comunicação será bloqueada para Arquivos do Azure. 

Para verificar se essa é a causa do erro, verifique se a seguinte subchave do registro está definida como um valor de 3:

**HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel**

Para saber mais, confira o tópico [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) no TechNet.

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Reverter o valor **LmCompatibilityLevel** para o valor padrão de 3 na seguinte subchave do registro:

  **HKLM\SYSTEM\CurrentControlSet\Control\Lsa**

<a id="error1816"></a>
## <a name="error-1816-not-enough-quota-is-available-to-process-this-command-when-you-copy-to-an-azure-file-share"></a>Erro 1816 "Não há cota suficiente disponível para processar este comando" quando você copia para um compartilhamento de arquivos do Azure

### <a name="cause"></a>Causa

O Erro 1816 ocorre quando você atingir o limite superior de identificadores abertos simultâneos permitidos para um arquivo no computador onde o compartilhamento de arquivos está sendo montado.

### <a name="solution"></a>Solução

Reduza o número de identificadores abertos simultâneos fechando alguns identificadores e tentando novamente. Para obter mais informações, consulte [armazenamento do Microsoft Azure lista de verificação de desempenho e escalabilidade](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Para exibir identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) do PowerShell.  

Para fechar identificadores abertos para um compartilhamento de arquivos, diretório ou arquivo, use o cmdlet [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) do PowerShell.

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="noaaccessfailureportal"></a>
## <a name="error-no-access-when-you-try-to-access-or-delete-an-azure-file-share"></a>Erro "sem acesso" ao tentar acessar ou excluir um compartilhamento de arquivos do Azure  
Ao tentar acessar ou excluir um compartilhamento de arquivos do Azure no portal, você pode receber o seguinte erro:

Sem acesso  
Código de erro: 403 

### <a name="cause-1-virtual-network-or-firewall-rules-are-enabled-on-the-storage-account"></a>Causa 1: as regras de firewall ou de rede virtual estão habilitadas na conta de armazenamento

### <a name="solution-for-cause-1"></a>Solução para a causa 1

Verifique se regras de firewall e de rede virtual estão configuradas corretamente na conta de armazenamento. Para testar se as regras de firewall ou de rede virtuais estão causando o problema, altere temporariamente a configuração da conta de armazenamento para **Permitir o acesso de todas as redes**. Para saber mais, confira [Configurar redes virtuais e firewalls do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security).

### <a name="cause-2-your-user-account-does-not-have-access-to-the-storage-account"></a>Causa 2: sua conta de usuário não tem acesso à conta de armazenamento

### <a name="solution-for-cause-2"></a>Solução para a causa 2

Navegue até a conta de armazenamento onde o compartilhamento de arquivos do Azure está localizado, clique em **Controle de acesso (IAM)** e verifique se sua conta de usuário tem acesso à conta de armazenamento. Para saber mais, confira [Como proteger a conta de armazenamento com o RBAC (Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/storage/blobs/security-recommendations#data-protection).

<a id="open-handles"></a>
## <a name="unable-to-delete-a-file-or-directory-in-an-azure-file-share"></a>Não é possível excluir um arquivo ou diretório em um compartilhamento de arquivos do Azure
Ao tentar excluir um arquivo, você pode receber o seguinte erro:

O recurso especificado está marcado para exclusão por um cliente SMB.

### <a name="cause"></a>Causa
Esse problema normalmente ocorre se o arquivo ou diretório tiver um identificador aberto. 

### <a name="solution"></a>Solução

Se os clientes SMB tiverem fechado todos os identificadores abertos e o problema continuar ocorrendo, execute o seguinte:

- Use o cmdlet do PowerShell [Get-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/get-azstoragefilehandle) para exibir identificadores abertos.

- Use o cmdlet do PowerShell [Close-AzStorageFileHandle](https://docs.microsoft.com/powershell/module/az.storage/close-azstoragefilehandle) para fechar identificadores abertos. 

> [!Note]  
> Os cmdlets Get-AzStorageFileHandle e close-AzStorageFileHandle estão incluídos no módulo AZ PowerShell versão 2,4 ou posterior. Para instalar o módulo AZ PowerShell mais recente, consulte [instalar o Azure PowerShell Module](https://docs.microsoft.com/powershell/azure/install-az-ps).

<a id="slowfilecopying"></a>
## <a name="slow-file-copying-to-and-from-azure-files-in-windows"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Windows

Você pode ver o desempenho lento ao tentar transferir arquivos para o Serviço de Arquivos do Azure.

- Se você não tiver um requisito mínimo de tamanho de E / S específico, recomendamos usar 1 MiB como o tamanho de E / S para um desempenho ideal.
-   Se você sabe o tamanho final de um arquivo que você está estendendo com gravações e o seu software não tem problemas de compatibilidade com o final ainda não escrito desse arquivo que contém zeros, defina o tamanho do arquivo antecipadamente em vez de realizar cada gravação como uma gravação de extensão.
-   Use o método de cópia correto:
    -   Use [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para qualquer transferência entre dois compartilhamentos de arquivos.
    -   Use o [Robocopy](/azure/storage/files/storage-files-deployment-guide#robocopy) entre compartilhamentos de arquivos e um computador local.

### <a name="considerations-for-windows-81-or-windows-server-2012-r2"></a>Considerações para Windows 8.1 ou Windows Server 2012 R2

Para clientes que estão executando o Windows 8.1 ou o Windows Server 2012 R2, verifique se o hotfix [KB3114025](https://support.microsoft.com/help/3114025) está instalado. Esse hotfix melhora o desempenho dos identificadores de criação e fechamento.

Você pode executar o script abaixo para verificar se o hotfix foi instalado:

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

Se o hotfix foi instalado, a seguinte saída será exibida:

`HKEY_Local_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies {96c345ef-3cac-477b-8fcd-bea1a564241c} REG_DWORD 0x1`

> [!Note]
> As imagens do Windows Server 2012 R2 no Azure Marketplace têm o hotfix KB3114025 instalado por padrão desde dezembro de 2015.

<a id="shareismissing"></a>
## <a name="no-folder-with-a-drive-letter-in-my-computer-or-this-pc"></a>Nenhuma pasta com uma letra de unidade em "Meu Computador" ou "este computador"

Se você mapear um compartilhamento de arquivos do Azure como administrador por meio do uso de rede, o compartilhamento parece estar ausente.

### <a name="cause"></a>Causa

Por padrão, o Explorador de Arquivos do Windows não é executado como administrador. Se você executar net use em um prompt de comando de administrador, mapeará a unidade de rede como um administrador. Como as unidades mapeadas são focadas no usuário, a conta de usuário que está conectada não exibe as unidades se eles estão montadas em uma conta de usuário diferente.

### <a name="solution"></a>Solução
Monte o compartilhamento de uma linha de comando de não administrador. Como alternativa, você pode seguir [Este tópico do TechNet](https://technet.microsoft.com/library/ee844140.aspx) para configurar o valor do registro **EnableLinkedConnections** .

<a id="netuse"></a>
## <a name="net-use-command-fails-if-the-storage-account-contains-a-forward-slash"></a>O comando net use falha se a conta de armazenamento contém uma barra

### <a name="cause"></a>Causa

O comando net use interpreta uma barra (/) como uma opção de linha de comando. Se o nome da sua conta de usuário começa com uma barra, o mapeamento da unidade falhará.

### <a name="solution"></a>Solução

Você pode usar qualquer uma das seguintes etapas para contornar o problema:

- Execute o seguinte comando do PowerShell:

  `New-SmbMapping -LocalPath y: -RemotePath \\server\share -UserName accountName -Password "password can contain / and \ etc"`

  A partir de um arquivo em lotes, você pode executar o comando desta forma:

  `Echo new-smbMapping ... | powershell -command –`

- Colocando aspas duplas em torno da chave para contornar esse problema, a menos que a barra seja o primeiro caractere. Se for, use o modo interativo e digite sua senha separadamente ou regenere as chaves para obter uma chave que não comece com uma barra.

<a id="cannotaccess"></a>
## <a name="application-or-service-cannot-access-a-mounted-azure-files-drive"></a>O aplicativo ou serviço não pode acessar uma unidade de Arquivos do Azure montada

### <a name="cause"></a>Causa

As unidades são montadas por usuário. Se o seu aplicativo ou serviço estiver em execução em uma conta de usuário diferente da que montou a unidade, o aplicativo não verá a unidade.

### <a name="solution"></a>Solução

use uma das seguintes soluções:

-   Monte a unidade a partir da mesma conta de usuário que contém o aplicativo. Você pode usar uma ferramenta como o PsExec.
- Passe o nome da conta de armazenamento e a chave nos parâmetros de nome do usuário e senha do comando net use.
- Use o comando cmdkey para adicionar as credenciais no Gerenciador de Credenciais. Execute-o em uma linha de comando no contexto da conta de serviço, seja por meio de um logon interativo ou usando `runas` .
  
  `cmdkey /add:<storage-account-name>.file.core.windows.net /user:AZURE\<storage-account-name> /pass:<storage-account-key>`
- Mapeie o compartilhamento diretamente sem usar uma letra de unidade mapeada. Alguns aplicativos podem não se reconectar à letra da unidade corretamente, portanto, usar o caminho UNC completo pode ser mais confiável. 

  `net use * \\storage-account-name.file.core.windows.net\share`

Depois de seguir estas instruções, você pode receber a seguinte mensagem de erro quando você executa o net use para a conta de serviço de rede/sistema: "Ocorreu um erro de sistema 1312. Uma sessão de logon especificada não existe. Talvez ela já tenha sido finalizada.” Se isso ocorrer, verifique se o nome de usuário que é passado para net use inclui informações de domínio (por exemplo: "[nome da conta de armazenamento].file.core.windows.net").

<a id="doesnotsupportencryption"></a>
## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>Erro "Você está copiando um arquivo para um destino que não dá suporte à criptografia"

Quando um arquivo é copiado pela rede, o arquivo é descriptografado no computador de origem, transmitido em texto não criptografado e criptografado novamente no destino. No entanto, você pode ver o seguinte erro quando estiver tentando copiar um arquivo criptografado: "Você está copiando o arquivo para um destino que não dá suporte à criptografia".

### <a name="cause"></a>Causa
Esse problema pode ocorrer se você estiver usando o sistema de arquivos com criptografia (EFS). Os arquivos criptografados pelo BitLocker podem ser copiados para os Arquivos do Azure. No entanto, os Arquivos do Azure não dão suporte a EFS NTFS.

### <a name="workaround"></a>Solução alternativa
Para copiar um arquivo pela rede, você deve primeiro descriptografá-lo. Use um dos métodos a seguir:

- Use o comando **copy /d**. Ele permite que os arquivos criptografados sejam salvos como arquivos descriptografados no destino.
- Defina a seguinte chave do registro:
  - Caminho = HKLM\Software\Policies\Microsoft\Windows\System
  - Tipo de valor = DWORD
  - Name = CopyFileAllowDecryptedRemoteDestination
  - Value = 1

Observe que a definição da chave do registro afeta todas as operações de cópia que são feitas para compartilhamentos de rede.

## <a name="slow-enumeration-of-files-and-folders"></a>Enumeração lenta dos arquivos e pastas

### <a name="cause"></a>Causa

Esse problema poderá ocorrer se não houver cache suficiente no computador cliente para diretórios grandes.

### <a name="solution"></a>Solução

Para resolver esse problema, ajuste o valor do registro **DirectoryCacheEntrySizeMax** para permitir o cache de listagens de diretório maiores no computador cliente:

- Local: HKLM\System\CCS\Services\Lanmanworkstation\Parameters
- Emaranhado do valor: DirectoryCacheEntrySizeMax 
- Tipo de valor: DWORD
 
 
Por exemplo, você pode defini-lo como 0x100000 e ver se o desempenho se torna melhor.

## <a name="error-aaddstenantnotfound-in-enabling-azure-active-directory-domain-service-aad-ds-authentication-for-azure-files-unable-to-locate-active-tenants-with-tenant-id-aad-tenant-id"></a>Erro AadDsTenantNotFound ao habilitar a autenticação do AAD DS (serviço de domínio Azure Active Directory) para arquivos do Azure "não é possível localizar locatários ativos com a ID de locatário AAD-Tenant-ID"

### <a name="cause"></a>Causa

O erro AadDsTenantNotFound ocorre quando você tenta [habilitar a autenticação Azure Active Directory Domain Services (Azure AD DS) em arquivos do Azure](storage-files-identity-auth-active-directory-domain-service-enable.md) em uma conta de armazenamento em que o [AAD DS (serviço de domínio do AAD)](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) não é criado no locatário do AAD da assinatura associada.  

### <a name="solution"></a>Solução

Habilite o AAD DS no locatário do AAD da assinatura na qual a conta de armazenamento é implantada. Você precisa de privilégios de administrador do locatário do AAD para criar um domínio gerenciado. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

[!INCLUDE [storage-files-condition-headers](../../../includes/storage-files-condition-headers.md)]

## <a name="error-system-error-1359-has-occurred-an-internal-error-received-over-smb-access-to-file-shares-with-azure-active-directory-domain-service-aad-ds-authentication-enabled"></a>Erro ' ocorreu o erro de sistema 1359. Um erro interno ' recebido por meio de acesso SMB a compartilhamentos de arquivos com a autenticação AAD DS (Azure Active Directory Domain Service) habilitada

### <a name="cause"></a>Causa

Erro ' ocorreu o erro de sistema 1359. Um erro interno ' ocorre quando você tenta se conectar ao compartilhamento de arquivos com a autenticação do AAD DS habilitada em um AAD DS com um nome DNS de domínio começando com um caractere numérico. Por exemplo, se o nome DNS do domínio do AAD DS for "1domain", você receberá esse erro ao tentar montar o compartilhamento de arquivos usando as credenciais do AAD. 

### <a name="solution"></a>Solução

No momento, você pode considerar reimplantar seu AAD DS usando um novo nome DNS de domínio que se aplica com as regras abaixo:
- Os nomes não podem começar com um caractere numérico.
- Os nomes devem ter de 3 a 63 caracteres de comprimento.

## <a name="unable-to-mount-azure-files-with-ad-credentials"></a>Não é possível montar os arquivos do Azure com as credenciais do AD 

### <a name="self-diagnostics-steps"></a>Etapas de autodiagnóstico
Primeiro, certifique-se de ter seguido todas as quatro etapas para [habilitar a autenticação do AD dos arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-identity-auth-active-directory-enable).

Em segundo lugar, tente [montar o compartilhamento de arquivos do Azure com a chave da conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows). Se você não conseguiu montar, baixe [AzFileDiagnostics.ps1](https://gallery.technet.microsoft.com/Troubleshooting-tool-for-a9fa1fe5) para ajudá-lo a validar o cliente que está executando o ambiente, detectar a configuração de cliente incompatível que causaria falha de acesso para os arquivos do Azure, fornece orientação prescritiva sobre AutoCorreção e coleta dos rastreamentos de diagnóstico.

Em terceiro lugar, você pode executar o cmdlet Debug-AzStorageAccountAuth para conduzir um conjunto de verificações básicas em sua configuração do AD com o usuário conectado do AD. Esse cmdlet é compatível com a [versão AzFilesHybrid v0.1.2+](https://github.com/Azure-Samples/azure-files-samples/releases). Você precisa executar esse cmdlet com um usuário do AD que tenha permissão de proprietário na conta de armazenamento de destino.  
```PowerShell
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```
O cmdlet executa essas verificações abaixo em sequência e fornece diretrizes para falhas:
1. CheckPort445Connectivity: Verifique se a porta 445 está aberta para conexão SMB
2. CheckDomainJoined: validar se o computador cliente está ingressado no domínio no AD
3. CheckADObject: Confirme se há um objeto no Active Directory que representa a conta de armazenamento e tem o SPN (nome da entidade de serviço) correto.
4. CheckGetKerberosTicket: tentativa de obter um tíquete Kerberos para conectar-se à conta de armazenamento 
5. CheckADObjectPasswordIsCorrect: Verifique se a senha configurada na identidade do AD que representa a conta de armazenamento corresponde à da conta de armazenamento kerb1 ou à chave kerb2
6. CheckSidHasAadUser: Verifique se o usuário conectado do AD está sincronizado com o Azure AD. Se você quiser procurar se um usuário específico do AD está sincronizado com o Azure AD, você pode especificar o-UserName e-Domain nos parâmetros de entrada.
7. CheckAadUserHasSid: verificar se um usuário do Azure AD tem um SID no AD, essa verificação requer que o usuário insira a ID de objeto do usuário do Azure AD com o parâmetro-ObjectId. 
8. CheckStorageAccountDomainJoined: Verifique as propriedades da conta de armazenamento para ver se a autenticação do AD foi habilitada e se as propriedades do AD da conta estão preenchidas.

## <a name="unable-to-configure-directoryfile-level-permissions-windows-acls-with-windows-file-explorer"></a>Não é possível configurar permissões de nível de diretório/arquivo (ACLs do Windows) com o explorador de arquivos do Windows

### <a name="symptom"></a>Sintoma

Você pode ter qualquer um dos sintomas descritos abaixo ao tentar configurar ACLs do Windows com o explorador de arquivos em um compartilhamento de arquivos montado:
- Depois de clicar na permissão Editar na guia Segurança, o assistente de permissão não é carregado. 
- Quando você tenta selecionar um novo usuário ou grupo, o local do domínio não exibe a AD DS domínio correto. 

### <a name="solution"></a>Solução

Recomendamos que você use a [ferramenta icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) para configurar as permissões de nível de diretório/arquivo como uma solução alternativa. 

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Caso ainda precise de ajuda, [contate o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
