---
title: Registrar e verificar um locatário Power BI (versão prévia)
description: Saiba como usar o portal de alcance do Azure para registrar e verificar um locatário Power BI.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: ce23119e568347fff2ad270cfea7f8b563cae529
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904985"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Registrar e verificar um locatário Power BI (versão prévia)

Este artigo mostra como usar o portal alcance do Azure para registrar e verificar um locatário Power BI.

> [!Note]
> Se a instância alcance e o locatário Power BI estiverem no mesmo locatário do Azure, você só poderá usar a autenticação de identidade gerenciada (MSI) para configurar uma verificação de um locatário Power BI. Se a instância alcance e o locatário Power BI estiverem em locatários diferentes do Azure, você deverá autenticar com a autenticação delegada e deverá usar o PowerShell para configurar suas verificações. Consulte [usar o PowerShell para registrar e verificar Power bi](powershell-register-scan-power-bi.md).

## <a name="create-a-security-group-for-permissions"></a>Criar um grupo de segurança para permissões

Para configurar a autenticação, crie um grupo de segurança e adicione a identidade gerenciada do catálogo a ele.

1. Na [portal do Azure](https://portal.azure.com), procure **Azure Active Directory**.
1. Crie um novo grupo de segurança em seu Azure Active Directory, seguindo [criar um grupo básico e adicionar membros usando Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Você pode ignorar esta etapa se já tiver um grupo de segurança que deseja usar.

1. Selecione **segurança** como o **tipo de grupo**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Tipo de grupo de segurança":::

1. Adicione a identidade gerenciada do catálogo a esse grupo de segurança. Selecione **Membros** e, em seguida, selecione **+ adicionar membros**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Adicione a instância gerenciada do catálogo ao grupo.":::

1. Pesquise seu catálogo e selecione-o.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Adicionar Catálogo pesquisando-o":::

    Você deverá ver uma notificação de êxito mostrando que ela foi adicionada.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Êxito ao adicionar catálogo MSI":::

## <a name="associate-the-security-group-with-the-tenant"></a>Associar o grupo de segurança ao locatário

1. Faça logon no [portal de administração do Power bi](https://app.powerbi.com/admin-portal/tenantSettings).

    > [!Important]
    > Você precisa ser um administrador de Power BI para ver a página de configurações de locatário.

1. Selecione **as configurações do desenvolvedor**  >  **permitir que as entidades de serviço usem somente leitura Power bi APIs de administração (versão prévia)**.
1. Selecione **grupos de segurança específicos**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Imagem mostrando como permitir que as entidades de serviço obtenham somente leitura Power BI permissões de API de administração":::

    > [!Caution]
    > Quando você permite que o grupo de segurança criado (que tem sua identidade gerenciada pelo catálogo de dados como um membro) Use somente leitura Power BI APIs de administração, você também permite que ele acesse os metadados (por exemplo, nomes de painel e relatório, proprietários, descrições, etc.) para todos os seus artefatos de Power BI nesse locatário. Depois que os metadados tiverem sido extraídos para as alcance do Azure, permissões do alcance, não Power BI permissões, determine quem pode ver os metadados.

    > [!Note]
    > Você pode remover o grupo de segurança das configurações do desenvolvedor, mas os metadados extraídos anteriormente não serão removidos da conta do alcance. Você pode excluí-lo separadamente, se desejar.

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Registrar seu Power BI e configurar uma verificação

Agora que você recebeu as permissões de catálogo para se conectar à API de administração do seu locatário do Power BI, você pode configurar sua verificação no portal do catálogo.

Primeiro, adicione um sinalizador de recurso especial à URL do alcance 

1. Adicione a seguinte cadeia de caracteres ao final do URI da sua instância do alcance: `?feature.ext.catalog={"pbi":"true"}` . Isso habilita a opção de registro Power BI em seu catálogo.

1. Selecione o ícone **centro de gerenciamento** .

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="Ícone do centro de gerenciamento.":::

1. Em seguida, selecione **+ novo** em **fontes de dados**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="Imagem do botão nova fonte de dados":::

    Selecione **Power bi** como sua fonte de dados.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="Imagem mostrando a lista de fontes de dados disponíveis para escolher":::

1. Dê um nome amigável à instância de Power BI.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Imagem mostrando Power BI nome amigável da fonte de dados":::

    O nome deve ter entre 3-63 e caracteres de comprimento e deve conter apenas letras, números, sublinhados e hifens.  Não são permitidos espaços.

    Por padrão, o sistema encontrará o locatário Power BI que existe na mesma assinatura do Azure.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="Power BI fonte de dados registrada":::

1. Dê um nome à sua verificação. Observe que o único método de autenticação com suporte é de **identidade gerenciada**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Imagem mostrando a configuração da verificação de Power BI":::

    O nome da verificação deve ter entre 3-63 e caracteres de comprimento e deve conter apenas letras, números, sublinhados e hifens.  Não são permitidos espaços.

1. Configurar um gatilho de verificação. Suas opções são **uma vez**, a **cada 7 dias** e a **cada 30 dias**.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Imagem do gatilho de verificação":::

1. Em **examinar nova verificação**, selecione **salvar e executar** para iniciar a verificação.

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Salvar e executar Power BI imagem da tela":::

## <a name="next-steps"></a>Próximas etapas

Para saber como usar os cmdlets do PowerShell para registrar e verificar um locatário Power BI, consulte:
  
- [Usar o PowerShell para registrar e verificar Power BI](powershell-register-scan-power-bi.md)
