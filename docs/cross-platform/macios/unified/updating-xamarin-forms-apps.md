---
title: Atualizando aplicativos xamarin. Forms existentes
description: "Siga estas etapas para atualizar um aplicativo xamarin. Forms existente para usar a API unificada e atualizar a versão 1.3.1"
ms.topic: article
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 48b8d1cf8e6242fde632bceec5d482f53037a954
ms.sourcegitcommit: 30055c534d9caf5dffcfdeafd6f08e666fb870a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2018
---
# <a name="updating-existing-xamarinforms-apps"></a>Atualizando aplicativos xamarin. Forms existentes

_Siga estas etapas para atualizar um aplicativo xamarin. Forms existente para usar a API unificada e atualizar a versão 1.3.1_

> [!IMPORTANT]
> Como xamarin. Forms 1.3.1 é a primeira versão que ofereça suporte a API unificado, toda a solução deve ser atualizada para usar a versão mais recente ao mesmo tempo como migrar o aplicativo do iOS para unificada. Isso significa que além de atualizar o projeto iOS unificada suporte, você também precisará editar o código no _todos os_ os projetos na solução.

A atualização é executada em duas etapas:

1. Migre o aplicativo do iOS para a API unificada usando o Visual Studio para a compilação do Mac na ferramenta de migração.

    - Use a ferramenta de migração para atualizar automaticamente o projeto.

    - Atualização iOS nativo APIs conforme descrito nas instruções para [atualizar aplicativos iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (especificamente no código de serviço de processador ou dependência personalizado).

2. Atualize toda a solução para xamarin. Forms versão 1.3.

    1. Instale o pacote do NuGet xamarin. Forms 1.3.1.

    2. Atualização de `App` classe no código compartilhado.

    3. Atualização de `AppDelegate` no projeto do iOS.

    4. Atualização de `MainActivity` no projeto Android.

    5. Atualização de `MainPage` no projeto do Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. iOS (migração unificado) do aplicativo

Parte da migração requer a atualização xamarin. Forms para a versão 1.3, que oferece suporte a API unificada. Em ordem para as referências de assembly correto a ser criado, primeiro, precisamos atualizar o projeto do iOS para usar a API unificada.

### <a name="migration-tool"></a>Ferramenta de migração

Clique no projeto iOS para que ele seja selecionado, escolha **projeto > migrar para o API unificada do xamarin...**  e Concordo com a mensagem de aviso será exibida.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Escolha o projeto > migrar para xamarin Unified API... e concordar com a mensagem de aviso é exibido")

Isso irá automaticamente:

 - Altere o tipo de projeto para dar suporte a API unificada de 64 bits.
 - Altere a referência de estrutura para **xamarin** (substituindo o antigo **monotouch** referência).
 - Altere as referências de namespace no código para remover o `MonoTouch` prefixo.
 - Atualização de **csproj** arquivo a usar os destinos de compilação correta para a API unificada.

**Limpar** e **Build** do projeto para garantir que não existem outros erros para corrigir. Nenhuma ação adicional é necessária. Essas etapas são explicadas em mais detalhes o [documentos unificado API](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Atualizar iOS nativo APIs (se necessário)

Se você tiver adicionado o código nativo do iOS adicionais (como processadores personalizados ou serviços de dependência), você precisará executar correções de código manual adicional. Compilar novamente o seu aplicativo e consulte o [instruções de aplicativos do iOS existente Atualizando](~/cross-platform/macios/unified/updating-ios-apps.md) para obter informações adicionais sobre as alterações que podem ser necessárias. [Estas dicas](~/cross-platform/macios/unified/updating-tips.md) também ajudam a identificar as alterações necessárias.

## <a name="2-xamarinforms-131-update"></a>2. Atualização do xamarin. Forms 1.3.1

Depois que o aplicativo iOS foi atualizado para a API unificada, o restante da solução precisa ser atualizado para xamarin. Forms versão 1.3.1. Isso inclui:

 - Atualizando o pacote do NuGet xamarin. Forms em cada projeto.
 - Alterar o código para usar o novo xamarin. Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android), e `FormsApplicationPage` classes (Windows Phone).

Essas etapas são explicadas abaixo:

### <a name="21-update-nuget-in-all-projects"></a>2.1 atualização NuGet em todos os projetos

Atualizar xamarin. Forms para 1.3.1 usando o NuGet Package Manager para todos os projetos na solução de pré-lançamento: PCL (se houver), iOS, Android e Windows Phone. É recomendável que você **excluir e adicionar novamente** o pacote NuGet do xamarin. Forms para atualizar a versão 1.3.

**Observação:** xamarin. Forms versão 1.3.1 está atualmente no *pré-lançamento*. Isso significa que você deve selecionar o **pré-lançamento** opção NuGet via (uma escala caixa no Visual Studio para Mac) ou uma lista suspensa de baixo no Visual Studio para ver a última versão de pré-lançamento.

> [!IMPORTANT]
> Se você estiver usando o Visual Studio, certifique-se de que a versão mais recente do NuGet Package Manager está instalada. Versões mais antigas do NuGet no Visual Studio não serão instalado a versão de unificada do xamarin. Forms 1.3.1 corretamente. Vá para **Ferramentas > extensões e atualizações...**  e clique no **instalado** lista para verificar se o **Gerenciador de pacotes do NuGet para Visual Studio** é pelo menos versão 2.8.5. Se ele for mais antigo, clique no **atualizações** lista para baixar a versão mais recente.

Após atualizar o pacote NuGet para xamarin. Forms 1.3.1, faça as seguintes alterações em cada projeto para atualizar para o novo `Xamarin.Forms.Application` classe.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 biblioteca de classes portátil (ou projeto compartilhado)

Alterar o **App.cs** arquivo de forma que:

 - O `App` classe agora herda de `Application`.
 - O `MainPage` estiver definida como a primeira página de conteúdo que você deseja exibir.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Removemos completamente o `GetMainPage` método e em vez disso, defina o `MainPage` *propriedade* no `Application` subclasse.

Essa nova `Application` classe base também oferece suporte a `OnStart`, `OnSleep`, e `OnResume` substituições para ajudá-lo a gerenciar o ciclo de vida do aplicativo.

O `App` classe é então passada para um novo `LoadApplication` método em cada projeto de aplicativo, conforme descrito abaixo:

### <a name="23-ios-app"></a>Aplicativo do 2.3 iOS

Alterar o **appdelegate. CS** arquivo de forma que:

 - A classe herda de `FormsApplicationDelegate` (em vez de `UIApplicationDelegate` anteriormente).
 - `LoadApplication` é chamado com uma nova instância da `App`.

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

### <a name="23-android-app"></a>2.3 aplicativo do android

Alterar o **MainActivity.cs** arquivo de forma que:

 - A classe herda de `FormsApplicationActivity` (em vez de `FormsActivity` anteriormente).
 - `LoadApplication` é chamado com uma nova instância do `App`

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

### <a name="24-windows-phone-app"></a>2.4 Windows Phone aplicativo

Precisamos atualizar o **MainPage** -o XAML e o code-behind.

Alterar o **MainPage. XAML** arquivo de forma que:

 - Elemento raiz XAML deve ser `winPhone:FormsApplicationPage`.
 - O `xmlns:phone` atributo deve ser *alterado* para `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Um exemplo atualizado é mostrado abaixo - você deve ter somente editar essas coisas (o restante dos atributos deve permanecer o mesmo):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Alterar o **MainPage.xaml.cs** arquivo de forma que:

 - A classe herda de `FormsApplicationPage` (em vez de `PhoneApplicationPage` anteriormente).
 - `LoadApplication` é chamado com uma nova instância do xamarin. Forms `App` classe. Talvez você precise qualificar totalmente essa referência como o Windows Phone tem sua própria `App` classe já definido.

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

Ocasionalmente, você verá um erro semelhante a isso após a atualização do pacote do NuGet xamarin. Forms. Ocorre quando o atualizador NuGet não remove completamente referências às versões mais antigas do seu **csproj** arquivos.

>O\_PROJECT.csproj: erro: este projeto faz referência a pacotes NuGet que estão faltando neste computador. Habilite a restauração de pacotes do NuGet para baixá-los.  Para obter mais informações, consulte http://go.microsoft.com/fwlink/?LinkID=322105. O arquivo ausente é... /.. /Packages/Xamarin.Forms.1.2.3.6257/Build/Portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (SEU\_PROJETO)

Para corrigir esses erros, abra o **csproj** do arquivo em um editor de texto e procure `<Target` elementos que se referem a versões mais antigas do xamarin. Forms, como o elemento mostrado abaixo. Você deve excluir manualmente este todo elemento do **csproj** de arquivo e salve as alterações.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

O projeto deve compilar com êxito depois que essas referências antigas são removidas.

## <a name="considerations"></a>Considerações

As seguintes considerações devem ser levadas em conta ao converter um projeto existente do xamarin. Forms da API clássica para a nova API unificada se esse aplicativo depende de um ou mais componentes ou pacote do NuGet.

### <a name="components"></a>Componentes

Qualquer componente que você incluiu em seu aplicativo também precisam ser atualizados para a API unificada ou você receberá um conflito ao tentar compilar. Para qualquer componente incluído, substituir a versão atual com uma nova versão do armazenamento de componente Xamarin que oferece suporte a API unificada e faça uma compilação limpa. Qualquer componente que ainda não foi convertido pelo autor, exibirá um aviso somente no repositório de componente de 32 bits.

### <a name="nuget-support"></a>Suporte do NuGet

Enquanto estamos contribuiu com alterações ao NuGet para funcionar com o suporte da API unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter NuGet reconheça as novas APIs.

Até lá, assim como os componentes, você precisará alternar qualquer pacote do NuGet incluir em seu projeto para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa posteriormente.

> [!IMPORTANT]
> **Observação:** se você tiver um erro no formato _"Erro 3 não pode incluir 'monotouch.dll' e 'Xamarin.iOS.dll' no mesmo projeto xamarin - 'Xamarin.iOS.dll' é referenciado explicitamente, enquanto a 'monotouch.dll' é referenciado por ' xxx, Versão = 0.0.000, Culture = neutral, PublicKeyToken = null'"_ depois de converter seu aplicativo para as APIs unificado, normalmente ele é devido à necessidade de um componente ou um pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que oferece suporte a APIs unificado e faça uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitação de 64 bits compilações de aplicativos xamarin

Para um aplicativo móvel de xamarin foi convertido para a API unificada, o desenvolvedor precisa ainda permitem a criação do aplicativo para computadores de 64 bits de opções do aplicativo. Consulte o **habilitando 64 Bit compilações do xamarin aplicativos de** do [considerações sobre a plataforma de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) compilações do documento para obter instruções detalhadas sobre como habilitar 64 bits.

## <a name="summary"></a>Resumo

O aplicativo xamarin. Forms agora deve ser atualizado para a versão 1.3.1 e o aplicativo iOS migradas para a API unificada (que dá suporte a arquiteturas de 64 bits na plataforma iOS).

Conforme observado acima, se seu aplicativo xamarin. Forms inclui código nativo como renderizadores personalizados ou, em seguida, eles também podem precisar de atualização para usar os novos tipos de serviços de dependência [introduzidos na API do Unified](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Atualizar dicas](~/cross-platform/macios/unified/updating-tips.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
