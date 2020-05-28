---
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="xaml-hot-reload-for-xamarinforms"></a>Hot recarregamento de XAML paraXamarin.Forms

Os Hot recarregamento de XAML são conectados ao fluxo de trabalho existente para aumentar a produtividade e poupar tempo. Sem o Hot recarregamento de XAML, você precisa compilar e implantar seu aplicativo toda vez que desejar ver uma alteração XAML. Com a recarga a quente, quando você salva o arquivo XAML, as alterações são refletidas em seu aplicativo em execução. Além disso, o estado de navegação e os dados serão mantidos, permitindo que você itere rapidamente na interface do usuário sem perder seu lugar no aplicativo. Portanto, com o Hot recarregamento de XAML, você gastará menos tempo recriando e implantando seus aplicativos para validar as alterações na interface do usuário.

> [!NOTE]
> Se você estiver escrevendo um aplicativo do WPF ou UWP, confira [Hot recarregamento de XAML para UWP e WPF](/visualstudio/debugger/xaml-hot-reload).
>
> O Hot recarregamento de Xamarin.Forms XAML _para não_ funciona atualmente para Xamarin.Forms projetos UWP.

## <a name="system-requirements"></a>Requisitos do sistema

| IDE/estrutura | Versão necessária |
|---
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---|---Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar as alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

-
Título: ' XAML quente recarregar para Xamarin.Forms ' Descrição: ' recarregar alterações no arquivo XAML instantaneamente em seu aplicativo em execução para que você não precise compilar seu Xamarin.Forms projeto após cada alteração em XAML. '
MS. Prod: MS. AssetID: MS. Technology: autor: MS. Author: MS. Date: no-loc:
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---------| | Visual Studio 2019 | 16,4 ou superior Visual Studio 2019 para Mac | 8,4 ou superior Xamarin.Forms | 4,1 ou superior

## <a name="enable-xaml-hot-reload-for-xamarinforms"></a>Habilitar o Hot recarregamento de XAML paraXamarin.Forms

Se você estiver iniciando com base em um modelo, o Hot recarregamento de XAML será ativado por padrão e o projeto será configurado para funcionar sem nenhuma configuração adicional. Depure seu aplicativo em um emulador do Android ou iOS, simulador ou dispositivo físico, altere o XAML e salve o arquivo para disparar um Hot recarregamento de XAML.

Se você estiver trabalhando a partir de uma Xamarin.Forms solução existente, nenhuma instalação adicional será necessária para usar o Hot recarregamento de XAML, mas talvez seja necessário verificar sua configuração para garantir a melhor experiência. Primeiro, habilite-o nas configurações do IDE:

* No Windows, marque a caixa de seleção **habilitar o WebLoad do xamarin** em **ferramentas**  >  **Opções**  >  **xamarin**  >  **Hot recarregar**.
* No Mac, marque a caixa de seleção **habilitar o Xamarin Hot reload** nas ferramentas de preferências do **Visual Studio**  >  **Preferences**  >  **para**  >  **recarregamento dinâmico**do xamarin.
  * Em versões anteriores do Visual Studio para Mac, o menu é em **Visual Studio**  >  **preferências**  >  **projetos**  >  **Xamarin Hot reload**.

Em seguida, em suas configurações de Build do Android e iOS, verifique se o vinculador está definido como "não vincular" ou "link nenhum". Para usar o recarregamento de XAML com um dispositivo IOS físico, você também precisa marcar **habilitar o intérprete mono** (Visual Studio 16,4 e superior) ou adicionar **--interpretador** para seus **args mTouch adicionais** (Visual Studio 16,3 e inferior).

Você pode usar o fluxograma a seguir para verificar a configuração do projeto existente para uso com o Hot recarregamento de XAML:

![Configuração de recarregamento dinâmico de XAML](hot-reload-images/hotreloadflowchart.png "Fluxograma de configuração de Hot recarregamento XAML")

## <a name="resilient-reloading"></a>Recarregamento resiliente

Se você fizer uma alteração que o Hot recarregamento de XAML não possa recarregar, ele mostrará uma mensagem de erro usando o IntelliSense. Essas alterações, conhecidas como edições rudes, incluem a digitação incorreta do XAML ou a ligação de um controle a um manipulador de eventos que não existe. Mesmo com uma edição rude, você pode continuar a recarregar sem reiniciar o aplicativo. faça outra alteração em outro lugar no arquivo XAML e clique em salvar. A edição rude não será recarregada, mas suas outras alterações continuarão sendo aplicadas.

## <a name="reload-on-multiple-platforms-at-once"></a>Recarregar em várias plataformas de uma vez

O Hot recarregamento de XAML dá suporte à depuração simultânea no Visual Studio e Visual Studio para Mac. Você pode implantar um Android e um destino iOS ao mesmo tempo para ver suas alterações refletidas em ambas as plataformas de uma só vez. Para depurar em várias plataformas, consulte:
* **Windows** [Como definir vários projetos de inicialização](https://docs.microsoft.com/visualstudio/ide/how-to-set-multiple-startup-projects?view=vs-2019)
* **Mac** [definir vários projetos de inicialização](https://docs.microsoft.com/visualstudio/mac/set-startup-projects?view=vsmac-2019)

## <a name="known-limitations"></a>Limitações conhecidas

* Outros Xamarin.Forms destinos, como UWP e MacOS, ainda *não* têm suporte. Você pode acompanhar o progresso do suporte a UWP [aqui](https://developercommunity.visualstudio.com/idea/661682/xaml-hot-reload-for-xamarinforms-on-uwp.html).
* Você não pode adicionar, remover ou renomear arquivos ou pacotes NuGet durante uma sessão de recarga ativa de XAML. Se você adicionar ou remover um arquivo ou pacote NuGet, recompile e reimplante seu aplicativo para continuar usando o Hot recarregamento de XAML.
* Defina seu vinculador para **não vincular** ou **vincular nenhum** para obter a melhor experiência. A configuração do **SDK do link somente** funciona na maior parte do tempo, mas pode falhar em determinados casos. As configurações do vinculador podem ser encontradas nas opções de Build do Android e iOS.
* A depuração em um iPhone físico exige que o intérprete use o Hot recarregamento de XAML. Para fazer isso, abra as configurações do projeto, selecione a guia Build do iOS e certifique-se **de habilitar a configuração do intérprete mono** está habilitada. Talvez seja necessário alterar a opção de **plataforma** na parte superior da página de propriedades para **iPhone**.
* Todas as referências criadas por meio da atribuição de um controle a outro campo ou propriedade usando seu `x:Name` valor não serão recarregadas.
* Atualizar a hierarquia visual do aplicativo Shell no AppShell. XAML pode causar problemas ao manter o estado do seu aplicativo. Se você tiver problemas, recompile o aplicativo para continuar recarregando.
* O Hot recarregamento de XAML não pode recarregar o código C#, incluindo manipuladores de eventos, controles personalizados, code-behind de página e classes adicionais.

## <a name="more-resources"></a>Mais recursos

* [Dicas e truques para o Hot recarregamento de XAML](https://devblogs.microsoft.com/xamarin/tips-tricks-xaml-hot-reload/)
* [Hot recarregamento de XAML para Xamarin.Forms detalhado: o Xamarin show](https://www.youtube.com/watch?v=crhjjPjzknk)

## <a name="troubleshooting"></a>Solução de problemas

* Se o Hot recarregamento do XAML falhar ao inicializar:
  * Atualize sua Xamarin.Forms versão.
  * Verifique se você está na versão mais recente do IDE.
  * Defina as configurações do vinculador do Android ou iOS para **não vincular** nas configurações de Build do projeto.
* Se nada acontecer ao salvar o arquivo XAML, verifique se o Hot recarregamento de XAML está habilitado no IDE.
* Se você estiver Depurando em um iPhone físico e seu aplicativo ficar sem resposta, verifique se o intérprete está habilitado. Para ativá-lo, marque **habilitar o intérprete mono** (Visual Studio 16.4/8.4 e superior) ou adicionar **--intérprete** para o campo **argumentos mTouch adicionais** (Visual Studio 16.3/8.3 e anterior) em suas configurações de Build do Ios.

Para relatar um bug, use **a ajuda**  >  **enviar comentários**para  >  **relatar um problema** no Windows e **ajudar**  >  **a relatar um problema** no Mac.
