---
title: Pré-requisitos do Cache Azure HPC
description: Pré-requisitos para usar o cache Azure HPC
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: 6da35cb60dc5f22be01ae25393bd62327db64867
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655655"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Pré-requisitos para cache Azure HPC

Antes de usar o portal Azure para criar um novo Cache Azure HPC, certifique-se de que seu ambiente atenda a esses requisitos.

## <a name="azure-subscription"></a>Assinatura do Azure

Recomenda-se uma assinatura paga.

> [!NOTE]
> Durante os primeiros meses da versão ga, a equipe de cache do Azure HPC deve adicionar sua assinatura à lista de acesso antes que ela possa ser usada para criar uma instância de cache. Esse procedimento ajuda a garantir que cada cliente obtenha uma resposta de alta qualidade de seus caches. Preencha [este formulário](https://aka.ms/onboard-hpc-cache) para solicitar acesso.

## <a name="network-infrastructure"></a>Infraestrutura da rede

Dois pré-requisitos relacionados à rede devem ser configurados antes que você possa usar seu cache:

* Uma sub-rede dedicada para a instância cache Azure HPC
* Suporte ao DNS para que o cache possa acessar o armazenamento e outros recursos

### <a name="cache-subnet"></a>Sub-rede de cache

O Cache Azure HPC precisa de uma sub-rede dedicada com essas qualidades:

* A sub-rede deve ter pelo menos 64 endereços IP disponíveis.
* A sub-rede não pode hospedar outras VMs, mesmo para serviços relacionados, como máquinas clientes.
* Se você usar várias instâncias de cache Do Azure HPC, cada uma precisa de sua própria sub-rede.

A melhor prática é criar uma nova sub-rede para cada cache. Você pode criar uma nova rede virtual e uma sub-rede como parte da criação do cache.

### <a name="dns-access"></a>Acesso ao DNS

O cache precisa de DNS para acessar recursos fora de sua rede virtual. Dependendo dos recursos que você está usando, talvez seja necessário configurar um servidor DNS personalizado e configurar o encaminhamento entre esse servidor e os servidores DNS do Azure:

* Para acessar os pontos finais de armazenamento do Azure Blob e outros recursos internos, você precisa do servidor DNS baseado no Azure.
* Para acessar o armazenamento no local, você precisa configurar um servidor DNS personalizado que possa resolver seus nomes de hostde armazenamento.

Se você só precisar de acesso ao armazenamento Blob, você pode usar o servidor DNS fornecido pelo Azure padrão para o seu cache. No entanto, se você precisar de acesso a outros recursos, você deve criar um servidor DNS personalizado e configurá-lo para encaminhar quaisquer solicitações de resolução específicas do Azure para o servidor DNS do Azure.

Um simples servidor DNS também pode ser usado para carregar conexões de cliente de equilíbrio entre todos os pontos de montagem de cache disponíveis.

Saiba mais sobre redes virtuais do Azure e configurações de servidor DNS na [resolução Name para recursos em redes virtuais do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

## <a name="permissions"></a>Permissões

Verifique esses pré-requisitos relacionados à permissão antes de começar a criar seu cache.

* A instância de cache precisa ser capaz de criar interfaces de rede virtuais (NICs). O usuário que cria o cache deve ter privilégios suficientes na assinatura para criar NICs.

* Se usar o armazenamento Blob, o Cache Azure HPC precisa de autorização para acessar sua conta de armazenamento. Use o RBAC (Role-based Access Control, controle de acesso baseado em função) para dar acesso ao cache ao armazenamento Blob. Duas funções são necessárias: Contribuinte de conta de armazenamento e contribuinte de dados blob de armazenamento.

  Siga as instruções em [Adicionar metas de armazenamento](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) para adicionar as funções.

## <a name="storage-infrastructure"></a>Infra-estrutura de armazenamento

O cache suporta os contêineres Azure Blob ou as exportações de armazenamento de hardware NFS. Adicione os alvos de armazenamento depois de criar o cache.

Cada tipo de armazenamento tem pré-requisitos específicos.

### <a name="blob-storage-requirements"></a>Requisitos de armazenamento blob

Se você quiser usar o armazenamento Azure Blob com o seu cache, você precisa de uma conta de armazenamento compatível e um contêiner Blob vazio ou um contêiner preenchido com dados formatados do Azure HPC Cache conforme descrito nos [dados do Move para o armazenamento Azure Blob](hpc-cache-ingest.md).

Crie a conta antes de tentar adicionar um alvo de armazenamento. Você pode criar um novo recipiente quando adicionar o destino.

Para criar uma conta de armazenamento compatível, use essas configurações:

* Desempenho: **Padrão**
* Tipo de conta: **StorageV2 (propósito geral v2)**
* Replicação: **Armazenamento localmente redundante (LRS)**
* Nível de acesso (padrão): **Quente**

É uma boa prática usar uma conta de armazenamento no mesmo local que seu cache.
<!-- clarify location - same region or same resource group or same virtual network? -->

Você também deve dar ao aplicativo de cache acesso à sua conta de armazenamento Do Zure, conforme mencionado em [Permissões,](#permissions)acima. Siga o procedimento em Adicionar metas de armazenamento para dar ao cache as funções de acesso [necessárias.](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account) Se você não é o proprietário da conta de armazenamento, faça com que o proprietário faça essa etapa.

### <a name="nfs-storage-requirements"></a>Requisitos de armazenamento NFS

Se usar um sistema de armazenamento NFS (por exemplo, um sistema NAS de hardware no local), certifique-se de que ele atenda a esses requisitos. Você pode precisar trabalhar com os administradores de rede ou gerentes de firewall para o seu sistema de armazenamento (ou data center) para verificar essas configurações.

> [!NOTE]
> A criação do destino de armazenamento falhará se o cache tiver acesso insuficiente ao sistema de armazenamento NFS.

Mais informações estão incluídas na [configuração NAS de solução de problemas e problemas de destino de armazenamento NFS](troubleshoot-nas.md).

* **Conectividade de rede:** O Cache Azure HPC precisa de acesso de rede de alta largura de banda entre a sub-rede de cache e o data center do sistema NFS. [Recomenda-se o ExpressRoute](https://docs.microsoft.com/azure/expressroute/) ou acesso similar. Se estiver usando uma VPN, talvez seja necessário configurá-la para grampear o TCP MSS em 1350 para garantir que pacotes grandes não sejam bloqueados. Leia [as restrições de tamanho do pacote VPN](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions) para ajudar a solucionar as configurações de VPN.

* **Acesso ao porto:** O cache precisa acessar portas TCP/UDP específicas no sistema de armazenamento. Diferentes tipos de armazenamento têm diferentes requisitos de porta.

  Para verificar as configurações do sistema de armazenamento, siga este procedimento.

  * Emita um `rpcinfo` comando para o sistema de armazenamento para verificar as portas necessárias. O comando abaixo lista as portas e formata os resultados relevantes em uma tabela. (Use o endereço IP do sistema no lugar do *<storage_IP>* termo.)

    Você pode emitir este comando de qualquer cliente Linux que tenha a infra-estrutura NFS instalada. Se você usar um cliente dentro da sub-rede de cluster, ele também pode ajudar a verificar a conectividade entre a sub-rede e o sistema de armazenamento.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  Certifique-se de que todas as ``rpcinfo`` portas retornadas pela consulta permitem tráfego irrestrito da sub-rede do Cache Azure HPC.

  * Se você não puder `rpcinfo` usar o comando, certifique-se de que essas portas comumente usadas permitam tráfego de entrada e saída:

    | Protocolo | Porta  | Serviço  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | montado   |
    | TCP/UDP  | 4047  | status   |

    Alguns sistemas usam números de portas diferentes para esses serviços - consulte a documentação do seu sistema de armazenamento para ter certeza.

  * Verifique as configurações do firewall para ter certeza de que eles permitem o tráfego em todas essas portas necessárias. Certifique-se de verificar firewalls usados no Azure, bem como firewalls locais em seu data center.

* **Acesso ao diretório:** Habilite o `showmount` comando no sistema de armazenamento. O Cache Azure HPC usa este comando para verificar se a configuração do destino de armazenamento aponta para uma exportação válida e também para garantir que várias montagens não acessem os mesmos subdiretórios (um risco de colisão de arquivos).

  > [!NOTE]
  > Se o sistema de armazenamento NFS usar o sistema operacional ONTAP 9.2 do NetApp, **não habilitar `showmount` **. [Entre em contato com o Microsoft Service e suporte](hpc-cache-support-ticket.md) para obter ajuda.

  Saiba mais sobre o acesso à listagem de diretórios no artigo de [solução de problemas de](troubleshoot-nas.md#enable-export-listing)destino de armazenamento NFS .

* **Acesso raiz** (leitura/gravação): O cache se conecta ao sistema back-end como ID do usuário 0. Verifique essas configurações no sistema de armazenamento:
  
  * Habilitar `no_root_squash`. Esta opção garante que o usuário raiz remoto possa acessar arquivos de propriedade da raiz.

  * Verifique as políticas de exportação para garantir que elas não incluam restrições ao acesso raiz da sub-rede do cache.

  * Se o seu armazenamento tiver alguma exportação que seja subdiretórios de outra exportação, certifique-se de que o cache tenha acesso raiz ao segmento mais baixo do caminho. Leia [o acesso raiz em caminhos de diretório](troubleshoot-nas.md#allow-root-access-on-directory-paths) no artigo de solução de problemas do alvo do armazenamento NFS para obter detalhes.

* O armazenamento back-end NFS deve ser uma plataforma de hardware/software compatível. Entre em contato com a equipe de cache do Azure HPC para obter detalhes.

## <a name="next-steps"></a>Próximas etapas

* [Crie uma instância de cache Azure HPC](hpc-cache-create.md) a partir do portal Azure
