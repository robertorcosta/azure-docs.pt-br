---
title: Conectar sua conta do AWS à central de segurança do Azure
description: Monitorando seus recursos do AWS na central de segurança do Azure
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 33ff6748eba9d0349df244e70f07ad178f258373
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250277"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Conectar suas contas do AWS à central de segurança do Azure

Com as cargas de trabalho de nuvem normalmente abrangendo várias plataformas de nuvem, os serviços de segurança de nuvem devem fazer o mesmo.

A central de segurança do Azure protege as cargas de trabalho no Azure, Amazon Web Services (AWS) e Google Cloud Platform (GCP).

Integrar sua conta do AWS à central de segurança, integra o Hub de segurança do AWS e a central de segurança do Azure. Assim, a central de segurança fornece visibilidade e proteção em ambos os ambientes de nuvem para fornecer:

- Provisionamento automático de agente (a central de segurança usa o [arco do Azure](../azure-arc/servers/overview.md) para implantar o agente de log Analytics em suas instâncias de AWS)
- Gerenciamento de política
- Gerenciamento de vulnerabilidades
- Detecção e resposta de ponto de extremidade inserido (EDR)
- Detecção de configurações incorretas de segurança
- Uma única exibição mostrando as recomendações da central de segurança e as conclusões do hub de segurança AWS
- Incorporação dos recursos do AWS aos cálculos de Pontuação segura da central de segurança
- Avaliações de conformidade regulatória de seus recursos do AWS

Na captura de tela abaixo, você pode ver as contas do AWS exibidas no painel Visão geral da central de segurança.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="3 projetos GCP listados no painel Visão geral da central de segurança" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Disponibilidade

|Aspecto|Detalhes|
|----|:----|
|Estado da versão:|Versão Prévia|
|Refere|Requer o [Azure defender para servidores](defender-for-servers-introduction.md)|
|Funções e permissões necessárias:|**Proprietário** ou **colaborador** na assinatura relevante do Azure|
|Nuvens:|![Sim](./media/icons/yes-icon.png) Nuvens comerciais<br>![Não](./media/icons/no-icon.png) National/soberanas (US Gov, China gov, outros gov)|
|||



## <a name="connect-your-aws-account"></a>Conectar-se à sua conta da AWS

### <a name="step-1-set-up-aws-security-hub"></a>Etapa 1. Configurar o Hub de segurança do AWS:

1. Para exibir as recomendações de segurança para várias regiões, repita as etapas a seguir para cada região relevante.

    > [!IMPORTANT]
    > Se você estiver usando uma conta mestre do AWS, repita as três etapas a seguir para configurar a conta mestre e todas as contas de membro conectadas em todas as regiões relevantes

    1. Habilite a [configuração do AWS](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Habilite o [Hub de segurança do AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Verifique se há dados que fluem para o Hub de segurança.

        Quando você habilita o Hub de segurança pela primeira vez, pode levar várias horas para que os dados estejam disponíveis.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Etapa 2. Configurar a autenticação para a central de segurança no AWS

Há duas maneiras de permitir que a central de segurança autentique no AWS:

- **Criar uma função iam para a central de segurança** -esse é o método mais seguro e é recomendado
- **Usuário do AWS para a central de segurança** -uma opção menos segura se você não tiver iam habilitado

#### <a name="create-an-iam-role-for-security-center"></a>Criar uma função IAM para a central de segurança
1. No console do Amazon Web Services, em **segurança, identidade & conformidade**, selecione **iam**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Serviços AWSs":::

1. Selecione **funções** e **criar função**.
1. Selecione **outra conta do AWS**.
1. Insira os seguintes detalhes:

    - **ID da conta** – Insira a ID da conta da Microsoft (**158177204117**), conforme mostrado na página conector do AWS na central de segurança.
    - **Exigir ID externa** -deve ser selecionado
    - **ID externa** – Insira a ID da assinatura, conforme mostrado na página conector do AWS na central de segurança 

1. Selecione **Avançar**.
1. Na seção **anexar políticas de permissão** , selecione as seguintes políticas:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Opcionalmente, adicione marcas. A adição de marcas ao usuário não afeta a conexão.
1. Selecione **Avançar**.

1. Na lista funções, escolha a função que você criou

1. Salve o ARN (Amazon Resource Name) para mais tarde. 

#### <a name="create-an-aws-user-for-security-center"></a>Criar um usuário do AWS para a central de segurança 
1. Abra a guia **usuários** e selecione **Adicionar usuário**.
1. Na etapa **detalhes** , insira um nome de usuário para a central de segurança e certifique-se de selecionar **acesso programático** para o tipo de acesso AWS. 
1. Selecione **Próximas Permissões**.
1. Selecione **anexar políticas existentes diretamente** e aplicar as seguintes políticas:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Selecione **Avançar: Marcas**. Opcionalmente, adicione marcas. A adição de marcas ao usuário não afeta a conexão.
1. Selecione **revisão**.
1. Salve a ID de **chave de acesso** e o arquivo CSV de **chave de acesso de segredo** gerados automaticamente para mais tarde.
1. Examine o resumo e clique em **criar usuário**.


### <a name="step-3-configure-the-ssm-agent"></a>Etapa 3. Configurar o agente SSM

O AWS Systems Manager é necessário para automatizar tarefas em seus recursos do AWS. Se suas instâncias do EC2 não tiverem o agente SSM, siga as instruções relevantes da Amazon:

- [Instalando e Configurando o agente do SSM em instâncias do Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Instalando e Configurando o agente SSM em instâncias do Amazon EC2 Linux](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-create-a-service-principal-for-onboarding-at-scale"></a>Etapa 4. Criar uma Entidade de Serviço para integração em escala

Como **proprietário** da assinatura que você deseja usar para a integração, crie uma entidade de serviço para integração de arco do Azure, conforme descrito em [criar uma entidade de serviço para integração em escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)


### <a name="step-5-connect-aws-to-security-center"></a>Etapa 5. Conectar o AWS à central de segurança

1. No menu da central de segurança, selecione **vários conectores de nuvem**.
1. Selecione **adicionar conta do AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Botão Adicionar conta do AWS na página de conectores de várias nuvens da central de segurança":::
1. Configure as opções na guia **autenticação AWS** :
    1. Insira um **nome de exibição** para o conector.
    1. Confirme se a assinatura está correta. É a assinatura que incluirá as recomendações do hub de segurança do AWS e do conector.
    1. Dependendo da opção de autenticação escolhida na [etapa 2. Configurar a autenticação para a central de segurança no AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Selecione  **assumir função** e cole o ARN de [criar uma função iam para a central de segurança](#create-an-iam-role-for-security-center) :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="colando o arquivo ARN no campo relevante do assistente de conexão AWS no portal do Azure":::

            OU

        - Selecione **credenciais** e cole a chave de **acesso** e a **chave secreta** do arquivo. csv que você salvou em [criar um usuário AWS para a central de segurança](#create-an-aws-user-for-security-center).
1. Selecione **Avançar**.
1. Configure as opções na guia **configuração de arco do Azure** :

    A central de segurança descobre as instâncias de EC2 na conta AWS conectada e usa o SSM para integrá-las ao arco do Azure. 

    > [!TIP]
    > A lista de sistemas operacionais com suporte está nas perguntas frequentes abaixo.

    1. Selecione o **grupo de recursos** e a **região do Azure** que o AWS EC2s descoberto será integrado à assinatura selecionada.
    1. Insira a **ID da entidade** de serviço e o **segredo do cliente da entidade de serviço** para o arco do Azure, conforme descrito aqui [criar uma entidade de serviço para integração em escala](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. Se o computador estiver se conectando à Internet por meio de um servidor proxy, especifique o endereço IP do servidor proxy ou o nome e o número da porta que o computador usa para se comunicar com o servidor proxy. Insira o valor no formato ```http://<proxyURL>:<proxyport>```
    1. Selecione **Examinar + criar**.

        Examinar as informações de resumo

        As seções de marcas listarão todas as marcas do Azure que serão criadas automaticamente para cada EC2 integrada com seus próprios detalhes relevantes para reconhecê-la facilmente no Azure. 

        Saiba mais sobre as marcas do Azure em [marcas de uso para organizar os recursos e a hierarquia de gerenciamento do Azure](../azure-resource-manager/management/tag-resources.md).

### <a name="step-7-confirmation"></a>Etapa 7. Confirmação

Quando o conector for criado com êxito e o Hub de segurança do AWS tiver sido configurado corretamente:

- A central de segurança verifica o ambiente em busca de instâncias do AWS EC2, integrando-as ao Azure Arc, habilitando a instalação do agente de Log Analytics e fornecendo recomendações de segurança e proteção contra ameaças. 
- O serviço ASC procura novas instâncias AWS EC2 a cada 6 horas e as integra de acordo com a configuração.
- O padrão de CIS AWS será mostrado no painel de conformidade regulatória da central de segurança.
- Se a política do hub de segurança estiver habilitada, as recomendações serão exibidas no portal da central de segurança e no painel de conformidade regulatória 5-10 minutos após a conclusão da integração.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Recursos e recomendações do AWS na página de recomendações da central de segurança":::



## <a name="monitoring-your-aws-resources"></a>Monitorando seus recursos do AWS

Como mostrado acima, a página recomendações de segurança da central de segurança do Azure exibe os recursos do AWS junto com os recursos do Azure e do GCP para uma exibição de várias nuvens verdadeira.

Para exibir todas as recomendações ativas para seus recursos por tipo de recurso, use a página de inventário de ativos da central de segurança e filtre para o tipo de recurso AWS no qual você está interessado:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtro de tipo de recurso da página de inventário de ativos mostrando as opções de AWS"::: 


## <a name="aws-in-security-center-faq"></a>Perguntas frequentes sobre AWS na central de segurança

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Quais sistemas operacionais para minhas instâncias do EC2 têm suporte?

Sistema operacional com suporte para integração automática ao arco do Azure para computadores AWS

- Ubuntu 16, 4 – o agente SSM é pré-instalado, por padrão
- Ubuntu 18, 4 – o agente SSM é pré-instalado, por padrão
- Windows Server – o agente SSM é pré-instalado, por padrão
- CentOS Linux 7 – o SSM deve ser instalado manualmente ou integrado separadamente
- SUSE Linux Enterprise Server (SLES) 15 (x64)-o SSM deve ser instalado manualmente ou integrado separadamente
- Red Hat Enterprise Linux (RHEL) 7 (x64)-o SSM deve ser instalado manualmente ou integrado separadamente


## <a name="next-steps"></a>Próximas etapas

Conectar sua conta do AWS faz parte da experiência de várias nuvens disponível na central de segurança do Azure. Para obter informações relacionadas, consulte a seguinte página:

- [Conectar suas contas do GCP à central de segurança do Azure](quickstart-onboard-gcp.md)
