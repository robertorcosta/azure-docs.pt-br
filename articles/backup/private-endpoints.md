---
title: Pontos de extremidade privados
description: Entenda o processo de criação de pontos de extremidade privados para o backup do Azure e os cenários em que o uso de pontos de extremidade privados ajuda a manter a segurança de seus recursos.
ms.topic: conceptual
ms.date: 05/07/2020
ms.openlocfilehash: 8ce767073e9acfe271e6e57f9e6d1237910b33e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85124248"
---
# <a name="private-endpoints-for-azure-backup"></a>Pontos de extremidade privados para o backup do Azure

O backup do Azure permite que você faça backup e restaure seus dados de seus cofres de serviços de recuperação usando [pontos de extremidade privados](https://docs.microsoft.com/azure/private-link/private-endpoint-overview). Os pontos de extremidade privados usam um ou mais endereços IP privados de sua VNet, colocando efetivamente o serviço em sua VNet.

Este artigo o ajudará a entender o processo de criação de pontos de extremidade privados para o backup do Azure e os cenários em que o uso de pontos de extremidade privados ajuda a manter a segurança de seus recursos.

## <a name="before-you-start"></a>Antes de começar

- Pontos de extremidade privados só podem ser criados para novos cofres de serviços de recuperação (que não têm nenhum item registrado no cofre). Portanto, os pontos de extremidade privados devem ser criados antes de tentar proteger os itens para o cofre.
- Uma rede virtual pode conter pontos de extremidade privados para vários cofres de serviços de recuperação. Além disso, um cofre dos serviços de recuperação pode ter pontos de extremidade privados para ele em várias redes virtuais. No entanto, o número máximo de pontos de extremidade privados que podem ser criados para um cofre é 12.
- Depois que um ponto de extremidade privado for criado para um cofre, o cofre será bloqueado. Ele não poderá ser acessado (para backups e restaurações) de redes que contêm um ponto de extremidade privado para o cofre. Se todos os pontos de extremidade privados para o cofre forem removidos, o cofre poderá ser acessado de todas as redes.
- Uma conexão de ponto de extremidade privada para backup usa um total de 11 IPs privados em sua sub-rede. Esse número pode ser maior (até 15) para determinadas regiões do Azure. Portanto, sugerimos que você tenha IPs privados suficientes disponíveis ao tentar criar pontos de extremidade privados para backup.
- Embora um cofre dos serviços de recuperação seja usado pelo (ambos) backup e Azure Site Recovery do Azure, este artigo aborda o uso de pontos de extremidade privados somente para o backup do Azure.
- No momento, o Azure Active Directory não dá suporte a pontos de extremidade privados. Portanto, os IPs e FQDNs necessários para Azure Active Directory trabalhar em uma região precisarão ter acesso de saída da rede protegida ao executar o backup de bancos de dados em VMs do Azure e fazer backup usando o agente MARS. Você também pode usar marcas NSG e marcas de firewall do Azure para permitir o acesso ao Azure AD, conforme aplicável.
- As redes virtuais com políticas de rede não têm suporte para pontos de extremidade privados. Você precisará desabilitar as políticas de rede antes de continuar.
- Você precisa registrar novamente o provedor de recursos dos serviços de recuperação com a assinatura se o tiver registrado antes de maio de 1 2020. Para registrar novamente o provedor, acesse sua assinatura no portal do Azure, navegue até provedor de **recursos** na barra de navegação à esquerda, selecione **Microsoft. recoveryservices** e clique em **registrar novamente**.

## <a name="recommended-and-supported-scenarios"></a>Cenários recomendados e com suporte

Embora os pontos de extremidade privados estejam habilitados para o cofre, eles são usados para backup e restauração de cargas de trabalho do SQL e do SAP HANA somente em uma VM do Azure e no backup do agente MARS. Você também pode usar o cofre para backup de outras cargas de trabalho (eles não precisariam de pontos de extremidade privados). Além do backup de cargas de trabalho do SQL e do SAP HANA e do backup usando o agente MARS, os pontos de extremidade privados também são usados para executar a recuperação de arquivos no caso do backup de VM do Azure. Para obter mais informações, confira a tabela a seguir:

| Backup de cargas de trabalho na VM do Azure (SQL, SAP HANA), backup usando o agente MARS | O uso de pontos de extremidade privados é recomendado para permitir backup e restauração sem a necessidade de permitir-listar qualquer IPs/FQDNs para o backup do Azure ou o armazenamento do Azure de suas redes virtuais. |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **Backup de VM do Azure**                                         | O backup da VM não exige que você permita o acesso a IPs ou FQDNs. Portanto, ele não requer pontos de extremidade privados para backup e restauração de discos.  <br><br>   No entanto, a recuperação de arquivo de um cofre que contém pontos de extremidade privados seria restrita a redes virtuais que contêm um ponto final privado para o cofre. <br><br>    Ao usar os discos não gerenciados do ACL'ed, verifique se a conta de armazenamento que contém os discos permite o acesso a **serviços confiáveis da Microsoft** se for ACL'ed. |
| **Backup de arquivos do Azure**                                      | Os backups de arquivos do Azure são armazenados na conta de armazenamento local. Portanto, ele não requer pontos de extremidade privados para backup e restauração. |

## <a name="creating-and-using-private-endpoints-for-backup"></a>Criando e usando pontos de extremidade privados para backup

Esta seção aborda as etapas envolvidas na criação e no uso de pontos de extremidade privados para o backup do Azure dentro de suas redes virtuais.

>[!IMPORTANT]
> É altamente recomendável que você siga as etapas na mesma sequência mencionada neste documento. Não fazer isso pode levar ao cofre que está sendo renderizado incompatível para usar pontos de extremidade privados e exigir que você reinicie o processo com um novo cofre.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

Consulte [esta seção](#create-a-recovery-services-vault-using-the-azure-resource-manager-client) para saber como criar um cofre usando o cliente Azure Resource Manager. Isso cria um cofre com sua identidade gerenciada já habilitada. Saiba mais sobre os cofres dos serviços de recuperação [aqui](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview).

## <a name="enable-managed-identity-for-your-vault"></a>Habilitar identidade gerenciada para seu cofre

Identidades gerenciadas permitem que o cofre crie e use pontos de extremidade privados. Esta seção fala sobre como habilitar a identidade gerenciada para seu cofre.

1. Vá para o cofre dos serviços de recuperação-> **identidade**.

    ![Alterar o status de identidade para ativado](./media/private-endpoints/identity-status-on.png)

1. Altere o **status** para **ativado** e clique em **salvar**.

1. Uma **ID de objeto** é gerada, que é a identidade gerenciada do cofre.

    >[!NOTE]
    >Uma vez habilitada, a identidade gerenciada não deve ser desabilitada (até mesmo temporariamente). Desabilitar a identidade gerenciada pode levar a um comportamento inconsistente.

## <a name="dns-changes"></a>Alterações de DNS

O uso de pontos de extremidade privados requer zonas de DNS privado para permitir que a extensão de backup resolva FQDNs de link privado para IPs privados. Totalmente, três zonas DNS privadas são necessárias. Embora duas dessas zonas devam ser mandatorily criadas, a terceira pode ser aceita para ser integrada ao ponto de extremidade privado (ao criar o ponto de extremidade privado) ou pode ser criada separadamente.

Você também pode usar seus servidores DNS personalizados. Consulte [alterações de DNS para servidores DNS personalizados](#dns-changes-for-custom-dns-servers) para obter detalhes sobre como usar servidores DNS personalizados.

### <a name="creating-mandatory-dns-zones"></a>Criando zonas DNS obrigatórias

Há duas zonas DNS obrigatórias que precisam ser criadas:

- `privatelink.blob.core.windows.net`(para dados de backup/restauração)
- `privatelink.queue.core.windows.net`(para comunicação de serviço)

1. Pesquise **DNS privado zona** na barra de pesquisa **todos os serviços** e selecione **DNS privado zona** na lista suspensa.

    ![Selecionar zona de DNS privado](./media/private-endpoints/private-dns-zone.png)

1. Uma vez no painel **DNS privado zona** , clique no botão **+ Adicionar** para começar a criar uma nova zona.

1. No painel **criar zona DNS privada** , preencha os detalhes necessários. A assinatura deve ser a mesma de onde o ponto de extremidade privado será criado.

    As zonas devem ser nomeadas como:

    - `privatelink.blob.core.windows.net`
    - `privatelink.queue.core.windows.net`

    | **Zona**                           | **Serviço** | **Detalhes de assinatura e grupo de recursos (RG)**                  |
    | ---------------------------------- | ----------- | ------------------------------------------------------------ |
    | `privatelink.blob.core.windows.net`  | Blob        | **Assinatura**: o mesmo que o ponto de extremidade privado precisa ser criado **RG**: ou o RG da VNET ou o do ponto de extremidade privado |
    | `privatelink.queue.core.windows.net` | Fila       | **RG**: o RG da VNET ou o do ponto de extremidade privado |

    ![Criar zona de DNS privado](./media/private-endpoints/create-private-dns-zone.png)

1. Depois de concluído, vá para revisar e criar a zona DNS.

### <a name="optional-dns-zone"></a>Zona DNS opcional

Os clientes podem optar por integrar seus pontos de extremidade privados com zonas DNS privadas para o backup do Azure (discutido na seção sobre a criação de pontos de extremidade privados) para comunicação de serviço. Se você não quiser se integrar à zona DNS privada, poderá optar por usar seu próprio servidor DNS ou criar uma zona DNS privada separadamente. Isso é além das duas zonas DNS privadas obrigatórias discutidas na seção anterior.

Se você quiser criar uma zona DNS privada separada no Azure, poderá fazer o mesmo usando as mesmas etapas usadas para criar zonas DNS obrigatórias. Os detalhes de nomenclatura e assinatura são compartilhados abaixo:

| **Zona**                                                     | **Serviço** | **Detalhes do grupo de recursos e da assinatura**                  |
| ------------------------------------------------------------ | ----------- | ------------------------------------------------------------ |
| `privatelink.<geo>.backup.windowsazure.com`  <br><br>   **Observação**: a *geografia* aqui refere-se ao código de região. Por exemplo, *wcus* e *ne* para o Oeste EUA Central e Europa setentrional respectivamente. | Backup      | **Assinatura**: o mesmo que o ponto de extremidade privado precisa ser criado **RG**: qualquer RG dentro da assinatura |

Consulte [esta lista](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) para códigos de região.

Para convenções de nomenclatura de URL em regiões nacionais:

- [China](https://docs.microsoft.com/azure/china/resources-developer-guide#check-endpoints-in-azure)
- [Alemanha](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)
- [US Gov](https://docs.microsoft.com/azure/azure-government/documentation-government-developer-guide)

### <a name="linking-private-dns-zones-with-your-virtual-network"></a>Vinculando zonas DNS privadas com sua rede virtual

As zonas DNS criadas acima agora devem ser vinculadas à rede virtual na qual são localizados os servidores de backup. Isso precisa ser feito para todas as zonas DNS que você criou.

1. Vá para a zona DNS (que você criou na etapa anterior) e navegue até os **links de rede virtual** na barra à esquerda. Uma vez lá, clique no botão **+ Adicionar**
1. Preencha os detalhes necessários. Os campos **assinatura** e **rede virtual** devem ser preenchidos com os detalhes correspondentes da rede virtual onde os servidores existem. Os outros campos devem ser deixados como estão.

    ![Adicionar link de rede virtual](./media/private-endpoints/add-virtual-network-link.png)

## <a name="grant-permissions-to-the-vault-to-create-required-private-endpoints"></a>Conceder permissões ao cofre para criar pontos de extremidade privados necessários

Para criar os pontos de extremidade privados necessários para o backup do Azure, o cofre (a identidade gerenciada do cofre) deve ter permissões para os seguintes grupos de recursos:

- O grupo de recursos que contém a VNet de destino
- O grupo de recursos em que os pontos de extremidade privados devem ser criados
- O grupo de recursos que contém as zonas de DNS privado

Recomendamos que você conceda a função de **colaborador** para esses três grupos de recursos ao cofre (identidade gerenciada). As etapas a seguir descrevem como fazer isso para um grupo de recursos específico (isso precisa ser feito para cada um dos três grupos de recursos):

1. Vá para o grupo de recursos e navegue até **controle de acesso (iam)** na barra esquerda.
1. Uma vez no **controle de acesso**, vá para **Adicionar uma atribuição de função**.

    ![Adicionar uma atribuição de função](./media/private-endpoints/add-role-assignment.png)

1. No painel **Adicionar atribuição de função** , escolha **colaborador** como a **função**e use o **nome** do cofre como a entidade de **segurança**. Selecione seu cofre e clique em **salvar** quando terminar.

    ![Escolher função e entidade de segurança](./media/private-endpoints/choose-role-and-principal.png)

Para gerenciar permissões em um nível mais granular, consulte [criar funções e permissões manualmente](#create-roles-and-permissions-manually).

## <a name="creating-and-approving-private-endpoints-for-azure-backup"></a>Criando e aprovando pontos de extremidade privados para o backup do Azure

### <a name="creating-private-endpoints-for-backup"></a>Criando pontos de extremidade privados para backup

Esta seção descreve o processo de criação de um ponto de extremidade privado para seu cofre.

1. Na barra de pesquisa, procure e selecione **link privado**. Isso levará você ao **centro de links privado**.

    ![Pesquisar link privado](./media/private-endpoints/search-for-private-link.png)

1. Na barra de navegação à esquerda, clique em **pontos de extremidade privados**. Uma vez no painel **pontos de extremidade particulares** , clique em **+ Adicionar** para começar a criar um ponto de extremidade privado para seu cofre.

    ![Adicionar ponto de extremidade privado no centro de link privado](./media/private-endpoints/add-private-endpoint.png)

1. Uma vez no processo **criar ponto de extremidade privado** , será necessário especificar detalhes para criar sua conexão de ponto de extremidade privada.

    1. **Noções básicas**: Preencha os detalhes básicos para seus pontos de extremidade privados. A região deve ser a mesma do cofre e do recurso.

        ![Preencher detalhes básicos](./media/private-endpoints/basic-details.png)

    1. **Recurso**: essa guia exige que você mencione o recurso de PaaS para o qual deseja criar a conexão. Selecione **Microsoft. recoveryservices/cofres** do tipo de recurso para sua assinatura desejada. Depois de concluído, escolha o nome do seu cofre de serviços de recuperação como o **recurso** e **AzureBackup** como o **subrecurso de destino**.

        ![Preencher a guia de recursos](./media/private-endpoints/resource-tab.png)

    1. **Configuração**: em configuração, especifique a rede virtual e a sub-rede em que você deseja que o ponto de extremidade privado seja criado. Essa seria a vnet em que a VM está presente. Você pode optar por **integrar seu ponto de extremidade privado** a uma zona DNS privada. Como alternativa, você também pode usar seu servidor DNS personalizado ou criar uma zona DNS privada.

        ![Guia de configuração de preenchimento](./media/private-endpoints/configuration-tab.png)

    1. Opcionalmente, você pode adicionar **marcas** para seu ponto de extremidade particular.

    1. Prossiga para **revisar + criar** depois de inserir os detalhes. Quando a validação for concluída, clique em **criar** para criar o ponto de extremidade privado.

## <a name="approving-private-endpoints"></a>Aprovando pontos de extremidade privados

Se o usuário que cria o ponto de extremidade privado também for o proprietário do cofre dos serviços de recuperação, o ponto de extremidade privado criado acima será aprovado automaticamente. Caso contrário, o proprietário do cofre deve aprovar o ponto de extremidade privado antes de poder usá-lo. Esta seção aborda a aprovação manual de pontos de extremidade privados por meio do portal do Azure.

Consulte [aprovação manual de pontos de extremidade privados usando o cliente Azure Resource Manager](#manual-approval-of-private-endpoints-using-the-azure-resource-manager-client) para usar o cliente Azure Resource Manager para aprovar pontos de extremidade privados.

1. No cofre dos serviços de recuperação, navegue até **conexões de ponto de extremidade privadas** na barra esquerda.
1. Selecione a conexão de ponto de extremidade privada que você deseja aprovar.
1. Selecione **aprovar** na barra superior. Você também pode selecionar **rejeitar** ou **remover** se quiser rejeitar ou excluir a conexão do ponto de extremidade.

    ![Aprovar pontos de extremidade privados](./media/private-endpoints/approve-private-endpoints.png)

## <a name="adding-dns-records"></a>Adicionando registros DNS

>[!NOTE]
> Essa etapa não será necessária se você estiver usando uma zona DNS integrada. No entanto, se você tiver criado sua própria zona de DNS privado do Azure ou estiver usando uma zona DNS privada personalizada, certifique-se de que as entradas sejam feitas conforme descrito nesta seção.

Depois de criar a zona DNS privada opcional e os pontos de extremidade privados para seu cofre, você precisará adicionar os registros DNS à sua zona DNS. Você pode fazer isso manualmente ou usando um script do PowerShell. Isso precisa ser feito apenas para a zona DNS de backup, os BLOBs e as filas serão atualizados automaticamente.

### <a name="add-records-manually"></a>Adicionar registros manualmente

Isso exige que você faça entradas para cada FQDN em seu ponto de extremidade privado em sua zona de DNS privado.

1. Vá para a **zona DNS privada** e navegue até a opção **visão geral** na barra esquerda. Uma vez lá, clique em **+ conjunto de registros** para começar a adicionar registros.

    ![Selecione + conjunto de registros para adicionar registros](./media/private-endpoints/select-record-set.png)

1. No painel **Adicionar conjunto de registros** que é aberto, adicione uma entrada para cada FQDN e IP privado como um registro **de tipo** . A lista de FQDNs e IPs pode ser obtida em seu ponto de extremidade privado (em **visão geral**). Conforme mostrado no exemplo abaixo, o primeiro FQDN do ponto de extremidade privado está sendo adicionado ao conjunto de registros na zona DNS privada.

    ![Lista de FQDNs e IPs](./media/private-endpoints/list-of-fqdn-and-ip.png)

    ![Adicionar conjunto de registros](./media/private-endpoints/add-record-set.png)

### <a name="add-records-using-powershell-script"></a>Adicionar registros usando o script do PowerShell

1. Inicie o **Cloud Shell** na portal do Azure e selecione **carregar arquivo** na janela do PowerShell.

    ![Selecione carregar arquivo na janela do PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Carregue este script: [DnsZoneCreation](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/dnszonerecordcreation.ps1)

1. Vá para sua pasta base (por exemplo: `cd /home/user` )

1. Execute o seguinte script:

    ```azurepowershell
    ./dnszonerecordcreation.ps1 -Subscription <SubscriptionId> -VaultPEName <VaultPE Name> -VaultPEResourceGroup <Vault PE RG> -DNSResourceGroup <Private DNS RG> -Privatezone <privatednszone>
    ```

    Estes são os parâmetros:

    - **assinatura**: a assinatura na qual os recursos (ponto de extremidade particular do cofre e zona DNS privada) residem
    - **vaultPEName**: nome do ponto de extremidade privado criado para o cofre
    - **vaultPEResourceGroup**: grupo de recursos que contém o ponto de extremidade privado do cofre
    - **dnsResourceGroup**: grupo de recursos que contém as zonas DNS privadas
    - **Privatezone**: nome da zona DNS privada

## <a name="using-private-endpoints-for-backup"></a>Usando pontos de extremidade privados para backup

Depois que os pontos de extremidade privados criados para o cofre em sua VNet tiverem sido aprovados, você poderá começar a usá-los para executar backups e restaurações.

>[!IMPORTANT]
>Verifique se você concluiu todas as etapas mencionadas acima no documento com êxito antes de continuar. Para recapitular, você deve ter concluído as etapas na seguinte lista de verificação:
>
>1. Criado um (novo) cofre de serviços de recuperação
>1. Habilitou o cofre para usar a identidade gerenciada atribuída pelo sistema
>1. Criou três zonas de DNS privado (duas se estiver usando uma zona DNS integrada para backup)
>1. Vinculou suas zonas de DNS privado à sua rede virtual do Azure
>1. Permissões relevantes atribuídas à identidade gerenciada do cofre
>1. Criou um ponto de extremidade privado para seu cofre
>1. Aprovado o ponto de extremidade privado (se não aprovado automaticamente)
>1. Adicionados registros DNS necessários à sua zona DNS privada para backup (aplicável somente se não estiver usando uma zona DNS privada integrada)

### <a name="backup-and-restore-of-workloads-in-azure-vm-sql-sap-hana"></a>Backup e restauração de cargas de trabalho na VM do Azure (SQL, SAP HANA)

Depois que o ponto de extremidade privado é criado e aprovado, nenhuma alteração adicional é necessária do lado do cliente para usar o ponto de extremidade privado. Toda a comunicação e transferência de dados de sua rede segura para o cofre serão executadas por meio do ponto de extremidade privado.
No entanto, se você remover pontos de extremidade privados para o cofre depois que um servidor (SQL/SAP HANA) tiver sido registrado nele, você precisará registrar novamente o contêiner com o cofre. Você não precisa interromper a proteção para eles.

### <a name="backup-and-restore-through-mars-agent"></a>Backup e restauração por meio do agente MARS

Ao usar o agente MARS para fazer backup de seus recursos locais, verifique se sua rede local (contendo seus recursos de backup) está emparelhada com a VNet do Azure que contém um ponto de extremidade privado para o cofre, para que você possa usá-lo. Em seguida, você pode continuar a instalar o agente MARS e configurar o backup conforme detalhado aqui. Você deve, no entanto, garantir que toda a comunicação para o backup ocorra somente por meio da rede emparelhada.

No entanto, se você remover pontos de extremidade privados para o cofre depois que um agente MARS tiver sido registrado nele, você precisará registrar novamente o contêiner com o cofre. Você não precisa interromper a proteção para eles.

## <a name="additional-topics"></a>Tópicos adicionais

### <a name="create-a-recovery-services-vault-using-the-azure-resource-manager-client"></a>Criar um cofre dos serviços de recuperação usando o cliente Azure Resource Manager

Você pode criar o cofre dos serviços de recuperação e habilitar sua identidade gerenciada (habilitar a identidade gerenciada é necessário, como veremos mais tarde) usando o cliente Azure Resource Manager. Um exemplo para fazer isso é compartilhado abaixo:

```rest
armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>?api-version=2017-07-01-preview @C:\<filepath>\MSIVault.json
```

O arquivo JSON acima deve ter o seguinte conteúdo:

JSON de solicitação:

```json
{
  "location": "eastus2",
  "name": "<vaultname>",
  "etag": "W/\"datetime'2019-05-24T12%3A54%3A42.1757237Z'\"",
  "tags": {
    "PutKey": "PutValue"
  },
  "properties": {},
  "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
  "type": "Microsoft.RecoveryServices/Vaults",
  "sku": {
    "name": "RS0",
    "tier": "Standard"
  },
  "identity": {
    "type": "systemassigned"
  }
}
```

JSON de resposta:

```json
{
   "location": "eastus2",
   "name": "<vaultname>",
   "etag": "W/\"datetime'2020-02-25T05%3A26%3A58.5181122Z'\"",
   "tags": {
     "PutKey": "PutValue"
   },
   "identity": {
     "tenantId": "<tenantid>",
     "principalId": "<principalid>",
     "type": "SystemAssigned"
   },
   "properties": {
     "provisioningState": "Succeeded",
     "privateEndpointStateForBackup": "None",
     "privateEndpointStateForSiteRecovery": "None"
   },
   "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>",
   "type": "Microsoft.RecoveryServices/Vaults",
   "sku": {
     "name": "RS0",
     "tier": "Standard"
   }
 }
```

>[!NOTE]
>O cofre criado neste exemplo por meio do cliente Azure Resource Manager já foi criado com uma identidade gerenciada atribuída pelo sistema.

### <a name="managing-permissions-on-resource-groups"></a>Gerenciando permissões em grupos de recursos

A identidade gerenciada para o cofre precisa ter as seguintes permissões no grupo de recursos e na rede virtual em que os pontos de extremidade privados serão criados:

- `Microsoft.Network/privateEndpoints/*`Isso é necessário para executar o CRUD em pontos de extremidade privados no grupo de recursos. Ele deve ser atribuído no grupo de recursos.
- `Microsoft.Network/virtualNetworks/subnets/join/action`Isso é necessário na rede virtual em que o IP privado está sendo anexado com o ponto de extremidade privado.
- `Microsoft.Network/networkInterfaces/read`Isso é necessário no grupo de recursos para obter a interface de rede criada para o ponto de extremidade privado.
- Função de colaborador de zona DNS privado essa função já existe e pode ser usada para fornecer `Microsoft.Network/privateDnsZones/A/*` `Microsoft.Network/privateDnsZones/virtualNetworkLinks/read` permissões e.

Você pode usar um dos seguintes métodos para criar funções com as permissões necessárias:

#### <a name="create-roles-and-permissions-manually"></a>Criar funções e permissões manualmente

Crie os seguintes arquivos JSON e use o comando do PowerShell no final da seção para criar funções:

PrivateEndpointContributorRoleDef.jsem

```json
{
  "Name": "PrivateEndpointContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows management of Private Endpoint",
  "Actions": [
    "Microsoft.Network/privateEndpoints/*",
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

NetworkInterfaceReaderRoleDef.jsem

```json
{
  "Name": "NetworkInterfaceReader",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows read on networkInterfaces",
  "Actions": [
    "Microsoft.Network/networkInterfaces/read"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

PrivateEndpointSubnetContributorRoleDef.jsem

```json
{
  "Name": "PrivateEndpointSubnetContributor",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows adding of Private Endpoint connection to Virtual Networks",
  "Actions": [
    "Microsoft.Network/virtualNetworks/subnets/join/action"
  ],
  "NotActions": [],
  "AssignableScopes": [
    "/subscriptions/00000000-0000-0000-0000-000000000000"
  ]
}
```

```azurepowershell
 New-AzRoleDefinition -InputFile "PrivateEndpointContributorRoleDef.json"
 New-AzRoleDefinition -InputFile "NetworkInterfaceReaderRoleDef.json"
 New-AzRoleDefinition -InputFile "PrivateEndpointSubnetContributorRoleDef.json"
```

#### <a name="use-a-script"></a>Usar um script

1. Inicie o **Cloud Shell** na portal do Azure e selecione **carregar arquivo** na janela do PowerShell.

    ![Selecione carregar arquivo na janela do PowerShell](./media/private-endpoints/upload-file-in-powershell.png)

1. Carregue o script a seguir: [VaultMsiPrereqScript](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/VaultMsiPrereqScript.ps1)

1. Vá para sua pasta base (por exemplo: `cd /home/user` )

1. Execute o seguinte script:

    ```azurepowershell
    ./VaultMsiPrereqScript.ps1 -subscription <subscription-Id> -vaultPEResourceGroup <vaultPERG> -vaultPESubnetResourceGroup <subnetRG> -vaultMsiName <msiName>
    ```

    Estes são os parâmetros:

    - **assinatura**: * * SubscriptionID que tem o grupo de recursos em que o ponto de extremidade privado para o cofre deve ser criado e a sub-rede em que o ponto de extremidade privado do cofre será anexado

    - **vaultPEResourceGroup**: grupo de recursos em que o ponto de extremidade privado para o cofre será criado

    - **vaultPESubnetResourceGroup**: grupo de recursos da sub-rede à qual o ponto de extremidade privado será Unido

    - **vaultMsiName**: nome do MSI do cofre, que é o mesmo que o **vaultname**

1. Conclua a autenticação e o script obterá o contexto da assinatura fornecida acima. Ele criará as funções apropriadas se elas estiverem ausentes do locatário e atribuirá funções ao MSI do cofre.

### <a name="creating-private-endpoints-using-azure-powershell"></a>Criando pontos de extremidade privados usando o Azure PowerShell

#### <a name="auto-approved-private-endpoints"></a>Pontos de extremidade privados aprovados automaticamente

```azurepowershell
$vault = Get-AzRecoveryServicesVault `
        -ResourceGroupName $vaultResourceGroupName `
        -Name $vaultName
  
$privateEndpointConnection = New-AzPrivateLinkServiceConnection `
        -Name $privateEndpointConnectionName `
        -PrivateLinkServiceId $vault.ID `
        -GroupId "AzureBackup"  
  
$privateEndpoint = New-AzPrivateEndpoint `
        -ResourceGroupName $vmResourceGroupName `
        -Name $privateEndpointName `
        -Location $location `
        -Subnet $subnet `
        -PrivateLinkServiceConnection $privateEndpointConnection `
        -Force
```

### <a name="manual-approval-of-private-endpoints-using-the-azure-resource-manager-client"></a>Aprovação manual de pontos de extremidade privados usando o cliente Azure Resource Manager

1. Use **getvault** para obter a ID de conexão de ponto de extremidade privada para seu ponto de extremidade particular.

    ```rest
    armclient GET /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/vaults/<vaultname>?api-version=2017-07-01-preview
    ```

    Isso retornará a ID de conexão do ponto de extremidade privado. O nome da conexão pode ser recuperado usando a primeira parte da ID de conexão da seguinte maneira:

    `privateendpointconnectionid = {peName}.{vaultId}.backup.{guid}`

1. Obtenha a **ID de conexão do ponto de extremidade privado** (e o **nome do ponto de extremidade privado**, sempre que necessário) da resposta e substitua-o no URI de Azure Resource Manager e JSON a seguir e tente alterar o status para "aprovado/rejeitado/desconectado", conforme demonstrado no exemplo a seguir:

    ```rest
    armclient PUT /subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>?api-version=2020-02-02-preview @C:\<filepath>\BackupAdminApproval.json
    ```

    JSON:

    ```json
    {
    "id": "/subscriptions/<subscriptionid>/resourceGroups/<rgname>/providers/Microsoft.RecoveryServices/Vaults/<vaultname>/privateEndpointConnections/<privateendpointconnectionid>",
    "properties": {
        "privateEndpoint": {
        "id": "/subscriptions/<subscriptionid>/resourceGroups/<pergname>/providers/Microsoft.Network/privateEndpoints/pename"
        },
        "privateLinkServiceConnectionState": {
        "status": "Disconnected",  //choose state from Approved/Rejected/Disconnected
        "description": "Disconnected by <userid>"
        }
    }
    }
    ```

### <a name="dns-changes-for-custom-dns-servers"></a>Alterações de DNS para servidores DNS personalizados

#### <a name="create-dns-zones-for-custom-dns-servers"></a>Criar zonas DNS para servidores DNS personalizados

Você precisa criar três zonas DNS privadas e vinculá-las à sua rede virtual.

| **Zona**                                                     | **Serviço** |
| ------------------------------------------------------------ | ----------- |
| `privatelink.<geo>.backup.windowsazure.com`      | Backup      |
| `privatelink.blob.core.windows.net`                            | Blob        |
| `privatelink.queue.core.windows.net`                           | Fila       |

>[!NOTE]
>No texto acima, *geo* refere-se ao código da região. Por exemplo, *wcus* e *ne* para o Oeste EUA Central e Europa setentrional respectivamente.

Consulte [esta lista](https://download.microsoft.com/download/1/2/6/126a410b-0e06-45ed-b2df-84f353034fa1/AzureRegionCodesList.docx) para códigos de região.

#### <a name="adding-dns-records-for-custom-dns-servers"></a>Adicionando registros DNS para servidores DNS personalizados

Isso exige que você faça entradas para cada FQDN em seu ponto de extremidade privado em sua zona de DNS privado.

Deve-se observar que usaremos os pontos de extremidade privados criados para backup, BLOB e serviço Fila.

- O ponto de extremidade privado para o cofre usa o nome especificado ao criar o ponto de extremidade privado
- Os pontos de extremidade privados para serviços BLOB e fila são prefixados com o nome do mesmo para o cofre.

Por exemplo, a imagem a seguir mostra os três pontos de extremidade privados criados para uma conexão de ponto de extremidades privada com o nome *pee2epe*:

![Três pontos de extremidade privados para uma conexão de ponto final particular](./media/private-endpoints/three-private-endpoints.png)

Zona DNS para o serviço de backup ( `privatelink.<geo>.backup.windowsazure.com` ):

1. Navegue até o ponto de extremidade privado para backup no **centro de links privado**. A página de visão geral lista o FQDN e os IPs privados para seu ponto de extremidade privado.

1. Adicione uma entrada para cada FQDN e IP privado como um registro de tipo.

    ![Adicionar entrada para cada FQDN e IP privado](./media/private-endpoints/add-entry-for-each-fqdn-and-ip.png)

Zona DNS para o serviço BLOB ( `privatelink.blob.core.windows.net` ):

1. Navegue até o ponto de extremidade privado para blob no **centro de links privado**. A página de visão geral lista o FQDN e os IPs privados para seu ponto de extremidade privado.

1. Adicione uma entrada para o FQDN e o IP privado como um registro de tipo.

    ![Adicionar entrada para o FQDN e o IP privado como um registro de tipo para o serviço blob](./media/private-endpoints/add-type-a-record-for-blob.png)

Zona DNS para o serviço Fila ( `privatelink.queue.core.windows.net` ):

1. Navegue até o ponto de extremidade privado para a fila no **centro de links privado**. A página de visão geral lista o FQDN e os IPs privados para seu ponto de extremidade privado.

1. Adicione uma entrada para o FQDN e o IP privado como um registro de tipo.

    ![Adicione a entrada para o FQDN e o IP privado como um registro de tipo para o serviço Fila](./media/private-endpoints/add-type-a-record-for-queue.png)

## <a name="frequently-asked-questions"></a>Perguntas frequentes

Q. Posso criar um ponto de extremidade privado para um cofre de backup existente?<br>
a. Não, os pontos de extremidade privados só podem ser criados para novos cofres de backup. Portanto, o cofre não deve ter nenhum item protegido. Na verdade, nenhuma tentativa de proteger itens para o cofre pode ser feita antes da criação de pontos de extremidade privados.

Q. Tentei proteger um item ao meu cofre, mas ele falhou e o cofre ainda não contém itens protegidos. Posso criar pontos de extremidade privados para este cofre?<br>
a. Não, o cofre não deve ter nenhuma tentativa de proteger nenhum item para ele no passado.

Q. Tenho um cofre que está usando pontos de extremidade privados para backup e restauração. Posso adicionar ou remover pontos de extremidade particulares para este cofre, mesmo se eu tiver itens de backup protegidos?<br>
a. Sim. Se você já tiver criado pontos de extremidade privados para um cofre e itens de backup protegidos, será possível adicionar ou remover pontos de extremidade privados posteriormente, conforme necessário.

Q. O ponto de extremidade privado para o backup do Azure também pode ser usado para Azure Site Recovery?<br>
a. Não, o ponto de extremidade privado para backup só pode ser usado para o backup do Azure. Você precisará criar um novo ponto de extremidade privado para Azure Site Recovery, se ele tiver suporte do serviço.

Q. Eu perdi uma das etapas neste artigo e passei para proteger minha fonte de dados. Ainda posso usar pontos de extremidade privados?<br>
a. Não seguir as etapas no artigo e continuar a proteger os itens pode levar o cofre a não conseguir usar pontos de extremidade privados. Portanto, é recomendável que você consulte esta lista de verificação antes de continuar a proteger os itens.

Q. Posso usar meu próprio servidor DNS em vez de usar a zona DNS privada do Azure ou uma zona DNS privada integrada?<br>
a. Sim, você pode usar seus próprios servidores DNS. No entanto, verifique se todos os registros DNS necessários foram adicionados conforme sugerido nesta seção.

Q. É necessário executar qualquer etapa adicional no meu servidor depois de seguir o processo neste artigo?<br>
a. Depois de seguir o processo detalhado neste artigo, você não precisa fazer mais trabalho para usar pontos de extremidade privados para backup e restauração.

## <a name="next-steps"></a>Próximas etapas

- Leia sobre todos os [recursos de segurança no backup do Azure](security-overview.md)
