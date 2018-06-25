---
title: Início rápido do Xamarin.Forms
description: Esse artigo explica como criar um aplicativo que converte um número de telefone alfanumérico inserido pelo usuário em um número de telefone e que chama o número.
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: 3f2f9c2d-d204-43bc-8c8a-a55ce1e6d2c8
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 05/03/2018
ms.openlocfilehash: 90394195afc4257656c8d09fab348156a7f549d5
ms.sourcegitcommit: 66682dd8e93c0e4f5dee69f32b5fc5a96443e307
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35242902"
---
# <a name="xamarinforms-quickstart"></a>Início rápido do Xamarin.Forms

Esse passo a passo demonstra como criar um aplicativo que converte um número de telefone alfanumérico inserido pelo usuário em um número de telefone e que chama o número. O aplicativo final é mostrado abaixo:

[![](quickstart-images/intro-app-examples-sml.png "Aplicativo Phoneword")](quickstart-images/intro-app-examples.png#lightbox "Aplicativo Phoneword")

Crie o aplicativo Phoneword da seguinte maneira:

# <a name="visual-studiotabvswin"></a>[Visual Studio](#tab/vswin)

1. Na tela **Iniciar**, inicialize o Visual Studio. Isso abre a página inicial:

    ![](quickstart-images/vs/start-page.png "Visual Studio")

2. No Visual Studio, clique em **Criar novo projeto...** para criar um novo projeto:

    ![](quickstart-images/vs/new-solution.png "Novo Projeto")

3. Na caixa de diálogo **Novo Projeto**, clique em **Multiplataforma**, selecione o modelo **Aplicativo Móvel (Xamarin.Forms)**, defina o Nome e o Nome da Solução como `Phoneword`, escolha um local adequado para o projeto e clique no botão **OK**:

    ![](quickstart-images/vs/new-project.w157.png "Modelos de Projeto de Multiplataforma")

4. Na caixa de diálogo **Novo Aplicativo de Plataforma Cruzada**, clique em **Aplicativo em Branco**, selecione **.NET Standard** como a estratégia de compartilhamento de código e clique no botão **OK**:

    ![](quickstart-images/vs/new-app.png "Novo Aplicativo de Multiplataforma")

5. No **Gerenciador de Soluções**, no projeto **Phoneword**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](quickstart-images/vs/open-mainpage-xaml.png "Abrir MainPage.xaml")

6. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código define declarativamente a interface do usuário para a página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, UWP" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    Salve as alterações em **MainPage.xaml** ao pressionar **CTRL+S** e feche o arquivo.

7. No **Gerenciador de Soluções**, expanda **MainPage.xaml** e clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](quickstart-images/vs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

8. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Os métodos `OnTranslate` e `OnCall` serão executados em resposta aos botões **Converter** e **Chamar** clicados na interface do usuário, respectivamente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos mais tarde.

    Salve as alterações em **MainPage.xaml.cs** ao pressionar **CTRL+S** e feche o arquivo.

9. No **Gerenciador de Soluções**, expanda **App.xaml** e clique duas vezes em **App.xaml.cs** para abri-lo:

    ![](quickstart-images/vs/open-app-class.png "Abrir App.xaml.cs")

10. Em **App.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. O construtor `App` define a classe `MainPage` como a página que será exibida quando o aplicativo for iniciado:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public App()
            {
                InitializeComponent();
                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Salve as alterações em **App.xaml.cs** ao pressionar **CTRL + S** e feche o arquivo.

11. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword** e selecione **Adicionar > Novo Item…**:

    ![](quickstart-images/vs/add-new-item.png "Adicionar Novo Item")

12. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Código > Classe**, nomeie o novo arquivo **PhoneTranslator** e clique no botão **Adicionar**:

    ![](quickstart-images/vs/add-translator-class.w157.png "Adicionar Nova Classe")

13. Em **PhoneTranslator.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código converterá uma palavra de telefone para um número de telefone:

    ```csharp
    using System.Text;

    namespace Core
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    Salve as alterações em **PhoneTranslator.cs** pressionando **CTRL + S** e feche o arquivo.

14. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword** e selecione **Adicionar > Novo Item…**:

    ![](quickstart-images/vs/add-new-item.png "Adicionar Novo Item")

15. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Código > Interface**, nomeie o novo arquivo **IDialer** e clique no botão **Adicionar**:

    ![](quickstart-images/vs/add-idialer-interface.w157.png "Adicionar Nova Interface")

16. Em **IDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código define um método `Dial` que deve ser implementado em cada plataforma para discar um número de telefone convertido:

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```

    Salve as alterações em **IDialer.cs** pressionando **CTRL + S** e feche o arquivo.

    > [!NOTE]
    > Agora o código comum para o aplicativo está concluído. O código de discagem do telefone específico da plataforma agora será implementado como um [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

17. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword.iOS** e selecione **Adicionar > Novo Item…**:

    ![](quickstart-images/vs/add-new-item-ios.png "Adicionar Novo Item")

18. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Código > Classe**, nomeie o novo arquivo **PhoneDialer** e clique no botão **Adicionar**:

    ![](quickstart-images/vs/new-phone-dialer-ios.w157.png "Adicionar Nova Classe")

19. Em **PhoneDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código cria o método <code>Dial</code> que será usado na plataforma iOS para discar um número de telefone convertido:

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    Salve as alterações em **PhoneDialer.cs** pressionando **CTRL + S** e feche o arquivo.

20. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Phoneword.Android** e selecione **Adicionar > Novo Item…**:

    ![](quickstart-images/vs/add-new-item-android.png "Adicionar Novo Item")

21. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Android > Classe**, nomeie o novo arquivo **PhoneDialer** e clique no botão **Adicionar**:

    ![](quickstart-images/vs/new-phone-dialer-android.w157.png "Adicionar Nova Classe")

22. Em **PhoneDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código cria o método `Dial` que será usado na plataforma do Android para discar um número de telefone convertido:

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionCall);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    Salve as alterações em **PhoneDialer.cs** pressionando **CTRL + S** e feche o arquivo.

23. Em **Gerenciador de Soluções**, no projeto **Phoneword.Android**, clique duas vezes em **MainActivity.cs** para abri-lo, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MainTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);

                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }  
    ```

    Salve as alterações em **MainActivity.cs** pressionando **CTRL+S** e feche o arquivo.

24. No **Gerenciador de Soluções**, no projeto **Phoneword.Android**, clique duas vezes em **Propriedades** e selecione a guia **Manifesto do Android**:

    ![](quickstart-images/vs/android-manifest.png "Abrir Manifesto do Android")

25. Na seção **Permissões necessárias**, habilite a permissão **CALL_PHONE**. Isso dá ao aplicativo permissão para fazer uma chamada telefônica:

    ![](quickstart-images/vs/android-manifest-changed.png "Habilitar a Permissão de CallPhone")

    Salve as alterações no manifesto pressionando **CTRL + S** e feche o arquivo.

26. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword.UWP** e selecione **Adicionar > Novo Item…**:

    ![](quickstart-images/vs/add-new-item-uwp.png "Adicionar Novo Item")

27. Na caixa de diálogo **Adicionar novo item**, selecione **Visual C# > Código > Classe**, nomeie o novo arquivo **PhoneDialer** e clique no botão **Adicionar**:

    ![](quickstart-images/vs/new-phone-dialer-uwp.w157.png "Adicionar Nova Classe")

28. Em **PhoneDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código cria o método `Dial`, bem como os métodos de ajuda, que serão usados na Plataforma Universal do Windows para discar um número de telefone convertido:

    ```csharp
    using Phoneword.UWP;
    using System;
    using System.Threading.Tasks;
    using Windows.ApplicationModel.Calls;
    using Windows.UI.Popups;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.UWP
    {
        public class PhoneDialer : IDialer
        {
            bool dialled = false;

            public bool Dial(string number)
            {
                DialNumber(number);
                return dialled;
            }

            async Task DialNumber(string number)
            {
                var phoneLine = await GetDefaultPhoneLineAsync();
                if (phoneLine != null)
                {
                    phoneLine.Dial(number, number);
                    dialled = true;
                }
                else
                {
                    var dialog = new MessageDialog("No line found to place the call");
                    await dialog.ShowAsync();
                    dialled = false;
                }
            }

            async Task<PhoneLine> GetDefaultPhoneLineAsync()
            {
                var phoneCallStore = await PhoneCallManager.RequestStoreAsync();
                var lineId = await phoneCallStore.GetDefaultLineAsync();
                return await PhoneLine.FromIdAsync(lineId);
            }
        }
    }
    ```

    Salve as alterações em **PhoneDialer.cs** pressionando **CTRL + S** e feche o arquivo.

29. No **Gerenciador de Soluções**, no projeto **Phoneword.UWP**, clique com o botão direito do mouse em **Referências** e selecione **Adicionar Referência...**:

    ![](quickstart-images/vs/uwp-add-reference.png "Adicionar Referência")

30. Na caixa de diálogo **Gerenciador de Referências**, selecione **Universal do Windows > Extensões > Extensões do Windows Mobile para UWP** e clique no botão **OK**:

    ![](quickstart-images/vs/uwp-add-reference-extensions.png "Adicionar Extensões Móveis do Windows para UWP")

31. No **Gerenciador de Soluções**, no projeto **Phoneword.UWP**, clique duas vezes em **Package.appxmanifest**:

    ![](quickstart-images/vs/uwp-manifest.png "Abrir o Manifesto UWP")

31. Na página **Funcionalidades**, habilite a funcionalidade **Chamada telefônica**. Isso dá ao aplicativo permissão para fazer uma chamada telefônica:

    ![](quickstart-images/vs/uwp-manifest-changed.png "Habilitar o Recurso de Chamada Telefônica")

    Salve as alterações no manifesto pressionando **CTRL + S** e feche o arquivo.

32. No Visual Studio, selecione o item de menu **Compilar > Compilar Solução** (ou pressione **CTRL+SHIFT+B**). O aplicativo será criado e uma mensagem de êxito será exibida na barra de status do Visual Studio:

    ![](quickstart-images/vs/build-successful.png "Build bem-sucedido")

    Se houver erros, repita as etapas anteriores e corrija qualquer erro até que o aplicativo seja compilado com êxito.

33. No **Gerenciador de Soluções**, clique com botão direito do mouse no projeto **Phoneword.UWP** e selecione **Definir como Projeto de Inicialização**:

    ![](quickstart-images/vs/uwp-set-as-startup-project.png "Definir como Projeto Inicial")

34. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo:

    ![](quickstart-images/vs/start.png "Barra de Ferramentas do Visual Studio")
    ![](quickstart-images/vs/phone-result-uwp.png "UWP do Aplicativo Phoneword")

35. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **Phoneword.Android** e selecione **Definir como Projeto de Inicialização**.
36. Na barra de ferramentas do Visual Studio, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo em um emulador do Android.
37. Se você tiver um dispositivo iOS e atender aos requisitos de sistema do Mac para o desenvolvimento do Xamarin.Forms, use uma técnica semelhante para implantar o aplicativo no dispositivo iOS. Como alternativa, implante o aplicativo para o [simulador remoto de iOS](~/tools/ios-simulator.md).

    Observação: não há suporte para chamadas telefônicas em todos os simuladores.

# <a name="visual-studio-for-mactabvsmac"></a>[Visual Studio para Mac](#tab/vsmac)

1. Inicialize o Visual Studio para Mac e, na página inicial, clique em **Novo Projeto...** para criar um novo projeto:

    ![](quickstart-images/xs/new-solution.png "Nova Solução")

2. Na caixa de diálogo **Escolher um modelo para seu novo projeto**, clique em **Multiplataforma > Aplicativo**, selecione o modelo do **Aplicativo de Formulários em Branco** e clique no botão **Avançar**:

    ![](quickstart-images/xs/choose-template.png "Escolher um modelo")

3. Na caixa de diálogo **Configurar seu Aplicativo de Formulários em Branco**, nomeie o novo aplicativo `Phoneword`, certifique-se de que o botão de opção **Usar biblioteca de classes portátil** esteja selecionado, verifique se a caixa de seleção **Usar XAML para arquivos de interface do usuário** está selecionada e clique no botão **Avançar**:

    ![](quickstart-images/xs/configure-app.png "Configurar o Aplicativo Forms")

4. Na caixa de diálogo **Configurar seu novo aplicativo de Formulários em Branco**, deixe os nomes do Projeto e da Solução definidos como `Phoneword`, escolha um local adequado para o projeto e clique no botão **Criar** para criar o projeto:

    ![](quickstart-images/xs/configure-project.png "Configurar o Projeto Forms")

5. No **Painel de Soluções**, selecione o projeto **Phoneword**, clique com botão direito do mouse e selecione **Adicionar > Novo arquivo...**:

    ![](quickstart-images/xs/add-new-file.png "Adicionar Novo Arquivo")

6. Na caixa de diálogo **Novo Arquivo**, selecione **Formulários > Formulários ContentPage XAML**, nomeie o novo arquivo **MainPage** e clique no botão **Novo**. Isso adicionará uma página chamada **MainPage** ao projeto:

    ![](quickstart-images/xs/add-mainpage-class.png "Adicionar Novo ContentPage")

7. No **Painel de Soluções**, clique duas vezes em **MainPage.xaml** para abri-lo:

    ![](quickstart-images/xs/open-mainpage-xaml.png "Abrir MainPage.xaml")

8. Em **MainPage.xaml**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código define declarativamente a interface do usuário para a página:

    ```xaml
    <?xml version="1.0" encoding="UTF-8"?>
    <ContentPage xmlns="http://xamarin.com/schemas/2014/forms"
                       xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
                       x:Class="Phoneword.MainPage">
        <ContentPage.Padding>
            <OnPlatform x:TypeArguments="Thickness">
                <On Platform="iOS" Value="20, 40, 20, 20" />
                <On Platform="Android, WinPhone, Windows" Value="20" />
            </OnPlatform>
        </ContentPage.Padding>
        <StackLayout>
          <Label Text="Enter a Phoneword:" />
          <Entry x:Name="phoneNumberText" Text="1-855-XAMARIN" />
          <Button x:Name="translateButon" Text="Translate" Clicked="OnTranslate" />
          <Button x:Name="callButton" Text="Call" IsEnabled="false" Clicked="OnCall" />
        </StackLayout>
    </ContentPage>
    ```

    Salve as alterações em **MainPage.xaml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

9. No **Painel de Soluções**, clique duas vezes em **MainPage.xaml.cs** para abri-lo:

    ![](quickstart-images/xs/open-mainpage-codebehind.png "Abrir MainPage.xaml.cs")

10. Em **MainPage.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Os métodos `OnTranslate` e `OnCall` serão executados em resposta aos botões **Converter** e **Chamar** clicados na interface do usuário, respectivamente:

    ```csharp
    using System;
    using Xamarin.Forms;

    namespace Phoneword
    {
        public partial class MainPage : ContentPage
        {
            string translatedNumber;

            public MainPage ()
            {
                InitializeComponent ();
            }

            void OnTranslate (object sender, EventArgs e)
            {
                translatedNumber = Core.PhonewordTranslator.ToNumber (phoneNumberText.Text);
                if (!string.IsNullOrWhiteSpace (translatedNumber)) {
                    callButton.IsEnabled = true;
                    callButton.Text = "Call " + translatedNumber;
                } else {
                    callButton.IsEnabled = false;
                    callButton.Text = "Call";
                }
            }

            async void OnCall (object sender, EventArgs e)
            {
                if (await this.DisplayAlert (
                        "Dial a Number",
                        "Would you like to call " + translatedNumber + "?",
                        "Yes",
                        "No")) {
                    var dialer = DependencyService.Get<IDialer> ();
                    if (dialer != null)
                        dialer.Dial (translatedNumber);
                }
            }
        }
    }
    ```

    > [!NOTE]
    > Tentar compilar o aplicativo neste ponto resultará em erros que serão corrigidos mais tarde.

    Salve as alterações em **MainPage.xaml.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

11. No **Painel de Soluções**, clique duas vezes em **App.xaml.cs** para abri-lo:

    ![](quickstart-images/xs/open-app-class.png "Abrir App.xaml.cs")

12. Em **App.xaml.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. O construtor `App` define a classe `MainPage` como a página que será exibida quando o aplicativo for iniciado:

    ```csharp
    using Xamarin.Forms;
    using Xamarin.Forms.Xaml;

    [assembly: XamlCompilation(XamlCompilationOptions.Compile)]
    namespace Phoneword
    {
        public partial class App : Application
        {
            public App()
            {
                InitializeComponent();
                MainPage = new MainPage();
            }

            protected override void OnStart()
            {
                // Handle when your app starts
            }

            protected override void OnSleep()
            {
                // Handle when your app sleeps
            }

            protected override void OnResume()
            {
                // Handle when your app resumes
            }
        }
    }
    ```

    Salve as alterações em **Phoneword.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

13. No **Painel de Soluções**, selecione o projeto **Phoneword**, clique com botão direito do mouse e selecione **Adicionar > Novo arquivo...**:

    ![](quickstart-images/xs/add-new-translator-file.png "Adicionar Novo Arquivo")

14. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia**, nomeie o novo arquivo **PhoneTranslator** e clique no botão **Novo**:

    ![](quickstart-images/xs/add-translator-class.png "Adicionar Nova Classe")

15. Em **PhoneTranslator.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código converterá uma palavra de telefone para um número de telefone:

    ```csharp
    using System.Text;

    namespace Core
    {
        public static class PhonewordTranslator
        {
            public static string ToNumber(string raw)
            {
                if (string.IsNullOrWhiteSpace(raw))
                    return null;

                raw = raw.ToUpperInvariant();

                var newNumber = new StringBuilder();
                foreach (var c in raw)
                {
                    if (" -0123456789".Contains(c))
                        newNumber.Append(c);
                    else
                    {
                        var result = TranslateToNumber(c);
                        if (result != null)
                            newNumber.Append(result);
                        // Bad character?
                        else
                            return null;
                    }
                }
                return newNumber.ToString();
            }

            static bool Contains(this string keyString, char c)
            {
                return keyString.IndexOf(c) >= 0;
            }

            static readonly string[] digits = {
                "ABC", "DEF", "GHI", "JKL", "MNO", "PQRS", "TUV", "WXYZ"
            };

            static int? TranslateToNumber(char c)
            {
                for (int i = 0; i < digits.Length; i++)
                {
                    if (digits[i].Contains(c))
                        return 2 + i;
                }
                return null;
            }
        }
    }
    ```

    Salve as alterações em **PhoneTranslator.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

16. No **Painel de Soluções**, selecione o projeto **Phoneword**, clique com botão direito do mouse e selecione **Adicionar > Novo arquivo...**:

    ![](quickstart-images/xs/add-new-interface.png "Adicionar Novo Arquivo")

17. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Interface Vazia**, nomeie o novo arquivo **IDialer** e clique no botão **Novo**:

    ![](quickstart-images/xs/add-idialer-interface.png "Adicionar Nova Interface")

18. Em **IDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código define um método `Dial` que deve ser implementado em cada plataforma para discar um número de telefone convertido:

    ```csharp
    namespace Phoneword
    {
        public interface IDialer
        {
            bool Dial(string number);
        }
    }
    ```
    Salve as alterações em **IDialer.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

    > [!NOTE]
    > Agora o código comum para o aplicativo está concluído. O código de discagem do telefone específico da plataforma agora será implementado como um [DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md).

19. No **Painel de Soluções**, selecione o projeto **Phoneword.iOS**, clique com botão direito do mouse e selecione **Adicionar > Novo arquivo...**:

    ![](quickstart-images/xs/add-new-file-ios.png "Adicionar Novo Arquivo")

20. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia**, nomeie o novo arquivo **PhoneDialer** e clique no botão **Novo**:

    ![](quickstart-images/xs/new-phonedialer-ios.png "Adicionar Nova Classe")

21. Em **PhoneDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código cria o método `Dial` que será usado na plataforma iOS para discar um número de telefone convertido:

    ```csharp
    using Foundation;
    using Phoneword.iOS;
    using UIKit;
    using Xamarin.Forms;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.iOS
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                return UIApplication.SharedApplication.OpenUrl (
                    new NSUrl ("tel:" + number));
            }
        }
    }
    ```

    Salve as alterações em **PhoneDialer.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

22. No **Painel de Soluções**, selecione o projeto **Phoneword.Droid**, clique com botão direito do mouse e selecione **Adicionar > Novo arquivo...**:

    ![](quickstart-images/xs/add-new-file-android.png "Adicionar Novo Arquivo")

23. Na caixa de diálogo **Novo Arquivo**, selecione **Geral > Classe Vazia**, nomeie o novo arquivo **PhoneDialer** e clique no botão **Novo**:

    ![](quickstart-images/xs/new-phonedialer-android.png "Adicionar Nova Classe")

24. Em **PhoneDialer.cs**, remova todo o código de modelo e substitua-o pelo código a seguir. Esse código cria o método `Dial` que será usado na plataforma do Android para discar um número de telefone convertido:

    ```csharp
    using Android.Content;
    using Android.Telephony;
    using Phoneword.Droid;
    using System.Linq;
    using Xamarin.Forms;
    using Uri = Android.Net.Uri;

    [assembly: Dependency(typeof(PhoneDialer))]
    namespace Phoneword.Droid
    {
        public class PhoneDialer : IDialer
        {
            public bool Dial(string number)
            {
                var context = MainActivity.Instance;
                if (context == null)
                    return false;

                var intent = new Intent (Intent.ActionCall);
                intent.SetData (Uri.Parse ("tel:" + number));

                if (IsIntentAvailable (context, intent)) {
                    context.StartActivity (intent);
                    return true;
                }

                return false;
            }

            public static bool IsIntentAvailable(Context context, Intent intent)
            {
                var packageManager = context.PackageManager;

                var list = packageManager.QueryIntentServices (intent, 0)
                    .Union (packageManager.QueryIntentActivities (intent, 0));

                if (list.Any ())
                    return true;

                var manager = TelephonyManager.FromContext (context);
                return manager.PhoneType != PhoneType.None;
            }
        }
    }
    ```

    Salve as alterações em **PhoneDialer.cs** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

25. No **Painel de Soluções**, no projeto **Phoneword.Droid**, clique duas vezes em **MainActivity.cs** para abri-lo, remova todo o código de modelo e substitua-o pelo código a seguir:

    ```csharp
    using Android.App;
    using Android.Content.PM;
    using Android.OS;

    namespace Phoneword.Droid
    {
        [Activity(Label = "Phoneword", Icon = "@drawable/icon", Theme = "@style/MyTheme", MainLauncher = true, ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsAppCompatActivity
        {
            internal static MainActivity Instance { get; private set; }

            protected override void OnCreate(Bundle bundle)
            {
                TabLayoutResource = Resource.Layout.Tabbar;
                ToolbarResource = Resource.Layout.Toolbar;

                base.OnCreate(bundle);

                Instance = this;
                global::Xamarin.Forms.Forms.Init(this, bundle);
                LoadApplication(new App());
            }
        }
    }        
    ```

    Salve as alterações em **MainActivity.cs** escolhendo **Arquivo > Salvar** (ou pressionando **&#8984; + S**) e feche o arquivo.

    > [!NOTE]
    > O código de exemplo usa `Theme="@style/MainTheme"` porque ele é baseado em um modelo mais antigo. É possível verificar o nome do estilo correto em **Phoneword/Droid/Resources/values/styles.xml** se ocorrer um erro do compilador para o nome do tema.

26. No **Painel de Soluções**, expanda a pasta **Propriedades** e clique duas vezes no arquivo **AndroidManifest.xml**:

    ![](quickstart-images/xs/android-manifest.png "Abrir Manifesto do Android")

27. Na seção **Permissões necessárias**, habilite a permissão **CallPhone**. Isso dá ao aplicativo permissão para fazer uma chamada telefônica:

    ![](quickstart-images/xs/android-manifest-changed.png "Habilitar a Permissão de CallPhone")

    Salve as alterações em **AndroidManifest.xml** ao escolher **Arquivo > Salvar** (ou ao pressionar **& #8984; + S**) e feche o arquivo.

28. No **Painel de Soluções**, remova a classe **PhonewordPage** do projeto **Phoneword**. Essa página foi adicionada automaticamente quando o projeto foi criado e não é mais necessária.
29. No Visual Studio para Mac, selecione o item de menu **Compilar > Compilar Tudo** (ou pressione **&#8984; + B**). O aplicativo será criado e uma mensagem de êxito será exibida na barra do Visual Studio para Mac.

    ![](quickstart-images/xs/build-successful.png "Build bem-sucedido")

30. Se houver erros, repita as etapas anteriores e corrija qualquer erro até que o aplicativo seja compilado com êxito.
31. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo no Simulador do iOS:

    ![](quickstart-images/xs/start.png "Barra de Ferramentas do Visual Studio para Mac")
    ![](quickstart-images/xs/phoneword-result-ios.png "Simulador de iOS")

    Observação: não há suporte para chamadas telefônicas no Simulador de iOS.

32. No **Painel de Soluções**, selecione o projeto **Phoneword.Droid**, clique com botão direito do mouse e selecione **Definir como projeto de inicialização**:

    ![](quickstart-images/xs/set-startup-project.png "Definir como Projeto Inicial")

33. Na barra de ferramentas do Visual Studio para Mac, pressione o botão **Iniciar** (o botão triangular que se parece com o botão Reproduzir) para inicializar o aplicativo em um emulador do Android:

    ![](quickstart-images/xs/phoneword-result-android.png "Android Emulator")

    Observação: não há suporte para chamadas telefônicas em emuladores de Android.

-----

Parabéns por concluir um aplicativo Xamarin.Forms. O [próximo tópico](~/xamarin-forms/get-started/hello-xamarin-forms/deepdive.md) deste guia analisa as etapas seguidas para entender os conceitos básicos do desenvolvimento de aplicativos com o Xamarin.Forms.


## <a name="related-links"></a>Links relacionados

- [Acessando recursos nativos por meio do DependencyService](~/xamarin-forms/app-fundamentals/dependency-service/index.md)
- [Phoneword (amostra)](https://developer.xamarin.com/samples/xamarin-forms/Phoneword/)
