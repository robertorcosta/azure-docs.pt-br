---
title: Conectar sua conta do GCP à central de segurança do Azure
description: Monitorando seus recursos do GCP na central de segurança do Azure
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c4020bc5e96ff35fa2d04a872216e43abbba5323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91328302"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Conectar suas contas do GCP à central de segurança do Azure

Com as cargas de trabalho de nuvem normalmente abrangendo várias plataformas de nuvem, os serviços de segurança de nuvem devem fazer o mesmo.

A central de segurança do Azure protege as cargas de trabalho no Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Integrar sua conta do GCP à central de segurança, integra o comando GCP Security e a central de segurança do Azure. Assim, a central de segurança fornece visibilidade e proteção em ambos os ambientes de nuvem para fornecer:

- Detecção de configurações incorretas de segurança
- Uma única exibição mostrando as recomendações da central de segurança e as descobertas do centro de comando de segurança GCP
- Incorporação dos recursos do GCP aos cálculos de Pontuação segura da central de segurança
- Integração das recomendações do centro de comando de segurança do GCP com base no padrão do CIS no painel de conformidade regulatória da central de segurança

Na captura de tela abaixo, você pode ver os projetos do GCP exibidos no painel Visão geral da central de segurança.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 projetos GCP listados no painel Visão geral da central de segurança" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia|
|Refere|Requer o [Azure defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Proprietário** ou **colaborador** na assinatura relevante do Azure|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||

## <a name="connect-your-gcp-account"></a>Conectar-se à sua conta da GCP

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Etapa 1. Configurar o centro de comando de segurança do GCP com a análise de integridade de segurança

Para todos os projetos GCP em sua organização, você também deve:

1. Configure a **central de comandos de segurança do GCP** usando [estas instruções da documentação do GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Habilite a **análise de integridade de segurança** usando [estas instruções da documentação do GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Verifique se há dados que fluem para o centro de comando de segurança.

As instruções para conectar seu ambiente GCP para configuração de segurança seguem as recomendações do Google para o consumo de recomendações de configuração de segurança. A integração aproveita o centro de comando de segurança do Google e consumirá recursos adicionais que podem afetar sua cobrança.

Quando você habilita a análise de integridade de segurança pela primeira vez, pode levar várias horas para que os dados estejam disponíveis.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Etapa 2. Habilitar a API do centro de comando de segurança do GCP

1. Na biblioteca de **API do console de nuvem**do Google, selecione o projeto que você deseja conectar à central de segurança do Azure.
1. Na biblioteca de API, localize e selecione a **API do centro de comando de segurança**.
1. Na página da API, selecione **habilitar**.

Saiba mais sobre a [API do centro de comando de segurança](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Etapa 3. Criar uma conta de serviço dedicada para a integração de configuração de segurança

1. No **console do GCP**, selecione o projeto que você deseja conectar à central de segurança.
1. No **menu de navegação**, em **iam &** opções de administração, selecione **contas de serviço**.
1. Selecione **criar conta de serviço**.
1. Insira um nome de conta e selecione **criar**.
1. Especifique a **função** como **Visualizador de administração da central de segurança**e selecione **continuar**.
1. A seção **conceder aos usuários acesso a esta conta de serviço** é opcional. Selecione **Concluído**.
1. Copie o **valor de email** da conta de serviço criada e salve-o para uso posterior.
1. No **menu de navegação**, em **iam &** opções de administração, selecione **iam**
    1. Mudar para o nível da organização.
    1. Selecione **Adicionar**.
    1. No campo **novos membros** , Cole o **valor de email** copiado anteriormente.
    1. Especifique a função como **Visualizador de administração da central de segurança** e, em seguida, selecione salvar.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Configurando as permissões GCP relevantes":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Etapa 4. Criar uma chave privada para a conta de serviço dedicada
1. Mudar para o nível de projeto.
1. No **menu de navegação**, em **iam &** opções de administração, selecione **contas de serviço**.
1. Abra a conta de serviço dedicada e selecione Editar.
1. Na seção **chaves** , selecione **Adicionar chave** e, em seguida, **criar nova chave**.
1. Na tela Criar chave privada, selecione **JSON**e, em seguida, selecione **criar**.
1. Salve esse arquivo JSON para uso posterior.


### <a name="step-5-connect-gcp-to-security-center"></a>Etapa 5. Conectar o GCP à central de segurança 
1. No menu da central de segurança, selecione **conectores de nuvem**.
1. Selecione Adicionar conta do GCP.
1. Na página integração, faça o seguinte e, em seguida, selecione **Avançar**.
    1. Valide a assinatura escolhida.
    1. No campo **nome de exibição** , insira um nome de exibição para o conector.
    1. No campo **ID da organização** , insira a ID da sua organização. Se você não souber, consulte [criando e gerenciando organizações](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. Na caixa arquivo de **chave privada** , navegue até o arquivo JSON que você baixou na [etapa 4. Crie uma chave privada para a conta de serviço dedicada](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Etapa 6. Confirmação

Quando o conector for criado com êxito e a central de comandos de segurança do GCP tiver sido configurada corretamente:

- O padrão de CIS GCP será mostrado no painel de conformidade regulatória da central de segurança.
- As recomendações de segurança para seus recursos do GCP serão exibidas no portal da central de segurança e no painel de conformidade regulatória 5-10 minutos após a conclusão da integração:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="recursos e recomendações do GCP na página de recomendações da central de segurança":::


## <a name="monitoring-your-gcp-resources"></a>Monitorando seus recursos do GCP

Como mostrado acima, a página recomendações de segurança da central de segurança do Azure exibe os recursos do GCP junto com os recursos do Azure e do AWS para uma exibição de várias nuvens verdadeira.

Para exibir todas as recomendações ativas para seus recursos por tipo de recurso, use a página de inventário de ativos da central de segurança e filtre para o tipo de recurso GCP no qual você está interessado:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="Filtro de tipo de recurso da página de inventário de ativos mostrando as opções de GCP"::: 


## <a name="next-steps"></a>Próximas etapas

Conectar sua conta do GCP faz parte da experiência de várias nuvens disponível na central de segurança do Azure. Para obter informações relacionadas, consulte a seguinte página:

- [Conectar suas contas do AWS à central de segurança do Azure](quickstart-onboard-aws.md)
