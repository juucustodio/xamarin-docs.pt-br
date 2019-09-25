---
ms.openlocfilehash: d46968f9c53314abe561e7f4871cfbf6e07b7002
ms.sourcegitcommit: 699de58432b7da300ddc2c85842e5d9e129b0dc5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2019
ms.locfileid: "61047420"
---
## <a name="firewall-configuration"></a>Configuração do firewall

Para que os testes sejam enviados para Xamarin Test Cloud, o computador que envia os testes deve ser capaz de se comunicar com os servidores de Test Cloud. Os firewalls devem ser configurados para permitir o tráfego de rede de e para os servidores localizados em **testcloud.xamarin.com** nas portas 80 e 443. Esse ponto de extremidade é gerenciado pelo DNS e o endereço IP está sujeito a alterações. 

Em algumas situações, um teste (ou um dispositivo que executa o teste) deve se comunicar com servidores Web protegidos por um firewall. Nesse cenário, o firewall deve ser configurado para permitir o tráfego dos seguintes endereços IP:

* **195.249.159.238**
* **195.249.159.239**
