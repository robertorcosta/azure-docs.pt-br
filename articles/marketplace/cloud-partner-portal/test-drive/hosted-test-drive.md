---
title: Unidade de teste hospedada | Azure Marketplace
description: Como configurar um manter um test drive hospedado do Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 2ce43402fb283cce09687bc7226770731f1bae0d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827524"
---
# <a name="hosted-test-drive"></a>Test drive hospedado

Um test drive hospedado elimina a complexidade da instalação porque a Microsoft instala e mantém o serviço que executa o provisionamento e o desprovisionamento do usuário do test drive. Este artigo é para editores que já têm ou estando criando uma oferta no AppSource e desejam oferecer a um test drive hospedado, que se conecte a uma instância do Dynamics 365 for Customer Engagement, do Dynamics 365 para Finanças ou do Dynamics 365 Business Central.

## <a name="how-to-publish-a-test-drive"></a>Como publicar um Test Drive

Navegue até uma oferta existente ou crie uma oferta.

Selecione a opção de test drive no menu lateral.

Selecione \'Sim\' para a opção \'Habilitar um test drive\'.

Forneça os seguintes campos na seção \'Detalhes\'.

- **Descrição**: forneça uma visão geral do test drive. Esse texto será mostrado ao usuário enquanto o test drive está sendo provisionado. Esse campo dá suporte a HTML, caso você queira fornecer conteúdo formatado.
- **Manual do Usuário**: faça upload de um manual do usuário detalhado (arquivo do tipo .pdf) que ajuda os usuários do test drive a entender como usar o aplicativo.
- **Vídeo de demonstração do test drive**: opcionalmente, faça upload de um vídeo que demonstra o aplicativo.

Conceda permissão ao AppSource para provisionar e desprovisionar usuários do test drive em seu locatário usando as instruções localizadas [aqui](https://github.com/Microsoft/AppSource/blob/patch-1/Microsoft%20Hosted%20Test%20Drive/Setup-your-Azure-subscription-for-Dynamics365-Microsoft-Hosted-Test-Drives.md).

Nesta etapa, você gerará os valores da \'ID do Aplicativo Azure AD\' e da \'Chave do Aplicativo Azure AD\' mencionados abaixo.

Forneça os seguintes campos na seção \'Configurações Técnicas\':

- **Tipo de test drive**: escolha a opção \'Hospedado pela Microsoft (exemplo do Dynamics 365 for Customer Engagement)'. Isso indica que a Microsoft hospedará e manterá o serviço que executa o provisionamento e o desprovisionamento de usuários do test drive.
- **Máximo de test drives simultâneos**: defina esse campo como o número de usuários simultâneos que podem ter um test drive ativo em qualquer determinado momento. Cada usuário consumirá uma licença do Dynamics enquanto seu test drive estiver ativo, portanto, verifique se você tem pelo menos esse número de licenças do Dynamics disponíveis para usuários do test drive. O valor recomendado é de 3 a 5.
- **Duração do test drive (horas)** : defina esse campo como o número de horas em que o test drive dos usuários ficará ativo. Depois desse número de horas, o usuário será desprovisionado do locatário. O valor recomendado é de 2 a 24 horas, dependendo da complexidade do aplicativo. O usuário sempre poderá solicitar outro test drive quando atingir o tempo limite e desejar acessar o test drive novamente.
- **URL da instância**: forneça uma URL para a qual o usuário do test drive navegará inicialmente quando os usuários iniciarem o test drive. Normalmente, essa é a URL da instância do Dynamics 365 que contém o aplicativo e os dados de exemplo instalados. Valor de exemplo: https: \//testdrive. CRM. Dynamics. com
- **ID do locatário do Azure AD**: forneça a ID do locatário do Azure para sua instância do Dynamics 365. Para recuperar esse valor, faça logon no portal do Azure e navegue até o \'Azure Active Directory\' -\> Selecione Propriedades na folha de menu −\> Copiar a ID do Diretório. Valor de exemplo: 72f988bf-86f1-41af-91ab-2d7cd0111234
- **ID do Azure AD**: ID do Aplicativo Azure AD criado na etapa 7.\ Valor de exemplo: 53852862-a2ae-4e43-9461-faa49650a096
- **Chave do Aplicativo Azure AD**: segredo da Chave do Aplicativo Azure AD criado na etapa 7.\ Valor de exemplo: IJUgaIOfq9b9LbUjeQmzNBW4VGn6grr1l/n3aMrnfdk=
- **Nome do Locatário do Azure AD**: forneça o nome do locatário do Azure para sua instância do Dynamics 365. Use o formato \<tenantname.\>onmicrosoft.com. Valor de exemplo: testdrive.onmicrosoft.com
- **URL da API Web da instância**: forneça a URL da API Web da sua instância do Dynamics 365. Você pode recuperar esse valor ao fazer logon na instância do Microsoft Dynamics 365 e navegar para Configuração –\> Personalização –\> Recursos para Desenvolvedores –\> API Web da Instância (copie esta URL). Valor de exemplo: https: \//testdrive. CRM. Dynamics. com/API/data/v 9.0 
- **Nome da função**: forneça o nome da função de segurança do Dynamics 365 personalizado criado para o test drive. Essa é a função que será atribuída aos usuários durante o test drive. Valor de exemplo: testdriverole

## <a name="next-steps"></a>Próximas etapas

Quando você estiver pronto para **publicar** sua oferta, depois que o aplicativo for aprovado na certificação, você terá uma **versão prévia** da sua oferta. Inicie um test drive na interface do usuário e verifique se os test drives estão sendo executados corretamente. Quando estiver satisfeito com a oferta de versão prévia, será a hora de **ativá-la**.
