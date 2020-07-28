---
title: Habilitar a replicação para computadores locais com pontos de extremidade privados no Azure Site Recovery
description: Este artigo descreve como configurar a replicação para computadores locais com pontos de extremidade privados usando Site Recovery.
author: mayurigupta13
ms.author: mayg
ms.service: site-recovery
ms.topic: article
ms.date: 07/14/2020
ms.openlocfilehash: c91c92a18570f569b6364b646e6fdf7bf534802f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095836"
---
# <a name="replicate-on-premises-machines-with-private-endpoints"></a>Replicar computadores locais com pontos de extremidade privados

Azure Site Recovery permite que você use pontos de extremidade particulares do [link privado do Azure](../private-link/private-endpoint-overview.md) para replicar seus computadores locais para a rede virtual no Azure. O suporte para acesso de ponto de extremidade privado a um cofre de recuperação tem suporte para as seguintes regiões:

- Comercial do Azure: Sul EUA Central, oeste dos EUA 2, leste dos EUA
- Azure governamental: US Gov-Virgínia, US Gov Arizona, US Gov Texas, US DoD Leste, US DoD Central

Este artigo fornece instruções para que você execute as seguintes etapas:

- Crie um cofre dos serviços de recuperação de backup do Azure para proteger seus computadores.
- Habilite uma identidade gerenciada para o cofre e conceda as permissões necessárias para acessar as contas de armazenamento do cliente para replicar o tráfego do local para os locais de destino do Azure. O acesso de identidade gerenciada para armazenamento é necessário quando você está configurando o acesso de link privado para o cofre.
- Fazer alterações de DNS necessárias para pontos de extremidade privados
- Criar e aprovar pontos de extremidade privados para um cofre dentro de uma rede virtual
- Crie pontos de extremidade privados para as contas de armazenamento. Você pode continuar a permitir o acesso público ou de firewall para armazenamento, conforme necessário. A criação de um ponto de extremidade privado para acessar o armazenamento não é obrigatória para Azure Site Recovery.
  
Veja abaixo uma arquitetura de referência sobre como o fluxo de trabalho de replicação é alterado para recuperação de desastres híbridos com pontos de extremidade privados. Pontos de extremidade privados não podem ser criados em sua rede local. Para usar links privados, você precisa criar uma rede virtual do Azure (chamada de "ignorar rede" neste artigo), estabelecer conectividade privada entre o local e a rede de bypass e, em seguida, criar pontos de extremidade privados na rede de bypass. A escolha da conectividade privada está a seu critério.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/architecture.png" alt-text="Arquitetura de referência para Site Recovery com pontos de extremidade privados.":::

## <a name="prerequisites-and-caveats"></a>Pré-requisitos e limitações

- O suporte para links privados está habilitado para Site Recovery infraestrutura com a versão **9,35** e superior.
- Pontos de extremidade privados podem ser criados somente para novos cofres de serviços de recuperação que não têm nenhum item registrado no cofre. Assim, os pontos de extremidade privados **devem ser criados antes que qualquer item seja adicionado ao cofre**. Examine a estrutura de preços para [pontos de extremidade privados](https://azure.microsoft.com/pricing/details/private-link/).
- Quando um ponto de extremidade privado é criado para um cofre, o cofre é bloqueado e **não pode ser acessado de redes diferentes das redes que têm pontos de extremidade privados**.
- Azure Active Directory atualmente não dá suporte a pontos de extremidade privados. Assim, os IPs e os nomes de domínio totalmente qualificados necessários para Azure Active Directory funcionar em uma região precisam ter permissão de acesso de saída da rede virtual do Azure protegida. Você também pode usar a marca do grupo de segurança de rede "Azure Active Directory" e as marcas de firewall do Azure para permitir acesso ao Azure Active Directory, conforme aplicável.
- **São necessários cinco endereços IP** na rede de bypass onde você cria seu ponto de extremidade privado. Quando você cria um ponto de extremidade privado para o cofre, Site Recovery cria cinco links privados para acessar seus microserviços.
- **Um endereço IP adicional é necessário** na rede de bypass para conectividade de ponto de extremidade privado para uma conta de armazenamento de cache. O método de conectividade, como Internet ou [ExpressRoute](../expressroute/index.yml), entre o local e o ponto de extremidade da conta de armazenamento está na sua descrição. O estabelecimento de um link privado é opcional. Pontos de extremidade privados para armazenamento só podem ser criados no tipo Uso Geral v2. Examine a estrutura de preços para [transferência de dados no GPv2](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

 ## <a name="creating-and-using-private-endpoints-for-site-recovery"></a>Criando e usando pontos de extremidade privados para Site Recovery

 Esta seção aborda as etapas envolvidas na criação e no uso de pontos de extremidade privados para Azure Site Recovery dentro de suas redes virtuais.

> [!NOTE]
> É altamente recomendável que você siga estas etapas na mesma sequência fornecida. Não fazer isso pode levar ao cofre que está sendo renderizado não é possível usar pontos de extremidade privados e exigir que você reinicie o processo com um novo cofre.

## <a name="create-a-recovery-services-vault"></a>Crie um cofre dos Serviços de Recuperação.

Um cofre dos serviços de recuperação é uma entidade que contém as informações de replicação dos computadores e é usado para disparar operações de Site Recovery. Para obter as etapas para criar um cofre dos serviços de recuperação na região do Azure em que você deseja realizar o failover, se houver um desastre, consulte [criar um cofre dos serviços de recuperação](./azure-to-azure-tutorial-enable-replication.md#create-a-recovery-services-vault).

## <a name="enable-the-managed-identity-for-the-vault"></a>Habilite a identidade gerenciada para o cofre.

Uma [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) permite que o cofre tenha acesso às contas de armazenamento do cliente. Site Recovery precisa acessar o armazenamento de destino e as contas de armazenamento de cache/log, dependendo do requisito de cenário. O acesso de identidade gerenciada é essencial quando você está usando o serviço de links privados para o cofre.

1. Vá para o cofre dos serviços de recuperação. Selecione **identidade** em _configurações_.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/enable-managed-identity-in-vault.png" alt-text="Mostra o portal do Azure e a página de serviços de recuperação.":::

1. Altere o **status** para _ativado_ e selecione **salvar**.

1. Uma **ID de objeto** é gerada indicando que o cofre agora está registrado com Azure Active Directory.

## <a name="create-private-endpoints-for-the-recovery-services-vault"></a>Criar pontos de extremidade privados para o cofre dos serviços de recuperação

Você precisará de um ponto de extremidade privado para o cofre na rede de bypass para a proteção de computadores na rede de origem local. Crie o ponto de extremidade privado usando o centro de links privado no portal ou por meio de [Azure PowerShell](../private-link/create-private-endpoint-powershell.md).

1. Na barra de pesquisa portal do Azure, procure e selecione "link privado". Essa ação leva você para o centro de links privado.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-links.png" alt-text="Mostra a pesquisa do portal do Azure para o centro de links privado.":::

1. Na barra de navegação à esquerda, selecione **pontos de extremidade privados**. Uma vez na página pontos de extremidade particulares, selecione ** \+ Adicionar** para começar a criar um ponto de extremidade privado para seu cofre.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints.png" alt-text="Mostra a criação de um ponto de extremidade privado no centro de links privado.":::

1. Uma vez na experiência "criar ponto de extremidade privado", é necessário especificar detalhes para criar sua conexão de ponto de extremidade privada.

   1. **Noções básicas**: Preencha os detalhes básicos para seus pontos de extremidade privados. A região deve ser igual à rede de bypass.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-basic-tab.png" alt-text="Mostra a guia básica, os detalhes do projeto, a assinatura e outros campos relacionados para criar um ponto de extremidade privado no portal do Azure.":::

   1. **Recurso**: essa guia exige que você mencione o recurso de plataforma como serviço para o qual deseja criar a conexão. Selecione _Microsoft. recoveryservices/cofres_ do **tipo de recurso** para sua assinatura selecionada. Em seguida, escolha o nome do seu cofre dos serviços de recuperação para o **recurso** e defina _Azure site Recovery_ como o **subrecurso de destino**.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-resource-tab.png" alt-text="Mostra a guia de recursos, o tipo de recurso, o recurso e os campos de subrecurso de destino para vincular a um ponto de extremidade privado no portal do Azure.":::

   1. **Configuração**: em configuração, especifique a rede e a sub-rede de bypass onde você deseja que o ponto de extremidade privado seja criado. Habilite a integração com a zona DNS privada selecionando **Sim**.
      Escolha uma zona DNS já criada ou crie uma nova. A seleção de **Sim** vincula automaticamente a zona à rede de bypass e adiciona os registros DNS necessários para a resolução DNS de novos IPS e nomes de domínio totalmente qualificados criados para o ponto de extremidade privado.

      Certifique-se de optar por criar uma nova zona DNS para cada novo ponto de extremidade privado conectando-se ao mesmo cofre. Se você escolher uma zona DNS privada existente, os registros CNAME anteriores serão substituídos. Consulte a [orientação do ponto de extremidade particular](../private-link/private-endpoint-overview.md#private-endpoint-properties) antes de continuar.

      Se o seu ambiente tiver um modelo Hub e spoke, você precisará apenas de um ponto de extremidade privado e apenas uma zona DNS privada para toda a configuração, já que todas as suas redes virtuais já têm o emparelhamento habilitado entre elas. Para obter mais informações, consulte [integração de DNS do ponto de extremidade privado](../private-link/private-endpoint-dns.md#virtual-network-workloads-without-custom-dns-server).

      Para criar manualmente a zona DNS privada, siga as etapas em [criar zonas DNS privadas e adicionar registros DNS manualmente](#create-private-dns-zones-and-add-dns-records-manually).

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-endpoints-configuration-tab.png" alt-text="Mostra a guia configuração com os campos de integração de rede e DNS para configuração de um ponto de extremidade privado no portal do Azure.":::

   1. **Marcas**: opcionalmente, você pode adicionar marcas para seu ponto de extremidade particular.

   1. **Examinar \+ criar**: quando a validação for concluída, selecione **criar** para criar o ponto de extremidade privado.

Depois que o ponto de extremidade privado for criado, cinco nomes de domínio totalmente qualificados serão adicionados ao ponto de extremidade privado. Esses links permitem que os computadores na rede local obtenham acesso por meio da rede de bypass para todos os microserviços necessários Site Recovery no contexto do cofre. O mesmo ponto de extremidade privado pode ser usado para a proteção de qualquer computador do Azure na rede de bypass e todas as redes emparelhadas.

Os cinco nomes de domínio são formatados com o seguinte padrão:

`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

## <a name="approve-private-endpoints-for-site-recovery"></a>Aprovar pontos de extremidade privados para Site Recovery

Se o usuário que cria o ponto de extremidade privado também for o proprietário do cofre dos serviços de recuperação, o ponto de extremidade privado criado acima será aprovado automaticamente em alguns minutos. Caso contrário, o proprietário do cofre deve aprovar o ponto de extremidade privado antes de usá-lo. Para aprovar ou rejeitar uma conexão de ponto de extremidade particular solicitada, acesse **conexões de ponto de extremidade privado** em "configurações" na página cofre de recuperação.

Você pode ir para o recurso de ponto de extremidade privado para examinar o status da conexão antes de continuar.

:::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/vault-private-endpoint-connections.png" alt-text="Mostra a página conexões de ponto de extremidade privada do cofre e a lista de conexões no portal do Azure.":::

## <a name="optional-create-private-endpoints-for-the-cache-storage-account"></a><a name="create-private-endpoints-for-the-cache-storage-account"></a>Adicional Criar pontos de extremidade privados para a conta de armazenamento de cache

Um ponto de extremidade privado para o armazenamento do Azure pode ser usado. A criação de pontos de extremidade privados para acesso de armazenamento é _opcional_ para replicação de Azure site Recovery. Ao criar um ponto de extremidade privado para armazenamento, você precisa de um ponto de extremidade privado para a conta de armazenamento de log/cache em sua rede virtual de bypass.

> [!NOTE]
> O ponto de extremidade privado para armazenamento só pode ser criado em contas de armazenamento **uso geral v2** . Para obter informações sobre preços, consulte [preços de blob de páginas padrão](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Siga as [orientações para a criação de armazenamento privado](../private-link/create-private-endpoint-storage-portal.md#create-your-private-endpoint) para criar uma conta de armazenamento com um ponto de extremidade privado. Certifique-se de selecionar **Sim** para integração com a zona DNS privada. Selecione uma zona DNS já criada ou crie uma nova.

## <a name="grant-required-permissions-to-the-vault"></a>Conceder as permissões necessárias ao cofre

Dependendo da sua configuração, talvez você precise de uma ou mais contas de armazenamento na região do Azure de destino. Em seguida, conceda as permissões de identidade gerenciadas para todas as contas de armazenamento de cache/log exigidas pelo Site Recovery. Nesse caso, você deve criar as contas de armazenamento necessárias com antecedência.

Antes de habilitar a replicação de máquinas virtuais, a identidade gerenciada do cofre deve ter as seguintes permissões de função, dependendo do tipo de conta de armazenamento:

- Contas de armazenamento baseadas no Resource Manager (tipo padrão):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Colaborador de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)
- Contas de armazenamento baseadas no Resource Manager (tipo Premium):
  - [Colaborador](../role-based-access-control/built-in-roles.md#contributor)
  - [Proprietário de Dados do Blob de Armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-owner)
- Contas de armazenamento clássicas:
  - [Colaborador da conta de armazenamento clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-contributor)
  - [Função do Serviço de Operador da Chave da Conta de Armazenamento Clássica](../role-based-access-control/built-in-roles.md#classic-storage-account-key-operator-service-role)

As etapas a seguir descrevem como adicionar uma atribuição de função a suas contas de armazenamento, uma de cada vez:

1. Vá para a conta de armazenamento e navegue até **controle de acesso (iam)** no lado esquerdo da página.

1. Uma vez no **controle de acesso (iam)**, na caixa "adicionar uma atribuição de função", selecione **Adicionar**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment.png" alt-text="Mostra a página de controle de acesso (IAM) em uma conta de armazenamento e o botão ' adicionar uma atribuição de função ' no portal do Azure.":::

1. Na página do lado "adicionar uma atribuição de função", escolha a função na lista acima no menu suspenso **função** . Insira o **nome** do cofre e selecione **salvar**.

   :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/storage-role-assignment-select-role.png" alt-text="Mostra a página de controle de acesso (IAM) em uma conta de armazenamento e as opções para selecionar uma função e a qual entidade para conceder essa função no portal do Azure.":::

Além dessas permissões, os serviços confiáveis da MS também precisam ter acesso permitido. Vá para "firewalls e redes virtuais" e selecione a caixa de seleção "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" em **exceções**.

## <a name="protect-your-virtual-machines"></a>Proteger suas máquinas virtuais

Depois que todas as configurações acima forem concluídas, continue com a configuração da sua infraestrutura local.

- [Implantar o servidor de configuração para VMware e máquinas físicas](./vmware-azure-deploy-configuration-server.md)
- OU [Configurar o ambiente do Hyper-V para replicação](./hyper-v-azure-tutorial.md#set-up-the-source-environment)

Quando a instalação for concluída, habilite a replicação para seus computadores de origem. Certifique-se de que a configuração da infraestrutura seja feita somente depois que os pontos de extremidade privados para o cofre já estiverem criados na rede de bypass.

## <a name="create-private-dns-zones-and-add-dns-records-manually"></a>Criar zonas DNS privadas e adicionar registros DNS manualmente

Se você não selecionou a opção de integração com a zona DNS privada no momento da criação do ponto de extremidade privado para o cofre, siga as etapas nesta seção.

Crie uma zona DNS privada para permitir que o provedor de Site Recovery (para computadores Hyper-V) ou o servidor de processo (para VMware/computadores físicos) resolva nomes de domínio totalmente qualificados de link privado para IPs privados.

1. Criar uma zona DNS privada

   1. Pesquise "zona de DNS privado" na barra de pesquisa **todos os serviços** e selecione "zonas de DNS privado" na lista suspensa.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/search-private-dns-zone.png" alt-text="Mostra a pesquisa da zona DNS privada na página novos recursos no portal do Azure.":::

   1. Na página "zonas de DNS privado", selecione o botão ** \+ Adicionar** para começar a criar uma nova zona.

   1. Na página "criar zona DNS privada", preencha os detalhes necessários. Insira o nome da zona DNS privada como `privatelink.siterecovery.windowsazure.com` . Você pode escolher qualquer grupo de recursos e qualquer assinatura para criá-lo.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/create-private-dns-zone.png" alt-text="Mostra a guia noções básicas da página Criar zona de DNS privado e detalhes do projeto relacionado no portal do Azure.":::

   1. Continue na guia **examinar \+ criar** para examinar e criar a zona DNS.

1. Vincular zona DNS privada à sua rede virtual

   A zona DNS privada criada acima agora deve estar vinculada ao bypass.

   1. Vá para a zona DNS privada que você criou na etapa anterior e navegue até os **links de rede virtual** no lado esquerdo da página. Uma vez lá, selecione o botão ** \+ Adicionar** .

   1. Preencha os detalhes necessários. Os campos **assinatura** e **rede virtual** devem ser preenchidos com os detalhes correspondentes da rede de bypass. Os outros campos devem ser deixados como estão.

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-virtual-network-link.png" alt-text="Mostra a página para adicionar um link de rede virtual com o nome do link, a assinatura e a rede virtual relacionada no portal do Azure.":::

1. Adicionar registros DNS

   Depois de criar a zona DNS privada necessária e o ponto de extremidade privado, você precisará adicionar os registros DNS à sua zona DNS.

   > [!NOTE]
   > Caso você esteja usando uma zona DNS privada personalizada, certifique-se de que entradas semelhantes sejam feitas conforme discutido abaixo.

   Esta etapa exige que você faça entradas para cada nome de domínio totalmente qualificado em seu ponto de extremidade privado em sua zona DNS privada.

   1. Acesse sua zona DNS privada e navegue até a seção **visão geral** no lado esquerdo da página. Uma vez lá, selecione ** \+ conjunto de registros** para começar a adicionar registros.

   1. Na página "Adicionar conjunto de registros" que é aberta, adicione uma entrada para cada nome de domínio totalmente qualificado e IP privado como um registro _de_ tipo. A lista de nomes de domínio totalmente qualificados e IPs pode ser obtida na página "ponto de extremidade particular" em **visão geral**. Conforme mostrado no exemplo abaixo, o primeiro nome de domínio totalmente qualificado do ponto de extremidade privado é adicionado ao conjunto de registros na zona DNS privada.

      Esses nomes de domínio totalmente qualificados correspondem ao padrão:`{Vault-ID}-asr-pod01-{type}-.{target-geo-code}.siterecovery.windowsazure.com`

      :::image type="content" source="./media/hybrid-how-to-enable-replication-private-endpoints/add-record-set.png" alt-text="Mostra a página para adicionar um registro de tipo DNS para o nome de domínio totalmente qualificado para o ponto de extremidade privado no portal do Azure.":::

## <a name="next-steps"></a>Próximas etapas

Agora que você habilitou pontos de extremidade privados para a replicação de sua máquina virtual, consulte estas outras páginas para obter informações adicionais e relacionadas:

- [Implantar um servidor de configuração local](./vmware-azure-deploy-configuration-server.md)
- [Configurar a recuperação de desastre de VMs locais do Hyper-V para o Azure](./hyper-v-azure-tutorial.md)
