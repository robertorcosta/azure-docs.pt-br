---
title: Implantar a visualização da bancada de trabalho blockchain do Azure
description: Como implantar a visualização da bancada de trabalho do Azure Blockchain
ms.date: 01/08/2020
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 141bb8825e47eb2309f9f551990a2976e8f4e209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943195"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Implantar a visualização da bancada de trabalho blockchain do Azure

A Visualização da bancada de trabalho do Azure Blockchain é implantada usando um modelo de solução no Azure Marketplace. O modelo simplifica a implantação de componentes necessários para criar aplicativos de blockchain. Uma vez implantado, o Blockchain Workbench fornece acesso aos aplicativos de cliente para criar e gerenciar usuários e aplicativos de blockchain.

Para obter mais informações sobre os componentes do Blockchain Workbench, consulte [Arquitetura do Azure Blockchain Workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Preparar para a implantação

O Workbench Blockchain permite que você implante uma razão blockchain juntamente com um conjunto de serviços do Microsoft Azure relevante geralmente usado para criar um aplicativo baseado em blockchain. Implantar o Blockchain Workbench resulta nos seguintes serviços do Azure que estão sendo provisionados dentro de um grupo de recursos em sua assinatura do Azure.

* Plano de Serviço de Aplicativos (Padrão)
* Application Insights
* Grade de Eventos
* Cofre de Chave do Azure
* Barramento de Serviço
* Banco de dados SQL (Standard S0) + SQL Logical Server
* Conta de armazenamento azure (LRS padrão)
* Conjunto de escala de máquina virtual com capacidade de 1
* Grupo de recursos da Rede Virtual (com Balanceador de carga, Grupo de Segurança de Rede, Endereço IP Público, Rede Virtual)
* Serviço Blockchain Azure. Se você estiver usando uma implantação anterior do Blockchain Workbench, considere reimplantar o Azure Blockchain Workbench para usar o Azure Blockchain Service.

A seguir está um exemplo de implantação criado no grupo de recursos do **myblockchain**.

![Exemplo de implantação](media/deploy/example-deployment.png)

O custo do Blockchain Workbench é uma agregação do custo de serviços subjacentes do Azure. Informações sobre preços para serviços do Azure podem ser calculadas usando a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Pré-requisitos

O Azure Blockchain Workbench requer a configuração do Azure AD e os registros de aplicativos. É possível fazer as [configurações do Azure AD](#azure-ad-configuration) manualmente antes da implantação ou executar uma implantação pós-script. Se estiver reimplementando o Blockchain Workbench, consulte as [configurações do Azure AD](#azure-ad-configuration) para verificar a configuração do Azure AD.

> [!IMPORTANT]
> O Workbench não precisa ser implantado no mesmo locatário que está sendo utilizado para registrar um aplicativo do Azure AD. O Workbench deve ser implantado em um locatário no qual você tenha permissões suficientes para implantar recursos. Para obter mais informações sobre os locatários do Microsoft Azure Active Directory, consulte [Como obter um locatário do Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [Integrando aplicativos com o Microsoft Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Implantar o Blockchain Workbench

Depois de concluir as etapas de pré-requisito, você estará pronto para implantar o Blockchain Workbench. As seções a seguir descrevem como implantar a estrutura.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione sua conta no canto superior direito e alterne para o locatário desejado do Azure AD em que deseja implantar o Azure Blockchain Workbench.
1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **Blockchain** > **Azure Blockchain Workbench (visualização)**.

    ![Criar Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Configuração | Descrição  |
    |---------|--------------|
    | Prefixo de recursos | Identificador exclusivo curto para sua implantação. Esse valor é usado como base para nomear recursos. |
    | Nome de usuário da VM | O nome de usuário é usado como administrador para todas as máquinas virtuais (VM). |
    | Tipo de autenticação | Selecione se deseja usar uma senha ou chave para se conectar a VMs. |
    | Senha | A senha é usada para se conectar a VMs. |
    | SSH | Use uma chave pública RSA no formato de única linha começando com **ssh-rsa** ou use o formato PEM de várias linha. É possível gerar chaves SSH usando `ssh-keygen` no Linux e OS X ou usando PuTTYGen no Windows. Para obter mais informações sobre como usar chaves SSH, veja [Como usar chaves SSH com o Windows no Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Banco de dados e senha Blockchain | Especifique a senha a ser usada para acessar o banco de dados criado como parte da implantação. A senha deve atender a três dos quatro requisitos seguintes: comprimento deve ser entre 12 & 72 caracteres, 1 caractere minúsculo, 1 caractere maiúsculo, 1 número\`e 1 caractere especial que não é sinal de número (#), por cento(%), vírgula(,), estrela(*), citação de volta( , citação dupla("), citação única('), traço(-) e semicoluna(;) |
    | Resultados da implantação | Especifique onde implantar os recursos Blockchain Workbench. Para melhor disponibilidade, isso deve corresponder à configuração do **Local**. |
    | Subscription | Especifique a assinatura do Azure que você deseja usar para sua implantação. |
    | Grupos de recursos | Crie um novo grupo de recursos selecionando **Criar novo** e especifique um nome exclusivo para o grupo de recursos. |
    | Location | Especifique a região em que você deseja implantar a estrutura. |

1. Selecione **OK** para concluir a seção de configuração básica.

1. Em **Configurações Avançadas**, escolha se você quer criar uma nova rede de blockchain ou usar uma rede de blockchain de prova de autoridade existente.

    Para **Criar novo**:

    A nova opção *de criação* implanta um livro-razão do Azure Blockchain Service Quorum com o sku básico padrão.

    ![Configurações avançadas para nova rede de blockchain](media/deploy/advanced-blockchain-settings-new.png)

    | Configuração | Descrição  |
    |---------|--------------|
    | Nível de preços do Azure Blockchain Service | Escolha o nível **básico** ou **padrão** de serviço blockchain do Azure que é usado para o Blockchain Workbench |
    | Configurações do Azure Active Directory | Escolha **Adicionar mais tarde**.</br>Observação: se você escolher [pré-configurar o Azure AD](#azure-ad-configuration) ou estiver reimplantando, escolha *Adicionar Agora*. |
    | Seleção de VM | Selecione o desempenho de armazenamento preferido e o tamanho da VM para sua rede blockchain. Escolha um tamanho menor de VM, como *Standard DS1 v2* se você estiver usando uma assinatura com limites de serviço baixo, como a camada gratuita do Azure. |

    Para **Usar existente**:

    A opção *usar existente* permite que você especifique uma rede de blockchain de PoA (Prova de Autoridade) do Ethereum. Os pontos de extremidade têm os seguintes requisitos.

   * O ponto de extremidade deve ser uma rede de blockchain de PoA (Prova de Autoridade) do Ethereum.
   * O ponto de extremidade deve estar publicamente acessível pela rede.
   * A rede de blockchain PoA deve ser configurada para definir o preço do gás como zero.

     > [!NOTE]
     > As contas do Blockchain Workbench não são financiadas. Se fundos forem necessários, as transações falharão.

     ![Configurações avançadas para rede de blockchain existente](media/deploy/advanced-blockchain-settings-existing.png)

     | Configuração | Descrição  |
     |---------|--------------|
     | Ponto de extremidade RPC do Ethereum | Fornecer o ponto de extremidade RPC de uma rede de blockchain de PoA existente. O ponto de extremidade começa com https:// ou http:// e termina com um número de porta. Por exemplo, `http<s>://<network-url>:<port>` |
     | Configurações do Azure Active Directory | Escolha **Adicionar mais tarde**.</br>Observação: se você escolher [pré-configurar o Azure AD](#azure-ad-configuration) ou estiver reimplantando, escolha *Adicionar Agora*. |
     | Seleção de VM | Selecione o desempenho de armazenamento preferido e o tamanho da VM para sua rede blockchain. Escolha um tamanho menor de VM, como *Standard DS1 v2* se você estiver usando uma assinatura com limites de serviço baixo, como a camada gratuita do Azure. |

1. Selecione **OK** para concluir as configurações avançadas.

1. Revise o resumo para verificar se os parâmetros são precisos.

    ![Resumo](media/deploy/blockchain-workbench-summary.png)

1. Selecione **Criar** para concordar com os termos e implantar o Azure Blockchain Workbench.

A implantação pode levar até 90 minutos. Você pode usar o Portal do Azure para monitorar o progresso. No grupo de recursos criado recentemente, selecione **Implantações > Visão geral** para ver o status dos artefatos implantados.

> [!IMPORTANT]
> Após a implantação, será necessário concluir as configurações do Active Directory. Se você escolheu **Adicionar mais tarde**, será necessário executar o [script de configuração do Azure AD](#azure-ad-configuration-script).  Se você escolheu **Adicionar agora**, deverá [configurar a URL de Resposta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Blockchain Workbench web URL

Concluída a implantação do Blockchain Workbench, um novo grupo de recursos conterá seus recursos do Blockchain Workbench. Os serviços do Blockchain Workbench são acessados por meio de uma URL da Web. As etapas a seguir mostram como recuperar a URL da Web do framework implantado.

1. Faça login no [portal Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, selecione **grupos de recursos**.
1. Escolha o nome do grupo de recursos especificado ao implantar o Blockchain Workbench.
1. Selecione a coluna **TIPO** para classificar a lista alfabeticamente por tipo.
1. Há dois recursos com o tipo **Serviço de Aplicativo**. Selecione o recurso do tipo **Serviço de Aplicativo** *sem* o sufixo "-api".

    ![Lista de Serviços de Aplicativo](media/deploy/resource-group-list.png)

1. Na visão geral do serviço **de aplicativo,** copie o valor da **URL,** que representa a URL da Web para o seu Blockchain Workbench implantado.

    ![Conceitos básicos do Serviço de Aplicativo](media/deploy/app-service.png)

Para associar um nome de domínio personalizado ao Blockchian Workbench, consulte [Configurando um nome de domínio personalizado para um aplicativo Web no Serviço de Aplicativo do Azure usando o Gerenciador de Tráfego](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Script de configuração do Azure AD

O Azure AD deverá ser configurado para concluir a implantação do Blockchain Workbench. Você usará um script do PowerShell para fazer a configuração.

1. Em um navegador, navegue até a [URL da Web do Blockchain Workbench](#blockchain-workbench-web-url).
1. Você verá instruções para configurar o Azure AD usando o Cloud Shell. Copie o comando e inicie o Cloud Shell.

    ![Inicializar o script do AAD](media/deploy/launch-aad-script.png)

1. Escolha o locatário do Azure AD no qual você implantou o Blockchain Workbench.
1. No ambiente Cloud Shell PowerShell, cole e execute o comando.
1. Quando solicitado, insira o locatário do Azure AD que você quer usar para o Blockchain Workbench. Este será o locatário contendo os usuários do Blockchain Workbench.

    > [!IMPORTANT]
    > O usuário autenticado requer permissões para criar registros de aplicativos do Azure AD e conceder permissões de aplicativos delegados no locatário. Talvez seja necessário solicitar a um administrador do locatário que execute o script de configuração do Azure AD ou criar um novo locatário.

    ![Inserir o locatário do Azure AD](media/deploy/choose-tenant.png)

1. Você deverá autenticar-se no locatário do Azure AD usando um navegador. Abra a URL da Web em um navegador, insira o código e faça a autenticação.

    ![Autenticar com código](media/deploy/authenticate.png)

1. O script gera várias mensagens de status. Você receberá uma mensagem de status **ÊXITO**, se o locatário for provisionado com êxito.
1. Navegue para A URL do Blockchain Workbench. Você será solicitado a dar consentimento para conceder permissões de leitura para o diretório. Isso permitirá que os usuários no locatário tenham acesso ao aplicativo Web do Blockchain Workbench. Se você for o administrador do locatário, poderá escolher se dará consentimento para toda a organização. Essa opção aceita consentimento para todos os usuários no locatário. Caso contrário, cada usuário será solicitado a consentir no primeiro uso do aplicativo Web do Blockchain Workbench.
1. Selecione **Aceitar** para conceder.

     ![Consentimento para ler perfis de usuários](media/deploy/graph-permission-consent.png)

1. Após o consentimento, o aplicativo Web do Blockchain Workbench poderá ser utilizado.

Você completou a implantação do Azure Blockchain Workbench. Consulte [os próximos passos](#next-steps) para que as sugestões sejam iniciadas usando sua implantação.

## <a name="azure-ad-configuration"></a>Configuração do Azure AD

Se você optar por configurar ou verificar manualmente as configurações do Azure AD antes da implantação, conclua todas as etapas desta seção. Se preferir definir as configurações do Azure AD automaticamente, use o [script de configuração do Azure AD](#azure-ad-configuration-script) após implantar o Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registro do aplicativo de API do Blockchain Workbench

A implantação do Blockchain Workbench exige o registro de um aplicativo do Microsoft Azure Active Directory. Você precisa de um locatário do Microsoft Azure Active Directory (Azure AD) para registrar o aplicativo. Você pode usar um locatário existente ou criar um novo locatário. Se estiver usando um locatário do Azure AD existente, você precisará de permissões suficientes para registrar aplicativos, conceder permissões de API do Graph e permitir o acesso de convidado em um locatário do Azure AD. Se você não tiver permissões suficientes em um inquilino Azure AD existente, crie um novo inquilino.


1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione sua conta no canto superior direito e alterne para o locatário desejado do Azure AD. O inquilino deve ser o inquilino do administrador de assinatura da assinatura onde o Azure Blockchain Workbench é implantado e você tem permissões suficientes para registrar aplicativos.
1. No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **inscrições de aplicativos** > **Novo registro**.

    ![Registro do aplicativo](media/deploy/app-registration.png)

1. Forneça um **nome** de exibição e escolha **Apenas Contas neste diretório organizacional**.

    ![Criar o Registro do aplicativo](media/deploy/app-registration-create.png)

1. Selecione **Registrar** para registrar o aplicativo Azure AD.

### <a name="modify-manifest"></a>Modificar manifesto

Em seguida, será necessário modificar o manifesto para usar as funções do aplicativo no Azure AD e especificar os administradores do Blockchain Workbench.  Para obter mais informações sobre manifestos de aplicativos, consulte [Manifesto de aplicativo do Microsoft Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).


1. Um GUID é necessário para o manifesto. Você pode gerar um GUID `[guid]::NewGuid()` usando `New-GUID` o comando PowerShell ou cmdlet. Outra opção é usar um site gerador de GUID.
1. Para o aplicativo registrado, **selecione Manifest** na seção **Gerenciar.**
1. Em seguida, atualize a seção **appRoles** do manifesto. Substitua pelo `"appRoles": []` JSON fornecido. Certifique-se de substituir o valor do campo **id** pelo GUID gerado. 

    ![Editar manifesto](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > O valor de **Administrador** é necessário para identificar administradores do Blockchain Workbench.

1. No manifesto, altere também o valor de **Oauth2AllowImplicitFlow** para **verdadeiro**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Selecione **Salvar** para salvar as alterações do manifesto.

### <a name="add-graph-api-required-permissions"></a>Adicionar permissões necessárias de API do Graph

O aplicativo de API precisa solicitar a permissão do usuário para acessar o diretório. Defina a seguinte permissão necessária para o aplicativo de API:

1. No registro do aplicativo *Blockchain API,* selecione **permissões de API**. Por padrão, a permissão Graph API **User.Read** é adicionada.
1. O aplicativo Workbench requer acesso à leitura das informações básicas do perfil dos usuários. Em *Permissões configuradas,* **selecione Adicionar uma permissão**. Nas **APIs da Microsoft,** selecione **Microsoft Graph**.
1. Como o aplicativo Workbench usa as credenciais autenticadas do usuário, **selecione permissões delegadas**.
1. Na categoria *Usuário,* escolha **User.ReadBasic.All** permission.

    ![Configuração de registro do aplicativo Azure AD mostrando a adição do Microsoft Graph User.ReadBasic.Toda a permissão delegada](media/deploy/add-graph-user-permission.png)

    Selecione **Adicionar Permissões**.

1. Em *Permissões configuradas,* selecione **O consentimento de admin de Concessão** para o domínio e selecione **Sim** para o prompt de verificação.

   ![Conceder permissões](media/deploy/client-app-grant-permissions.png)

   A concessão de permissão permite que o Blockchain Workbench acesse os usuários no diretório. A permissão de leitura é necessária para pesquisar e adicionar membros ao Blockchain Workbench.

### <a name="get-application-id"></a>Obter ID do aplicativo

As informações de locatário e ID do aplicativo são necessárias para a implantação. Coletar e armazenar as informações para uso durante a implantação.

1. Para o aplicativo registrado, selecione **Visão Geral**.
1. Copie e armazene o valor **de ID do aplicativo** para uso posterior durante a implantação.

    ![Propriedades do aplicativo de API](media/deploy/app-properties.png)

    | Configurando para armazenar  | Usar na implantação |
    |------------------|-------------------|
    | ID do aplicativo (cliente) | Configuração do Azure Active Directory > ID do aplicativo |

### <a name="get-tenant-domain-name"></a>Obter nome de domínio do locatário

Colete e guarde o nome de domínio do locatário do Active Directory em que os aplicativos são registrados. 

No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **Personalizar nomes de domínio**. Copie e guarde o nome de domínio.

![Nome de domínio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Configurações do usuário convidado

Se houver usuários convidados no locatário do Azure AD, siga as etapas adicionais para garantir que a atribuição e o gerenciamento de usuários do Blockchain Workbench funcionem corretamente.

1. Alterne o locatário do Azure AD e selecione **Azure Active Directory > Configurações do usuário > Gerenciar configurações de colaboração externa**.
1. Defina **Permissões de usuário convidado são limitadas** para **Não**.
    ![Configuração de colaboração externa](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configurando a URL de resposta

Após a implantação do Azure Blockchain Workbench, será necessário configurar a **URL de Resposta** do aplicativo cliente do Azure Active Directory da URL da Web do Blockchain Workbench implantado.

1. Faça login no [portal Azure](https://portal.azure.com).
1. Verifique se que você está no locatário em que registrou o aplicativo cliente do Microsoft Azure Active Directory.
1. No painel de navegação esquerdo, selecione o serviço do **Azure Active Directory**. Selecione **inscrições do Aplicativo**.
1. Selecione o aplicativo cliente do Microsoft Azure Active Directory registrado na seção de pré-requisitos.
1. Selecione **Autenticação**.
1. Especifique a URL principal da implantação do Azure Blockchain Workbench que você recuperou na seção [URL da Web do Blockchain Workbench.](#blockchain-workbench-web-url) A URL de resposta é prefixada com `https://`. Por exemplo, `https://myblockchain2-7v75.azurewebsites.net`

    ![URLs de resposta de autenticação](media/deploy/configure-reply-url.png)

1. Na seção **de configuração Avançada,** verifique **tokens de acesso** e **tokens de ID**.

    ![Configurações avançadas de autenticação](media/deploy/authentication-advanced-settings.png)

1. Selecione **Salvar** para atualizar o registro do cliente.

## <a name="remove-a-deployment"></a>Remove uma implantação

Quando uma implantação não é mais necessária, você pode remover uma implantação, excluindo o grupo de recursos do Workbench Blockchain.

1. No portal do Azure, navegue até **Grupo de recursos** no painel de navegação esquerdo e selecione o grupo de recursos que você deseja excluir. 
1. Selecione **Excluir grupo de recursos**. Verifique a exclusão digitando o nome do grupo de recursos e selecione **Excluir**.

    ![Excluir grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo de procedimentos, você implantou o Workbench do Azure Blockchain. Para saber como usar o aplicativo blockchain, continue no próximo artigo de instruções.

> [!div class="nextstepaction"]
> [Criar um aplicativo blockchain no Azure Blockchain Workbench](create-app.md)
