---
title: Atualizando aplicativos existentes do xamarin. Forms
description: Este documento descreve as etapas que devem ser seguidas para atualizar um aplicativo xamarin. Forms da API clássica para a API unificada.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: d5c16b034b07d3e9875412f041c16b293557438a
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61211845"
---
# <a name="updating-existing-xamarinforms-apps"></a>Atualizando aplicativos existentes do xamarin. Forms

_Siga estas etapas para atualizar um aplicativo xamarin. Forms existente para usar a API unificada e atualizar para a versão 1.3.1_

> [!IMPORTANT]
> Como a primeira versão que ofereça suporte a API unificada do xamarin. Forms 1.3.1, toda a solução deve ser atualizada para usar a versão mais recente ao mesmo tempo como migrar o aplicativo do iOS para unificado. Isso significa que além de atualizar o projeto do iOS para suporte unificado, você também precisará editar o código em _todos os_ os projetos na solução.

A atualização é executada em duas etapas:

1. Migre o aplicativo do iOS para a API unificada, usando o Visual Studio para o build do Mac na ferramenta de migração.

    - Use a ferramenta de migração para atualizar automaticamente o projeto.

    - Atualização do iOS nativo APIs conforme descrito nas instruções para [atualizar aplicativos iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (especificamente no código de serviço de dependência ou de renderizador personalizado).

2. Atualize a solução inteira para xamarin. Forms versão 1.3.

    1. Instale o pacote do NuGet xamarin. Forms 1.3.1.

    2. Atualização de `App` classe no código compartilhado.

    3. Atualização de `AppDelegate` no projeto do iOS.

    4. Atualização de `MainActivity` no projeto do Android.

    5. Atualização de `MainPage` no projeto do Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. o aplicativo (migração unificado) do iOS

Parte da migração requer a atualização de xamarin. Forms para a versão 1.3, que oferece suporte a API unificada. Em ordem para as referências de assembly correto a ser criado, primeiro precisamos atualizar o projeto do iOS para usar a API unificada.

### <a name="migration-tool"></a>Ferramenta de migração

Clique no projeto do iOS para que ele seja selecionado, escolha **projeto > migrar para a API unificada do xamarin. IOS...**  e concordar com a mensagem de aviso que aparece.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Escolher projeto > migrar para a API unificada do xamarin. IOS... e Concordo com a mensagem de aviso é exibido")

Isso será automaticamente:

 - Altere o tipo de projeto para dar suporte a API unificada de 64 bits.
 - Altere a referência do framework para **xamarin. IOS** (substituindo o antigo **monotouch** referência).
 - Altere as referências de namespace no código para remover o `MonoTouch` prefixo.
 - Atualizar o **csproj** arquivo para usar os destinos de compilação correta para a API unificada.

**Limpa** e **Build** o projeto para garantir que não existem outros erros para corrigir. Nenhuma ação adicional é necessária. Essas etapas são explicadas em mais detalhes os [documentos de API unificada](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Atualizar APIs do iOS nativas (se necessário)

Se você tiver adicionado o código nativo do iOS adicionais (como renderizadores personalizados ou serviços de dependência) talvez precise executar correções adicionais de código manual. Compilar novamente o seu aplicativo e consulte a [atualizando existentes de instruções de aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md) para obter mais informações sobre as alterações que podem ser necessárias. [Estas dicas](~/cross-platform/macios/unified/updating-tips.md) também ajudará a identificar as alterações necessárias.

## <a name="2-xamarinforms-131-update"></a>2. Atualização do xamarin. Forms 1.3.1

Depois que o aplicativo iOS foi atualizado para a API unificada, o restante da solução precisa ser atualizado para xamarin. Forms versão 1.3.1. Isso inclui:

 - Atualizando o pacote do NuGet xamarin. Forms em cada projeto.
 - Alterar o código para usar o xamarin. Forms novos `Application`, `FormsApplicationDelegate` (iOS) `FormsApplicationActivity` (Android), e `FormsApplicationPage` classes (Windows Phone).

Essas etapas são explicadas abaixo:

### <a name="21-update-nuget-in-all-projects"></a>2.1 atualizar o NuGet em todos os projetos

Atualizar o xamarin. Forms para 1.3.1 de pré-lançamento usando o Gerenciador de pacotes do NuGet para todos os projetos na solução: PCL (se houver), iOS, Android e Windows Phone. É recomendável que você **excluir e adicionar novamente** o pacote do NuGet xamarin. Forms para atualizar para a versão 1.3.

**OBSERVAÇÃO:** Versão do xamarin. Forms 1.3.1 está atualmente em *pré-lançamento*. Isso significa que você deve selecionar o **pré-lançamento** opção no NuGet via (um tique integrados no Visual Studio para Mac) ou uma lista suspensa de busca no Visual Studio para ver a versão de pré-lançamento mais recente.

> [!IMPORTANT]
> Se você estiver usando o Visual Studio, verifique se que a versão mais recente do Gerenciador de pacotes do NuGet está instalada. Versões mais antigas do NuGet no Visual Studio não instalará corretamente a versão unificada do xamarin. Forms 1.3.1. Vá para **Ferramentas > extensões e atualizações...**  e clique em de **instalado** lista para verificar se o **Gerenciador de pacotes do NuGet para Visual Studio** é pelo menos versão 2.8.5. Se ele for mais antigo, clique no **atualizações** lista para baixar a versão mais recente.

Depois que você atualizou o pacote do NuGet para xamarin. Forms 1.3.1, faça as seguintes alterações em cada projeto para atualizar para o novo `Xamarin.Forms.Application` classe.

### <a name="22-portable-class-library-or-shared-project"></a>2.2 biblioteca de classes portátil (ou projeto compartilhado)

Alterar o **App.cs** arquivo, de modo que:

 - O `App` agora herda `Application`.
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

Nós removemos completamente o `GetMainPage` método e em vez disso, defina a `MainPage` *propriedade* no `Application` subclasse.

Essa nova `Application` classe base também oferece suporte a `OnStart`, `OnSleep`, e `OnResume` substituições para ajudá-lo a gerenciar o ciclo de vida do seu aplicativo.

O `App` classe é então passado para um novo `LoadApplication` método em cada projeto de aplicativo, conforme descrito abaixo:

### <a name="23-ios-app"></a>Aplicativo do 2.3 iOS

Alterar o **AppDelegate.cs** arquivo, de modo que:

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

### <a name="23-android-app"></a>2.3 aplicativo android

Alterar o **MainActivity.cs** arquivo, de modo que:

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

### <a name="24-windows-phone-app"></a>2.4 Windows Phone App

Precisamos atualizar o **MainPage** -o XAML e code-behind.

Alterar o **MainPage. XAML** arquivo, de modo que:

 - O elemento raiz XAML deve ser `winPhone:FormsApplicationPage`.
 - O `xmlns:phone` atributo deve ser *alterado* para `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Um exemplo atualizado é mostrado abaixo, você só precisará editar essas coisas (o restante dos atributos deve permanecer o mesmo):

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Alterar o **MainPage.xaml.cs** arquivo, de modo que:

 - A classe herda de `FormsApplicationPage` (em vez de `PhoneApplicationPage` anteriormente).
 - `LoadApplication` é chamado com uma nova instância do xamarin. Forms `App` classe. Talvez você precise qualificar totalmente essa referência, pois o Windows Phone tem sua própria `App` classe já definida.

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

Ocasionalmente, você verá um erro semelhante a isso depois de atualizar o pacote NuGet do xamarin. Forms. Ocorre quando o atualizador do NuGet não remove completamente as referências a versões mais antigas do seu **csproj** arquivos.

>YOUR\_PROJECT.csproj: Erro: Este projeto faz referência a pacotes NuGet que estão faltando neste computador. Habilite a restauração do pacote do NuGet para baixá-los.  Para obter mais informações, consulte http://go.microsoft.com/fwlink/?LinkID=322105. O arquivo está faltando é... /.. /Packages/Xamarin.Forms.1.2.3.6257/Build/Portable-Win+net45+wp80+MonoAndroid10+MonoTouch10/Xamarin.Forms.targets. (SEU\_PROJETO)

Para corrigir esses erros, abra o **csproj** do arquivo em um editor de texto e procure `<Target` elementos que se referem às versões mais antigas do xamarin. Forms, como o elemento mostrado abaixo. Você deve excluir manualmente este elemento inteiro do **csproj** de arquivo e salve as alterações.

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

As seguintes considerações devem ser levadas em conta ao converter um projeto xamarin. Forms existente da API clássica para a nova API unificada se baseia-se de que o aplicativo em um ou mais componente ou pacote do NuGet.

### <a name="components"></a>Componentes

Qualquer componente que você incluiu em seu aplicativo também precisam ser atualizados para a API unificada, ou você receberá um conflito ao tentar compilar. Para qualquer componente incluído, substituir a versão atual com uma nova versão de Store de componente do Xamarin que dá suporte a API unificada e fazer uma compilação limpa. Qualquer componente que ainda não foi convertido pelo autor, exibirá um aviso apenas no repositório de componente de 32 bits.

### <a name="nuget-support"></a>Suporte do NuGet

Enquanto estamos contribuiu com as alterações no NuGet para trabalhar com o suporte de API unificada, não houve uma nova versão do NuGet, portanto, estamos avaliando como obter o NuGet para reconhecer as novas APIs.

Até esse momento, assim como os componentes, você precisará alternar qualquer pacote NuGet que você incluiu em seu projeto para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa posteriormente.

> [!IMPORTANT]
> Se você tiver um erro no formulário _"Erro 3 não pode incluir 'MonoTouch. dll' e 'Xamarin' no mesmo projeto xamarin. IOS – 'Xamarin' é referenciada explicitamente, enquanto 'MonoTouch. dll' é referenciado por ' xxx, versão = 0.0.000, Cultura = neutral, PublicKeyToken = null'"_ após converter seu aplicativo para as APIs unificadas, ela normalmente é devido a ter um componente ou o pacote do NuGet no projeto que não foi atualizado para a API unificada. Você precisará remover o componente/NuGet existente, atualize para uma versão que dá suporte a APIs unificadas e fazer uma compilação limpa.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Habilitação de 64 bits compilações de aplicativos xamarin. IOS

Para um aplicativo móvel de xamarin. IOS que foi convertido para a API unificada, o desenvolvedor precisa ainda permitem a criação do aplicativo para computadores de 64 bits de opções do aplicativo. Consulte a **habilitando bits de 64 compilações de aplicativos xamarin. IOS** da [considerações sobre plataformas de 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) compilações de documento para obter instruções detalhadas sobre como habilitar a 64 bits.

## <a name="summary"></a>Resumo

O aplicativo xamarin. Forms agora deve ser atualizado para a versão 1.3.1 e o aplicativo iOS é migrado para a API unificada (que dá suporte a arquiteturas de 64 bits na plataforma iOS).

Conforme observado acima, se seu aplicativo xamarin. Forms inclui código nativo, como renderizadores personalizados ou, em seguida, eles também podem precisar de atualização para usar os novos tipos de serviços de dependência [introduzidos na API unificada](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Links relacionados

- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Atualizando aplicativos do iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Trabalhar com tipos nativos em aplicativos de plataforma cruzada](~/cross-platform/macios/native-types-cross-platform.md)
- [Dicas de atualização](~/cross-platform/macios/unified/updating-tips.md)
- [Diferenças de API unificada do vs clássico](https://developer.xamarin.com/releases/ios/api_changes/classic-vs-unified-8.6.0/)
