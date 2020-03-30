---
title: Copiar ou mover dados para o Armazenamento Azure usando o AzCopy v10 | Microsoft Docs
description: O AzCopy é um utilitário de linha de comando que você pode usar para copiar dados para, de ou entre contas de armazenamento. Este artigo ajuda você a baixar o AzCopy, conectar-se à sua conta de armazenamento e, em seguida, transferir arquivos.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 90adb3b973b6952d10ef89e4b40d0fc68489e224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061101"
---
# <a name="get-started-with-azcopy"></a>Introdução ao AzCopy

O AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos para ou de uma conta de armazenamento. Este artigo ajuda você a baixar o AzCopy, conectar-se à sua conta de armazenamento e, em seguida, transferir arquivos.

> [!NOTE]
> AzCopy **V10** é a versão suportada atualmente do AzCopy.
>
> Se você precisar usar a AzCopy **v8.1,** consulte A Use a versão anterior da seção [AzCopy](#previous-version) deste artigo.

<a id="download-and-install-azcopy" />

## <a name="download-azcopy"></a>Baixar o AzCopy

Primeiro, baixe o arquivo executável AzCopy V10 para qualquer diretório em seu computador. AzCopy V10 é apenas um arquivo executável, então não há nada para instalar.

- [Windows 64-bit](https://aka.ms/downloadazcopy-v10-windows) (zip)
- [Windows 32-bit](https://aka.ms/downloadazcopy-v10-windows-32bit) (zip)
- [Linux](https://aka.ms/downloadazcopy-v10-linux) (tar)
- [MacOS](https://aka.ms/downloadazcopy-v10-mac) (zip)

Esses arquivos são compactados como um arquivo zip (Windows e Mac) ou um arquivo tar (Linux). Para baixar e descompactar o arquivo tar no Linux, consulte a documentação para sua distribuição Linux.

> [!NOTE]
> Se você quiser copiar dados para e do serviço de [armazenamento Azure Table,](https://docs.microsoft.com/azure/storage/tables/table-storage-overview) instale [a versão 7.3 do AzCopy](https://aka.ms/downloadazcopynet).


## <a name="run-azcopy"></a>Executar AzCopy

Para conveniência, considere adicionar a localização do diretório do executável Do AzCopy ao seu caminho do sistema para facilitar o uso. Dessa forma, `azcopy` você pode digitar a partir de qualquer diretório em seu sistema.

Se você optar por não adicionar o diretório AzCopy ao seu caminho, você terá que alterar diretórios para o local do seu AzCopy executável e digitar `azcopy` ou `.\azcopy` em solicitações de comando do Windows PowerShell.

Para ver uma lista de `azcopy -h` comandos, digite e pressione a tecla ENTER.

Para saber mais sobre um comando específico, basta incluir `azcopy list -h`o nome do comando (Por exemplo: ).

![Ajuda inline](media/storage-use-azcopy-v10/azcopy-inline-help.png)

Para encontrar documentação de referência detalhada para cada parâmetro de comando e comando, consulte [azcopy](storage-ref-azcopy.md)

> [!NOTE] 
> Como proprietário da sua conta do Azure Storage, você não tem permissões automaticamente atribuídas para acessar dados. Antes de fazer qualquer coisa significativa com o AzCopy, você precisa decidir como fornecerá credenciais de autorização para o serviço de armazenamento. 

## <a name="choose-how-youll-provide-authorization-credentials"></a>Escolha como você fornecerá credenciais de autorização

Você pode fornecer credenciais de autorização usando o Azure Active Directory (AD) ou usando um token SAS (SAS) de Assinatura de Acesso Compartilhado.

Use esta tabela como guia:

| Tipo de armazenamento | Método de autorização atualmente suportado |
|--|--|
|**Armazenamento blob** | Azure AD & SAS |
|**Armazenamento blob (namespace hierárquico)** | Azure AD & SAS |
|**Armazenamento de arquivos** | Somente SAS |

### <a name="option-1-use-azure-active-directory"></a>Opção 1: Use o Diretório Ativo do Azure

Ao usar o Azure Active Directory, você pode fornecer credenciais uma vez em vez de ter que anexar um token SAS a cada comando.  

> [!NOTE]
> Na versão atual, se você planeja copiar blobs entre contas de armazenamento, você terá que anexar um token SAS a cada URL de origem. Você pode omitir o token SAS somente a partir da URL de destino. Por exemplo, consulte [Copiar bolhas entre contas de armazenamento](storage-use-azcopy-blobs.md).

O nível de autorização que você precisa é baseado em se você planeja carregar arquivos ou apenas baixá-los.

Se você quiser apenas baixar arquivos, verifique se o [Leitor de Dados blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) foi atribuído à sua identidade de usuário, identidade gerenciada ou diretor de serviço.

> Identidades de usuários, identidades gerenciadas e diretores de serviço são cada um um tipo de princípio de *segurança,* por isso usaremos o termo diretor de *segurança* para o restante deste artigo.

Se você quiser carregar arquivos, verifique se uma dessas funções foi atribuída ao seu diretor de segurança:

- [Colaborador de dados de blob de armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-queue-data-contributor)
- [Proprietário de Dados do Blob de Armazenamento](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)

Essas funções podem ser atribuídas ao seu diretor de segurança em qualquer um desses escopos:

- Contêiner (sistema de arquivos)
- Conta de armazenamento
- Resource group
- Subscription

Para saber como verificar e atribuir funções, consulte [Grant acesso ao Blob do Azure e dados de fila com RBAC no portal Azure](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-rbac-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Tenha em mente que as atribuições de função RBAC podem levar até cinco minutos para se propagar.

Você não precisa ter uma dessas funções atribuídas ao seu diretor de segurança se o seu diretor de segurança for adicionado à lista de controle de acesso (ACL) do contêiner de destino ou diretório. Na ACL, seu principal de segurança precisa de permissão de gravação no diretório de destino e executar permissão no contêiner e em cada diretório pai.

Para saber mais, consulte [o controle de acesso no Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

#### <a name="authenticate-a-user-identity"></a>Autenticar uma identidade de usuário

Depois de verificar se sua identidade de usuário recebeu o nível de autorização necessário, abra um prompt de comando, digite o seguinte comando e pressione a tecla ENTER.

```azcopy
azcopy login
```

Se você pertence a mais de uma organização, inclua o ID de inquilino da organização à qual a conta de armazenamento pertence.

```azcopy
azcopy login --tenant-id=<tenant-id>
```

Substitua `<tenant-id>` o espaço reservado pelo ID do inquilino da organização à qual a conta de armazenamento pertence. Para encontrar o ID do inquilino, selecione O ID do Diretório **Ativo do Azure > Propriedades >** no portal Azure.

Esse comando retorna um código de autenticação e a URL de um site. Abra o site, forneça o código e, em seguida, escolha o botão **Avançar**.

![Criar um contêiner](media/storage-use-azcopy-v10/azcopy-login.png)

Uma janela de entrada será exibida. Nessa janela, entre em sua conta do Azure usando suas credenciais de conta do Azure. Depois de entrar com êxito, feche a janela do navegador e comece a usar o AzCopy.

<a id="service-principal" />

#### <a name="authenticate-a-service-principal"></a>Autenticar um diretor de serviço

Esta é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário, especialmente quando executado no local. Se você planeja executar o AzCopy em VMs que são executados no Azure, uma identidade de serviço gerenciada é mais fácil de administrar. Para saber mais, consulte o [Authenticate uma](#managed-identity) seção de identidade gerenciada deste artigo.

Antes de executar um script, você tem que fazer login interativamente pelo menos uma vez para que você possa fornecer ao AzCopy as credenciais do seu diretor de serviço.  Essas credenciais são armazenadas em um arquivo protegido e criptografado para que seu script não precise fornecer essas informações confidenciais.

Você pode entrar em sua conta usando um segredo de cliente ou usando a senha de um certificado que está associado ao registro do aplicativo do seu diretor de serviço.

Para saber mais sobre como criar o principal do serviço, consulte [Como: Usar o portal para criar um aplicativo e serviço azure ad que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Para saber mais sobre os principais serviços em geral, consulte [Os principais objetos de aplicação e serviço no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)

##### <a name="using-a-client-secret"></a>Usando um segredo de cliente

Comece definindo `AZCOPY_SPA_CLIENT_SECRET` a variável de ambiente para o segredo do cliente do registro do aplicativo do seu diretor de serviço.

> [!NOTE]
> Certifique-se de definir esse valor a partir do seu prompt de comando, e não nas configurações variáveis de ambiente do seu sistema operacional. Dessa forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como você pode fazer isso no PowerShell.

```azcopy
$env:AZCOPY_SPA_CLIENT_SECRET="$(Read-Host -prompt "Enter key")"
```

> [!NOTE]
> Considere usar um prompt como mostrado neste exemplo. Dessa forma, sua senha não aparecerá no histórico de comandos do seu console.  

Em seguida, digite o seguinte comando e pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --application-id <application-id> --tenant-id=<tenant-id>
```

Substitua `<application-id>` o espaço reservado pelo ID do aplicativo do seu principal de serviço. Substitua `<tenant-id>` o espaço reservado pelo ID do inquilino da organização à qual a conta de armazenamento pertence. Para encontrar o ID do inquilino, selecione O ID do Diretório **Ativo do Azure > Propriedades >** no portal Azure. 

##### <a name="using-a-certificate"></a>Usando um certificado

Se você preferir usar suas próprias credenciais para autorização, você pode carregar um certificado para o seu registro de aplicativo e, em seguida, usar esse certificado para fazer login.

Além de carregar seu certificado no seu registro de aplicativo, você também precisará ter uma cópia do certificado salvo na máquina ou VM onde o AzCopy estará sendo executado. Esta cópia do certificado deve estar dentro . PFX ou . Formato PEM e deve incluir a chave privada. A chave privada deve ser protegida por senha. Se você estiver usando o Windows e seu certificado existir apenas em um armazenamento de certificados, certifique-se de exportar esse certificado para um arquivo PFX (incluindo a chave privada). Para obter orientação, consulte [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps)

Em seguida, `AZCOPY_SPA_CERT_PASSWORD` defina a variável de ambiente para a senha do certificado.

> [!NOTE]
> Certifique-se de definir esse valor a partir do seu prompt de comando, e não nas configurações variáveis de ambiente do seu sistema operacional. Dessa forma, o valor está disponível apenas para a sessão atual.

Este exemplo mostra como você pode fazer essa tarefa no PowerShell.

```azcopy
$env:AZCOPY_SPA_CERT_PASSWORD="$(Read-Host -prompt "Enter key")"
```

Em seguida, digite o seguinte comando e pressione a tecla ENTER.

```azcopy
azcopy login --service-principal --certificate-path <path-to-certificate-file> --tenant-id=<tenant-id>
```

Substitua `<path-to-certificate-file>` o espaço reservado pelo caminho relativo ou totalmente qualificado para o arquivo do certificado. O AzCopy salva o caminho para este certificado, mas ele não salva uma cópia do certificado, então certifique-se de manter esse certificado no lugar. Substitua `<tenant-id>` o espaço reservado pelo ID do inquilino da organização à qual a conta de armazenamento pertence. Para encontrar o ID do inquilino, selecione O ID do Diretório **Ativo do Azure > Propriedades >** no portal Azure.

> [!NOTE]
> Considere usar um prompt como mostrado neste exemplo. Dessa forma, sua senha não aparecerá no histórico de comandos do seu console. 

<a id="managed-identity" />

#### <a name="authenticate-a-managed-identity"></a>Autenticar uma identidade gerenciada

Esta é uma ótima opção se você planeja usar o AzCopy dentro de um script que é executado sem interação do usuário, e o script é executado a partir de uma Máquina Virtual Azure (VM). Ao usar esta opção, você não terá que armazenar nenhuma credencial na VM.

Você pode entrar em sua conta usando uma identidade gerenciada em todo o sistema que você habilitou em sua VM, ou usando o ID do cliente, ID de objeto ou ID de recurso de uma identidade gerenciada atribuída pelo usuário que você atribuiu à sua VM.

Para saber mais sobre como ativar uma identidade gerenciada em todo o sistema ou criar uma identidade gerenciada atribuída pelo usuário, consulte [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal Azure](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).

##### <a name="using-a-system-wide-managed-identity"></a>Usando uma identidade gerenciada em todo o sistema

Primeiro, certifique-se de que você habilitou uma identidade gerenciada em todo o sistema em sua VM. Consulte [a identidade gerenciada atribuída ao sistema](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity).

Em seguida, no console de comando, digite o seguinte comando e pressione a tecla ENTER.

```azcopy
azcopy login --identity
```

##### <a name="using-a-user-assigned-managed-identity"></a>Usando uma identidade gerenciada atribuída pelo usuário

Primeiro, certifique-se de que você habilitou uma identidade gerenciada atribuída pelo usuário em sua VM. Consulte [a identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#user-assigned-managed-identity).

Em seguida, no console de comando, digite qualquer um dos seguintes comandos e pressione a tecla ENTER.

```azcopy
azcopy login --identity --identity-client-id "<client-id>"
```

Substitua `<client-id>` o espaço reservado pelo ID do cliente da identidade gerenciada atribuída pelo usuário.

```azcopy
azcopy login --identity --identity-object-id "<object-id>"
```

Substitua `<object-id>` o espaço reservado pelo ID do objeto da identidade gerenciada atribuída pelo usuário.

```azcopy
azcopy login --identity --identity-resource-id "<resource-id>"
```

Substitua `<resource-id>` o espaço reservado pelo ID de recurso da identidade gerenciada atribuída pelo usuário.

### <a name="option-2-use-a-sas-token"></a>Opção 2: Use um token SAS

Você pode anexar um token SAS a cada URL de origem ou destino que use em seus comandos AzCopy.

Este exemplo de comando copia recursivamente dados de um diretório local para um contêiner blob. Um token SAS fictício é anexado ao final da URL do contêiner.

```azcopy
azcopy copy "C:\local\path" "https://account.blob.core.windows.net/mycontainer1/?sv=2018-03-28&ss=bjqt&srt=sco&sp=rwddgcup&se=2019-05-01T05:01:17Z&st=2019-04-30T21:01:17Z&spr=https&sig=MGCXiyEzbtttkr3ewJIh2AR8KrghSy1DGM9ovN734bQF4%3D" --recursive=true
```

Para saber mais sobre os tokens SAS e como obter um, consulte [Usando assinaturas de acesso compartilhado (SAS)](https://docs.microsoft.com/azure/storage/common/storage-sas-overview).

## <a name="transfer-files"></a>Transferir arquivos

Depois de autenticar sua identidade ou obter um token SAS, você pode começar a transferir arquivos.

Para encontrar comandos de exemplo, consulte qualquer um desses artigos.

- [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)

- [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)

- [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

- [Transferir dados com armazenamento AzCopy e Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer#azcopy)

## <a name="use-azcopy-in-a-script"></a>Use o AzCopy em um script

### <a name="obtain-a-static-download-link"></a>Obtenha um link de download estático

Com o tempo, o link de [download](#download-and-install-azcopy) do AzCopy apontará para novas versões do AzCopy. Se o script baixar o AzCopy, o script pode parar de funcionar se uma versão mais recente do AzCopy modificar recursos dos recursos dos seus recursos.

Para evitar esses problemas, obtenha um link estático (sem alteração) para a versão atual do AzCopy. Dessa forma, seu script baixa a mesma versão exata do AzCopy cada vez que ele é executado.

Para obter o link, execute este comando:

| Sistema operacional  | Comando |
|--------|-----------|
| **Linux** | `curl -v https://aka.ms/downloadazcopy-v10-linux` |
| **Windows** | `(curl https://aka.ms/downloadazcopy-v10-windows -MaximumRedirection 0 -ErrorAction silentlycontinue).RawContent` |

> [!NOTE]
> Para linux, `--strip-components=1` `tar` no comando remove a pasta de nível superior que contém o nome da versão e, em vez disso, extrai o binário diretamente na pasta atual. Isso permite que o script seja `azcopy` atualizado com `wget` uma nova versão de apenas atualizando a URL.

A URL aparece na saída deste comando. Seu script pode então baixar a AzCopy usando essa URL.

| Sistema operacional  | Comando |
|--------|-----------|
| **Linux** | `wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1` |
| **Windows** | `Invoke-WebRequest https://azcopyvnext.azureedge.net/release20190517/azcopy_windows_amd64_10.1.2.zip -OutFile azcopyv10.zip <<Unzip here>>` |

### <a name="escape-special-characters-in-sas-tokens"></a>Escape personagens especiais em tokens SAS

Em arquivos em `.cmd` lote que têm a extensão, você terá que escapar dos `%` caracteres que aparecem em tokens SAS. Você pode fazer isso `%` adicionando um `%` caractere adicional ao lado dos caracteres existentes na seqüência de token SAS.

### <a name="run-scripts-by-using-jenkins"></a>Executar scripts usando Jenkins

Se você planeja usar [Jenkins](https://jenkins.io/) para executar scripts, certifique-se de colocar o seguinte comando no início do script.

```
/usr/bin/keyctl new_session
```

## <a name="use-azcopy-in-azure-storage-explorer"></a>Use o AzCopy no Azure Storage Explorer

[O Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) usa o AzCopy para executar todas as suas operações de transferência de dados. Você pode usar [o Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) se quiser aproveitar as vantagens de desempenho do AzCopy, mas prefere usar uma interface gráfica do usuário em vez da linha de comando para interagir com seus arquivos.

O Storage Explorer usa a chave da sua conta para executar operações, portanto, depois de entrar no Storage Explorer, você não precisará fornecer credenciais de autorização adicionais.

<a id="previous-version" />

## <a name="use-the-previous-version-of-azcopy"></a>Use a versão anterior do AzCopy

Se você precisar usar a versão anterior do AzCopy (AzCopy v8.1), consulte qualquer um dos seguintes links:

- [AzCopy no Windows (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy)

- [AzCopy no Linux (v8)](https://docs.microsoft.com/previous-versions/azure/storage/storage-use-azcopy-linux)

## <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas do AzCopy

Consulte [Configurar, otimizar e solucionar problemas do AzCopy](storage-use-azcopy-configure.md)

## <a name="next-steps"></a>Próximas etapas

Se você tiver perguntas, problemas ou feedback geral, envie-os na página [do GitHub.](https://github.com/Azure/azure-storage-azcopy)
