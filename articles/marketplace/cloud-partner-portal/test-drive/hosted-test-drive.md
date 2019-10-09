---
title: Unidade de teste hospedada | Azure Marketplace
description: Como configurar um manter um test drive hospedado do Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 67d8421b2e545c951dcbc3280a306514e4b14897
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030060"
---
# <a name="hosted-test-drive"></a>Test drive hospedado

Um test drive hospedado elimina a complexidade da instalação porque a Microsoft instala e mantém o serviço que executa o provisionamento e o desprovisionamento do usuário do test drive. Este artigo é para editores que já têm ou estando criando uma oferta no AppSource e desejam oferecer a um test drive hospedado, que se conecte a uma instância do Dynamics 365 for Customer Engagement, do Dynamics 365 para Finanças ou do Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Navegue até uma oferta existente ou crie uma oferta.

Selecione a opção de test drive no menu lateral.

Selecione \'Sim\' para a opção \'Habilitar um test drive\'.

Forneça os seguintes campos na seção \'Detalhes\'.

- **Descrição**: Forneça uma visão geral do seu Test Drive. Esse texto será mostrado ao usuário enquanto o test drive está sendo provisionado. Esse campo dá suporte a HTML, caso você queira fornecer conteúdo formatado.
- **Manual do usuário**: Carregue um manual de usuário Detalhado (arquivo do tipo. pdf) que ajuda a testar os usuários da unidade a entender como usar seu aplicativo.
- **Vídeo de demonstração do Test Drive**: Opcionalmente, carregue um vídeo que demonstra seu aplicativo.

Conceda permissão ao AppSource para provisionar e desprovisionar usuários do test drive em seu locatário usando as instruções localizadas [aqui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Nesta etapa, você gerará os valores da \'ID do Aplicativo Azure AD\' e da \'Chave do Aplicativo Azure AD\' mencionados abaixo.

Forneça os seguintes campos na seção \'Configurações Técnicas\':

- **Tipo de unidade de teste**: Escolha a opção \'Microsoft hospedado (exemplo: Dynamics 365 para compromisso com o cliente). Isso indica que a Microsoft hospedará e manterá o serviço que executa o provisionamento e o desprovisionamento de usuários do test drive.
- **Máximo de unidades de teste simultâneas**: Defina esse campo como o número de usuários simultâneos que podem ter uma unidade de teste ativa em qualquer momento determinado. Cada usuário consumirá uma licença do Dynamics enquanto seu test drive estiver ativo, portanto, verifique se você tem pelo menos esse número de licenças do Dynamics disponíveis para usuários do test drive. O valor recomendado é de 3 a 5.
- **Duração da unidade de teste (horas)** : Defina esse campo como o número de horas em que os usuários Test Drive estarão ativos. Depois desse número de horas, o usuário será desprovisionado do locatário. O valor recomendado é de 2 a 24 horas, dependendo da complexidade do aplicativo. O usuário sempre poderá solicitar outro test drive quando atingir o tempo limite e desejar acessar o test drive novamente.
- **URL da instância**: Forneça uma URL para a qual o usuário da unidade de teste será navegado inicialmente quando iniciar a unidade de teste. Normalmente, essa é a URL da instância do Dynamics 365 que contém o aplicativo e os dados de exemplo instalados. Valor de exemplo: https: \//testdrive. CRM. Dynamics. com
- **ID do locatário do Azure ad**: Forneça a ID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, faça logon no portal do Azure e navegue até o \'Azure Active Directory\' -\> Selecione Propriedades na folha de menu −\> Copiar a ID do Diretório. Valor de exemplo: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID do aplicativo Azure ad**: ID do Aplicativo Azure AD criado no valor da etapa 7. \ exemplo: 53852862-a2ae-4e43-9461-faa49650a096
- **Chave de aplicativo Azure ad**: Segredo para o Aplicativo Azure AD criado no valor da etapa 7. \ exemplo: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nome do locatário do Azure ad**: Forneça o nome do locatário do Azure para sua instância do Dynamics 365. Use o formato \<tenantname.\>onmicrosoft.com. Valor de exemplo: testdrive.onmicrosoft.com
- **URL da API Web da instância**: Forneça a URL da API da Web para sua instância do Dynamics 365. Você pode recuperar esse valor ao fazer logon na instância do Microsoft Dynamics 365 e navegar para Configuração –\> Personalização –\> Recursos para Desenvolvedores –\> API Web da Instância (copie esta URL). Valor de exemplo: https: \//testdrive. CRM. Dynamics. com/API/data/v 9.0 
- **Nome da função**: Forneça o nome da função de segurança personalizada do Dynamics 365 que você criou para o Test Drive. Essa é a função que será atribuída aos usuários durante o test drive. Valor de exemplo: testdriverole

## <a name="next-steps"></a>Próximas etapas

Quando você estiver pronto para **publicar** sua oferta, depois que o aplicativo for aprovado na certificação, você terá uma **versão prévia** da sua oferta. Inicie um test drive na interface do usuário e verifique se os test drives estão sendo executados corretamente. Quando estiver satisfeito com a oferta de versão prévia, será a hora de **ativá-la**.
