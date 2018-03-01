## <a name="firewall-configuration"></a>Configuração de firewall

Em ordem para testes sejam enviados para Xamarin Test Cloud, o computador enviando os testes deve ser capaz de se comunicar com os servidores de nuvem de teste. Os firewalls devem ser configurados para permitir o tráfego de rede de e para os servidores localizados em **testcloud.xamarin.com** nas portas 80 e 443. Esse ponto de extremidade é gerenciado pelo DNS e o endereço IP está sujeita a alterações. 

Em algumas situações, um teste (ou um dispositivo que executa o teste) deve se comunicar com servidores web protegidos por um firewall. Neste cenário, o firewall deve ser configurado para permitir o tráfego de endereços IP a seguir:

* **195.249.159.238**
* **195.249.159.239**
