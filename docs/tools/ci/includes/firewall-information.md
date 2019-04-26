---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61047420"
---
## <a name="firewall-configuration"></a>Configuração do firewall

Em ordem para os testes sejam enviados para o Xamarin Test Cloud, o computador de envio de testes deve ser capaz de se comunicar com os servidores de teste de nuvem. Firewalls devem ser configurados para permitir o tráfego de rede de e para os servidores localizados em **testcloud.xamarin.com** nas portas 80 e 443. Esse ponto de extremidade é gerenciado pelo DNS e o endereço IP está sujeita a alterações. 

Em algumas situações, um teste (ou um dispositivo que executa o teste) deve se comunicar com servidores web protegidos por um firewall. Nesse cenário, o firewall deve ser configurado para permitir o tráfego de endereços IP a seguir:

* **195.249.159.238**
* **195.249.159.239**
