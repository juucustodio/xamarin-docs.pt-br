## <a name="firewall-configuration"></a>Configuração do firewall

Em ordem para os testes sejam enviados para o Xamarin Test Cloud, o computador de envio de testes deve ser capaz de se comunicar com os servidores de teste de nuvem. Firewalls devem ser configurados para permitir o tráfego de rede de e para os servidores localizados em **testcloud.xamarin.com** nas portas 80 e 443. Esse ponto de extremidade é gerenciado pelo DNS e o endereço IP está sujeita a alterações. 

Em algumas situações, um teste (ou um dispositivo que executa o teste) deve se comunicar com servidores web protegidos por um firewall. Nesse cenário, o firewall deve ser configurado para permitir o tráfego de endereços IP a seguir:

* **195.249.159.238**
* **195.249.159.239**
