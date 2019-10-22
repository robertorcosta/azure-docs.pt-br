---
title: Monitore seu aplicativo Web com testes na Web de várias etapas e informações de Aplicativo Azure | Microsoft Docs
description: Configurar testes na Web de várias etapas para monitorar seus aplicativos Web com o Aplicativo Azure insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/25/2019
ms.reviewer: sdash
ms.openlocfilehash: f34695cb4a92fbed285ba8c56764606a124194a4
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2019
ms.locfileid: "72678228"
---
# <a name="multi-step-web-tests"></a>Teste na Web de várias etapas

Você pode monitorar uma sequência registrada de URLs e interações com um site por meio de testes na Web de várias etapas. Este artigo explicará o processo de criação de um teste na Web de várias etapas com Visual Studio Enterprise.

> [!NOTE]
> Testes na Web de várias etapas dependem de arquivos do Visual Studio WebTest. Foi [anunciado](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) que o Visual Studio 2019 será a última versão com a funcionalidade WebTest. É importante entender que, embora nenhum novo recurso seja adicionado, a funcionalidade do WebTest no Visual Studio 2019 ainda é suportada e continuará a ter suporte durante o ciclo de vida do suporte do produto. A equipe de produto Azure Monitor resolveu perguntas sobre o futuro de testes de disponibilidade de várias etapas [aqui](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  

## <a name="pre-requisites"></a>Pré-requisitos

* Visual Studio 2017 Enterprise ou superior.
* Ferramentas de teste de carga e desempenho na Web do Visual Studio.

Para localizar o pré-requisito das ferramentas de teste. Inicie o **Instalador do Visual Studio**  > **componentes individuais**  > **depuração e teste**  > **ferramentas de teste de carga e desempenho da Web**.

![Captura de tela da interface do usuário do instalador do Visual Studio com componentes individuais selecionados com uma caixa de seleção ao lado do item para ferramentas de teste de carga e desempenho na Web](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Testes na Web de várias etapas têm custos adicionais associados a eles. Para saber mais, consulte o [Guia de preços oficial](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Registrar um teste na Web de várias etapas

Para criar um teste de várias etapas, registre o cenário usando Visual Studio Enterprise e, em seguida, carregue a gravação no Application Insights. Application Insights repete o cenário em intervalos definidos e verifica a resposta.

> [!IMPORTANT]
> * Você não pode usar funções codificadas ou loops em seus testes. O teste deve estar contido completamente no script. WebTest. No entanto, você pode usar plug-ins padrão.
> * Somente caracteres em inglês têm suporte nos testes na Web de várias etapas. Se você usar o Visual Studio em outras linguagens, atualize o arquivo de definição de teste da Web para converter/excluir caracteres que não são do inglês.

Use o Visual Studio Enterprise para registrar uma sessão da Web.

1. Crie um projeto de teste de carga e desempenho na Web. **Arquivo**  > **novo** **projeto** de  >   > **teste** do**Visual C#**   > 

    ![IU do novo projeto do Visual Studio](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Abra o arquivo `.webtest` e inicie a gravação.

    ![IU de gravação de teste do Visual Studio](./media/availability-multistep/open-web-test.png)

3. Clique nas etapas que você deseja que o teste simule para simular como parte da gravação.

    ![IU de gravação do navegador](./media/availability-multistep/record.png)

4. Edite o teste para:

    * Adicione validações para verificar o texto recebido e os códigos de resposta.
    * Remova todas as interações de uneccesary. Você também pode remover solicitações dependentes de imagens ou adicionar sites de acompanhamento que não são relevantes para você Considerando seu teste de sucesso.
    
    Tenha em mente que você só pode editar o script de teste – você pode adicionar um código personalizado ou chamar outros testes da Web. Não insira loops no teste. Você pode usar plug-ins de teste na Web padrão.

5. Execute o teste no Visual Studio para validar e verificar se ele funciona.

    O executor de teste da Web abre um navegador da Web e repete as ações registradas. Certifique-se de que tudo se comporta conforme o esperado.

## <a name="upload-the-web-test"></a>Carregar o teste na Web

1. No portal de Application Insights no painel disponibilidade, selecione **criar teste**  > **tipo de teste**  > **teste na Web de várias etapas**.

2. Defina os locais de teste, a frequência e os parâmetros de alerta.

### <a name="frequency--location"></a>Local de & de frequência

|Configuração| Explicação
|----|----|----|
|**Frequência de teste**| Define a frequência com que o teste é executado a partir de cada local de teste. Com uma frequência padrão de cinco minutos e cinco locais de teste, seu site é testado em média a cada minuto.|
|**Locais de teste**| São os locais de onde nossos servidores enviam solicitações da Web para sua URL. **Nosso número mínimo de locais de teste recomendados é cinco** para garantir que você possa distinguir problemas em seu site por meio de problemas de rede. Você pode selecionar até 16 locais.

### <a name="success-criteria"></a>Critérios de sucesso

|Configuração| Explicação
|----|----|----|
| **Tempo limite do teste** |Diminua esse valor para ser alertado sobre respostas lentas. O teste será contado como uma falha se as respostas do seu site não tiverem sido recebidas nesse período. Se você tiver selecionado **analisar solicitações dependentes**, todas as imagens, arquivos de estilo, scripts e outros recursos dependentes deverão ser recebidos dentro desse período.|
| **Resposta HTTP** | O código de status retornado que é contado como êxito. 200 é o código que indica que uma página da Web normal foi retornada.|
| **Correspondência de conteúdo** | Uma cadeia de caracteres, como "bem-vindo!" Testamos se uma correspondência exata que diferencia maiúsculas de minúsculas ocorre em todas as respostas. Ele deve ser uma cadeia de caracteres simples, sem curingas. Não se esqueça de que, se o conteúdo da página for alterado, talvez seja necessário atualizá-lo. **Somente caracteres em inglês têm suporte com correspondência de conteúdo** |

### <a name="alerts"></a>Alertas

|Configuração| Explicação
|----|----|----|
|**Quase em tempo real (visualização)** | É recomendável usar alertas quase em tempo real. A configuração desse tipo de alerta é feita após a criação do teste de disponibilidade.  |
|**Clássico** | Não recomendamos o uso de alertas clássicos para novos testes de disponibilidade.|
|**Limite de local de alerta**|Recomendamos um mínimo de 3/5 locais. A relação ideal entre o limite de local de alerta e o número de locais de teste é o **limite de local de alerta**  = **número de locais de teste-2, com um mínimo de cinco locais de teste.**|

## <a name="advanced-configuration"></a>Configuração avançada

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Tempo de conexão e números aleatórios em seu teste

Suponha que você esteja testando uma ferramenta que obtém dados dependentes de tempo, como ações de um feed externo. Ao registrar seu teste na Web, você precisa usar horários específicos, mas defini-los como parâmetros do teste, StartTime e EndTime.

![Captura de tela de meu aplicativo de ações incríveis](./media/availability-multistep/app-insights-72webtest-parameters.png)

Quando você executa o teste, deseja que EndTime sempre seja a hora atual e StartTime deva ser 15 minutos atrás.

O plug-in de data e hora do teste na Web fornece a maneira de lidar com os tempos de parametrização.

1. Adicione um plug-in de teste na Web para cada valor de parâmetro variável desejado. Na barra de ferramentas de teste na Web, escolha **Adicionar plug-in de teste da Web**.
    
    ![Adicionar plug-in de teste na Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    Neste exemplo, usamos duas instâncias do plug-in de data e hora. Uma instância é por "15 minutos atrás" e outra para "agora".

2. Abra as propriedades de cada plug-in. Dê um nome a ele e defina-o para usar a hora atual. Para um deles, defina adicionar minutos =-15.

    ![Parâmetros de contexto](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Nos parâmetros de teste na Web, use {{nome do plug-in}} para fazer referência a um nome de plug-in.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Agora, carregue seu teste no Portal. Ele usará os valores dinâmicos em todas as execuções do teste.

### <a name="dealing-with-sign-in"></a>Lidando com a entrada

Se os usuários entrarem em seu aplicativo, você terá várias opções para simular a entrada para que você possa testar as páginas por trás da entrada. A abordagem usada depende do tipo de segurança fornecido pelo aplicativo.

Em todos os casos, você deve criar uma conta em seu aplicativo apenas para fins de teste. Se possível, restrinja as permissões dessa conta de teste para que não haja possibilidade de testes na Web que afetem usuários reais.

**Nome de usuário e senha simples** Registre um teste na Web da maneira usual. Exclua os cookies primeiro.

**Autenticação SAML**

|Nome da propriedade| Descrição|
|----|-----|
| URI do público | O URI do público-alvo do token SAML.  Esse é o URI para o serviço de controle de acesso (ACS) – incluindo o nome do host e o namespace do ACS. |
| Senha do certificado | A senha para o certificado de cliente que concederá acesso à chave privada inserida. |
| Certificado do cliente  | O valor do certificado de cliente com a chave privada no formato codificado em base64. |
| Identificador de nome | O identificador de nome para o token |
| Não após | O TimeSpan para o qual o token será válido.  O padrão é 5 minutos. |
| Não Antes De | O TimeSpan para o qual um token criado no passado será válido (para endereçar distorções de tempo).  O padrão é (negativo) 5 minutos. |
| Nome do parâmetro de contexto de destino | O parâmetro de contexto que receberá a declaração gerada. |


**Segredo do cliente** Se seu aplicativo tiver uma rota de entrada que envolva um segredo do cliente, use essa rota. Azure Active Directory (AAD) é um exemplo de um serviço que fornece uma entrada de segredo do cliente. No AAD, o segredo do cliente é a chave do aplicativo.

Veja um exemplo de teste na Web de um aplicativo Web do Azure usando uma chave de aplicativo:

![Captura de tela de exemplo](./media/availability-multistep/client-secret.png)

Obter o token do AAD usando o segredo do cliente (AppKey).
Extraia o token de portador da resposta.
Chame a API usando o token de portador no cabeçalho de autorização.
Verifique se o teste na Web é um cliente real, ou seja, se ele tem seu próprio aplicativo no AAD, e use sua chave de aplicativo clientId +. Seu serviço em teste também tem seu próprio aplicativo no AAD: o URI do appID deste aplicativo é refletido no teste na Web no campo de recurso.

### <a name="open-authentication"></a>Autenticação aberta
Um exemplo de autenticação aberta é entrar com sua conta da Microsoft ou do Google. Muitos aplicativos que usam o OAuth fornecem a alternativa de segredo do cliente, portanto, sua primeira tática deve ser investigar essa possibilidade.

Se o teste tiver de entrar usando OAuth, a abordagem geral será:

Use uma ferramenta como o Fiddler para examinar o tráfego entre o navegador da Web, o site de autenticação e seu aplicativo.
Execute duas ou mais entradas usando diferentes computadores ou navegadores, ou em intervalos longos (para permitir que os tokens expirem).
Ao comparar sessões diferentes, identifique o token passado de volta do site de autenticação, que é passado para o servidor de aplicativos após a entrada.
Registre um teste na Web usando o Visual Studio.
Parametrizar os tokens, definir o parâmetro quando o token for retornado do autenticador e usá-lo na consulta ao site. (O Visual Studio tenta parametrizar o teste, mas não parametriza corretamente os tokens.)

## <a name="troubleshooting"></a>solução de problemas

[Artigo de solução de problemas](troubleshoot-availability.md)dedicado.

## <a name="next-steps"></a>Próximos passos

* [Alertas de disponibilidade](availability-alerts.md)
* [Testes da Web de ping de URL](monitor-web-app-availability.md)
