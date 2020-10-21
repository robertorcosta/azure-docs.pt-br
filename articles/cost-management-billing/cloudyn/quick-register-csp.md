---
title: Registrar-se usando as informações do Parceiro CSP com o Cloudyn no Azure
description: Saiba mais sobre o processo de registro necessário para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: quickstart
ms.custom: seodec18
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ROBOTS: NOINDEX
ms.openlocfilehash: 1cbdeb09a1f3182aef9dcc6445c5b5e22e2448ff
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132339"
---
# <a name="register-with-the-csp-partner-program-and-view-cost-data"></a>Registrar-se mo programa de Parceiro CSP e exibir dados de custo

Como parceiro CSP, você pode registrar-se com o Cloudyn. O registro fornece acesso ao portal do Cloudyn. Este guia de início rápido fornece detalhes do processo de registro necessários para criar uma assinatura de avaliação do Cloudyn e entrar no portal do Cloudyn. Também mostra como começar a exibir dados de custo imediatamente.


[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

> [!NOTE]
>
> Somente os parceiros Diretos do CSP e provedores Indiretos do CSP podem concluir o registro de Cloudyn.
>
> A configuração da API do Partner Center é necessária para autenticação e acesso a dados. Uma conta de Administrador Global do Centro de Parceiro é necessária para provisionar o acesso de API.
> Para obter mais informações, consulte [Conectar-se à API do Partner Center](/partner-center/develop/set-up-api-access-in-partner-center).
>
> Acesso a Cloudyn pode ficar disponível para revendedores Indiretos CSP depois que seu provedor Indireto CSP registrar com Cloudyn. Revendedores indiretos CSP poderão fornecer acesso Cloudyn para clientes do Azure e assinaturas.
>
>O Cloudy é compatível com o Modelo de Aplicativo Seguro da Microsoft. Para obter mais informações, confira [Habilitando a estrutura de Modelo de Aplicativo Seguro](/partner-center/develop/enable-secure-app-model).

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="register-with-cloudyn"></a>Registrar com Cloudyn

1. No portal do Azure, clique em **Gerenciamento de Custo + Cobrança** na lista de serviços.
2. Em **Visão geral**, clique em **Cloudyn**  
    ![Página do Cloudyn mostrada no portal do Azure](./media/quick-register-csp/cost-mgt-billing-service.png)
3. Na página **Cloudyn**, clique em **Ir para o Cloudyn** para abrir a página de registro do Cloudyn em uma nova janela.
4. Na página de registro de avaliação do portal do Cloudyn, digite o nome de sua empresa, selecione **Administrador do Microsoft CSP Partner Program** e, em seguida, clique em **Avançar**.  
5. Insira uma **ID do Aplicativo**, **ID de Comércio**, **Chave secreta do aplicativo** e selecione o **Plano de Preços Padrão**. Caso não tenha as informações em mãos, entre no portal do Partner Center em [https://partnercenter.microsoft.com](https://partnercenter.microsoft.com) com sua conta principal de administrador e execute as seguintes etapas:
   1. Vá para **Painel**, clique no símbolo **Configurações**, clique em **Configurações de Parceiro**e clique em **Gerenciamento de Aplicativo**.
   2. Caso tenha criado anteriormente um Aplicativo Web, ignore esta etapa. Caso contrário, clique em **Adicionar novo aplicativo Web** na seção **Aplicativo Web**.
   3. Copie o GUID da **ID do Aplicativo** do aplicativo Web.
   4. Copie o GUID da **ID do comércio** do aplicativo Web.
   5. Selecione a duração da validade da chave como um ou dois anos, conforme necessário. Selecione **Adicionar chave** e, em seguida, copie e salve o valor da chave secreta.  
    ![Painel de parceiros no qual você copia informações de credenciais](./media/quick-register-csp/csp-partner-center.png)
   6. Volte para a página de registro do Cloudyn e cole as informações.  
      ![Colar informações de credenciais na página de registro Cloudyn](./media/quick-register-csp/csp-reg.png)
6. Concorde com os Termos de Uso e, em seguida, valide as informações. Clique em **Avançar** para autorizar o Cloudyn a coletar dados do recurso do Azure. Os dados coletados incluem dados de uso, desempenho, cobrança e marcação de suas assinaturas.  
7. Em **Convidar outros stakeholders**, adicione usuários digitando seus endereços de email. Quando concluir, clique em **Avançar**. Leva cerca de duas horas para que todos os seus dados de cobrança sejam adicionados ao Cloudyn.
8. Clique em **Ir para o Cloudyn** para abrir o portal de Cloudyn e, em seguida, na página **Gerenciamento de Contas de Nuvem**, você deverá ver as informações de sua conta do CSP registrada.

## <a name="configure-indirect-csp-access-in-cloudyn"></a>Configurar acesso indireto de CSP em Cloudyn

Por padrão, a API do Partner Center só está acessível a CSPs diretos. No entanto, um provedor de CSP direto pode configurar o acesso para seus clientes ou parceiros de CSP indireto usando grupos de entidades em Cloudyn.

Para permitir acesso de parceiros ou clientes do CSP indiretos, siga as etapas em [Registrar com Cloudyn](#register-with-cloudyn) para configurar um registro de avaliação. Em seguida, conclua estas etapas para segmentar dados de CSP indireto usando grupos de entidades Cloudyn. Em seguida, atribua as permissões de usuário apropriadas para os grupos de entidades.

1. Crie um grupo de entidades com as informações em [Criar entidades](tutorial-user-access.md#create-and-manage-entities).
2. Siga as etapas em [Atribuir assinaturas a Entidades de Custo](https://www.youtube.com/watch?v=d9uTWSdoQYo). Associe a conta do cliente de CSP indireto e suas assinaturas do Azure para a entidade que você criou anteriormente.
3. Siga as etapas em [Criar um usuário com acesso de administrador](tutorial-user-access.md#create-a-user-with-admin-access) para criar uma conta de usuário com acesso de Administrador. Em seguida, verifique se a conta de usuário tem acesso de administrador às entidades específicas criadas anteriormente para a conta indireta.

Parceiros de CSP indiretos entram no portal Cloudyn usando as contas que você criou para eles.


[!INCLUDE [cost-management-create-account-view-data](../../../includes/cost-management-create-account-view-data.md)]

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você usou as informações do CSP para se registrar no Cloudyn. Você também entrou no portal do Cloudyn e começou a exibir os dados de custo. Para saber mais sobre a Cloudyn, continue o tutorial para o Cloudyn.

> [!div class="nextstepaction"]
> [Examinar o uso e os custos](tutorial-review-usage.md)