---
title: Sobre os preços da WAN virtual
titleSuffix: Azure Virtual WAN
description: Este artigo descreve as perguntas comuns sobre preços de WAN virtual
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: b4025990a1a62351d3971d788558dea8ecb390ba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327950"
---
# <a name="about-virtual-wan-pricing"></a>Sobre os preços da WAN virtual

A WAN virtual do Azure traz vários serviços de rede e segurança juntos em uma estrutura unificada. Ele se baseia em uma arquitetura de Hub e spoke, em que os hubs são gerenciados pela Microsoft com vários serviços fornecidos no Hub, como VPN, ExpressRoute, VPN de usuário (ponto a site), firewall, roteamento, etc.

Cada serviço na WAN virtual é cobrado. Portanto, sugerir um preço único não é aplicável à WAN virtual. A [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) fornece um mecanismo para derivar o custo, que se baseia nos serviços provisionados em uma WAN virtual. Este artigo aborda as perguntas mais frequentes sobre os preços da WAN virtual.

>[!NOTE]
>Para obter informações de preços atuais, consulte [preços de WAN virtual](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

## <a name="common-pricing-questions"></a><a name="questions"></a>Perguntas comuns sobre preços

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>O que é uma unidade de escala?

Uma **unidade de escala** fornece a unidade para a capacidade de agregação de S2S (site a site), ponto a site (P2S) e EXPRESSROUTE (er) em um hub virtual. Por exemplo:

* **1 unidade de escala de VPN S2S** implica uma capacidade total de gateway de vpn de 500 Mbps (instâncias duplas implantadas para resiliência) em um hub virtual custando $0.361/hora.
* **1 unidade de escala er** implica um total de 2 Gbps de gateway er no Hub virtual custando $0,42/hr.
* **5 unidades de escala de er** implicariam um total de 10 Gbps de gateway de er dentro de uma VNet de Hub virtual com preço de US $0,42 * 5/hr. ER incrementa $0,25/h da sexta para 10º unidade de escala.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>O que é uma unidade de conexão?

Uma **unidade de conexão** se aplica a qualquer ponto de extremidade local/não-Microsoft que se conecte aos gateways do Azure. Para VPN site a site, isso implica ramificações. Para VPN de usuário (ponto a site), isso implica usuários remotos. Para o ExpressRoute, isso implica conexões de circuito do ExpressRoute.<br>Por exemplo:

* Uma conexão de ramificação conectando-se à VPN do Azure em um hub virtual custa US $0,05/h. Portanto, as conexões de ramificação 100 que se conectam a um hub virtual do Azure custarão US $0,05 * 100/hr.

* Duas conexões de circuito do ExpressRoute conectando-se a um hub virtual custam US $0,05 * 2/hr.

* Três conexões de usuário remotas conectando-se ao gateway P2S do Hub virtual do Azure custarão $0,03 * 3/hr.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Como as cobranças de transferência de dados são calculadas?

* Qualquer tráfego que insira o Azure não será cobrado. O tráfego que sai do Azure (via VPN, ExpressRoute ou conexões de VPN de usuário ponto a site) está sujeito aos [encargos de transferência de dados do Azure](https://azure.microsoft.com/pricing/details/bandwidth/)padrão.

* Para encargos de transferência de dados entre um hub de WAN virtual e um hub remoto de WAN virtual ou VNet em uma região diferente do hub de origem, as tarifas de transferência de dados se aplicam para o tráfego que sai de um Hub. Exemplo: o tráfego que sai de um hub leste dos EUA será cobrado $0,02/GB para um hub oeste dos EUA. Não há nenhum encargo para o tráfego entrando no Hub oeste dos EUA. As tabelas a seguir mostram os encargos.

As tabelas a seguir usam as seguintes abreviações: {nome: América do Norte}, {UE: Europa}, {MEA: África do Oriente Médio}, {OC: Oceania (Austrália Central e Austrália Central 2)}, {LATAm: América Latina} 

**Preço dentro do continente (*)**

| Intra-Continent| Preço ($/GB)|
|---|---|
| NAM to NAM|US$ 0,02 |
| UE para a UE |US$ 0,02 |
| Ásia-Ásia (exceto China)|$0.10 |
| MEA MEA|$0.16 |
| LATAÉ-LATA |$0.16 |
| OC-OC|$0.12 |

**Preço entre continental (*)**

| Inter-Continental| Preço ($/GB)|
|---|---|
| DE bepara a UE ou para a UE |$0.07 |
| DO inferem a qualquer lugar |$0.17 |
| DE MEA para qualquer lugar |$0.17 |
| DE OCEANIA para qualquer lugar |$0.12 |
| DA Ásia (exceto a CHINA) a qualquer lugar |$0.12 |

(*) Alguns encargos podem ser aplicados a partir de 1º de agosto de 2020.

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Qual é a diferença entre uma taxa de Hub padrão e uma taxa de processamento de Hub padrão?

A WAN virtual vem em dois tipos:

* Uma **Wan virtual básica**, em que os usuários podem implantar vários hubs e aproveitar a conectividade VPN site a site. Uma WAN virtual básica não tem recursos avançados, como hubs totalmente entrelaçados, conectividade de ExpressRoute, conectividade de VPN de ponto a site/VPN de usuário, conectividade transitiva de VNet para VNet, conectividade de trânsito de VPN e ExpressRoute ou firewall do Azure, etc. Não há nenhuma taxa base ou taxa de processamento de dados para hubs em uma WAN virtual básica.

* Uma **Wan virtual padrão** fornece recursos avançados, como hubs totalmente entrelaçados, conectividade de expressroute, conectividade VPN de usuário/ponto a site, conectividade transitiva de VNet para vnet, conectividade de trânsito de VPN e ExpressRoute, firewall do Azure, etc. Todo o roteamento de Hub virtual é fornecido por um roteador que habilita vários serviços em um hub virtual. Há uma taxa base para o Hub, cujo preço é de $0,25/hr. Também há um encargo para o processamento de dados no roteador do Hub virtual para conectividade de trânsito de VNet para VNet. Consulte a figura a seguir.

 Na figura de **exemplo** abaixo, há dois VNETS, vnet 1 e vnet 2. Vamos supor que há 1 Gbps de dados sendo enviados de uma VM na VNET 1 para outra VM na VNET 2. As tarifas a seguir se aplicam:

* Taxa base do Hub virtual $0,25/hora

* Taxa de processamento de dados de Hub virtual de $0,02/GB para 1 Gbps

**Exemplo**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Exemplo":::

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a WAN Virtual, veja as [Perguntas frequentes](virtual-wan-faq.md).

* Para obter os preços atuais, consulte [preços de WAN virtual](https://azure.microsoft.com/pricing/details/virtual-wan/).
