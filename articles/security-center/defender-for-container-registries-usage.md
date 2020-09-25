---
title: Como usar o Azure defender para registros de contêiner
description: Saiba mais sobre como usar o Azure defender para registros de contêiner para verificar imagens em seus registros
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 04a7b7f40e1591a919668e940a883dde9a6f4689
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91302002"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>Usar o Azure defender para registros de contêiner para verificar se há vulnerabilidades em suas imagens

Esta página explica como usar o verificador de vulnerabilidades interno para verificar as imagens de contêiner armazenadas em seu registro de contêiner do Azure baseado em Azure Resource Manager.

Quando o **Azure defender para registros de contêiner** estiver habilitado, qualquer imagem que você enviar por push para o registro será verificada imediatamente. Além disso, qualquer imagem extraída nos últimos 30 dias também é verificada. 

Quando o verificador relata vulnerabilidades à central de segurança, a central de segurança apresenta as descobertas e as informações relacionadas como recomendações. Além disso, as descobertas incluem informações relacionadas, como etapas de correção, CVEs relevantes, pontuações de CVSS e muito mais. Você pode exibir as vulnerabilidades identificadas para uma ou mais assinaturas ou para um registro específico.

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|GA (em disponibilidade geral)|
|Refere|O **Azure defender para registros de contêiner** é cobrado conforme mostrado na [página de preços](security-center-pricing.md)|
|Registros e imagens com suporte:|![Sim ](./media/icons/yes-icon.png) registros de ACR hospedados pelo Linux que são acessíveis pela Internet pública e fornecem acesso ao shell.<br>![Não há ](./media/icons/no-icon.png) registros de ACR hospedados pelo Windows.<br>![Nenhum ](./media/icons/no-icon.png) registro ' particular '-a central de segurança exige que seus registros sejam acessíveis pela Internet pública. A central de segurança não pode se conectar ou verificar registros com acesso limitado com um firewall, um ponto de extremidade de serviço ou pontos de extremidades privados, como o link privado do Azure.<br>![Não há imagens de super nada, como imagens de ](./media/icons/no-icon.png) [rascunho do Docker](https://hub.docker.com/_/scratch/) ou imagens "Distroless" que contenham apenas um aplicativo e suas dependências de tempo de execução sem um Gerenciador de pacotes, Shell ou sistema operacional.|
|Funções e permissões necessárias:|**Leitor de segurança** e [função de leitor do registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Identificar vulnerabilidades em imagens em registros de contêiner do Azure 

1. Para habilitar verificações de vulnerabilidade de imagens armazenadas em seu registro de contêiner do Azure baseado em Azure Resource Manager:

    1. Habilite o **Azure defender para registros de contêiner** para sua assinatura.

        A central de segurança agora está pronta para verificar as imagens em seus registros.

        >[!NOTE]
        > Esse recurso é cobrado por imagem.

1. As verificações de imagem são disparadas em cada Push ou importação e, se a imagem tiver sido retirada nos últimos 30 dias. 

    Quando a verificação é concluída (normalmente após aproximadamente 2 minutos, mas pode ter até 15 minutos), as descobertas estão disponíveis como recomendações da central de segurança.

1. [Exiba e corrija as descobertas conforme explicado abaixo](#view-and-remediate-findings).

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>Identificar vulnerabilidades em imagens em outros registros de contêiner 

1. Use as ferramentas ACR para trazer imagens para o registro do Hub do Docker ou do registro de contêiner da Microsoft.  Quando a importação for concluída, as imagens importadas serão verificadas pelo Azure defender. 

    Saiba mais em [importar imagens de contêiner para um registro de contêiner](../container-registry/container-registry-import-images.md)

    Quando a verificação é concluída (normalmente após aproximadamente 2 minutos, mas pode ter até 15 minutos), as descobertas estão disponíveis como recomendações da central de segurança.

1. [Exiba e corrija as descobertas conforme explicado abaixo](#view-and-remediate-findings).


## <a name="view-and-remediate-findings"></a>Exibir e corrigir as descobertas

1. Para exibir as descobertas, vá para a página **recomendações** . Se forem encontrados problemas, você verá as **vulnerabilidades de recomendação nas imagens do registro de contêiner do Azure que devem ser corrigidas**

    ![Recomendação para corrigir problemas ](media/monitor-container-security/acr-finding.png)

1. Selecione a recomendação. 

    A página detalhes da recomendação é aberta com informações adicionais. Essas informações incluem a lista de registros com imagens vulneráveis ("recursos afetados") e as etapas de correção. 

1. Selecione um registro específico para ver os repositórios dentro dele que têm repositórios vulneráveis.

    ![Selecionar um registro](media/monitor-container-security/acr-finding-select-registry.png)

    A página detalhes do registro é aberta com a lista de repositórios afetados.

1. Selecione um repositório específico para ver os repositórios dentro dele que têm imagens vulneráveis.

    ![Selecionar um repositório](media/monitor-container-security/acr-finding-select-repository.png)

    A página detalhes do repositório é aberta. Ele lista as imagens vulneráveis junto com uma avaliação da gravidade das descobertas.

1. Selecione uma imagem específica para ver as vulnerabilidades.

    ![Selecionar imagens](media/monitor-container-security/acr-finding-select-image.png)

    A lista de conclusões para a imagem selecionada é aberta.

    ![Lista de conclusões](media/monitor-container-security/acr-findings.png)

1. Para saber mais sobre uma localização, selecione a localização. 

    O painel detalhes de conclusões é aberto.

    [![Painel de detalhes de conclusões](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    Esse painel inclui uma descrição detalhada do problema e links para recursos externos para ajudar a mitigar as ameaças.

1. Siga as etapas na seção correção deste painel.

1. Quando você executou as etapas necessárias para corrigir o problema de segurança, substitua a imagem no registro:

    1. Envie por push a imagem atualizada. Isso irá disparar uma verificação. 
    
    1. Marque a página recomendações para a recomendação "vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas". 
    
        Se a recomendação ainda aparecer e a imagem que você tratou ainda aparecer na lista de imagens vulneráveis, verifique as etapas de correção novamente.

    1. Quando tiver certeza de que a imagem atualizada foi enviada por push, verificada e não está mais aparecendo na recomendação, exclua a imagem "antiga" vulnerável do registro.


## <a name="disable-specific-findings-preview"></a>Desabilitar descobertas específicas (versão prévia)

Se você tiver uma necessidade organizacional para ignorar uma localização, em vez de corrigi-la, você pode opcionalmente desabilitá-la. As conclusões desabilitadas não afetam sua pontuação segura ou geram ruído indesejado.

Quando uma localização corresponde aos critérios que você definiu em suas regras de desabilitação, ela não aparecerá na lista de conclusões. Os cenários típicos incluem:

- Desabilitar conclusões com severidade abaixo da média
- Desabilitar conclusões que não são patches
- Desabilitar conclusões com Pontuação CVSS abaixo de 6,5
- Desabilitar conclusões com texto específico na verificação ou categoria de segurança (por exemplo, "RedHat", "atualização de segurança do CentOS para sudo")

> [!IMPORTANT]
> Para criar uma regra, você precisa de permissões para editar uma política no Azure Policy.
>
> Saiba mais em [permissões de RBAC no Azure Policy](../governance/policy/overview.md#rbac-permissions-in-azure-policy).

Você pode usar qualquer um dos seguintes critérios: 

- Localizando ID 
- Categoria
- Verificação de segurança 
- Pontuações do CVSS v3
- Gravidade 
- Status de patch 

Para criar uma regra:

1. Na página de detalhes de recomendações para **vulnerabilidades nas imagens do registro de contêiner do Azure devem ser corrigidas**, selecione **desabilitar regra**.
1. Selecione o escopo relevante.
1. Defina seus critérios.
1. Selecione **aplicar regra**.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="Criar uma regra de desabilitação para descobertas de VA no registro":::

1. Para exibir, substituir ou excluir uma regra: 
    1. Selecione **desabilitar regra**.
    1. Na lista escopo, as assinaturas com regras ativas são mostradas como **regra aplicada**.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="Modificar ou excluir uma regra existente":::
    1. Para exibir ou excluir a regra, selecione o menu de reticências ("...").


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Azure defender](azure-defender.md)