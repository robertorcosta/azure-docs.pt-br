---
title: Instalar o gateway de dados local
description: Antes de poder acessar dados nas instalações dos Aplicativos Lógicos do Azure, baixe e instale o gateway de dados local
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 12/05/2019
ms.openlocfilehash: f2f8b9f207993c49201d03d3d1fed3c5800e8780
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673824"
---
# <a name="install-on-premises-data-gateway-for-azure-logic-apps"></a>Instalar o gateway de dados no local para os Aplicativos Lógicos do Azure

Antes de [se conectar a fontes de dados locais do Azure Logic Apps](../logic-apps/logic-apps-gateway-connection.md), baixe e instale o gateway de dados [local](https://aka.ms/on-premises-data-gateway-installer) em um computador local. O gateway funciona como uma ponte que fornece transferência rápida de dados e criptografia entre fontes de dados no local e seus aplicativos lógicos. Você pode usar a mesma instalação de gateway com outros serviços de nuvem, como Power BI, Power Automate, Power Apps e Azure Analysis Services. Para obter informações sobre como usar o gateway com esses serviços, consulte estes artigos:

* [Gateway de dados do Microsoft Power Automate on-premises](/power-automate/gateway-reference)
* [Gateway de dados local do Microsoft Power BI](/power-bi/service-gateway-onprem)
* [Gateway de dados do Microsoft Power Apps no local](/powerapps/maker/canvas-apps/gateway-reference)
* [Gateway de dados local do Azure Analysis Services](../analysis-services/analysis-services-gateway.md)

Este artigo mostra como baixar, instalar e configurar seu gateway de dados no local para que você possa acessar fontes de dados locais do Azure Logic Apps. Você também pode aprender mais sobre [como o gateway de dados funciona](#gateway-cloud-service) mais tarde neste tópico. Para obter mais informações sobre o gateway, consulte [O que é um gateway no local?](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem) Para automatizar as tarefas de instalação e gerenciamento de gateway, visite a galeria PowerShell para obter os [cmdlets DataGateway PowerShell](https://www.powershellgallery.com/packages/DataGateway/3000.15.15).

<a name="requirements"></a>

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tiver uma conta no Azure com uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

  * Sua conta Azure deve pertencer a um único [inquilino ou diretório do Azure Active Directory (Azure AD).](../active-directory/fundamentals/active-directory-whatis.md#terminology) Você deve usar a mesma conta do Azure para instalar e administrar o gateway em seu computador local.

  * Durante a instalação do gateway, você faz login com sua conta Do Azure, que vincula sua instalação de gateway à sua conta do Azure e somente a essa conta. Mais tarde, no portal Azure, você deve usar a mesma conta do Azure e o inquilino Azure AD ao criar um recurso de gateway do Azure que registra e reivindica sua instalação de gateway. Nos Aplicativos de Lógica do Azure, os gatilhos e ações no local usam o recurso gateway para se conectar a fontes de dados locais.

    > [!NOTE]
    > Você pode vincular apenas uma instalação de gateway e um recurso de gateway do Azure entre si. Você não pode vincular a mesma instalação de gateway a várias contas do Azure ou aos recursos do gateway do Azure. No entanto, uma conta do Azure pode ser vinculada a várias instalações de gateway e recursos de gateway do Azure. Em um gatilho ou ação no local, você pode selecionar entre suas várias assinaturas do Azure e, em seguida, selecionar um recurso de gateway associado.

  * Você precisa fazer login com uma conta de trabalho ou escola, também `username@contoso.com`conhecida como uma conta *de organização,* que parece . Você não pode usar contas Azure B2B (convidado) @hotmail.com ou @outlook.comcontas pessoais da Microsoft, como ou .

    > [!TIP]
    > Se você se inscreveu para uma oferta do Office 365 e não `username@domain.onmicrosoft.com`forneceu seu endereço de e-mail de trabalho, seu endereço pode parecer . Sua conta é armazenada dentro de um inquilino em um Diretório Ativo do Azure (Azure AD). Na maioria dos casos, o Nome Principal do Usuário (UPN) para sua conta Azure AD é o mesmo que seu endereço de e-mail.
    >
    > Para usar uma [assinatura do Visual Studio Standard](https://visualstudio.microsoft.com/vs/pricing/) vinculada a uma conta da Microsoft, primeiro crie um inquilino no [Azure AD](../active-directory/develop/quickstart-create-new-tenant.md) ou use o diretório padrão. Adicione um usuário com uma senha ao diretório e, em seguida, dê a esse usuário acesso à sua assinatura do Azure. Em seguida, você pode entrar durante a instalação do gateway com esse nome de usuário e senha.

* Aqui estão os requisitos para o computador local:

  **Requisitos mínimos**

  * .NET Framework 4.7.2
  * Versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

  **Requisitos recomendados**

  * CPU com oito núcleos
  * Memória de 8 GB
  * Versão de 64 bits do Windows Server 2012 R2 ou posterior
  * Armazenamento de unidade de estado sólido (SSD) para spooling

  > [!NOTE]
  > O gateway não suporta o Windows Server Core.

* **Considerações relacionadas**

  * Instale o gateway de dados no local apenas em um computador local, não em um controlador de domínio. Você não precisa instalar o gateway no mesmo computador que a fonte de dados. Você precisa de apenas um gateway para todas as suas fontes de dados, para que você não precise instalar o gateway para cada fonte de dados.

    > [!TIP]
    > Para minimizar a latência, você pode instalar o gateway o mais próximo possível da sua fonte de dados ou no mesmo computador, presumindo que você tenha permissões.

  * Instale o gateway em um computador que está em uma rede com fio, conectado à internet, sempre ligado e não durma. Caso contrário, o gateway não pode ser executado, e o desempenho pode sofrer por uma rede sem fio.

  * Se você planeja usar a autenticação do Windows, certifique-se de instalar o gateway em um computador que seja membro do mesmo ambiente do Active Directory que suas fontes de dados.

  * A região que você seleciona para a instalação do gateway é o mesmo local que você deve selecionar quando criar mais tarde o recurso gateway do Azure para o seu aplicativo lógico. Por padrão, essa região é o mesmo local que seu inquilino Azure AD que gerencia sua conta do Azure. No entanto, você pode alterar o local durante a instalação do gateway.

  * Se você estiver atualizando sua instalação de gateway para a versão mais recente, desinstale seu gateway atual primeiro para uma experiência mais limpa.

  * O gateway tem dois modos: modo padrão e modo pessoal, que se aplica apenas ao Power BI. Você não pode ter mais de um gateway rodando no mesmo modo no mesmo computador.

  * O Azure Logic Apps suporta operações de leitura e gravação através do gateway. No entanto, essas operações têm [limites em seu tamanho de carga.](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)

<a name="install-gateway"></a>

## <a name="install-data-gateway"></a>Instalar gateway de dados

1. [Baixe e execute o instalador do gateway em um computador local](https://aka.ms/on-premises-data-gateway-installer).

1. Revise os requisitos mínimos, mantenha o caminho de instalação padrão, aceite os termos de uso e selecione **Instalar**.

   ![Revisar os requisitos e aceitar os termos de uso](./media/logic-apps-gateway-install/review-and-accept-terms-of-use.png)

1. Depois que o gateway for instalado com sucesso, forneça o endereço de e-mail da sua conta do Azure e selecione **Entrar,** por exemplo:

   ![Entrar com uma conta corporativa ou de estudante](./media/logic-apps-gateway-install/sign-in-gateway-install.png)

   Sua instalação do gateway pode ser vinculada a apenas uma conta do Azure.

1. Selecione **Registre um novo gateway neste computador** > **Next**. Esta etapa registra sua instalação de gateway com o [serviço de nuvem gateway](#gateway-cloud-service).

   ![Registre gateway no computador local](./media/logic-apps-gateway-install/register-gateway-local-computer.png)

1. Forneça essas informações para a instalação do gateway:

   * Um nome de gateway único em todo o seu inquilino Azure AD
   * A chave de recuperação, que deve ter pelo menos oito caracteres, que você quer usar
   * Confirmação para a chave de recuperação

   ![Fornecer informações para instalação de gateway](./media/logic-apps-gateway-install/gateway-name-recovery-key.png)

   > [!IMPORTANT]
   > Salve e mantenha a chave de recuperação em um local seguro. Você precisa desta chave se quiser alterar o local, mover, recuperar ou assumir uma instalação de gateway.

   Observe a opção **de adicionar a um cluster de gateway existente,** que você seleciona quando instala gateways adicionais para [cenários de alta disponibilidade](#high-availability).

1. Verifique a região para obter o serviço de nuvem gateway e o Ônibus de [Serviço Azure](https://azure.microsoft.com/services/service-bus/) que é usado pela instalação do gateway. Por padrão, essa região é o mesmo local que o inquilino Azure AD para sua conta azure.

   ![Confirme região para serviço de gateway e ônibus de serviço](./media/logic-apps-gateway-install/confirm-gateway-region.png)

1. Para aceitar a região padrão, **selecione Configurar**. No entanto, se a região padrão não for a mais próxima de você, você pode mudar a região.

   *Por que alterar a região de instalação do gateway?*

   Por exemplo, para reduzir a latência, é possível alterar a região do gateway para a mesma região que o aplicativo lógico. Ou então, é possível selecionar a região mais próxima à fonte de dados local. O *recurso de gateway no Azure* e o aplicativo lógico podem ter locais diferentes.

   1. Ao lado de região atual, selecione **Alterar Região**.

      ![Alterar a região de gateway atual](./media/logic-apps-gateway-install/change-gateway-service-region.png)

   1. Na página seguinte, abra a lista **Selecionar região,** selecione a região desejada e selecione **'Feito ''Feito'.**

      ![Selecione outra região para o serviço gateway](./media/logic-apps-gateway-install/select-region-gateway-install.png)

1. Revise as informações na janela final de confirmação. Este exemplo usa a mesma conta para Logic Apps, Power BI, Power Apps e Power Automate, de modo que o gateway está disponível para todos esses serviços. Quando estiver pronto, selecione **Fechar**.

   ![Confirmar informações do gateway de dados](./media/logic-apps-gateway-install/finished-gateway-default-location.png)

1. Agora [crie o recurso Azure para sua instalação de gateway](../logic-apps/logic-apps-gateway-connection.md).

## <a name="check-or-adjust-communication-settings"></a>Verifique ou ajuste as configurações de comunicação

O gateway de dados no local depende do [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) para conectividade em nuvem e estabelece as conexões de saída correspondentes à região Azure associada do gateway. Se o ambiente de trabalho exigir que o tráfego passe por um proxy ou firewall para acessar a internet, essa restrição pode impedir que o gateway de dados no local se conecte ao serviço de nuvem de gateway e ao Ônibus de Serviço do Azure. O gateway tem várias configurações de comunicação, que você pode ajustar. Para saber mais, consulte esses tópicos:

* [Ajuste as configurações de comunicação para o gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-communication)
* [Definir as configurações de proxy do gateway de dados local](https://docs.microsoft.com/data-integration/gateway/service-gateway-proxy)

<a name="high-availability"></a>

## <a name="high-availability-support"></a>Suporte de alta disponibilidade

Para evitar pontos únicos de falha para acesso a dados no local, você pode ter várias instalações de gateway (apenas no modo padrão) com cada uma em um computador diferente e configurá-las como um cluster ou grupo. Dessa forma, se o gateway principal estiver indisponível, as solicitações de dados serão encaminhadas para o segundo gateway, e assim por diante. Como você pode instalar apenas um gateway padrão em um computador, você deve instalar cada gateway adicional que está no cluster em um computador diferente. Todos os conectores que trabalham com o gateway de dados no local suportam alta disponibilidade.

* Você já deve ter pelo menos uma instalação de gateway com a mesma conta do Azure como o gateway principal e a chave de recuperação para essa instalação.

* O gateway primário deve estar executando a atualização do gateway de novembro de 2017 ou posterior.

Depois de configurar seu gateway principal, ao instalar outro gateway, **selecione Adicionar a um cluster de gateway existente,** selecione o gateway principal, que é o primeiro gateway que você instalou e forneça a chave de recuperação para esse gateway. Para obter mais informações, consulte [Clusters de alta disponibilidade para gateway de dados locais](https://docs.microsoft.com/data-integration/gateway/service-gateway-install#add-another-gateway-to-create-a-cluster).

<a name="update-gateway-installation"></a>

## <a name="change-location-migrate-restore-or-take-over-existing-gateway"></a>Alterar o local, migrar, restaurar ou assumir um gateway existente

Se for necessário alterar o local do gateway, mover a instalação do gateway para um novo computador, recuperar um gateway danificado ou assumir a propriedade de um gateway existente, você precisará da chave de recuperação que foi fornecida durante a instalação do gateway.

1. Execute o instalador de gateway no computador que tenha o gateway existente. Se você não tiver o instalador de gateway mais recente, [baixe a versão mais recente do gateway](https://aka.ms/on-premises-data-gateway-installer).

   > [!NOTE]
   > Antes de restaurar o gateway no computador que tem a instalação original do gateway, você deve primeiro desinstalar o gateway nesse computador. Esta ação desconecta o gateway original.
   > Se você remover ou excluir um cluster de gateway para qualquer serviço de nuvem, você não poderá restaurar esse cluster.

1. Depois que o instalador for aberto, faça login com a mesma conta do Azure que foi usada para instalar o gateway.

1. Selecione **Migrar, restaurar ou assumir um gateway** > existente**Next,** por exemplo:

   ![Selecionar "Migrar, restaurar ou assumir um gateway existente"](./media/logic-apps-gateway-install/migrate-recover-take-over-gateway.png)

1. Selecione entre os clusters e gateways disponíveis e digite a chave de recuperação do gateway selecionado, por exemplo:

   ![Selecione gateway e forneça a chave de recuperação](./media/logic-apps-gateway-install/select-existing-gateway.png)

1. Para alterar a região, selecione **Alterar Região**e selecione a nova região.

1. Quando estiver pronto, **selecione Configurar para** que você possa terminar sua tarefa.

## <a name="tenant-level-administration"></a>Administração em nível de inquilino

Para obter visibilidade em todos os gateways de dados locais em um inquilino Azure AD, os administradores globais nesse inquilino podem entrar no [centro de administração da plataforma de energia](https://powerplatform.microsoft.com) como administrador de inquilinos e selecionar a opção **Gateways de dados.** Para obter mais informações, consulte [a administração em nível de inquilino para o gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-tenant-level-admin).

<a name="restart-gateway"></a>

## <a name="restart-gateway"></a>Reiniciar o gateway

Por padrão, a instalação do gateway em seu computador local é executada como uma conta de serviço do Windows chamada "Serviço de gateway de dados on-premises". No entanto, a `NT SERVICE\PBIEgwService` instalação do gateway usa o nome para suas credenciais de conta "Log On As" e tem permissões "Fazer logon como serviço".

> [!NOTE]
> Sua conta de serviço do Windows difere da conta usada para se conectar a fontes de dados locais e da conta Do Azure que você usa quando faz login em serviços na nuvem.

Como qualquer outro serviço do Windows, você pode iniciar e parar o gateway de várias maneiras. Para obter mais informações, consulte [Reiniciar um gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-restart).

<a name="gateway-cloud-service"></a>

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Os usuários da sua organização podem acessar dados locais para os quais já têm acesso autorizado. No entanto, antes que esses usuários possam se conectar à sua fonte de dados local, você precisa instalar e configurar um gateway de dados no local. Normalmente, um admin é a pessoa que instala e configura um gateway. Essas ações podem exigir permissões do Administrador do Servidor ou conhecimento especial sobre seus servidores locais.

O gateway ajuda a facilitar a comunicação mais rápida e segura nos bastidores. Essa comunicação flui entre um usuário na nuvem, o serviço de nuvem gateway e sua fonte de dados no local. O serviço de nuvem do gateway criptografa e armazena suas credenciais da fonte de dados e os detalhes do gateway. O serviço também encaminha consultas e seus resultados entre o usuário, o gateway e sua fonte de dados no local.

O gateway funciona com firewalls e usa apenas conexões de saída. Todo o tráfego se origina como tráfego de saída protegido do agente gateway. O gateway retransmite dados de fontes locais em canais criptografados através [do Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md). O barramento de serviço cria um canal entre o gateway e o serviço de chamada, mas não armazena nenhum dado. Todos os dados que trafegam através do gateway são criptografados.

![Arquitetura para gateway de dados no local](./media/logic-apps-gateway-install/how-on-premises-data-gateway-works-flow-diagram.png)

> [!NOTE]
> Dependendo do serviço de nuvem, talvez seja necessário configurar uma fonte de dados para o gateway.

Essas etapas descrevem o que acontece quando você interage com um elemento conectado a uma fonte de dados local:

1. O serviço em nuvem cria uma consulta, juntamente com as credenciais criptografadas para a fonte de dados. Em seguida, o serviço envia a consulta e as credenciais para a fila de gateway para processamento.

1. O serviço de nuvem gateway analisa a consulta e empurra a solicitação para o Azure Service Bus.

1. A Azure Service Bus envia as solicitações pendentes para o gateway.

1. O gateway obtém a consulta, descriptografa as credenciais e se conecta a uma ou mais fontes de dados com essas credenciais.

1. O gateway envia a consulta para a fonte de dados para execução.

1. Os resultados são enviados da fonte de dados de volta ao gateway e, em seguida, para o serviço de nuvem do gateway. O serviço de nuvem do gateway então usa os resultados.

### <a name="authentication-to-on-premises-data-sources"></a>Autenticação em fontes de dados locais

Uma credencial armazenada é usada para se conectar do gateway a fontes de dados locais. Independentemente do usuário, o gateway usa a credencial armazenada para se conectar. Pode haver exceções de autenticação para serviços específicos, como DirectQuery e LiveConnect for Analysis Services in Power BI.

### <a name="azure-active-directory-azure-ad"></a>Active Directory do Azure (Azure AD)

Os serviços de nuvem da Microsoft usam [o Azure AD](../active-directory/fundamentals/active-directory-whatis.md) para autenticar usuários. Um inquilino Azure AD contém nomes de usuário e grupos de segurança. Normalmente, o endereço de e-mail que você usa para fazer login é o mesmo que o Nome Principal do Usuário (UPN) da sua conta.

### <a name="what-is-my-upn"></a>O que é a minha UPN?

Se você não é um admin de domínio, você pode não conhecer sua UPN. Para encontrar a UPN para `whoami /upn` sua conta, execute o comando da sua estação de trabalho. Embora o resultado pareça um endereço de e-mail, o resultado é o UPN para sua conta de domínio local.

### <a name="synchronize-an-on-premises-active-directory-with-azure-ad"></a>Sincronizar um Active Directory local com o Azure AD

O UPN para suas contas de diretório ativo no local e contas Azure AD deve ser o mesmo. Portanto, certifique-se de que cada conta do Active Directory no local corresponda à sua conta Azure AD. Os serviços em nuvem só sabem sobre contas dentro do Azure AD. Então, você não precisa adicionar uma conta ao seu Diretório Ativo no local. Se a conta não existir no Azure AD, você não poderá usar essa conta.

Aqui estão as maneiras que você pode combinar suas contas de diretório ativo no local com o Azure AD.

* Adicione contas manualmente ao Azure AD.

  Crie uma conta no portal Azure ou no centro de administração microsoft 365. Certifique-se de que o nome da conta corresponda à UPN para a conta do Active Directory no local.

* Sincronize contas locais com seu inquilino Azure AD usando a ferramenta Azure Active Directory Connect.

  A ferramenta Azure AD Connect oferece opções para sincronização de diretórios e configuração de autenticação. Essas opções incluem sincronização de hash por senha, autenticação de passagem e federação. Se você não é um administrador de inquilinos ou um administrador de domínio local, entre em contato com o administrador de TI para configurar o Azure AD Connect. O Azure AD Connect garante que o Azure AD UPN corresponda ao UPN active local. Essa correspondência ajuda se você estiver usando conexões ao vivo do Analysis Services com recursos de Power BI ou SSO (Single Sign-on).

  > [!NOTE]
  > Sincronizar contas com a ferramenta Azure AD Connect cria novas contas no seu inquilino Azure AD.

<a name="faq"></a>

## <a name="faq-and-troubleshooting"></a>Perguntas frequentes e solução de problemas

Para saber mais, consulte esses tópicos:

* [Faq de gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem-faq)
* [Solucionar problemas no gateway de dados no local](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot)
* [Monitorar e otimizar o desempenho do gateway](https://docs.microsoft.com/data-integration/gateway/service-gateway-performance)

## <a name="next-steps"></a>Próximas etapas

* [Conecte-se a dados locais de aplicativos lógicos](../logic-apps/logic-apps-gateway-connection.md)
* [Recursos de integração corporativa](../logic-apps/logic-apps-enterprise-integration-overview.md)
* [Conectores de Aplicativos Lógicos do Azure](../connectors/apis-list.md)
