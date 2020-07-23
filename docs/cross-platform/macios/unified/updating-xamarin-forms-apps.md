---
title: Atualizando aplicativos Xamarin. Forms existentes
description: Este documento descreve as etapas que devem ser seguidas para atualizar um aplicativo Xamarin. Forms do API Clássica para a API Unificada.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: 620b941d7e6ac50d716916cdde18fe9aed0b0709
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86929591"
---
# <a name="updating-existing-xamarinforms-apps"></a>Atualizando aplicativos Xamarin. Forms existentes

_Siga estas etapas para atualizar um aplicativo Xamarin. Forms existente para usar o API Unificada e atualizar para a versão 1.3.1_

> [!IMPORTANT]
> Como o Xamarin. Forms 1.3.1 é a primeira versão que dá suporte ao API Unificada, toda a solução deve ser atualizada para usar a versão mais recente ao mesmo tempo que migrar o aplicativo iOS para unificada. Isso significa que, além de atualizar o projeto do iOS para o suporte unificado, você também precisará editar o código em _todos_ os projetos na solução.

A atualização é executada em duas etapas:

1. Migre o aplicativo iOS para o API Unificada usando a ferramenta de migração de Visual Studio para Mac de desenvolvimento.

    - Use a ferramenta de migração para atualizar automaticamente o projeto.

    - Atualize as APIs nativas do iOS conforme descrito nas instruções para [atualizar aplicativos do IOS](~/cross-platform/macios/unified/updating-ios-apps.md) (especificamente no processador personalizado ou no código do serviço de dependência).

2. Atualize a solução inteira para Xamarin. Forms versão 1,3.

    1. Instale o pacote NuGet do Xamarin. Forms 1.3.1.

    2. Atualize a `App` classe no código compartilhado.

    3. Atualize o `AppDelegate` no projeto do Ios.

    4. Atualize o `MainActivity` no projeto do Android.

    5. Atualize o `MainPage` no projeto Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. aplicativo iOS (migração unificada)

Parte da migração requer a atualização do Xamarin. Forms para a versão 1,3, que dá suporte ao API Unificada. Para que as referências de assembly corretas sejam criadas, primeiro precisamos atualizar o projeto do iOS para usar o API Unificada.

### <a name="migration-tool"></a>Ferramenta de migração

Clique no projeto do iOS para que ele seja selecionado e escolha **projeto > migrar para Xamarin. iOS API unificada...** e concorde com a mensagem de aviso que aparece.

![Escolha o projeto > migrar para o Xamarin. iOS API Unificada... e concordo com a mensagem de aviso que aparece](updating-xamarin-forms-apps-images/beta-tool1.png)

Isso irá automaticamente:

- Altere o tipo de projeto para dar suporte à API unificada de 64 bits.
- Altere a referência de estrutura para **Xamarin. Ios** (substituindo a referência **MonoTouch** antiga).
- Altere as referências de namespace no código para remover o `MonoTouch` prefixo.
- Atualize o arquivo **csproj** para usar os destinos de compilação corretos para o API unificada.

**Limpe** e **compile** o projeto para garantir que não haja nenhum outro erro a ser corrigido. Nenhuma ação adicional deve ser necessária. Essas etapas são explicadas em mais detalhes no [API unificada docs](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Atualizar APIs nativas do iOS (se necessário)

Se você tiver adicionado código nativo do iOS adicional (como renderizadores personalizados ou serviços de dependência), talvez seja necessário executar correções de código manuais adicionais. Recompile seu aplicativo e consulte as [instruções Atualizando aplicativos Ios existentes](~/cross-platform/macios/unified/updating-ios-apps.md) para obter informações adicionais sobre as alterações que podem ser necessárias. [Essas dicas](~/cross-platform/macios/unified/updating-tips.md) também ajudarão a identificar as alterações necessárias.

## <a name="2-xamarinforms-131-update"></a>2. Xamarin. Forms 1.3.1 Update

Depois que o aplicativo iOS tiver sido atualizado para o API Unificada, o restante da solução precisará ser atualizado para o Xamarin. Forms versão 1.3.1. Isso inclui:

- Atualizando o pacote NuGet do Xamarin. Forms em cada projeto.
- Alterar o código para usar as novas classes Xamarin. Forms `Application` , `FormsApplicationDelegate` (Ios), `FormsApplicationActivity` (Android) e `FormsApplicationPage` (Windows Phone).

Estas etapas são explicadas abaixo:

### <a name="21-update-nuget-in-all-projects"></a>2,1 atualizar o NuGet em todos os projetos

Atualize Xamarin. Forms para 1.3.1 pré-lançamento usando o Gerenciador de pacotes NuGet para todos os projetos na solução: PCL (se presente), iOS, Android e Windows Phone. É recomendável que você **exclua e adicione novamente** o pacote NuGet do Xamarin. Forms para atualizar para a versão 1,3.

> [!NOTE]
> O Xamarin. Forms versão 1.3.1 está atualmente em *pré-lançamento*. Isso significa que você deve selecionar a opção de **pré-lançamento** no NuGet (por meio de uma caixa de marcação no Visual Studio para Mac ou uma lista suspensa no Visual Studio) para ver a versão de pré-lançamento mais recente.

> [!IMPORTANT]
> Se você estiver usando o Visual Studio, verifique se a versão mais recente do Gerenciador de pacotes NuGet está instalada. As versões mais antigas do NuGet no Visual Studio não instalarão corretamente a versão unificada do Xamarin. Forms 1.3.1. Acesse **ferramentas > extensões e atualizações...** e clique na lista **instalada** para verificar se o **Gerenciador de pacotes NuGet para Visual Studio** tem pelo menos a versão 2.8.5. Se for mais antigo, clique na lista de **atualizações** para baixar a versão mais recente.

Depois de atualizar o pacote NuGet para Xamarin. Forms 1.3.1, faça as seguintes alterações em cada projeto para atualizar para a nova `Xamarin.Forms.Application` classe.

### <a name="22-portable-class-library-or-shared-project"></a>2,2 biblioteca de classes portátil (ou projeto compartilhado)

Altere o arquivo **app.cs** para que:

- A `App` classe agora é herdada de `Application` .
- A `MainPage` propriedade é definida como a primeira página de conteúdo que você deseja exibir.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Removemos completamente o `GetMainPage` método e, em vez disso, definimos a `MainPage` *Propriedade* na `Application` subclasse.

Essa nova `Application` classe base também dá suporte `OnStart` às `OnSleep` `OnResume` substituições, e para ajudá-lo a gerenciar o ciclo de vida do aplicativo.

`App`Em seguida, a classe é passada para um novo `LoadApplication` método em cada projeto de aplicativo, conforme descrito abaixo:

### <a name="23-ios-app"></a>Aplicativo iOS 2,3

Altere o arquivo **AppDelegate.cs** para que:

- A classe é herdada de `FormsApplicationDelegate` (em vez de `UIApplicationDelegate` anteriormente).
- `LoadApplication`é chamado com uma nova instância do `App` .

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>2,3 aplicativo Android

Altere o arquivo **MainActivity.cs** para que:

- A classe é herdada de `FormsApplicationActivity` (em vez de `FormsActivity` anteriormente).
- `LoadApplication`é chamado com uma nova instância do`App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>2,4 Windows Phone aplicativo

Precisamos atualizar a **MainPage** -tanto o XAML quanto o codebehind.

Altere o arquivo **MainPage. XAML** para que:

- O elemento XAML raiz deve ser `winPhone:FormsApplicationPage` .
- O `xmlns:phone` atributo deve ser *alterado* para`xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Um exemplo atualizado é mostrado abaixo-você só deve editar essas coisas (o restante dos atributos deve permanecer o mesmo):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Altere o arquivo **MainPage.XAML.cs** para que:

- A classe é herdada de `FormsApplicationPage` (em vez de `PhoneApplicationPage` anteriormente).
- `LoadApplication`é chamado com uma nova instância da classe Xamarin. Forms `App` . Talvez seja necessário qualificar totalmente essa referência, pois Windows Phone tem sua própria `App` classe já definida.

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>Solução de problemas

Ocasionalmente, você verá um erro semelhante a este depois de atualizar o pacote NuGet do Xamarin. Forms. Isso ocorre quando o atualizador do NuGet não remove completamente as referências a versões mais antigas dos seus arquivos **csproj** .

>SEU \_ Project. csproj: erro: este projeto faz referência a pacotes NuGet ausentes neste computador. Habilite a restauração do pacote NuGet para baixá-los.  Para obter mais informações, consulte https://go.microsoft.com/fwlink/?LinkID=322105. O arquivo ausente é.. /.. /packages/Xamarin.Forms.1.2.3.6257/build/portable-win + Net45 + wp80 + MonoAndroid10 + MonoTouch10/Xamarin. Forms. targets. (Seu \_ PROJETO

Para corrigir esses erros, abra o arquivo **csproj** em um editor de texto e procure `<Target` os elementos que se referem a versões mais antigas do Xamarin. Forms, como o elemento mostrado abaixo. Você deve excluir manualmente esse elemento inteiro do arquivo **csproj** e salvar as alterações.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see https://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

O projeto deve ser compilado com êxito depois que essas referências antigas forem removidas.

## <a name="considerations"></a>Considerações

As considerações a seguir devem ser levadas em conta ao converter um projeto Xamarin. Forms existente do API Clássica para o novo API Unificada se esse aplicativo depender de um ou mais componentes ou pacote NuGet.

### <a name="components"></a>Componentes

Qualquer componente que você tenha incluído em seu aplicativo também precisará ser atualizado para o API Unificada ou você receberá um conflito quando tentar compilar. Para qualquer componente incluído, substitua a versão atual por uma nova versão da loja de componentes do Xamarin que dá suporte à API Unificada e faça uma compilação limpa. Qualquer componente que ainda não tenha sido convertido pelo autor, exibirá um aviso de 32 bits apenas no repositório de componentes.

### <a name="nuget-support"></a>Suporte do NuGet

Embora tenhamos contribuído com alterações no NuGet para trabalhar com o suporte de API Unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter o NuGet para reconhecer as novas APIs.

Até esse momento, assim como os componentes, você precisará alternar qualquer pacote NuGet que tenha incluído em seu projeto para uma versão que dê suporte às APIs unificadas e faça uma compilação limpa posteriormente.

> [!IMPORTANT]
> Se você tiver um erro no formato _"erro 3, não será possível incluir ' monotouch.dll ' e ' Xamarin.iOS.dll ' no mesmo Xamarin. o projeto do IOS-' Xamarin.iOS.dll ' é referenciado explicitamente, enquanto ' monotouch.dll ' é referenciado por ' xxx, Version = 0.0.000, Culture = neutral, PublicKeyToken = null '"_ depois de converter seu aplicativo para as APIs unificadas, normalmente, isso é devido à existência de um componente ou pacote NuGet no projeto que não foi atualizado para o API unificada. Você precisará remover o componente/NuGet existente, atualizar para uma versão que ofereça suporte a APIs unificadas e fazer uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitando Builds de 64 bits de aplicativos Xamarin. iOS

Para um aplicativo móvel Xamarin. iOS que foi convertido no API Unificada, o desenvolvedor ainda precisa habilitar a criação do aplicativo para computadores de 64 bits a partir das opções do aplicativo. Consulte as **compilações de 64 bits de aplicativos Xamarin. Ios** do documento [Considerações sobre plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) para obter instruções detalhadas sobre como habilitar os builds de 64 bits.

## <a name="summary"></a>Resumo

O aplicativo Xamarin. Forms agora deve ser atualizado para a versão 1.3.1 e o aplicativo iOS migrado para a API Unificada (que dá suporte a arquiteturas de 64 bits na plataforma iOS).

Conforme observado acima, se seu aplicativo Xamarin. Forms inclui código nativo como renderizadores personalizados ou serviços de dependência, eles também podem precisar de atualização para usar os novos tipos [introduzidos no API unificada](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Links Relacionados

- [Atualizando aplicativos iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Atualizando aplicativos iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Trabalhando com tipos nativos em aplicativos multiplataforma](~/cross-platform/macios/native-types-cross-platform.md)
- [Atualizando dicas](~/cross-platform/macios/unified/updating-tips.md)
- [Diferenças clássicas do vs API Unificada](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
