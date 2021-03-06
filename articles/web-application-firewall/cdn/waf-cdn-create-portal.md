---
title: 'Tutorial: Criar política do WAF para a CDN do Azure – portal do Azure'
description: Neste tutorial, você aprenderá a criar uma política de WAF (Firewall do Aplicativo Web) na CDN do Azure usando o portal do Azure.
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: c5505b9437a4bd8dced6a090817b17d5e29374f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327931"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>Tutorial: Criar uma política de WAF na CDN do Azure usando o portal do Azure

Este tutorial mostra a você como criar uma política básica de WAF (Firewall do Aplicativo Web) do Azure e aplicá-la a um ponto de extremidade da CDN (Rede de Distribuição de Conteúdo) do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma política de WAF
> * Associá-la a um ponto de extremidade da CDN. Você pode associar uma política WAF somente a pontos de extremidade hospedados no SKU da **CDN do Azure Standard da Microsoft**.
> * Configurar regras de WAF

## <a name="prerequisites"></a>Pré-requisitos

Crie um perfil e um ponto de extremidade da CDN do Azure seguindo as instruções em [Início rápido: Criar um ponto de extremidade e um perfil de CDN do Azure](../../cdn/cdn-create-new-endpoint.md). 

## <a name="create-a-web-application-firewall-policy"></a>Criar uma política de Firewall do Aplicativo Web

Primeiro, crie uma política básica de WAF com um DRS (Conjunto de Regras Padrão) gerenciado usando o portal.

1. No canto superior esquerdo da tela, selecione **Criar um recurso**> pesquise por **WAF**> selecione **Firewall do aplicativo Web** > selecione **Criar**.
2. Na guia **Noções básicas** da página **Criar uma política de WAF**, insira ou selecione as seguintes informações, aceite os padrões das configurações restantes e selecione **Examinar + criar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Política para            |Selecione a CDN do Azure (Versão Prévia).|
    | Subscription            |Selecione o nome da assinatura do Front Door.|
    | Resource group          |Selecione o nome do grupo de recursos do Front Door.|
    | Nome de política             |Insira um nome exclusivo para a política de WAF.|

   ![Criar uma política de WAF](../media/waf-cdn-create-portal/basic.png)

3. Na guia **Associação** da página **Criar uma política de WAF**, selecione **Adicionar um ponto de extremidade da CDN**, insira as configurações a seguir e clique em **Adicionar**:

    | Configuração                 | Valor                                              |
    | ---                     | ---                                                |
    | Perfil da CDN              | Selecione o nome do seu perfil CDN.|
    | Ponto de extremidade           | Selecione o nome do ponto de extremidade e selecione **Adicionar**.|
    
    > [!NOTE]
    > Se o ponto de extremidade estiver associado a uma política de WAF, ele será exibido esmaecido. Primeiro, é preciso remover o ponto de extremidade da política associada e, em seguida, associá-lo novamente à nova política de WAF.
1. Selecione **Examinar + criar**e **Criar**.

## <a name="configure-web-application-firewall-policy-optional"></a>Configurar política de Firewall do Aplicativo Web (opcional)

### <a name="change-mode"></a>Alterar o modo

Ao criar uma política de WAF, por padrão, ela fica em modo de *Detecção*. No modo de *Detecção*, o WAF não bloqueia nenhuma solicitação. Em vez disso, as solicitações que correspondem às regras de WAF são registradas em logs do WAF.

Para ver o WAF na prática, altere as configurações de modo de *Detecção* para *Prevenção*. No modo de *Prevenção*, as solicitações que cumprem as regras definidas no DRS (Conjunto de Regras Padrão) são bloqueadas e registradas em logs do WAF.

 ![Alterar o modo de política WAF](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>Regras personalizadas

Para criar uma regra personalizada, selecione **Adicionar regra personalizada** na seção **Regras personalizadas**. Isso abre a página de configuração de regras personalizadas. Há dois tipos de regras personalizadas: **regra de correspondência** e **regra de limite de taxa**.

A captura de tela a seguir mostra uma regra de correspondência personalizada para bloquear uma solicitação se a cadeia de caracteres de consulta contém o valor **blockme**.

![Adicionar regra de correspondência personalizada](../media/waf-cdn-create-portal/custommatch.png)

As regras de limite de taxa exigem dois campos adicionais: **Duração do limite de taxa** e **Limiar do limite de taxa (solicitações)** , conforme mostrado no seguinte exemplo:

![Adicionar regra de limite de taxa](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>DRS (Conjunto de Regras Padrão)

O Conjunto de Regras Padrão gerenciado pelo Azure é habilitado por padrão. Para desabilitar uma regra individual em um grupo de regras, expanda as regras nesse grupo, marque a caixa de seleção ao lado do número da regra e clique em **Desabilitar** na guia acima. Para alterar os tipos de ações para regras individuais no grupo de regras, marque a caixa de seleção ao lado do número da regra e clique em **Alterar ação** na guia acima.

 ![Alterar conjunto de regras de WAF](../media/waf-cdn-create-portal/managed2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Remova o grupo de recursos e todos os recursos relacionados quando eles não forem mais necessários.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre o Firewall do Aplicativo Web do Azure](../overview.md)
