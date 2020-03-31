---
title: Crie uma sonda personalizada usando o portal
titleSuffix: Azure Application Gateway
description: Saiba como criar uma investigação personalizada para o Gateway de Aplicativo usando o portal
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 15daf47a1cb44635932311e60b3690af9ff58677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74074602"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma investigação personalizada para o Gateway de Aplicativo usando o portal

> [!div class="op_single_selector"]
> * [Portal Azure](application-gateway-create-probe-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell clássico do Azure](application-gateway-create-probe-classic-ps.md)

Neste artigo, você adiciona um teste de saúde personalizado a um gateway de aplicativo existente através do portal Azure. Usando os testes de saúde, o Azure Application Gateway monitora a saúde dos recursos no pool back-end.

## <a name="before-you-begin"></a>Antes de começar

Se você ainda não tiver um gateway de aplicativo, visite [Criar um Gateway de Aplicativo](application-gateway-create-gateway-portal.md) a fim de criar um gateway de aplicativo para trabalhar.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Criar teste para Application Gateway v2 SKU

As investigações são configuradas em um processo de duas etapas pelo portal. O primeiro passo é inserir os valores necessários para a configuração do teste. Na segunda etapa, você testa a saúde do backend usando esta configuração do teste e salva a sonda. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>Insira propriedades do teste

1. Faça login no [portal Azure](https://portal.azure.com). Se você ainda não tem uma conta, você pode se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

2. No painel Favoritos do portal do Azure, clique em Todos os recursos. Clique no gateway de aplicativo na folha Todos os recursos. Se a assinatura que você selecionou já contém vários recursos, você pode inserir partners.contoso.net na caixa Filtrar por nome... para acessar facilmente o gateway de aplicativo.

3. Selecione **testes de saúde** e selecione **Adicionar** para adicionar um novo teste de saúde.

   ![Adicionar nova sonda][4]

4. Na página **Adicionar teste de saúde,** preencha as informações necessárias para o teste e quando concluir selecione **OK**.

   |**Configuração** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável dado à sonda que está acessível no portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que a investigação de integridade usa. |
   |**Host**|ou seja contoso.com|Este valor é o nome do host virtual (diferente do nome do host VM) em execução no servidor do aplicativo. O teste é enviado para (protocolo)://(nome de host):(porto de httpsetting)/urlPath.  Isso é aplicável quando vários sites são configurados no Application Gateway. Se o Gateway de aplicativo estiver configurado para um único site, digite '127.0.0.1'.|
   |**Escolha o nome do host nas configurações HTTP do backend**|Sim ou não|Define o cabeçalho *host* no teste para o nome de host do recurso back-end no pool back-end associado à configuração HTTP à qual este teste está associado. Especialmente necessário no caso de backends de vários inquilinos, como o serviço de aplicativo Azure. [Saiba mais](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Caminho**|/ ou outro caminho|O restante da URL completa para a investigação personalizada. Um caminho válido começa com "/". Para o caminho padrão\/de http: /contoso.com basta usar '/' |
   |**Intervalo (segundos)**|30|Frequência com que a investigação é executada para verificar a integridade. Não é recomendável defini-la abaixo de 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a sonda espera antes de cronometrar. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda será marcada como falha. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada http possa ser feita a fim de garantir que a página de integridade do back-end estará disponível. Observe que o valor de tempo não deve ser mais do que o valor 'Intervalo' usado nesta configuração do teste ou o valor 'Tempo de solicitação' na configuração HTTP que será associada a este teste.|
|**Limiar insalubre**|3|Número de tentativas fracassadas consecutivas para ser considerado insalubre. O limiar pode ser definido como 1 ou mais.|
   |**Use condições de correspondência de sonda**|Sim ou não|Por padrão, uma resposta HTTP(S) com código de status entre 200 e 399 é considerada saudável. Você pode alterar o intervalo aceitável do código de resposta backend ou do corpo de resposta backend. [Saiba mais](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|
   |**Configurações HTTP**|seleção de dropdown|O teste será associado às configurações HTTP selecionadas aqui e, portanto, monitorará a saúde desse pool de backend que está associado à configuração HTTP selecionada. Ele usará a mesma porta para a solicitação do teste que a que está sendo usada na configuração HTTP selecionada. Você só pode escolher as configurações HTTP que não estão associadas a nenhum outro teste personalizado. <br>Observe que apenas as configurações HTTP estão disponíveis para associação que tenham o mesmo protocolo do protocolo escolhido nesta configuração do teste e tenham o mesmo estado para o switch *de configuração Pick Host Host From Backend HTTP.*|
   
   > [!IMPORTANT]
   > A sonda somente monitorará a saúde do backend quando estiver associado a uma ou mais configurações HTTP. Ele monitorará os recursos back-end desses pools back-end associados às configurações HTTP às quais este teste está associado. A solicitação do teste será enviada para http://(nome do host):(porto de httpsetting)/urlPath.

### <a name="test-backend-health-with-the-probe"></a>Teste a saúde do backend com a sonda

Depois de inserir as propriedades do teste, você pode testar a saúde dos recursos back-end para verificar se a configuração do teste está correta e se os recursos back-end estão funcionando conforme o esperado.

1. Selecione **Teste** e observe o resultado do teste. O gateway de aplicativo testa a saúde de todos os recursos de backend nos pools de backend associados às configurações HTTP usadas para este teste. 

   ![Testar a saúde do backend][5]

2. Se houver algum recurso de backend insalubre, verifique a coluna **Detalhes** para entender o motivo do estado insalubre do recurso. Se o recurso tiver sido marcado como insalubre devido a uma configuração incorreta do teste, selecione o **Link 'Voltar para sondar'** e editar a configuração do teste. Caso contrário, se o recurso tiver sido marcado como insalubre devido a um problema com o backend, resolva os problemas com o recurso backend e, em seguida, teste o backend novamente selecionando o **link 'Voltar para sondar** e selecionar **Teste**.

   > [!NOTE]
   > Você pode optar por salvar a sonda mesmo com recursos de backend insalubres, mas não é recomendado. Isso porque o Gateway de aplicativo remove esses recursos de backend do pool de backend que são determinados como insalubres pelo teste. Caso não haja recursos saudáveis em um pool de backend, você não poderá acessar sua aplicação e receberá um erro de 502.

   ![Exibir o resultado da sonda][6]

3. Selecione **Adicionar** para salvar a sonda. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Criar teste para Application Gateway v1 SKU

As investigações são configuradas em um processo de duas etapas pelo portal. A primeira etapa é criar a investigação. Na segunda etapa, você adiciona a investigação às configurações de HTTP do back-end do Gateway de Aplicativo.

### <a name="create-the-probe"></a><a name="createprobe"></a>Criar a sonda

1. Faça login no [portal Azure](https://portal.azure.com). Se você ainda não tem uma conta, você pode se inscrever para uma [avaliação gratuita de um mês](https://azure.microsoft.com/free)

2. No painel Favoritos do portal do Azure, selecione **Todos os recursos**. Selecione o gateway de aplicativo na página **Todos os recursos.** Se a assinatura que você selecionou já contém vários recursos, você pode inserir partners.contoso.net na caixa Filtrar por nome... para acessar facilmente o gateway de aplicativo.

3. Selecione **Testes** e selecione **Adicionar** para adicionar um teste.

   ![Folha Adicionar Investigação com as informações preenchidas][1]

4. Na lâmina do **teste de saúde Adicionar,** preencha as informações necessárias para a sonda e quando concluir selecione **OK**.

   |**Configuração** | **Valor** | **Detalhes**|
   |---|---|---|
   |**Nome**|customProbe|Este valor é um nome amigável dado à sonda que está acessível no portal.|
   |**Protocolo**|HTTP ou HTTPS | O protocolo que a investigação de integridade usa. |
   |**Host**|ou seja contoso.com|Este valor é o nome do host virtual (diferente do nome do host VM) em execução no servidor do aplicativo. O teste é enviado para (protocolo)://(nome de host):(porto de httpsetting)/urlPath.  Isso é aplicável quando vários sites são configurados no Application Gateway. Se o Gateway de aplicativo estiver configurado para um único site, digite '127.0.0.1'.|
   |**Escolha o nome do host nas configurações HTTP do backend**|Sim ou não|Define o cabeçalho *host* no teste para o nome de host do recurso back-end no pool back-end associado à configuração HTTP à qual este teste está associado. Especialmente necessário no caso de backends de vários inquilinos, como o serviço de aplicativo Azure. [Saiba mais](https://docs.microsoft.com/azure/application-gateway/configuration-overview#pick-host-name-from-back-end-address)|
   |**Caminho**|/ ou outro caminho|O restante da URL completa para a investigação personalizada. Um caminho válido começa com "/". Para o caminho padrão\/de http: /contoso.com basta usar '/' |
   |**Intervalo (segundos)**|30|Frequência com que a investigação é executada para verificar a integridade. Não é recomendável defini-la abaixo de 30 segundos.|
   |**Tempo limite (segundos)**|30|A quantidade de tempo que a sonda espera antes de cronometrar. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda será marcada como falha. O intervalo de tempo limite deve ser alto o suficiente para que uma chamada http possa ser feita a fim de garantir que a página de integridade do back-end estará disponível. Observe que o valor de tempo não deve ser mais do que o valor 'Intervalo' usado nesta configuração do teste ou o valor 'Tempo de solicitação' na configuração HTTP que será associada a este teste.|
|**Limiar insalubre**|3|Número de tentativas fracassadas consecutivas para ser considerado insalubre. O limiar pode ser definido como 1 ou mais.|
   |**Use condições de correspondência de sonda**|Sim ou não|Por padrão, uma resposta HTTP(S) com código de status entre 200 e 399 é considerada saudável. Você pode alterar o intervalo aceitável do código de resposta backend ou do corpo de resposta backend. [Saiba mais](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#probe-matching)|

   > [!IMPORTANT]
   > O nome do host não é o mesmo que o nome do servidor. Esse valor é o nome do host virtual em execução no servidor de aplicativos. A investigação é enviada para http://(nome do host):(porta de httpsetting)/urlPath

### <a name="add-probe-to-the-gateway"></a>Adicionar a investigação ao gateway

Agora que a investigação foi criada, é hora de adicioná-la ao gateway. As configurações da investigação são definidas nas configurações de http do back-end do Gateway de Aplicativo.

1. Clique em **Configurações de HTTP** do Gateway de Aplicativo e clique nas configurações de HTTP do back-end atual listadas na janela para abrir a folha de configuração.

   ![janela de configurações de https][2]

2. Na página de configurações **do aplicativoGatewayBackEndHttpConfigurações,** verifique a caixa de seleção **de teste personalizado de Uso** e escolha o teste criado na seção Criar a seção de [teste](#createprobe) na lista de testes **personalizados.**
   Ao concluir, clique em **Salvar** e as configurações são aplicadas.

## <a name="next-steps"></a>Próximas etapas

Veja a saúde dos recursos backend conforme determinado pela sonda utilizando a [visão de saúde backend](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health).

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png
