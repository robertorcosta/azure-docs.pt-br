---
title: Montar o Avere vFXT – Azure
description: Como montar os clientes com Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rohogue
ms.openlocfilehash: c461b379629927e8f367fad9bfc70b87413f47b7
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255393"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montar o cluster do Avere vFXT  

Siga estas etapas para conectar computadores cliente ao seu cluster vFXT.

1. Decida como balancear a carga de tráfego do cliente entre seus nós de cluster. Leia [Balancear a carga do cliente](#balance-client-load) a seguir, para saber mais detalhes. 
1. Identifique o endereço IP e o caminho de junção para montar.
1. Emita o [comando de montagem](#mount-command-arguments) com os argumentos adequados.

## <a name="balance-client-load"></a>Balancear a carga do cliente

Para ajudar a balancear as solicitações do cliente entre todos os nós do cluster, monte os clientes no intervalo completo dos endereços IP voltados para o cliente. Existem várias maneiras simples de automatizar essa tarefa.

> [!TIP] 
> Outros métodos de balanceamento de carga podem ser adequados para sistemas grandes ou complexos. [Abra um tíquete de suporte](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) se precisar de ajuda.
> 
> Se você preferir usar um servidor DNS para balanceamento de carga automático do lado do servidor, será necessário configurar e gerenciar seu próprio servidor DNS no Azure. Nesse caso, é possível configurar o DNS round robin para o cluster vFXT de acordo com este documento: [Configuração de DNS de cluster Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Exemplo de script de montagem de cliente balanceado

Este exemplo de código usa endereços IP do cliente como um elemento aleatório para distribuir clientes para todos os endereços IP disponíveis dos clusters vFXT.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default 
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

A função acima faz parte do exemplo do Lote disponível no site [Exemplos do Avere vFXT](https://github.com/Azure/Avere#tutorials).

## <a name="create-the-mount-command"></a>Criar o comando de montagem 

> [!NOTE]
> Se você não tiver criado um novo contêiner de Blob ao criar o cluster Avere vFXT, siga as etapas em [Configurar o armazenamento](avere-vfxt-add-storage.md) antes de tentar montar os clientes.

No cliente, o comando ``mount`` mapeia o servidor virtual (vserver) no cluster vFXT para um caminho no sistema de arquivos local. O formato é ``mount <vFXT path> <local path> {options}``

Existem três elementos no comando de montagem: 

* Caminho vFXT – (uma combinação de endereço IP e o caminho de junção do namespace descrito abaixo)
* Caminho local – o caminho no cliente 
* Opções de comando de montagem – listadas em [Argumentos de comando de montagem](#mount-command-arguments)

### <a name="junction-and-ip"></a>IP e junção

O caminho vserver é uma combinação de seu *endereço IP* com o caminho para uma *junção de namespace*. A junção de namespace é um caminho virtual que foi definido quando o sistema de armazenamento foi adicionado.

Se o cluster tiver sido criado com o Armazenamento de Blobs, o caminho de namespace será `/msazure`

Exemplo: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Se você tiver adicionado armazenamento após a criação do cluster, o caminho de junção do namespace corresponderá ao valor definido em **Caminho do namespace** ao criar a junção. Por exemplo, se você tiver usado ``/avere/files`` como caminho do namespace, os clientes montarão *IP_address*:/avere/files no ponto de montagem local.

![Caixa de diálogo "Adicionar nova junção" com /avere/files no campo de caminho do namespace](media/avere-vfxt-create-junction-example.png)


O endereço IP é um dos endereços IP voltados para o cliente definidos para o vserver. É possível encontrar o intervalo dos IPs voltados para o cliente em dois locais no painel de controle do Avere:

* Tabela **VServers** (guia Painel) – 

  ![A guia Painel do painel de controle do Avere com a guia VServer selecionada na tabela de dados abaixo do gráfico e a seção do endereço IP circulada](media/avere-vfxt-ip-addresses-dashboard.png)

* Página de configurações **Rede voltada para o cliente** – 

  ![Página de configuração Configurações > VServer > Rede voltada para o cliente com um círculo ao redor da seção de Intervalo de Endereços da tabela para um determinado vserver](media/avere-vfxt-ip-addresses-settings.png)

Além dos caminhos, inclua o [Montar argumentos de comando](#mount-command-arguments) descrito abaixo ao montar cada cliente.

### <a name="mount-command-arguments"></a>Montar argumentos de comando

Para garantir uma montagem de cliente sem dificuldades, passe esses argumentos e configurações em seu comando de montagem: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Configurações necessárias | |
--- | --- 
``hard`` | As montagens flexíveis do cluster vFXT estão associadas com as falhas no aplicativo e possível perda de dados. 
``proto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede NFS.
``mountproto=netid`` | Essa opção é compatível com o tratamento apropriado de erros de rede para operações de montagem.
``retry=n`` | Configure ``retry=30`` para evitar falhas transitórias de montagem. Recomenda-se um valor diferente em montagens em primeiro plano.

| Configurações preferenciais  | |
--- | --- 
``nointr``            | A opção "nointr" é preferencial para clientes com kernels herdados (antes de abril de 2008) compatíveis com essa opção. Observe que a opção "intr" é o padrão.


## <a name="next-steps"></a>Próximas etapas

Após montar os clientes, é possível usá-los para preencher o armazenamento de dados de back-end (arquivista de núcleo). Confira estes documentos para saber mais sobre outras tarefas de configuração:

* [Mover dados para o arquivista de núcleo do cluster](avere-vfxt-data-ingest.md) – Como usar vários clientes e threads para fazer upload de dados de modo eficiente
* [Personalizar o ajuste do cluster](avere-vfxt-tuning.md) – Adaptar as configurações do cluster de acordo com sua carga de trabalho
* [Gerenciar o cluster](avere-vfxt-manage-cluster.md) – Como iniciar ou parar o cluster e gerenciar nós
