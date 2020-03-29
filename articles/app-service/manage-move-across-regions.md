---
title: Mova um aplicativo para outra região
description: Aprenda a mover recursos do App Service de uma região para outra.
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 7e68f12ce062831ad361c88345188aca61922c4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77925703"
---
# <a name="move-an-app-service-app-to-another-region"></a>Mova um aplicativo de serviço de aplicativo para outra região

Este artigo descreve como mover os recursos do App Service para uma região diferente do Azure. Você pode transferir seus recursos para outra região por uma série de razões. Por exemplo, aproveitar uma nova região do Azure, implantar recursos ou serviços disponíveis apenas em regiões específicas, atender aos requisitos internos de política e governança ou em resposta aos requisitos de planejamento de capacidade.

Os recursos do App Service são específicos da região e não podem ser movidos entre regiões. Você deve criar uma cópia dos recursos existentes do App Service na região de destino, mova seu conteúdo para o novo aplicativo. Se o aplicativo de origem usar um domínio personalizado, você pode [migrá-lo para o novo aplicativo na região de destino](manage-custom-dns-migrate-domain.md) quando estiver concluído.

Para facilitar a cópia do seu aplicativo, você pode [clonar um aplicativo individual do App Service](app-service-web-app-cloning.md) em um plano de Serviço de Aplicativo em outra região, mas ele tem [limitações](app-service-web-app-cloning.md#current-restrictions), especialmente que ele não suporta aplicativos Linux.

## <a name="prerequisites"></a>Pré-requisitos

- Certifique-se de que o aplicativo App Service está na região do Azure da qual você deseja se mover.
- Certifique-se de que a região de destino suporta o App Service e qualquer serviço relacionado, cujos recursos você deseja mover.
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>Preparar

Identifique todos os recursos do App Service que você está usando no momento. Por exemplo: 

- Aplicativos do Serviço de Aplicativo
- [Planos de serviço de aplicativos](overview-hosting-plans.md)
- [Slots de implantação](deploy-staging-slots.md)
- [Domínios personalizados comprados no Azure](manage-custom-dns-buy-domain.md)
- [Certificados SSL](configure-ssl-certificate.md)
- [Integração da Rede Virtual Do Azure](web-sites-integrate-with-vnet.md)
- [Conexões híbridas](app-service-hybrid-connections.md).
- [Identidades gerenciadas](overview-managed-identity.md)
- [Configurações de backup](manage-backup.md)

Certos recursos, como certificados importados ou conexões híbridas, contêm integração com outros serviços do Azure. Para obter informações sobre como movimentar esses recursos em todas as regiões, consulte a documentação dos respectivos serviços.

## <a name="move"></a>Mover

1. [Crie um backup do aplicativo de origem](manage-backup.md).
1. [Crie um aplicativo em um novo plano de Serviço de Aplicativo, na região alvo.](app-service-plan-manage.md#create-an-app-service-plan)
2. [Restaurar o backup no aplicativo de destino](web-sites-restore.md)
2. Se você usar um domínio personalizado, [vincule-o preventivamente ao aplicativo de destino](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively) e `awverify.` [habilite o domínio no aplicativo de destino](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app).
3. Configure tudo o resto em seu aplicativo de destino para ser o mesmo que o aplicativo de origem e verifique sua configuração.
4. Quando estiver pronto para o domínio personalizado para apontar para o aplicativo de destino, [remapeie o nome de domínio](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name).

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>Limpar os recursos de origem

Exclua o aplicativo de origem e o plano de serviço de aplicativo. [Um plano de Serviço de Aplicativo no nível não gratuito carrega uma taxa, mesmo que nenhum aplicativo esteja sendo executado nele.](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>Próximas etapas

[Clonagem de aplicativo do Serviço de Aplicativo do Azure usando o Azure PowerShell](app-service-web-app-cloning.md)